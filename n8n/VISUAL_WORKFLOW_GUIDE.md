# 👀 Visual Workflow Verification Guide

## What the CORS FIXED Workflow Should Look Like

After importing `Lead Intelligence Kickoff - CORS FIXED.json`, your workflow should have this structure:

---

## 📊 Node Structure (7 Nodes Total)

```
1. Webhook (Trigger)
   ↓
2. Check HTTP Method (IF node)
   ├─ True (OPTIONS) ──→ 3. OPTIONS Response
   └─ False (POST) ────→ 4. Validate Required Fields
                            ├─ True (Valid) ──→ 5. Format Data
                            │                      ↓
                            │                   6. Success Response
                            └─ False (Invalid) ─→ 7. Error Response
```

---

## 🔍 Node-by-Node Verification

### Node 1: Webhook (Trigger)
**Node Type:** `n8n-nodes-base.webhook`
**Position:** Top-left
**Icon:** Webhook symbol

**Configuration:**
- **Path:** `sa1`
- **Response Mode:** "Using 'Respond to Webhook' Node"
- **HTTP Method:** Not set (accepts all methods)
- **Authentication:** None

**How to check:**
1. Click on the Webhook node
2. Parameters panel shows on right
3. Verify `path` field = `sa1`

---

### Node 2: Check HTTP Method (IF)
**Node Type:** `n8n-nodes-base.if`
**Position:** After Webhook
**Icon:** Diamond/branching symbol

**Configuration:**
- **Condition:** String comparison
- **Value 1:** `={{$httpMethod}}`
- **Operation:** "Equal"
- **Value 2:** `OPTIONS`

**Purpose:** Routes OPTIONS requests to CORS response, POST requests to validation

**How to check:**
1. Click on "Check HTTP Method" node
2. Should show IF condition settings
3. Verify condition checks for OPTIONS

---

### Node 3: OPTIONS Response
**Node Type:** `n8n-nodes-base.respondToWebhook`
**Position:** Upper branch from IF node
**Icon:** Response symbol

**Configuration:**
- **Respond With:** "Text"
- **Response Body:** Empty string
- **Response Code:** 204
- **Response Headers:** 4 entries
  - Access-Control-Allow-Origin: *
  - Access-Control-Allow-Methods: POST, OPTIONS, GET
  - Access-Control-Allow-Headers: Content-Type, Accept, Origin
  - Access-Control-Max-Age: 86400

**Purpose:** Handles CORS preflight (OPTIONS) requests

**How to check:**
1. Click on "OPTIONS Response" node
2. Scroll down to "Options" section
3. Expand "Response Headers"
4. Should see 4 header entries

---

### Node 4: Validate Required Fields (IF)
**Node Type:** `n8n-nodes-base.if`
**Position:** Lower branch from Check HTTP Method
**Icon:** Diamond/branching symbol

**Configuration:**
- **Condition 1:** `={{$json.body.contact.name}}` is not empty
- **Condition 2:** `={{$json.body.leadExamples}}` is not empty

**Purpose:** Validates required form fields

**How to check:**
1. Click node
2. Should show 2 conditions
3. Both check for "isNotEmpty"

---

### Node 5: Format Data (SET)
**Node Type:** `n8n-nodes-base.set`
**Position:** After successful validation
**Icon:** Database/set symbol

**Configuration:**
- **16 string values** being set
- Includes: submittedAt, contactName, leadExamples, etc.
- Last value: `rawData` = full JSON

**Purpose:** Extracts and formats all form fields

**How to check:**
1. Click node
2. Should show long list of "String" values
3. Verify `rawData` is last entry

---

### Node 6: Success Response
**Node Type:** `n8n-nodes-base.respondToWebhook`
**Position:** After Format Data
**Icon:** Response symbol

**Configuration:**
- **Respond With:** "JSON"
- **Response Code:** 200
- **Response Body:**
  ```json
  {
    "success": true,
    "message": "Kickoff data modtaget",
    "timestamp": "{{$now.toISO()}}",
    "contactName": "{{$json.contactName}}"
  }
  ```
