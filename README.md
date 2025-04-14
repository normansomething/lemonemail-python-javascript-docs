# Lemon Documentation

Welcome to the official documentation for **Lemon Email**, your go-to platform for streamlined email communication and contact management. This guide covers use cases, API integration, and advanced functionalities like AI-driven messaging.

---

## Table of Contents
1. [ Simple](#simple)
2. [Intermediate](#intermediate)
   - [API Integration](#api-integration)
       - [Key Features](#key-features)
       - [Authorization](#authorization)
       - [Example Request](#example-request)
3. [Advanced](#advanced)
    - [AI Integration](#ai-integration)
       - [OpenAI](#openai)
       - [Webhooks](#webhooks)

---

## Use Cases

### Simple

<details>
<summary>Messages</summary>

#### Types of Messages
- **Broadcasts**: Send a single message to a large audience efficiently. Perfect for campaigns and announcements.
- **Funnels**: Guide your audience through automated message flows.
- **Transactional**: Deliver real-time messages triggered by user actions, like order confirmations or password resets.
- **Throttles**: Manage message delivery rates to avoid overwhelming recipients.
</details>

<details>
<summary>Contacts</summary>

#### Contact Management
- **Contact Lists**: Organize your audience with customizable lists.
- **Segments**: Group your audience by behavior, demographics, or preferences.
- **Suppression**: Exclude specific contacts or domains to ensure compliance.
- **Forms**: Collect data and grow your audience with customizable forms.
</details>

<details>
<summary>Integrate</summary>

#### Tools for Integration
- **API & SMTP**: Send messages programmatically or via email protocols.
- **Webhooks**: Automate workflows with real-time event notifications.
- **Zapier & Pabbly**: Connect Lemon to various platforms and automate tasks effortlessly.
</details>

---

### Intermediate

##### API Integration
Lemon's RESTful API provides developers full control over messaging and contact management.

##### Key Features
- Create and manage contact lists, suppression lists, and broadcasts.
- Send transactional emails and tag/untag contacts dynamically.

##### Authorization
Include your API key in the `X-Auth-APIKey` header. Retrieve your API key from the **Integrate > API & SMTP** section in the Lemon UI.

###### Example Request
```http
POST /transactional/send HTTP/1.1
Host: app.xn--lemn-sqa.com
Content-Type: application/json
X-Auth-APIKey: your_lemon_api_key

{
  "fromname": "Your SaaS",
  "fromemail": "noreply@yoursaas.com",
  "to": "recipient@example.com",
  "subject": "Welcome!",
  "body": "Thank you for joining us."
}
```

For more details about available routes, refer to the API documentation at [Lemon API Docs](https://app.xn--lemn-sqa.com/api/doc#/).

---

### Advanced

##### AI Integration
Combine Lemon with AI technologies for enhanced personalization and efficiency. Below are code examples using OpenAI, Anthropic Claude, Google AI, and IBM Watson.

######  Examples

<details>
<summary>OpenAI</summary>

##### Python Code
```python
import openai
import requests

def generate_and_send_email(openai_api_key, lemon_api_key, user_data):
    openai.api_key = openai_api_key

    # Generate email content with OpenAI
    response = openai.Completion.create(
        engine="text-davinci-002",
        prompt=f"Write an email to {user_data['name']} about their recent activity: {user_data['activity']}",
        max_tokens=200
    )
    email_content = response.choices[0].text.strip()

    # Send email using Lemon API
    response = requests.post(
        'https://app.xn--lemn-sqa.com/api/transactional/send',
        headers={
            'Content-Type': 'application/json',
            'X-Auth-APIKey': lemon_api_key
        },
        json={
            "fromname": "Your SaaS",
            "fromemail": "noreply@yoursaas.com",
            "to": user_data['email'],
            "subject": "Your Activity Update",
            "body": f"<html><body>{email_content}</body></html>"
        }
    )
    return response.json()

# Usage
result = generate_and_send_email(
    'your_openai_api_key',
    'your_lemon_api_key',
    {
        "name": "John",
        "email": "john@example.com",
        "activity": "Completed 5 workouts this week"
    }
)
print(result)
```
</details>


For more AI integrations, refer to the relevant  [AI Integration with Lemon](https://lemon.email/introduction/integration-with-ai-services-advanced/).



##### Webhooks

Lemon's webhook system allows you to receive real-time event notifications to trigger actions in your application. For example, you can process user activity events like a completed workout and send a personalized email using the transactional email API.

<details>
  <summary>Python Webhook Example</summary>
  
  ```python
  from flask import Flask, request, jsonify
  import requests

  app = Flask(__name__)

  @app.route('/webhook/user-activity', methods=['POST'])
  def user_activity_webhook():
      data = request.json

      if data['activity_type'] == 'workout_completed':
          response = requests.post(
              'https://app.xn--lemn-sqa.com/api/transactional/send',
              headers={
                  'Content-Type': 'application/json',
                  'X-Auth-APIKey': 'your_lemon_api_key'
              },
              json={
                  "fromname": "FitnessSaaS",
                  "fromemail": "noreply@fitnesssaas.com",
                  "to": data['user_email'],
                  "subject": "Great job on your workout!",
                  "body": f"<html><body>You completed a {data['workout_type']} workout. Keep it up!</body></html>"
              }
          )

          if response.status_code == 200:
              return jsonify({"status": "success", "message": "Email sent"}), 200
          else:
              return jsonify({"status": "error", "message": "Failed to send email"}), 500

      return jsonify({"status": "success", "message": "Webhook received"}), 200

  if __name__ == '__main__':
      app.run(port=5000)
  ```
</details>

<details>
  <summary>Node.js Webhook Example</summary>

  ```javascript
  const express = require('express');
  const axios = require('axios');
  const app = express();

  app.use(express.json());

  app.post('/webhook/user-activity', async (req, res) => {
      const data = req.body;

      if (data.activity_type === 'workout_completed') {
          try {
              const response = await axios.post('https://app.xn--lemn-sqa.com/api/transactional/send', {
                  fromname: "FitnessSaaS",
                  fromemail: "noreply@fitnesssaas.com",
                  to: data.user_email,
                  subject: "Great job on your workout!",
                  body: `<html><body>You completed a ${data.workout_type} workout. Keep it up!</body></html>`
              }, {
                  headers: {
                      'Content-Type': 'application/json',
                      'X-Auth-APIKey': 'your_lemon_api_key'
                  }
              });
              
              if (response.status === 200) {
                  res.json({ status: "success", message: "Email sent" });
              } else {
                  res.status(500).json({ status: "error", message: "Failed to send email" });
              }
          } catch (error) {
              console.error('Error sending email:', error);
              res.status(500).json({ status: "error", message: "Failed to send email" });
          }
      } else {
          res.json({ status: "success", message: "Webhook received" });
      }
  });

  app.listen(5000, () => console.log('Server running on port 5000'));
  ```
</details>

For more advanced examples and detailed information, refer to the [Lemon Webhook Examples](https://lemon.email/introduction/webhook-examples-advanced).
