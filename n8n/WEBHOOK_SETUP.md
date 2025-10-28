# n8n Webhook Setup Instructions

## 🚨 Current Issues

Your webhook at `https://n8n.svenarnarsson.com/webhook/sa1` has two problems:

1. **Error 500:** "Unused Respond to Webhook node found in the workflow"
2. **CORS Error:** Only allows `OPTIONS, GET` methods, but form needs `POST`

---

## ✅ Quick Fix - Import Corrected Workflow

### Option 1: Import Fixed Workflow (Easiest)

1. **Download the fixed workflow:**
   - File: `n8n/lead-intel-kickoff-fixed.json`

2. **Import to n8n:**
   - Open n8n → Workflows
   - Click **"Import from File"**
   - Select `lead-intel-kickoff-fixed.json`
   - Click **"Import"**

3. **Activate the workflow:**
   - Click **"Active"** toggle in top-right
   - Test URL should be: `https://n8n.svenarnarsson.com/webhook/sa1`

### Option 2: Fix Existing Workflow Manually

#### Step 1: Fix the Webhook Node

1. Open your existing workflow
2. Click on the **Webhook** node
3. Ensure these settings:
   - **HTTP Method:** `POST` (not GET!)
   - **Path:** `sa1`
   - **Response Mode:** `Using 'Respond to Webhook' Node`

4. Under **Options** → **Response Headers**, add:
   ```
   Access-Control-Allow-Origin: *
   Access-Control-Allow-Methods: POST, OPTIONS, GET
   Access-Control-Allow-Headers: Content-Type, Accept
   ```

#### Step 2: Add/Fix Respond to Webhook Node

The error "Unused Respond to Webhook node" means:
- You have a "Respond to Webhook" node that's **not connected** to the webhook flow
- OR you're missing this node entirely

**Fix:**

1. If you have a "Respond to Webhook" node:
   - **Connect it** directly after your Webhook node
   - The flow should be: `Webhook → [processing] → Respond to Webhook`

2. If you don't have one, add it:
   - Click **"+"** after your Webhook node
   - Search for **"Respond to Webhook"**
   - Configure:
     - **Respond With:** `JSON`
     - **Response Code:** `200`
     - **Response Body:**
       ```json
       {
         "success": true,
         "message": "Kickoff data modtaget",
         "timestamp": "{{$now.toISO()}}"
       }
       ```

#### Step 3: Save and Activate

1. Click **"Save"** (top-right)
2. Toggle **"Active"** to ON
3. The webhook should now be live at: `https://n8n.svenarnarsson.com/webhook/sa1`

---

## 🧪 Test the Webhook

### Using curl:
```bash
curl -X POST https://n8n.svenarnarsson.com/webhook/sa1 \
  -H "Content-Type: application/json" \
  -d '{
    "test": true,
    "contact": {"name": "Test User"},
    "submittedAt": "'$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
  }'
```

**Expected Response:**
```json
{
  "success": true,
  "message": "Kickoff data modtaget",
  "timestamp": "2025-10-28T13:20:00.000Z"
}
```

### Using the Form:
1. Go to: https://intelligent-lead-agent-nhzn2j02t-arnarssons-projects.vercel.app
2. Click **"🧪 Udfyld testdata (TEST)"** button
3. Click **"Send formular"**
4. Should see: **"✅ Sendt! Vi har modtaget jeres kickoff‑data."**

---

## 🔍 Verify CORS Headers

Run this to check CORS is working:
```bash
curl -X OPTIONS https://n8n.svenarnarsson.com/webhook/sa1 \
  -H "Origin: https://intelligent-lead-agent-nhzn2j02t-arnarssons-projects.vercel.app" \
  -H "Access-Control-Request-Method: POST" \
  -H "Access-Control-Request-Headers: Content-Type" \
  -v 2>&1 | grep -i "access-control"
```

**Should see:**
```
access-control-allow-origin: *
access-control-allow-methods: POST, OPTIONS, GET
access-control-allow-headers: Content-Type, Accept
```

---

## 📊 Workflow Structure

Your n8n workflow should look like this:

```
┌─────────────┐
│   Webhook   │ (HTTP Method: POST, Path: sa1)
│  (Trigger)  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  [Optional  │ (Process data, save to DB, send email, etc.)
│ Processing] │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Respond to │ (REQUIRED - must be connected!)
│   Webhook   │
└─────────────┘
```

**Key Points:**
- ✅ Webhook node must have `responseMode: "responseNode"`
- ✅ Webhook HTTP method must be `POST`
- ✅ "Respond to Webhook" node must be in the flow and connected
- ✅ CORS headers must allow POST and your domain

---

## 🆘 Still Not Working?

### Check n8n Logs:
```bash
# If using Docker:
docker logs n8n

# If using npm:
tail -f ~/.n8n/logs/n8n.log
```

### Common Issues:

1. **"Unused Respond to Webhook node"**
   - → Connect the node to your workflow

2. **"Method Not Allowed"**
   - → Change HTTP Method to POST in Webhook node

3. **CORS errors in browser console**
   - → Add CORS headers in Webhook node options

4. **No response from webhook**
   - → Make sure workflow is **Active** (toggle ON)

---

## 📝 Production Checklist

Before going live:

- [ ] Webhook returns HTTP 200 for valid data
- [ ] CORS headers allow your production domain
- [ ] "Respond to Webhook" node is connected and working
- [ ] Workflow is activated (Active toggle ON)
- [ ] Test with curl shows success response
- [ ] Test from form shows success message
- [ ] Error handling works (test with invalid data)
- [ ] Data is being saved/processed as expected

---

## 💾 Save Form Data (Optional)

Add these nodes after the Webhook to save data:

### Save to Google Sheets:
```
Webhook → Respond to Webhook → Google Sheets (Append)
```

### Save to Database:
```
Webhook → Respond to Webhook → PostgreSQL/MySQL/MongoDB
```

### Send Email Notification:
```
Webhook → Respond to Webhook → Send Email
```

**Important:** Always respond to webhook first, then do processing to avoid timeouts!

---

## 📞 Need Help?

If still having issues:
1. Export your workflow (JSON)
2. Check n8n community forum: https://community.n8n.io
3. Share the error message and workflow structure