- **Response Headers:** 3 CORS entries

**Purpose:** Returns success to form

---

### Node 7: Error Response
**Node Type:** `n8n-nodes-base.respondToWebhook`
**Position:** Lower branch from Validate
**Icon:** Response symbol

**Configuration:**
- **Respond With:** "JSON"
- **Response Code:** 400
- **Response Body:**
  ```json
  {
    "success": false,
    "error": "Manglende påkrævede felter: contactName eller leadExamples"
  }
  ```
- **Response Headers:** 3 CORS entries

**Purpose:** Returns validation error to form

---

## ✅ Visual Verification Checklist

When you open the workflow in n8n editor:

- [ ] **7 nodes total** visible on canvas
- [ ] **Webhook node** at the start (left side)
- [ ] **2 IF nodes** (diamond shapes) - Check Method and Validate
- [ ] **3 Respond to Webhook nodes** - OPTIONS, Success, Error
- [ ] **1 SET node** (Format Data)
- [ ] **Connection lines** match the flow diagram above
- [ ] **Node names** match exactly as shown
- [ ] **No red error icons** on any node
- [ ] **No yellow warning icons** on any node

---

## 🎨 Expected Canvas Layout

```
        Webhook
           |
    Check HTTP Method
      /           \
(OPTIONS)        (POST)
     |              |
OPTIONS Res.   Validate Fields
                 /        \
            (Valid)     (Invalid)
                |           |
          Format Data   Error Res.
                |
          Success Res.
```

---

## 🔴 Red Flags (Wrong Workflow)

If you see these, you imported the WRONG workflow:

❌ **Only 5 nodes** - That's the SIMPLE WORKING version (has CORS issues)
❌ **DataTable node** - That's the original (causes errors)
❌ **No "Check HTTP Method" node** - Missing OPTIONS handler
❌ **Webhook has httpMethod: POST** - Won't handle OPTIONS
❌ **No response headers configured** - Missing CORS headers

---

## 🟢 Green Flags (Correct Workflow)

If you see these, you have the RIGHT workflow:

✅ **7 nodes total**
✅ **"Check HTTP Method" IF node** exists
✅ **"OPTIONS Response" node** exists
✅ **All 3 response nodes have CORS headers**
✅ **Webhook accepts all methods** (no httpMethod restriction)
✅ **Flow branches: OPTIONS → Response, POST → Validation**

---

## 📸 How to Take a Screenshot for Debugging

If still having issues:

1. Open workflow in n8n
2. Zoom out to see all nodes
3. Take screenshot (Cmd+Shift+4 on Mac)
4. Check if layout matches the diagram above

---

## 🧪 Test Each Node Individually

You can test the workflow step-by-step in n8n:

1. Click **"Execute Workflow"** button (top-right)
2. Send a test request from your form
3. Click on each node to see its output
4. Green checkmarks = node executed successfully
5. Red X = node failed (click to see error)

**Expected flow for form submission:**

1. ✅ Webhook receives POST
2. ✅ Check HTTP Method → False branch (not OPTIONS)
3. ✅ Validate Required Fields → True branch (fields valid)
4. ✅ Format Data → Extracts all fields
5. ✅ Success Response → Returns 200 OK

---

## 💡 Common Import Issues

### "Workflow has no trigger"
- **Fix:** Webhook node isn't configured as trigger
- **Solution:** Re-import the workflow

### "Unused Respond to Webhook node"
- **Fix:** Response mode not set correctly
- **Solution:** Check Webhook node has "responseMode": "responseNode"

### "Nodes not connected"
- **Fix:** Connection lines missing
- **Solution:** Re-import, don't manually create workflow

### "Can't find imported workflow"
- **Fix:** Import succeeded but workflow not showing
- **Solution:** Click "Workflows" in sidebar → Should see new workflow in list

---

Your workflow should look EXACTLY like the diagrams above. If it doesn't, you may have imported the wrong file or need to re-import! 🎯
