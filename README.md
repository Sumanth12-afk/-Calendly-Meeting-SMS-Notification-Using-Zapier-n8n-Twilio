# 📅 Calendly Meeting SMS Notification Using Zapier + n8n + Twilio

This project allows you to send an automated SMS to a user whenever a meeting is scheduled through **Calendly**, using **Zapier** to catch the trigger and **n8n** with **Twilio** to send the SMS.

> ✅ Works without coding knowledge — perfect for automation beginners

---

## 🚀 What This Does

1. User schedules a meeting in **Calendly**
2. Zapier detects the new invitee and posts data to an **n8n webhook**
3. n8n processes the data and sends an SMS using **Twilio**

---

## 🛠 Tools Used

| Tool     | Purpose                      |
| -------- | ---------------------------- |
| Calendly | Collects meeting bookings    |
| Zapier   | Connects Calendly to n8n     |
| n8n      | Webhook + logic + Twilio SMS |
| Twilio   | Sends the actual SMS         |

---

## 📸 Screenshots

### Zapier Flow:

![Zapier Flow](./screenshots/zapier-flow.png)

### n8n Workflow:

![n8n Flow](./screenshots/n8n-workflow.png)

---

## 📦 Folder Structure

```
calendly-sms-integration/
├── screenshots/
│   ├── zapier-flow.png
│   └── n8n-workflow.png
├── README.md
```

---

## 📋 Step-by-Step Setup

### 🔹 Step 1: Set Up Calendly

1. Go to [Calendly](https://calendly.com/)
2. Create a new Event Type (e.g., "Meeting")
3. Under **Location**, select `Phone Call`
4. Enable: `✅ Require invitee's phone number`

---

### 🔹 Step 2: Create Zap in Zapier

1. Go to [Zapier](https://zapier.com/)
2. Click **Create Zap**

#### ⚡ Trigger

* **App**: Calendly
* **Event**: Invitee Created
* **Webhook Scope**: `user`
* **Event Type**: Leave blank or select your specific event

> Note: You must have a **paid Calendly plan** to use real-time webhook triggers

#### 🧩 Action (Webhooks by Zapier)

* **App**: Webhooks by Zapier
* **Action**: POST
* **URL**: Your **n8n production webhook URL** (from Step 3)
* **Payload Type**: JSON

##### Send these fields:

| Key            | Value                                  |
| -------------- | -------------------------------------- |
| `name`         | `Invitee Name`                         |
| `start_time`   | `Scheduled Event Start Time`           |
| `event_type`   | `Event Type Name`                      |
| `phone_number` | `Scheduled Event Location` ✅ *(phone)* |

---

### 🔹 Step 3: Set Up n8n Workflow

1. Go to your **n8n instance**
2. Create a new workflow

#### 🧷 Nodes:

1. **Webhook Node**

   * Method: `POST`
   * Path: `calendly-sms`
   * Authentication: None

2. **Function Node** (JavaScript)

```javascript
const data = $input.first().json.body || $input.first().json;

return [{
  json: {
    name: data._0,
    start_time: data._1,
    event_type: data._2,
    phone_number: data._3.replace(/\s+/g, '')
  }
}];
```

3. **Twilio Node**

   * From: Your verified Twilio number
   * To: `{{$json["phone_number"]}}`
   * Message:

   ```
   Hi {{$json["name"]}}, your {{$json["event_type"]}} is scheduled for {{$json["start_time"]}}. We'll contact you at {{$json["phone_number"]}}.
   ```

4. Click **Activate Workflow** ✅

---

## ✅ Live Flow Summary

* Calendly triggers Zapier ✅
* Zapier posts to n8n ✅
* n8n parses data and Twilio sends SMS ✅

---

## 🧪 Testing

* Book a meeting in Calendly with a new phone number
* Wait \~1–15 mins if you're on Zapier Free
* You’ll receive a Twilio SMS automatically 💬

---

## 📌 Notes

* Zapier Free = 15-minute polling, not real-time
* Calendly Free = Doesn’t support Zapier real-time triggers
* For full automation, upgrade **Calendly to Standard**

---

## 🙌 Contribute

Pull requests welcome! Feel free to improve field validation, formatting, or expand to WhatsApp or email notifications.

---

## 🔗 License

MIT
