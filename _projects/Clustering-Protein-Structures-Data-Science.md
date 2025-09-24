---
layout: page
title: Clustering binding sites based on distance and similarity metrics
description: ETL pipeline for exploring protein binding sites and similarity-based clustering
img: assets/img/clustering-protein/clustering-protein-data-science.png
importance:   
featured: 
category:  
---

This was a continuation of my work with the research team under Dr. Petr Popov at Constructor University. In the [first part of the project](https://surajkarak.github.io/projects/Spatiotemporal-analysis-protein-ligand-interactions/), I processed a collection of PDB files, computed the protein-ligand interaction fingerprints, explored their distributions including Gaussian smoothing and time series analysis followed by a visualization and an ETL pipeline to automate the whole workflow. 

Following this, the research team was looking for ways to derive some patterns among various “binding sites” and “hotspots” in another collection of frames. 

Binding sites are specific regions on a protein where small molecules - these could be drugs, ligands, or other biomolecules - can attach to interact with the protein. These sites influence the protein’s function, like how the protein behaves, how it interacts with other molecules, or how effective a drug can be. For e.g., in drug design, researchers often want to know exactly where a drug molecule binds on a protein to either block or activate a particular biological process.

Understanding these binding sites is central to structural bioinformatics. So in this project, I helped the research team find patterns among the various binding sites and hot spots. I proposed clustering the sites after representing them as numerical vectors, and based on comparable distance metrics, and finally creating a pipeline to automate the tasks from extraction to clustering and visualisations.


## What I used

- Python in Visual Studio for extraction and analysis
- Standard data analysis packages - pandas, numpy
- scipy.stats, statsmodels, sklearn for statistical analysis and time series tests
- Packages & other tools for visualisation - matplotlib, seaborn, plotly 

## Data source and extraction

The data for this project came from precomputed .pkl files - one for each frame containing information about target structure and predicted binding sites with properties. Each .pkl file contained a dict with keys "target" and "sites".:

-  Binding site data, for example: 
```python
{'file': '0.pkl',
 'site': {
    'residues': ['A_14_GLY', 'A_15_VAL', ...],
    'hotspots': [
        {'center': array([44.39, 32.44, 13.74]), 'scores': {'small_molecule': 0.99}},
        {'center': array([48.49, 33.62, 16.08]), 'scores': {'ion:-2': 0.59, 'ion:SO4': 0.46}}
    ]
  }
}
```
- Target data, for example:
```python
{'file': '0.pkl',
 'target': {
    'chain_ids': [...],
    'atom_names': [...],
    'res_ids': [...],
    'coords': [...],
    'elements': [...]
 }}
```

## Clustering methodology

In order to cluster the different binding sites, I first had to decide on the appropriate features. After discussing with the research team, I first had to represent each binding site by a “distance” metric. This could take one of three forms.

- 1. Residue overlap metric - a simple intersection of the sets of residues involved in two binding sites

```python
def residue_overlap_distance(site1, site2):
    residues1 = set(site1.get('residues', []))
    residues2 = set(site2.get('residues', []))
    return 1 - len(residues1 & residues2) / len(residues1 | residues2)
```

- 2. Residue score metric - directly using the hotspot “scores” as feature vectors representing each binding site and finding the distance (Euclidean, L1, or Jaccard distance)

```python
def residue_score_distance(site1, site2, distancetype):
    res_scores_1 = site1.get('residue_scores', {})
    res_scores_2 = site2.get('residue_scores', {})
    rnames = list(set.union(set(res_scores_1.keys()), set(res_scores_2.keys())))
    res_scores_1 = {**dict.fromkeys(rnames, 0), **res_scores_1}
    res_scores_2 = {**dict.fromkeys(rnames, 0), **res_scores_2}
    res_scores_1 = np.asarray(list(res_scores_1.values()), np.float32)
    res_scores_2 = np.asarray(list(res_scores_2.values()), np.float32)
    
    dist = np.sqrt(np.sum((res_scores_1 - res_scores_2) ** 2))
    
    return dist
```
- 3. Distance vector metric 

This third approach is the most complex. It builds a consistent numerical representation of each binding site by encoding how its atoms relate to a global set of atoms of interest. This involved a few steps: 


1. Identifying atoms in hotspots

A hotspot is essentially a functional region of a binding site. I needed to know exactly which atoms belonged to each hotspot. This involved mapping residue identifiers in a binding site to the corresponding atoms in the target structure. It ensures the right atoms are pulled out from the matching .pkl target file and records their details (chain ID, residue ID, atom name, element, and coordinates).
 
```python
def get_atoms_in_binding_site(binding_site):  
    file_name = binding_site['file'] 
    target= [entry for entry in all_target_data if entry['file'] in file_name] # extra check to make sure that the atoms are from the residues in the same pkl file 
    target_data = next((entry for entry in target if entry['file'] == file_name), None)
    binding_site_residues = binding_site['site']['residues']
		parsed_residues = []
		for residue in binding_site_residues:
		        chain_id, res_id, res_name = residue.split('_')
		        parsed_residues.append((chain_id, int(res_id), res_name))        
		     
		chain_ids = target_data['target']['chain_ids']
		res_ids = target_data['target']['res_ids']
		res_names = target_data['target']['res_names']
		atom_names = target_data['target']['atom_names']
		coords = target_data['target']['coords']
		elements = target_data['target']['elements']
		
		atoms_in_site = []
		for chain_id, res_id, res_name in parsed_residues:
		    mask = (chain_ids == chain_id) & (res_ids == res_id) & (res_names == res_name)
		    matching_indices = np.where(mask)[0]
		
		    for idx in matching_indices:
		        atoms_in_site.append({
		            'chain_id': chain_id,
		            'res_id': res_id,
		            'res_name': res_name,
		            'atom_name': atom_names[idx],
		            'coords': coords[idx].tolist(),
		            'element': elements[idx],
		        })
		
		return atoms_in_site
```

2. Defining a global set of atoms

Once atoms are extracted from all binding sites, I collected them into a global catalogue of unique atoms by:

- Looping through all sites, runs, getting their atoms, and flattening the results
- Assigning each atom a unique identifier string, like "A_14_GLY_CA"
- Storing these unique atoms in an OrderedSet, which ensures that the feature vectors will always follow the same order

```python
def atoms_of_interest(binding_sites): 
    atoms_of_interest = []
    for site in binding_sites:
        atoms = get_atoms_in_binding_site(site) 
        atoms_of_interest.append(atoms)
    all_atoms = [
        f"{atom['chain_id']}_{atom['res_id']}_{atom['res_name']}_{atom['atom_name']}" 
        for atom_list in atoms_of_interest 
        for atom in atom_list
        ]
    unique_atoms = OrderedSet(all_atoms) # Ordered set to maintain order in the calculation of the hotspot distances to atoms and binding vector for each site
    atoms_of_interest_flat = [atom for atoms in atoms_of_interest for atom in atoms]  # atoms_of_interest_flat has to be a dictionary
    print("Set of atoms of interest created (Distance Vector Method)") 
    return unique_atoms, atoms_of_interest_flat 
```
This global set of atoms was crucial: it defines the dimensions of the vector space in which all binding sites will be represented.

3. Looking up atom coordinates

I also wanted to retrieve the coordinates for any given atom ID. It searches through the global flat list of atom dictionaries (atoms_of_interest_flat) and returns the XYZ coordinates.

```python
def get_atom_coordinates(atom_data):  
   
    chain_id, res_id, res_name, atom_name = atom_data
    for entry in atoms_of_interest_flat:  # atoms_of_interest_flat has to be global and in a dictionary format
        if (
            entry['chain_id'] == chain_id and
            entry['res_id'] == int(res_id) and
            entry['res_name'] == res_name and
            entry['atom_name'] == atom_name
        ):
            return entry['coords']
    raise ValueError(f"Coordinates not found for atom: {atom_data}")
``` 
This step is what enables distance calculations later on.

3. Building the binding site vector

The heart of the method is binding_site_vector(binding_site, unique_atoms). For a given site, it constructs a fixed-length vector whose entries correspond to the global set of unique atoms.

Here’s the idea:

- Initialize the vector with a large dummy value (e.g. 10.0) for all atoms.
- For each hotspot in the binding site, compute the Euclidean distance between the hotspot center and the coordinates of each atom in the site.
- Update the vector entry for that atom with this distance.

Each hotspot generates its own vector. Then, for the final representation of the binding site, the code takes the minimum distance across all hotspots for each atom. This way, the binding site vector encodes the closest approach of its atoms to each hotspot.

```python
def binding_site_vector(binding_site, unique_atoms):
    #dummy vector for all unique atoms with a large default value (e.g., 10.0) to keep vector lengths same, i.e. equal to length of unique atoms
    dummy_vector = {atom: 10.0 for atom in unique_atoms}
    residues_in_site = set(binding_site['site']['residues']) 

    # get atoms from residues 
    atoms_in_site = set(
        f"{atom['chain_id']}_{atom['res_id']}_{atom['res_name']}_{atom['atom_name']}"
        for atom in atoms_of_interest_flat
        if f"{atom['chain_id']}_{atom['res_id']}_{atom['res_name']}" in residues_in_site
    )

    hotspot_vectors = []
    for hotspot in binding_site['site']['hotspots']:
        hotspot_coords = hotspot['center']   
        hotspot_vector = dummy_vector.copy()  
        
        for atom in unique_atoms.intersection(atoms_in_site):
            atom_data = atom.split('_')
            atom_coords = get_atom_coordinates(atom_data)  
            distance = np.linalg.norm(np.array(hotspot_coords) - np.array(atom_coords))
            hotspot_vector[atom] = distance # updates the distance for only that element of the hotspot vector where the id matches this particular atom

        # add the hotspot vector to the list of vectors
        hotspot_vectors.append(hotspot_vector)

    # Calculate the binding site vector as the minimum distance across all hotspots for each atom
    binding_site_vector = [
        min(hotspot_vector[atom] for hotspot_vector in hotspot_vectors) for atom in unique_atoms 
    ]

    return binding_site_vector

``` 
The result is a dense numerical vector of equal length for all binding sites, ready for pairwise comparison
4. Computing the distance between 2 distance vectors and distance matrix
Once I had site vectors, it was just a matter of computing the distance (using (L2/Euclidean or L1/Manhattan etc.) between 2 vectors and then the distance matrix. 

The distance matrix is a 2D array where entry (i, j) represents the distance between binding site i and binding site j. And although the binding sites themselves are the “items” being clustered, each row in the distance matrix corresponds to a binding site, and the algorithm assigns a cluster label to that row.

```python
def pairwise_distances_with_library(sites, distance_func, distance_type): 
    def distance_wrapper(i, j, distance_type):
        return distance_func(sites[int(i[0])], sites[int(j[0])], distance_type)
    
    indices = list(range(len(sites)))
    condensed_matrix = pdist([[i] for i in indices], metric=lambda i, j: distance_wrapper(i, j, distance_type))
    
    return squareform(condensed_matrix)
``` 
This is unlike traditional clustering applications in “business” where a product or item is assigned a cluster based on its features. In fact, you can think of the distance matrix as a collection of items with their features, except the items themselves are entire rows (binding sites) and the features are the columns. Or in other words, instead of using the raw attributes of a binding site, we use its pattern of distances to every other binding site as its effective “feature vector.”

### Clustering Algorithms

For each distance metric, I used four clustering algorithms as the research team wanted the flexibility of using anyone of them based on their needs. 

- Agglomerative Clustering - suitable for small to medium-sized dataset where hierarchy is meaningful
- DBSCAN - density-based, can identify noise points.
- OPTICS - Like DBSCAN but more flexible with varying densities (visualised via reachability plot).
- MeanShift - Finds dense regions by shifting points to cluster centers.

To visualise the clusters. I used t-SNE plots. This also helped with dimensionality reduction since the original data (or the pairwise distance matrix) might have many features or represent complex relationships. t-SNE converts the distance matrix into a two-dimensional representation where similar points are closer together and dissimilar points are farther apart.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/clustering-protein/mean-shift-t-sne.png" title="Meanshift t-SNE plot" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
  


## Deploying into production with an ETL pipeline

Once the logic and workflow was agreed on, the research team wanted a configurable pipeline where they could:

- Choose the metric type (residue_overlap, residue_score, or distance_vector) and for each of these, additional parameters like options normalisation: min, max, average, or union.
- Select the distance metric (Euclidean, Manhattan, cosine, Jaccard, etc.)
- Experiment with different clustering algorithms (hierarchical, KMeans, DBSCAN, HDBSCAN)
- Control other parameters like the subset of binding sites, dimensionality reduction method, or output visualisations

To support this, I built a single production-ready Python file (clustering_pipeline.py) that functions as an ETL pipeline:

- **Extraction -** with functions for loading binding site and atom data from the provided .pkl files. And also for handling mapping atoms and constructing the unique atom identifiers.
- **Transformation -** functions to calculate the
    - residue_overlap_distance, residue_score_distance and distance_between_vectors for the 3 methods, each taking in 2 sites as arguments. The 3rd function also depended on other helper functions like:
    - get_atoms_in_binding_site, atoms_of_interest (to list all unique atoms from all binding sites in all files), get_atom_coordinates (to get coordinates for atom specified)and binding_site_vector (to calculate the vector for a binding site based on the distances between hotspots and unique atoms)
    - pairwise_distances_with_library – builds pairwise distance matrices between binding sites for the chosen distance metric.
- **Loading & Analysis**
    - perform_clustering (that takes in the distance_matrix and other clustering_args ) – applies the chosen clustering algorithm (hierarchical, KMeans, DBSCAN, HDBSCAN) and
    - Supports dimensionality reduction and generation of t-SNE plots for visualization.
- **Configuration Layer**
    - Parameters are stored in a JSON file (e.g., metric type, distance metric, clustering algorithm, number of clusters, subset size).
    - The pipeline reads this JSON at runtime and executes the full workflow accordingly.
- **Main Function**
    - Orchestrates the end-to-end pipeline.
    - Makes it easy to run the entire workflow with a single command:

This structure allowed the research team to test out with different settings and parameters without editing the codebase — they only need to update the JSON configuration file.




## Some challenges and lessons from this project

- The **data extraction** was a bit tricky. The data type (pdb files) were new to me so I had to discuss with the research team in-depth and understand the ideas of “frame”, “ligand”, “amino acids”, ”interaction fingerprints” and protein structure, what metrics needed to be extracted and how to go about doing that.
- The packages used for extraction - **MDAnalysis** and **ProLIF** - were new to me so I had to spend some time understanding the documentation and testing out object outputs.
- The **extraction process** itself was not straightforward (calculate center of mass of ligand, then list of interacting amino acids and then get interaction fingerprints)
- Unlike data science projects I was used to, the research team preferred **using command line prompts to accept inputs** - generally a path to a folder with a json file containing everything - files to extract, parameters for various functions, metrics to explore and arguments for plotting and smoothening the distributions. This took some getting used to but was manageable.
- **Learning to think of the frames as snapshots** and looking at them as a “time series”, and also thinking of the time series and distributions as almost interchangeable. Again, not how I have traditionally done time series projects but in this particular use case I can see how it makes sense and got used to it.
- Perhaps the most important lesson was **taking the time to understand the domain**, data structure and features, and discussing with the subject matter experts in-depth at the start.