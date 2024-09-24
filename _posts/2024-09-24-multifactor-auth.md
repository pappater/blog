---
layout: post
title: "How multi-factor auth (MFA) works"
date: 2024-09-24 06:41:20 +0530
categories: jekyll update
---

The process you went through for logging in using multi-factor authentication (MFA) is based on a concept called **Time-based One-Time Password (TOTP)**. Here’s a detailed explanation of how it works and how you could create such an app:

### How It Works

1. **Account Setup**:

   - When you first set up MFA, Heroku generates a **secret key** (which is embedded in the QR code you scanned).
   - The authenticator app (e.g., Google Authenticator) reads this secret key by scanning the QR code and saves it.

2. **TOTP Algorithm**:
   - The TOTP algorithm combines the secret key with the current time to generate a unique, temporary password.
   - The code you see in your authenticator app changes every 30 seconds, and it's calculated using the **current time** (Unix timestamp) and the stored secret key.
3. **Login Process**:
   - When you log in, Heroku asks for the 6-digit code from the authenticator app.
   - You open the app, which generates a code using the TOTP algorithm, and you enter it in the login form.
   - Heroku then uses the same secret key (which was stored when you set up MFA) and the current time to generate its own version of the 6-digit code.
   - If the code you enter matches what Heroku generates, you are granted access.

### How to Create Such an App

To create your own MFA authenticator app, you'd follow these steps:

#### 1. **Generate Secret Keys (QR Code)**

- When the user signs up, generate a **secret key** for them, which will be used to generate one-time passwords.
- Convert the secret key into a QR code so the user can scan it with their authenticator app.

#### 2. **Implement the TOTP Algorithm**

TOTP is based on the **HMAC (Hashed Message Authentication Code)** algorithm. Here’s how it works:

- Use the secret key and the current time in seconds (divided by 30 to get time intervals).
- Apply HMAC with the secret key and the time interval as inputs.
- Truncate the result to generate a 6-digit code.

There are libraries in many languages that handle the TOTP algorithm for you:

- **JavaScript**: You can use `otplib` for generating and verifying TOTP codes.
- **Python**: Libraries like `pyotp`.
- **Node.js**: The `speakeasy` library is a good choice.

#### 3. **Verifying the Code**

- When the user logs in and submits the 6-digit code, you calculate the code yourself on the server using the same TOTP algorithm and the secret key.
- Compare the code you generated with the one the user submitted.
- If they match, the user is authenticated.

#### Steps to Create the App:

1. **Generate Secret Key**:

   - On user registration, generate a base32-encoded secret key and display it as a QR code.
   - Use a library like `qrcode` in JavaScript to create the QR code.

2. **User Scans the QR Code**:

   - The user scans the QR code with an authenticator app like Google Authenticator, which saves the secret key.

3. **Generate TOTP Codes**:

   - Your authenticator app generates a new TOTP code every 30 seconds using the secret key and the current time.

4. **Verification**:
   - When logging in, the user inputs the

6-digit code from their authenticator app.

- Your server calculates the expected TOTP code using the secret key and the current time, then compares it with the code entered by the user.
- If the codes match, the user is authenticated.

### Basic Example of How to Implement TOTP in Node.js

To give you a basic idea of how you could implement TOTP in an application, here's a high-level outline of the process using **Node.js** and the `speakeasy` library:

1. **Install the required packages**:

   ```bash
   npm install speakeasy qrcode
   ```

2. **Generate a Secret and QR Code**:
   ```javascript
   const speakeasy = require("speakeasy");
   const qrcode = require("qrcode");
   ```

// Generate a secret key for the user
const secret = speakeasy.generateSecret({ length: 20 });

// Create a QR code for the user to scan with their authenticator app
qrcode.toDataURL(secret.otpauth_url, (err, data) => {
console.log(data); // Display this in your frontend
});

````

3. **Verify the TOTP Code**:
When the user logs in and provides their 6-digit code, verify it like this:
```javascript
const userToken = '123456';  // The 6-digit code provided by the user
const verified = speakeasy.totp.verify({
  secret: secret.base32,  // The secret stored for the user
  encoding: 'base32',
  token: userToken
});

if (verified) {
  console.log('Authenticated!');
} else {
  console.log('Invalid code.');
}
````

### Conclusion

By understanding how TOTP works and using libraries to manage the complexity of encryption and time-based code generation, you can create a secure multi-factor authentication system for your applications. This can be implemented in various programming languages, but libraries like `speakeasy` make it relatively simple in environments like Node.js.
