---
layout: page
permalink: /contact-test/
title: Contact-Test
description: Here's how you can work with me.
nav: false
nav_order: 5
---
<!-- <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSdw_pvTXWeLi-0jVwf1i8bz2wZdZmFr3T6EYyKD4OcKkH61tg/viewform?embedded=true" width="640" height="810" frameborder="0" marginheight="0" marginwidth="0">Loading…</iframe>   -->

<div class="form-container">
  <h3>Just drop me a quick note</h3>
  <form id="contact-form" action="https://script.google.com/macros/s/AKfycbxGLOouZrzm2TtDftH401OSGhQ6xMnugtju-OcmBKLKARSbIyzfeNGxZFJdVC9tMIyS/exec" method="POST">
    <input type="text" id="name" name="name" placeholder="Your Name" required>
    <input type="email" id="email" name="email" placeholder="Your Email for me to get back to" required>
    <textarea id="message" name="message" rows="5" placeholder="Any info you want to share..." required></textarea>
    <button type="submit" value="Send" </button>
  </form>
  <p id="form-status"></p>
</div>

<script>
  document.getElementById('contact-form').addEventListener('submit', async (event) => {
    event.preventDefault();
    
    const data = {
      name: event.target.name.value,
      email: event.target.email.value,
      message: event.target.message.value,
    };
    
    try {
      const response = await fetch('https://script.google.com/macros/s/AKfycbxGLOouZrzm2TtDftH401OSGhQ6xMnugtju-OcmBKLKARSbIyzfeNGxZFJdVC9tMIyS/exec', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(data),
      });
      
      const result = await response.json();
      if (result.result === 'success') {
        alert('Message sent successfully!');
      } else {
        alert('An error occurred: ' + result.message);
      }
    } catch (error) {
      alert('An error occurred. Please try again.');
      console.error(error);
    }
  });
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

  button[type="submit"] {
    background-color: var(--global-theme-color);
    color: white;
    border: none;
    cursor: pointer;
    font-size: 16px;
  }

  button[type="submit"]:hover {
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