---
layout: page
permalink: /contact/
title: Contact
description: Here's how you can work with me.
nav: true
nav_order: 5
---

<div class="form-container">
    <h3>Just drop me a quick note</h3>
    <form method="post" action="https://script.google.com/macros/s/AKfycbyV_QoGxxHrttPuNMpPHu0mbWBi-P9cS1c1pUxlGXXrnD4R-KJH14r15ePfAJMkJ8mO/exec" name="contact-form">
      <input type="text" id="name" name="name" placeholder="Your Name" required>
      <input type="email" id="email" name="email" placeholder="Your Email for me to get back to" required>
      <textarea id="message" name="message" rows="5" placeholder="Any info you want to share..." required></textarea>
      <input type="submit" value="Send" id="submit">
    </form>
    <p id="form-status"></p>
</div>

<script>
    const scriptURL = 'https://script.google.com/macros/s/AKfycbyV_QoGxxHrttPuNMpPHu0mbWBi-P9cS1c1pUxlGXXrnD4R-KJH14r15ePfAJMkJ8mO/exec'

    const form = document.forms['contact-form']
    const formStatus = document.getElementById('form-status');

    form.addEventListener('submit', async(e) => {
      e.preventDefault();
      fetch(scriptURL, { method: 'POST', body: new FormData(form)})
      .then(response => {
        formStatus.textContent = 'Message sent successfully!';
        form.reset();
      })
      .catch(error => {
        formStatus.textContent = 'An error occurred. Please try again.';
      })
    })

  </script>

<style>
  .form-container {
    background-color: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
  }

  input, textarea {
    width: 100%;
    padding: 12px;
    margin: 10px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-sizing: border-box;
  }

  input[type="submit"] {
    background-color: var(--global-theme-color);
    color: white;
    border: none;
    cursor: pointer;
    font-size: 16px;
  }

  input[type="submit"]:hover {
    background-color: #218838;
  }

  h3 {
    text-align: center;
    margin-bottom: 20px;
  }

  #form-status {
    text-align: center;
    color: #28a745;
  }
</style>