# Static Form Email Setup Guide (Vercel Compatible)

This guide outlines how to quickly replace a legacy PHP email script with a modern, serverless API configuration using **Web3Forms**. This setup is 100% static and optimized for deployment on platforms like Vercel.

---

## 🛠️ Step-by-Step Action Plan

### Step 1: Get an Access Key
1. Go to [web3forms.com](https://web3forms.com).
2. Enter your email address in the registration box.
3. Check your inbox for a **Free Access Key** (keep this key handy).

### Step 2: Clean Up Legacy Files
1. Find your old `.php` file (e.g., `contact.php`, `send-email.php`).
2. **Delete the PHP file** entirely from your project repository.

### Step 3: Update the Frontend Code

#### 1. HTML (`index.html`)
Replace your existing form structure with the template below. Make sure to paste your access key into the designated hidden input field.

```html
<form id="contact-form">
    <!-- Replace with your actual Web3Forms Access Key -->
    <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE">

    <!-- Anti-spam Honeypot (Hidden from users) -->
    <input type="checkbox" name="botcheck" style="display: none;">

    <div>
        <label for="name">Name</label>
        <input type="text" id="name" name="name" required placeholder="John Doe">
    </div>

    <div>
        <label for="email">Email</label>
        <input type="email" id="email" name="email" required placeholder="john@example.com">
    </div>

    <div>
        <label for="message">Message</label>
        <textarea id="message" name="message" required placeholder="Your message..."></textarea>
    </div>

    <button type="submit" id="submit-btn">Send Message</button>
    <div id="form-result"></div>
</form>
```

#### 2. JavaScript (`script.js`)
Add the following event listener to your main JavaScript file to capture form submissions asynchronously:

```javascript
const form = document.getElementById('contact-form');
const result = document.getElementById('form-result');
const submitBtn = document.getElementById('submit-btn');

form.addEventListener('submit', function(e) {
    e.preventDefault();
    
    // UI Feedback: Disable button during transmission
    submitBtn.innerText = "Sending...";
    submitBtn.disabled = true;
    result.innerHTML = "Processing your message...";
    result.style.color = "orange";

    const formData = new FormData(form);
    const object = Object.fromEntries(formData);
    const json = JSON.stringify(object);

    // Route data via HTTPS API instead of a local file
    fetch('https://web3forms.com', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json'
            },
            body: json
        })
        .then(async (response) => {
            let res = await response.json();
            if (response.status == 200) {
                result.innerHTML = "Success! Your message has been sent.";
                result.style.color = "green";
                form.reset(); 
            } else {
                result.innerHTML = res.message || "Submission failed.";
                result.style.color = "red";
            }
        })
        .catch(error => {
            console.error(error);
            result.innerHTML = "Network error. Please try again later.";
            result.style.color = "red";
        })
        .then(function() {
            // UI Reset: Re-enable buttons after processing
            submitBtn.innerText = "Send Message";
            submitBtn.disabled = false;
            setTimeout(() => { result.innerHTML = ""; }, 5000); 
        });
});
```

### Step 4: Deploy to Vercel
1. Commit your updated codebase to a Git repository (GitHub/GitLab/Bitbucket).
2. Log into the [Vercel Dashboard](https://vercel.com).
3. Click **Add New** > **Project** and import your repository.
4. Keep the default configuration and click **Deploy**.

Your production environment is completely ready, fully automated, and maintenance-free.
