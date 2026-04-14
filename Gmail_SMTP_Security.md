# Practical No. 11
## Aim: Security Assessment of Gmail Using SMTP

---

## What is SMTP?
SMTP (Simple Mail Transfer Protocol) is the protocol used to send emails. Gmail's SMTP server is `smtp.gmail.com` on port 587 (with TLS/STARTTLS) or port 465 (with SSL). This practical demonstrates how to send email programmatically using Python and Gmail's SMTP server — and assess the security implications.

---

## Step 1: Enable 2-Step Verification on Gmail

1. Open your browser and go to: https://myaccount.google.com
2. Click **Security** in the left sidebar (or navigate to "Security and sign-in").
3. Under **"How you sign in to Google"**, click **2-Step Verification**.
4. Follow the steps to enable 2-Step Verification (2FA) if it is not already enabled.
5. You will see confirmation: **"On since [date]"**

### Why 2FA is required:
- App Passwords (used in the next step) are only available when 2-Step Verification is enabled.
- This adds a layer of security — even if your password is stolen, the attacker needs the second factor.

---

## Step 2: Create an App Password

1. Go to: https://myaccount.google.com/security
2. Search for **"App passwords"** in the search bar at the top, or find it under 2-Step Verification settings.
3. You will see: *"You don't have any app passwords."*
4. In the **App name** field, type any name (e.g., `SAA`)
5. Click **Create**.
6. Google will display a **16-character app password** (e.g., `acoe pfsm ibpu ibuk`).
7. **Copy this password immediately** — you won't be able to see it again.
8. Click **Done**.

**Important**: Remove spaces from the app password when using it in code. `acoe pfsm ibpu ibuk` becomes `acoepfsmibpuibuk` in the code.

---

## Step 3: Write the Python SMTP Code

Create a new Python file `smtp_test.py`:

```python
import smtplib
from email.mime.text import MIMEText

# Your Gmail credentials
EMAIL = "laveshburla12@gmail.com"
APP_PASSWORD = "qcljfchohcprffbj"    # 16-character App Password (no spaces)

def send_email():
    # Create the email message
    msg = MIMEText("This is a security test email.")
    msg['Subject'] = "Security Test"
    msg['From'] = EMAIL
    msg['To'] = "sairajburla@gmail.com"    # Recipient email address
    
    try:
        # Connect to Gmail SMTP server on port 587
        server = smtplib.SMTP("smtp.gmail.com", 587)
        
        # Start TLS encryption (upgrades connection to secure)
        server.starttls()
        
        # Login with email and App Password
        server.login(EMAIL, APP_PASSWORD)
        
        # Send the email
        server.send_message(msg)
        
        # Close the connection
        server.quit()
        
        print("Email sent successfully")
    
    except Exception as e:
        print("Error:", e)

if __name__ == "__main__":
    send_email()
```

---

## Step 4: Run the Code

```bash
python smtp_test.py
```

### Expected Output:
```
Email sent successfully
```

The recipient (`sairajburla@gmail.com`) will receive an email with:
- **Subject**: Security Test
- **Body**: This is a security test email.
- **From**: laveshburla12@gmail.com

---

## Step 5: Security Assessment Observations

### What was tested:
1. **Authentication via App Password** — Instead of using the main Google password, an App Password is used. This limits the scope of access.
2. **TLS Encryption (STARTTLS)** — The connection is upgraded from plaintext to encrypted using `server.starttls()`. This prevents eavesdropping.
3. **SMTP Protocol** — Port 587 with STARTTLS is the recommended secure method.

### Security Risks Identified:
| Risk | Description |
|---|---|
| **App Password in plaintext code** | The App Password is hardcoded in the Python script — anyone with access to the script file can use it to send emails from your account |
| **No MFA for App Password** | App Passwords bypass 2FA — they grant full Gmail access without the second factor |
| **Email spoofing risk** | SMTP allows setting any "From" address — phishing emails often use this |
| **No email content encryption** | The email body is sent in plaintext — SMTP only encrypts the transport, not the content |
| **Credential exposure in logs** | If the script outputs logs, App Password could be exposed |

---

## Enhanced Version with HTML Email and Better Security

