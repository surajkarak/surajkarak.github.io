---
layout: page
permalink: /contact/
title: Contact
description: Here's how you can work with me.
nav: true
nav_order: 6
---

<div class="form-container">
    <h3>Just drop me a quick note</h3>
    <form method="post" action="https://script.google.com/macros/s/AKfycbyV_QoGxxHrttPuNMpPHu0mbWBi-P9cS1c1pUxlGXXrnD4R-KJH14r15ePfAJMkJ8mO/exec" name="contact-form" novalidate>
      <label for="name">Name</label>
      <input type="text" id="name" name="name" placeholder="Your name" autocomplete="name" required minlength="2" maxlength="100">

      <label for="email">Email</label>
      <input type="email" id="email" name="email" placeholder="Where I should reply" autocomplete="email" required>

      <label for="message">Message</label>
      <textarea id="message" name="message" rows="6" placeholder="What's on your mind?" required minlength="10" maxlength="3000"></textarea>

      <input type="text" name="website" id="website" tabindex="-1" autocomplete="off" class="honeypot" aria-hidden="true">

      <input type="submit" value="Send" id="submit">
    </form>
    <p id="form-status" role="status" aria-live="polite"></p>
</div>

<script>
    const scriptURL = 'https://script.google.com/macros/s/AKfycbyV_QoGxxHrttPuNMpPHu0mbWBi-P9cS1c1pUxlGXXrnD4R-KJH14r15ePfAJMkJ8mO/exec';

    const form = document.forms['contact-form'];
    const formStatus = document.getElementById('form-status');
    const submitBtn = document.getElementById('submit');

    form.addEventListener('submit', async (e) => {
      e.preventDefault();

      if (form.website.value) {
        return;
      }

      const originalLabel = submitBtn.value;
      submitBtn.disabled = true;
      submitBtn.value = 'Sending…';
      formStatus.textContent = '';
      formStatus.className = '';

      try {
        await fetch(scriptURL, { method: 'POST', body: new FormData(form), mode: 'no-cors' });
        formStatus.textContent = 'Message sent — I\'ll reply soon.';
        formStatus.className = 'status-success';
        form.reset();
      } catch (err) {
        formStatus.textContent = 'Couldn\'t send (' + err.message + '). Please try again or reach out on LinkedIn.';
        formStatus.className = 'status-error';
      } finally {
        submitBtn.disabled = false;
        submitBtn.value = originalLabel;
      }
    });
</script>

<style>
  .form-container {
    background-color: white;
    padding: 24px;
    border-radius: 8px;
    box-shadow: 0 0 15px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
  }

  .form-container label {
    display: block;
    margin-top: 12px;
    margin-bottom: 4px;
    font-weight: 500;
    color: #333;
  }

  .form-container input,
  .form-container textarea {
    width: 100%;
    padding: 12px;
    margin: 0 0 4px 0;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-sizing: border-box;
    font-family: inherit;
    font-size: 1rem;
  }

  .form-container input:focus,
  .form-container textarea:focus {
    outline: none;
    border-color: var(--global-theme-color);
    box-shadow: 0 0 0 2px rgba(0, 0, 0, 0.05);
  }

  .form-container input[type="submit"] {
    margin-top: 16px;
    background-color: var(--global-theme-color);
    color: white;
    border: none;
    cursor: pointer;
    font-size: 16px;
    transition: filter 0.15s ease;
  }

  .form-container input[type="submit"]:hover:not(:disabled) {
    filter: brightness(0.9);
  }

  .form-container input[type="submit"]:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .form-container .honeypot {
    position: absolute;
    left: -9999px;
    width: 1px;
    height: 1px;
    opacity: 0;
    pointer-events: none;
  }

  .form-container h3 {
    text-align: center;
    margin-bottom: 20px;
  }

  #form-status {
    text-align: center;
    margin-top: 16px;
    min-height: 1.5em;
    font-weight: 500;
  }

  #form-status.status-success {
    color: #28a745;
  }

  #form-status.status-error {
    color: #dc3545;
  }
</style>
