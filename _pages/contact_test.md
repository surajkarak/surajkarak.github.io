---
layout: page
permalink: /contact/
title: Contact
description: Here's how you can work with me.
nav: false
nav_order: 5
---
<!-- <iframe src="https://docs.google.com/forms/d/e/1FAIpQLSdw_pvTXWeLi-0jVwf1i8bz2wZdZmFr3T6EYyKD4OcKkH61tg/viewform?embedded=true" width="640" height="810" frameborder="0" marginheight="0" marginwidth="0">Loading…</iframe>   -->

<div class="form-container">
  <h3>Just drop me a quick note</h3>
  <form id="contact-form" onsubmit="submitForm(event)">
    <input type="text" id="name" name="name" placeholder="Your Name" required>
    <input type="email" id="email" name="email" placeholder="Your Email for me to get back to" required>
    <textarea id="message" name="message" rows="5" placeholder="Any info you want to share..." required></textarea>
    <input type="submit" value="Send">
  </form>
  <p id="form-status"></p>
</div>

<script>
  function submitForm(event) {
    event.preventDefault(); // Prevent the default form submission
    
    const form = document.getElementById('contact-form');
    const formData = {
      name: form.name.value,
      email: form.email.value,
      message: form.message.value
    };

    fetch('https://script.google.com/macros/s/AKfycbz9wpDs2xXyGaUnFk2SaAxbPVs_pkKkGXNcE22h2QOHRTu2pF_XNMjS_u6gLJU2wC9Tdg/exec', {
      method: 'POST',
      body: JSON.stringify(formData),
      headers: {
        'Content-Type': 'application/json'
      }
    })
    .then(response => response.json())
    .then(data => {
      if (data.result === 'success') {
        alert('Your message has been sent!');
        form.reset(); // Clear the form
      } else {
        alert('There was a problem with your submission.');
      }
    })
    .catch(error => {
      alert('An error occurred. Please try again.');
      console.error('Error:', error);
    });
  }
</script>

<!-- <script>
  const form = document.getElementById('contact-form');
  const formStatus = document.getElementById('form-status');

  form.addEventListener('submit', function(e) {
    e.preventDefault();

    const data = {
      name: form.name.value,
      email: form.email.value,
      message: form.message.value
    };

    fetch('https://script.google.com/macros/s/AKfycbx3B4FT-E127k8PWV--2GA1VoRfIqm6CaQvY5uCnKmrDxZx6izuOiOd5JAzAoaXiEnbfg/exec', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(data),
    })
    .then(response => response.json())
    .then(response => {
      formStatus.textContent = 'Message sent successfully!';
      form.reset();
    })
    .catch(error => {
      formStatus.textContent = 'An error occurred. Please try again.';
    });
  });
</script> -->

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