```python
import smtplib
import os
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

# Best practice: Store credentials in environment variables, not in code
# Set these in your system:
# export EMAIL_ADDRESS="your@gmail.com"
# export EMAIL_APP_PASSWORD="yourapppassword"
EMAIL = os.environ.get("EMAIL_ADDRESS", "laveshburla12@gmail.com")
APP_PASSWORD = os.environ.get("EMAIL_APP_PASSWORD", "qcljfchohcprffbj")

def send_html_email(to_address, subject, body_text, body_html=None):
    # Create multipart message (supports both text and HTML)
    msg = MIMEMultipart("alternative")
    msg['Subject'] = subject
    msg['From'] = EMAIL
    msg['To'] = to_address
    
    # Plain text version
    part1 = MIMEText(body_text, "plain")
    msg.attach(part1)
    
    # HTML version (optional)
    if body_html:
        part2 = MIMEText(body_html, "html")
        msg.attach(part2)
    
    try:
        # Use SMTP_SSL for port 465, or SMTP + starttls() for port 587
        with smtplib.SMTP("smtp.gmail.com", 587) as server:
            server.ehlo()           # Say hello to server
            server.starttls()       # Upgrade to encrypted connection
            server.ehlo()           # Say hello again after TLS
            server.login(EMAIL, APP_PASSWORD)
            server.sendmail(EMAIL, to_address, msg.as_string())
        
        print(f"Email sent successfully to {to_address}")
        return True
    
    except smtplib.SMTPAuthenticationError:
        print("Authentication failed. Check your email and App Password.")
        return False
    except smtplib.SMTPException as e:
        print(f"SMTP error: {e}")
        return False
    except Exception as e:
        print(f"Unexpected error: {e}")
        return False

if __name__ == "__main__":
    send_html_email(
        to_address="sairajburla@gmail.com",
        subject="Security Assessment Test",
        body_text="This is a security test email sent via Python SMTP.",
        body_html="<h2>Security Test</h2><p>This email was sent via <b>Python SMTP</b>.</p>"
    )
```

---

## SMTP Security Protocols Comparison

| Protocol | Port | Encryption | Use |
|---|---|---|---|
| SMTP (plain) | 25 | None | Server-to-server (can be intercepted) |
| SMTP + STARTTLS | 587 | TLS (after STARTTLS upgrade) | Recommended for clients |
| SMTPS (SSL) | 465 | SSL/TLS from start | Also secure for clients |

---

## Gmail SMTP Settings Reference

| Setting | Value |
|---|---|
| SMTP Server | smtp.gmail.com |
| Port (TLS/STARTTLS) | 587 |
| Port (SSL) | 465 |
| Authentication | Required (email + App Password) |
| Encryption | STARTTLS or SSL |

---

## How to Fix Security Issues Found

1. **Store credentials securely**: Use environment variables or a secrets manager (e.g., Python `keyring`, AWS Secrets Manager) instead of hardcoding in the script.
2. **Rotate App Passwords regularly**: Delete old app passwords and create new ones.
3. **Use OAuth 2.0**: Google recommends OAuth 2.0 for programmatic Gmail access instead of App Passwords.
4. **Email Content Encryption**: Use S/MIME or PGP to encrypt email body for sensitive communications.
5. **Principle of Least Privilege**: Create App Passwords with minimal required access.

---

## Key Points for Exam

- Gmail's SMTP server: `smtp.gmail.com`, Port 587 (STARTTLS), Port 465 (SSL).
- **App Password** is a 16-character password generated by Google for third-party apps.
- App Passwords require **2-Step Verification** to be enabled first.
- `smtplib.SMTP()` creates an SMTP connection.
- `server.starttls()` upgrades the connection to TLS (Transport Layer Security).
- `server.login(email, app_password)` authenticates with Gmail.
- `server.send_message(msg)` sends the email.
- `server.quit()` closes the connection cleanly.
- The email was sent **without entering credentials interactively** — they are embedded in the code (which is a security risk).
- **STARTTLS** is the security feature that encrypts the email in transit — but the email body itself is not end-to-end encrypted.

---

## Commands Summary

```bash
# Install required library (already in Python standard library, no pip needed)
python smtp_test.py          # Run the SMTP test script

# Steps to get App Password:
# 1. myaccount.google.com → Security → 2-Step Verification → Enable
# 2. myaccount.google.com → Security → Search "App passwords"
# 3. Create app password → Copy the 16-character password
# 4. Use it in the code as APP_PASSWORD (remove spaces)
```
