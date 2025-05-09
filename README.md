# 📅 Calendly Meeting SMS Notification Using Zapier + n8n + Twilio

This project allows you to send an automated SMS to a user whenever a meeting is scheduled through **Calendly**, using **Zapier** to catch the trigger and **n8n** with **Twilio** to send the SMS. It also includes a styled landing page generated using Hugging Face's DeepSeek tool and hosted on Netlify.

> ✅ Works without coding knowledge — perfect for automation beginners

---

## 🚀 What This Does

1. User visits a custom **website with Calendly embedded**
2. Schedules a meeting through the embedded Calendly widget
3. Zapier detects the new invitee and posts data to an **n8n webhook**
4. n8n processes the data and sends an SMS using **Twilio**

---

## 🛠 Tools Used

| Tool                  | Purpose                          |
| --------------------- | -------------------------------- |
| Calendly              | Collects meeting bookings        |
| Zapier                | Connects Calendly to n8n         |
| n8n                   | Webhook + logic + Twilio SMS     |
| Twilio                | Sends the actual SMS             |
| Hugging Face DeepSeek | Generates the website HTML       |
| Netlify               | Hosts your HTML as a public site |

---

## 📸 Screenshots

### Zapier Flow:

![Zapier Flow] ![image](https://github.com/user-attachments/assets/2b3fb9f6-36b5-4e8f-b12b-6945a48abcfe)


### n8n Workflow:

![n8n Flow] ![image](https://github.com/user-attachments/assets/3a93a8fd-fba7-4de6-a9f4-b58ec090033b)


---

## 📦 Folder Structure

```
calendly-sms-integration/
├── screenshots/                    # Zapier and n8n workflow images
│   ├── zapier-flow.png
│   └── n8n-workflow.png
├── website/                        # Generated DeepSeek HTML page
│   └── index.html
├── calendly-sms-workflow.json      # Exported n8n workflow
├── README.md                       # Full setup documentation
```

---

## 📋 Step-by-Step Setup

### 🔹 Step 1: Build Your Calendly Webpage Using DeepSeek

1. Go to Hugging Face Space [DeepSeek HTML Generator](https://huggingface.co/spaces/enzostvs/deepsite)
2. Use this prompt:

   > "Create a modern and minimal standalone landing page titled 'Book My Time'. The page should have a centered header, a short paragraph explaining how to schedule a meeting, and a large section in the middle where I can embed a Calendly booking calendar. Use light colors, clean fonts, and responsive layout suitable for both desktop and mobile."
3. Copy the generated HTML file
4. Paste your Calendly embed code inside the `<body>` section where the calendar should appear

### 🔹 Step 2: Host Your Page Using Netlify

1. Save your HTML file as `index.html`
2. Put it inside a folder (e.g., `booking-page`)
3. Compress the folder to a ZIP file: `booking-page.zip`
4. Go to [Netlify](https://netlify.com)
5. Sign up or log in
6. Click “Add new site” → “Deploy manually”
7. Drag and drop your `.zip` file
8. Netlify will deploy it instantly and give you a live link (e.g., `https://your-booking-site.netlify.app`)

---

### 🔹 Step 3: Set Up Calendly Event

1. Go to [Calendly](https://calendly.com/)
2. Create a new Event Type (e.g., "Meeting")
3. Under **Location**, select `Phone Call`
4. Enable: `✅ Require invitee's phone number`

---

### 🔹 Step 4: Create Zap in Zapier

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
* **URL**: Your **n8n production webhook URL** (from Step 5)
* **Payload Type**: JSON

##### Send these fields:

| Key            | Value                                  |
| -------------- | -------------------------------------- |
| `name`         | `Invitee Name`                         |
| `start_time`   | `Scheduled Event Start Time`           |
| `event_type`   | `Event Type Name`                      |
| `phone_number` | `Scheduled Event Location` ✅ *(phone)* |

---

### 🔹 Step 5: Set Up n8n Workflow

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

* Visitor books from embedded Calendly page ✅
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
