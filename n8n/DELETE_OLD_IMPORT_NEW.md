# 🚨 STOP - Delete Everything and Start Fresh

## The Problem

You're still using an OLD workflow. I can tell because:

- ❌ Node is called "Format Data" (should be "Extract Clean Fields")
- ❌ It's a SET node (should be CODE node)
- ❌ DataTable operation is "Update" (should be "Upsert")
- ❌ Input shows headers (should NOT have headers)

You need to DELETE all old workflows and import the ULTRA CLEAN one fresh.

---

## ✅ Step-by-Step Fix (Do This NOW)

### Step 1: Delete ALL Old Workflows

1. **Go to n8n:** https://n8n.svenarnarsson.com
2. **Click "Workflows"** (left sidebar)
3. **For EACH workflow** you see that has "Lead" or "Kickoff" or "Intelligence" in the name:
   - Click the **3 dots** (⋮) next to the workflow name
   - Click **"Delete"**
   - Confirm deletion
4. **Make sure** you've deleted ALL of them

**Alternative if you want to keep them:**
- Just make sure they're ALL inactive (toggle OFF)
- And make sure NONE have path `sa1`

### Step 2: Import ULTRA CLEAN (Fresh)

1. **Click "Workflows"** (should be empty now)
2. **Click "Import from File"** (top-right)
3. **Select file:** `/Users/sven/Downloads/Lead Intelligence - ULTRA CLEAN.json`
4. **Click "Import"**

You should see the workflow editor open.

### Step 3: Verify You Have the RIGHT Workflow

**CHECK THESE THINGS:**

1. **Workflow name** at top should say: **"Lead Intelligence - ULTRA CLEAN"**

2. **Look for a node** called **"Extract Clean Fields"**
   - If you see "Format Data" → WRONG workflow, delete and re-import
   - If you see "Extract Clean Fields" → CORRECT! ✅

3. **Click on "Extract Clean Fields" node**
   - Should show JavaScript code
   - First line should say: `// Extract ONLY the fields we want - nothing else!`
   - If it shows a SET node configuration → WRONG workflow

4. **Look at the DataTable node name**
   - Should say: **"Save to DataTable"**
   - NOT "Update row(s)" ❌
   - NOT "Upsert row(s)" ❌

5. **Check the connections**
   - Should be: `Extract Clean Fields → Save to DataTable`
   - NOT: `Format Data → anything`

### Step 4: Configure the DataTable Node

1. **Click on "Save to DataTable" node**
2. **Operation:** Should be **"Upsert"** (not Update)
3. **Data Table:** Select **"Kickoff Submissions"** (or your table name)
4. **Columns to Match On:** Add **"submittedAt"**
5. **Mapping Column Mode:** **"Map Automatically"**
6. **Click "Save"**

### Step 5: Activate

1. **Toggle "Active"** to ON (top-right)
2. You should see the workflow status change to "Active"

---

## 🧪 Test It

### Terminal Test

```bash
curl -X POST https://n8n.svenarnarsson.com/webhook/sa1 \
  -H 'Content-Type: application/json' \
  -d '{"contact":{"name":"Test"},"leadExamples":"Test data"}'
```

**Should return:**
```json
{"success": true, "message": "Kickoff data modtaget"}
```

### Form Test

1. Open: https://intelligent-lead-agent-nhzn2j02t-arnarssons-projects.vercel.app
2. Click "🧪 Udfyld testdata (TEST)"
3. Click "Send formular"
4. Should see success message

### Check the Execution

1. Go to **Executions** tab
2. Click latest execution
3. Click **"Extract Clean Fields"** node
4. Look at **OUTPUT**
5. **Should show ONLY 15 fields:**
   - submittedAt
   - contactName
   - referralSource
   - jobsites
   - jobsitePriority
   - leadExamples
   - minEmployees
   - maxEmployees
   - industries
   - geografi
   - funding
   - hubspotAPI
   - leadHandling
   - commChannel
   - notes

6. **Should NOT show:**
   - ❌ headers
   - ❌ params
   - ❌ query
   - ❌ body
   - ❌ webhookUrl

---

## 🔍 Visual Verification

### Correct Workflow (ULTRA CLEAN)

```
Webhook
  ↓
Check HTTP Method
  ├─ OPTIONS → OPTIONS Response
  └─ POST → Validate Required Fields
              ├─ Valid → Extract Clean Fields (CODE node)
              │            ├─→ Save to DataTable
              │            └─→ Success Response
              └─ Invalid → Error Response
```

**Node names you should see:**
- ✅ Webhook
- ✅ Check HTTP Method
- ✅ OPTIONS Response
- ✅ Validate Required Fields
- ✅ **Extract Clean Fields** ← KEY NODE (Code node)
- ✅ **Save to DataTable** ← DataTable node
- ✅ Success Response
- ✅ Error Response

**Total: 8 nodes**

### Wrong Workflow (Old ones)

If you see these node names, you have the WRONG workflow:
- ❌ Format Data (SET node)
- ❌ Format Data CLEAN (SET node)
- ❌ Upsert row(s) (old DataTable)
- ❌ Update row(s) (old DataTable)

**Delete it and import ULTRA CLEAN again!**

---

## 🆘 Common Mistakes

### Mistake 1: You imported but didn't activate

**Fix:** Toggle "Active" to ON after import

### Mistake 2: Multiple workflows active

**Fix:** Only ONE workflow with path `sa1` can be active at a time. Deactivate all others.

### Mistake 3: Imported wrong file

**Fix:** Make sure you selected "Lead Intelligence - ULTRA CLEAN.json" (not FOOLPROOF, not SIMPLE, not FIXED)

### Mistake 4: Still using old workflow

**Symptom:** Node names don't match (Format Data instead of Extract Clean Fields)

**Fix:** You didn't actually import the new one. Delete everything and re-import.

---

## ✅ Success Checklist

After completing all steps:

- [ ] ALL old workflows deleted or deactivated
- [ ] Imported "Lead Intelligence - ULTRA CLEAN.json"
- [ ] Workflow name shows "Lead Intelligence - ULTRA CLEAN"
- [ ] Node called "Extract Clean Fields" exists (CODE node)
- [ ] Node called "Save to DataTable" exists
- [ ] NO node called "Format Data"
- [ ] NO node called "Update row(s)"
- [ ] Workflow is activated (Active toggle ON)
- [ ] curl test returns success
- [ ] Form submission works
- [ ] Execution shows ONLY 15 fields in code node output
- [ ] NO headers/params/query/body in execution output

---

## 🎯 Final Check

Before testing, take a screenshot of your workflow editor and verify:

1. **Top of editor** says "Lead Intelligence - ULTRA CLEAN"
2. **8 nodes** visible on canvas
3. **One node** has a `</>` icon (that's the Code node - "Extract Clean Fields")
4. **DataTable node** says "Save to DataTable"
5. **Connection** goes from Code node to DataTable

If ANY of these don't match → You have the wrong workflow!

---

**File location again:** `/Users/sven/Downloads/Lead Intelligence - ULTRA CLEAN.json`

Delete everything, import THIS file, activate it, and it will work! 🚀
