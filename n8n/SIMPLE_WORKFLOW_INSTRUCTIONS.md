# ✅ Simplified Workflow - Quick Import Guide

## 🎯 What This Fixes

Your workflow was failing with: **"At least one condition is required"** from the DataTable node.

This simplified workflow **removes the DataTable node entirely** to get your webhook working immediately. You can add storage later once the core webhook is proven functional.

---

## 📥 Import Instructions

### Step 1: Import the Simplified Workflow

1. **Go to n8n**: https://n8n.svenarnarsson.com
2. **Click**: Workflows → **"Import from File"**
3. **Select**: `Lead Intelligence Kickoff - SIMPLE WORKING.json` (in Downloads folder)
4. **Click**: Import

### Step 2: Activate the Workflow

1. **Toggle "Active"** to ON (top-right corner)
2. The webhook URL will be: `https://n8n.svenarnarsson.com/webhook/sa1`

### Step 3: Test It Immediately

**Using the form:**
1. Go to: https://intelligent-lead-agent-nhzn2j02t-arnarssons-projects.vercel.app
2. Click **"🧪 Udfyld testdata (TEST)"** button (or press Ctrl+Shift+T)
3. Click **"Send formular"**
4. ✅ Should see: **"Sendt! Vi har modtaget jeres kickoff‑data."**

**Using curl (terminal test):**
```bash
curl -X POST https://n8n.svenarnarsson.com/webhook/sa1 \
  -H "Content-Type: application/json" \
  -d '{
    "contact": {"name": "Test User"},
    "leadExamples": "Test company data",
    "submittedAt": "'$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
  }'
```

**Expected response:**
```json
{
  "success": true,
  "message": "Kickoff data modtaget",
  "submittedAt": "2025-10-28T..."
}
```

---

## 🔍 What Changed?

### Removed (Causing Errors)
- ❌ **"Upsert row(s)"** DataTable node - This required conditions we hadn't configured
- ❌ MongoDB node (was disabled anyway)
- ❌ Google Sheets node (was disabled anyway)

### Kept (Core Functionality)
- ✅ **Webhook** - Accepts POST requests with CORS
- ✅ **Validate Required Fields** - Checks contactName and leadExamples
- ✅ **Format Data** - Extracts and formats all form fields safely
- ✅ **Success/Error Responses** - Returns proper JSON responses

### Added Safety
- All data extraction uses **optional chaining** to prevent errors:
  ```javascript
  // Safe - won't crash if field is missing
  "={{$json.body.icp?.minEmployees || ''}}"
  "={{$json.body.jobsites ? $json.body.jobsites.join(', ') : ''}}"
  ```
- **rawData field** - Contains full JSON as backup

---

## 📊 Simplified Workflow Structure

```
┌─────────────┐
│   Webhook   │ POST to /webhook/sa1
│  (Trigger)  │ (with CORS headers)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Validate   │ Check required fields:
│   Fields    │ - contactName
└──────┬──────┘ - leadExamples
       │
       ├─── ✅ Valid ───────┐
       │                     ▼
       │              ┌─────────────┐
       │              │ Format Data │ Extract all fields
       │              └──────┬──────┘
       │                     │
       │                     ▼
       │              ┌─────────────┐
       │              │   Success   │ Return 200 OK
       │              │  Response   │ with JSON
       │              └─────────────┘
       │
       └─── ❌ Invalid ─────┐
                            ▼
                     ┌─────────────┐
                     │    Error    │ Return 400
                     │  Response   │ with error
                     └─────────────┘
```

---

## 🎯 Verify It's Working

### 1. Check n8n Execution Log

1. Go to n8n → **Workflows** → "Lead Intelligence Kickoff - SIMPLE WORKING"
2. Click **"Executions"** tab (top-right)
3. You should see successful executions when form is submitted
4. Click on an execution to see the data flow

### 2. Check Browser Console (No Errors)

1. Open form in browser
2. Press **F12** (Developer Tools)
3. Go to **Console** tab
4. Fill test data and submit
5. Should see: ✅ Green success messages, no red errors

### 3. Check Network Tab (200 OK)

1. In Developer Tools, go to **Network** tab
2. Submit form
3. Look for request to `sa1`
4. Should show: **Status: 200** (not 400 or 500)

---

## ➕ Optional: Add Storage Later

Once the webhook is working, you can add storage:

### Option A: Google Sheets
```
Format Data → Google Sheets (Append) → Success Response
```
- Node: Google Sheets
- Operation: Append
- Configure with your sheet ID

### Option B: MongoDB
```
Format Data → MongoDB (Insert) → Success Response
```
- Node: MongoDB
- Operation: Insert
- Configure with your MongoDB connection

### Option C: DataTable (Properly Configured)
```
Format Data → DataTable (Upsert) → Success Response
```
- Node: DataTable (Upsert)
- **Important**: Configure "Matching Columns" or "Conditions"
- Map all fields properly

---

## 🆘 Troubleshooting

### Still Getting Errors?

**"404 Not Found"**
- → Workflow is not active
- → Check workflow is toggled ON
- → Verify path is exactly `sa1`

**"CORS error in browser"**
- → CORS headers not applied
- → Re-import the simplified workflow
- → Check webhook node has responseHeaders configured

**"500 Internal Server Error"**
- → Check n8n execution log for details
- → Look for specific error message
- → Verify all nodes are properly connected

**Form shows error but n8n shows success**
- → Check response format from Success Response node
- → Should return JSON with `success: true`
- → Verify response code is 200

### Check Webhook Configuration

Open the **Webhook node** and verify:
- ✅ **Path**: `sa1`
- ✅ **HTTP Method**: `POST`
- ✅ **Response Mode**: `Using 'Respond to Webhook' Node`
- ✅ **Response Headers**: Three entries (Origin, Methods, Headers)

---

## ✅ Success Checklist

After importing and activating, verify:

- [ ] Workflow shows **"Active"** badge in n8n
- [ ] Webhook URL is `https://n8n.svenarnarsson.com/webhook/sa1`
- [ ] curl test returns `{"success": true}`
- [ ] Form submission shows success message
- [ ] No CORS errors in browser console
- [ ] Execution appears in n8n **Executions** tab
- [ ] Formatted data visible in execution details

---

## 🎉 What You Get

This simplified workflow gives you:
1. **Working webhook** - Form successfully sends data to n8n
2. **Proper validation** - Required fields are checked
3. **Safe data handling** - No crashes from missing fields
4. **Full data backup** - rawData field contains complete JSON
5. **Clear responses** - Success/error messages work correctly

**Storage comes later** - Focus on getting the webhook working first!

---

## 📞 Next Steps

1. **Import** the simplified workflow
2. **Activate** it
3. **Test** with the form
4. **Verify** executions appear in n8n
5. **Celebrate** 🎉 - Your webhook is working!
6. **Add storage** when you're ready (optional)

The key insight: **Get it working simply first, add complexity later.**
