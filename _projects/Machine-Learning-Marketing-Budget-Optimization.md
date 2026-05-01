---
layout: page
title: Clustering products for marketing budget optimization
description: An ML clustering + budget optimization pipeline to maximize contribution margin
img: assets/img/budget-allocation/predicting-budget-allocation.png
importance: 1
featured: true
category: work
---

This project is a result of my work with [rebuy](https://www.rebuy.de/), a recommerce startup in Berlin. Their marketing and acquisitions team needed a data-driven way to allocate budgets for their marketing campaigns. 

As is typical in the ecommerce industry, companies handle thousands of unique products with widely varying margins, stock levels, and price competitiveness. To efficiently allocate their ad budgets across their inventory, I developed a budget allocation tool - a machine learning pipeline that predicts product-level performance, clusters similar items into campaigns, and optimizes marketing spend at the campaign level. 

Each campaign would group a set of products in a way as to maximise the profit (contribution margin). The pipeline involved data extraction from their data warehouse Snowflake, EDA, feature engineering, predicting modelling, response curve generation, clustering and constrianed optimization.


## What I used

- Snowflake for data warehousing
- SQL for data extraction
- [HEX notebook](https://hex.tech/product/notebooks/) - the company tool for data science projects and publishing, works just like Jupyter
- scipy, numpy, sklearn, scipy, plotly, matplotlib - Packages relevant for data science, ML prediction, constrained optimization and plotting

## Data source and extraction

The data was stored in the company’s central data warehouse using Snowflake. Using SQL I extracted **weekly level data** comprising a number or product features including:

- Google ads performance data: ad costs, impressions, clicks, conversions
- Product data: sale price, competitor price, contribution margin (CM1), stock availability.
- Product category data: Manufacturer, Category name etc.
- Some engineered features like PCI (Price Competitiveness Index): product price vs competitor price.

The result was a nice usable dataframe that was essentially a weekly time series of all products going back 18 months like below (brand names masked for anonymization and only select features shown). 
<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/data-frame.png" title="Data frame for ML marketing budget optimization" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<br>

## Preprocessing and exploratory data analysis

Some essential preprocessing and EDA included:

- Removal of nulls, duplicates and outliers and converting snapshot date to datetime format.
- Exploring distributions of the different features to get a sense of the range of values and which ones still have outliers.
- Correlation analysis to find out what features are most correlated to CM1 (and which pairs are correlated among themselves to remove redundant ones and prevent multicollinearity before modelling).

<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/correlation_matrix.png" title="Correlation matrix for ML marketing campaign budget optimization" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<br>

It became clear that while no feature is highly correlated with CM1 or ROAS

- CLICKS is highly correlated with AD_COSTS (0.89), IMPRESSIONS (0.8) - which makes sense since more spending means more eyeballs and more clicks, and somewhat highl correlated with CONVERSIONS (0.69), which also makes sense along similar lines.

This means we could only include AD_COSTS among these, which also is necessary since we want to know how much impact AD_COSTS has on CM1. 

- Defining feature and target sets for modelling - with those select features and the target as CM1.

### Predictive modelling

The overall logic flow for this was as follows:

#### 1. Engineer new features that represent ad cost-CM1 behaviour of each product

In clustering the products into different buckets, we also want to include features that represent the product’s ad-cost CM1 behaviour - i.e. how each product’s CM1 changes with ad cost. This is done by a “response curve” creation - by fitting a log curve to the ad costs and CM1 value for each product, which will give us **2 “similarity features” - a and b** - that come from the log function that represents the response curve for each product. 

> Why log function? This is a common function used in marketing mix modelling how performance of ads responds to ad costs. Log functions capture the effect of increasing performance with ad costs and then a subsequent "diminishing returns effect" - where initial spend gives big returns but spending more and more does not boost performance as much compared to the initial spend.

For this step, I also needed to generate artificial data of ad costs for each product and predict CM1 for those ad costs, because there was not enough data points for all products. 

For the prediction, a **RandomForest model** was chosen, trained on the available data. RandomForest works well in this case because it can handle non-linear relationships between features (like price, margins, and competition) without requiring strong assumptions. It also performs well on tabular data and gives stable predictions, which is important since these predictions are later used to fit spend–response curves.


#### 2. Preparing the data for clustering

Next up was the preparation of the dataframe that would go into the clustering algorithm. This involved a few steps:

- **Associating the similarity features for each product:** After the feature engineering step to get the similarity features a and b, I merge these with the other features selected from the original dataframe. 

- **Latest week’s data snapshot:** Then I took a “latest snapshot” of the latest week numbers of the products in the cleaned dataframe. This is because we wanted the clustering for the next month’s campaigns to be influenced by the latest week’s data.  

- **Include core products:**There was also a business requirement to check for some “core products” which was a list of products provided separately. I checked if any of these core products were dropped in the data cleaning stage (due to any missing value in any of the columns mainly) and merged them back into the latest week’s snapshot. This means that there would be some products that go into the clustering algorithm that had some features missing. This is fine because we do want to cluster all the products for the next month’s campaign.

- **Impute missing a and b values in the latest snapshot.** : However, we do want to impute missing a and b values in this data set since the clustering should also consider how products behave in their ad-costs-CM1 response curve. And this is available for all products since the a and b values were predicted from historical data and not just the latest week’s data. The imputation is doing using **K**-**Nearest** **Neighbours**- i.e filling in missing a and b values by looking at similar products. The idea is that products with similar characteristics are likely to have similar response curves, so this method suffices in this case.

#### 3. Clustering using KMeans

For the actual clustering, I normalized the features to ensure that no individual feature was influencing the clustering disproportionately, and used the elbow method to find the approximate number of clusters. The elbow method yields a plot of the inertia against the no. of clusters tested. This gives an idea of how closely packed the products are within a cluster (so that different clusters are appropriately separated and distinguishable) for various cluster number choices.

<br>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/elbow-method-clustering.png" title="Elbow method for finding number of clusters" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

The no. of clusters to be chosen is the point at which there is an “elbow” in the graph - a point of somewhat sharp bent, which indicates that the products within each cluster for that many clusters are more closely packed and any more clusters don’t give significant improvements in distinguishing the product clusters. This usually came around to 8 to 10 in the graph but the decision also depended on business needs - how many campaigns the acquisition team wanted to set up in the coming month. 

With the number of clusters chosen, a KMeans algorithm was used to cluster the products. KMeans again is a commonly used clustering algorithm - it is simple, fast, and works well on numerical data.

<details>
  <summary>See code</summary>

    ```python
    from sklearn.cluster import KMeans
    from sklearn.preprocessing import StandardScaler

    def cluster_similar_products(df, n_clusters=10):
        feature_df = df.drop(['PRODUCT_ID','CM1','AD_COSTS'], axis=1)

        # Normalize the features
        scaler = StandardScaler()
        normalized_features = scaler.fit_transform(feature_df)
        
        # Perform K-means clustering
        kmeans = KMeans(n_clusters=n_clusters, random_state=42)
        clusters = kmeans.fit_predict(normalized_features)
        
        # Add cluster information to the feature DataFrame
        df['Cluster'] = clusters
        return df

    ``` 
</details>

<br>


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/cluster_table.png" title="Clustered dataframe with products tagged to clusters" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

Although it doesn’t show the product clusters as distinct in 2 dimensions (since the features used for clustering are many), it helps to see a pairwise plot of 2 metrics for each cluster. 

<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/pairwise_plot_cluster.png" title="Pairwise plot of products by cluster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

Once the clustering is done, I merged the cluster assignments of each product to the weekly snapshot data from the original data frame and aggregated the values of key metrics per cluster for each SNAPSHOT_DATE, to be fit these values to a log curve again. 

<br>


```python
    weekly_cluster_df = pd.merge(df[['SNAPSHOT_DATE','PRODUCT_ID','CM1','AD_COSTS']], clustered_df[['PRODUCT_ID','Cluster']], on=['PRODUCT_ID'], how='inner')
    weekly_cluster_grouped = weekly_cluster_df.groupby(['SNAPSHOT_DATE','Cluster'])[['CM1','AD_COSTS']].sum().reset_index()
``` 

<br>

#### 4. Fitting the weekly aggregate data to a log curve

Next, for each cluster, I fit the aggregated AD_COSTS and CM1 values to a log curve. This is so that we can get the “a” and “b” similarity values for each cluster.
<br>

<details>
  <summary>See code</summary>

    ```python 
    from scipy.optimize import curve_fit

    def log_func(x, a, b):
        return a * np.log(x) + b



    df_cluster_function = pd.DataFrame(columns=['Cluster', 'a', 'b', 'cost'])

    for cluster in range(10):
        weekly_cluster_select = weekly_cluster_grouped[weekly_cluster_grouped['Cluster'] == cluster]
        
        # Prepare data for curve fitting
        x = weekly_cluster_select['AD_COSTS'].values
        y = weekly_cluster_select['CM1'].values
        
        # Perform logarithmic curve fitting
        popt, _ = curve_fit(log_func, x, y)
        a, b = popt
        mean_ad_cost = x.mean()
        
        new_row = pd.DataFrame([{'Cluster': cluster, 'a': a, 'b': b, 'cost': mean_ad_cost}])
        df_cluster_function = pd.concat([df_cluster_function, new_row], ignore_index=True)
    ``` 
</details>

<br>

And using these a and b values for each cluster, I optimized a set budget across the clusters.

#### 5. Budget allocation across clusters

The most important step in the project - the actual budget allocation - was essentially an optimization problem. The goal was to distribute a fixed total budget across clusters to maximize expected CM1 (minimize the negative of expected CM1) based on the fitted response curves. The constraints were 

- each cluster has minimum and maximum spend limits based on past data.
- the total of minimum budgets should not exceed total budget and
- sum of maximum budgets should be at least the total budget

<br>

<details>
  <summary>See code</summary>

    ```python
    from scipy.optimize import minimize

    def objective(budgets, a_values, b_values):
        return -np.sum([log_func(budget, a, b) for budget, a, b in zip(budgets, a_values, b_values)])

    def constraint(budgets):
        return np.sum(budgets) - total_budget

    # Example data
    np.random.seed(42)  # for reproducibility
    num_campaigns = 10
    total_budget = 20000

    a_values = df_cluster_function['a'].values
    b_values = df_cluster_function['b'].values

    # # Define min and max boundaries for each campaign
    min_budgets = df_cluster_function['cost'].values * 0.5
    max_budgets = df_cluster_function['cost'].values * 3


    # Ensure that the sum of min budgets doesn't exceed total budget
    # and the sum of max budgets is at least the total budget
    while np.sum(min_budgets) > total_budget or np.sum(max_budgets) < total_budget:
        min_budgets = np.random.uniform(100, 500, num_campaigns)
        max_budgets = np.random.uniform(1000, 2000, num_campaigns)

    # Initial guess: equal distribution, but respecting min/max bounds
    initial_budgets = np.clip(np.ones(num_campaigns) * (total_budget / num_campaigns), min_budgets, max_budgets)

    # Adjust initial guess to meet total budget constraint
    initial_budgets *= total_budget / np.sum(initial_budgets)

    # Optimization
    constraints = {'type': 'eq', 'fun': constraint}
    bounds = [(min_budget, max_budget) for min_budget, max_budget in zip(min_budgets, max_budgets)]

    result = minimize(objective, initial_budgets, args=(a_values, b_values), 
                    method='SLSQP', bounds=bounds, constraints=constraints)

    optimized_budgets = result.x
    ```
</details>
<br>

The SLSQP (Sequential Least Squares Programming) method was chosen for the optimization  because  SLSQP, a method that can handle non-linear relationships and constraints. The result is an optimized budget allocation for the different clusters that together maximizes the CM1. 

<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/CM1-Budget-Curves-per-cluster.png" title="CM1 Budget Response Curves per cluster" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

### 6. Merging small impact clusters

There may be some clusters which are small in their impact - small budget allocated and small resulting CM1 compared to the others. They may also have fewer products. From a business perspective, it makes sense to merge these together into a single cluster. 

With these new clusters, the optimization is done again to get the optimum budgets allocated to the clusters.

<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/budget-allocation-results.png" title="ML Marketing Campagin Budget Optimization Results" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/budget-allocation/final budget allocation output.png" title="ML Marketing Campagin Budget Optimization Results Final Output" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br>

As a last step, products which have no data are bucketed in a “Cluster 100” so that the acquisition team can filter them out as needed.