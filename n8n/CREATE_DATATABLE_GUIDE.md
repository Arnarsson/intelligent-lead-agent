# 📊 DataTable Setup Guide

## 🎯 Create the Correct DataTable Structure

You need a DataTable with ONLY these columns (not all the webhook metadata):

### ✅ Required Columns

| Column Name | Type | Description |
|-------------|------|-------------|
| submittedAt | String | Submission timestamp (used as unique key) |
| contactName | String | Contact person name |
| referralSource | String | How they found us |
| jobsites | String | Comma-separated list of jobsites |
| jobsitePriority | String | Priority ranking of jobsites |
| leadExamples | String | Example companies |
| minEmployees | String | Minimum employee count |
| maxEmployees | String | Maximum employee count |
| industries | String | Comma-separated industries |
| geografi | String | Geographic location |
| funding | String | Funding status |
| hubspotAPI | String | HubSpot API info |
| leadHandling | String | How to handle leads |
| commChannel | String | Communication channel |
| notes | String | Additional notes |

**Total: 15 columns** (NO headers, NO params, NO query, NO body, NO webhookUrl)

---

## 📋 How to Create DataTable in n8n

### Option 1: Create New DataTable

1. **Go to n8n:** https://n8n.svenarnarsson.com
2. **Click "Data"** in left sidebar
3. **Click "Create Data Table"** (top-right)
4. **Name:** `Lead Submissions Clean`
5. **Add columns** - Click "Add Column" 15 times for each field above
6. For each column:
   - **Name:** Use exact name from table above
   - **Type:** String
   - **Click "Add"**
7. **Save** the DataTable

### Option 2: Use Existing but Empty It

If you have "Kickoff Submissions" table:

1. Go to n8n → **Data** → **Kickoff Submissions**
2. **Delete all rows** with bad data (header rows)
3. Keep the table structure
4. The new workflow will populate it correctly

---

## 🔧 Update the Workflow to Use Correct Table

After creating the DataTable:

1. Open the workflow in editor
2. Click on **"Save to DataTable"** node
3. In "Data Table" dropdown:
   - If you created new: Select **"Lead Submissions Clean"**
   - If using existing: Keep **"Kickoff Submissions"**
4. **Click "Save"**

---

## ❌ What You DON'T Want in DataTable

These fields should NOT appear in your DataTable:

- ❌ `headers` (HTTP headers like user-agent, host, etc.)
- ❌ `params` (URL parameters)
- ❌ `query` (Query string)
- ❌ `body` (Raw request body)
- ❌ `webhookUrl` (The webhook URL)
- ❌ `executionMode` (n8n execution mode)

These are webhook metadata that n8n adds automatically. Our workflow should filter them out!

---

## 🎯 What the DataTable Should Look Like

### Good Example (Correct)

| submittedAt | contactName | leadExamples | industries |
|-------------|-------------|--------------|------------|
| 2025-10-28T14:31:56Z | Henning Morten | Synthesia, Lunar... | Tech, SaaS |
| 2025-10-28T15:20:00Z | John Doe | Company A, B, C | Finance |

### Bad Example (What You're Getting Now)

| headers | params | query | body | submittedAt | contactName |
|---------|--------|-------|------|-------------|-------------|
| {huge object...} | {} | {} | {huge object...} | 2025-10-28... | Henning... |

---

## 🛠️ The Fix

The FOOLPROOF workflow I created should prevent this, but if you're still seeing headers, here's what to check:

### 1. Verify "Keep Only Set" is ON

In the workflow:
1. Click **"Format Data CLEAN"** node
2. Scroll to **"Options"**
3. **"Keep Only Set"** must be **ON** ✅

If it's OFF, the node passes through ALL webhook data!

### 2. Check the Connection

Make sure the connection goes:
```
Format Data CLEAN → Save to DataTable
```

NOT:
```
Webhook → Save to DataTable ❌
```

### 3. Test with Execution Inspection

1. Submit form
2. Go to **Executions** tab
3. Click latest execution
4. Click **"Format Data CLEAN"** node
5. Look at OUTPUT
6. Should see ONLY 15 fields
7. Should NOT see headers, params, query, body

If you see headers in the output, the node isn't configured correctly!

---

## 📄 Google Sheet Alternative

Since you linked a Google Sheet, you might prefer saving to Google Sheets instead of n8n DataTable.

**Advantages:**
- Easier to view and export data
- Better for sharing with team
- No DataTable configuration issues

**To use Google Sheets instead:**

1. In workflow, **disable** "Save to DataTable" node
2. Add a **Google Sheets** node
3. Configure:
   - **Operation:** Append
   - **Document ID:** Your sheet ID from the URL
   - **Sheet Name:** "Kickoff Submissions"
4. Connect: `Format Data CLEAN → Google Sheets`
5. Save and activate

---

## ✅ Verification Checklist

After fixing:

- [ ] DataTable has 15 columns (not 20+)
- [ ] No "headers" column in DataTable
- [ ] No "params" or "query" columns
- [ ] No "body" or "webhookUrl" columns
- [ ] Only clean form data visible
- [ ] Each row represents one form submission
- [ ] All text is readable (not [object Object])

---

## 🆘 Still Seeing Headers?

If you're still getting headers in your DataTable:

**Nuclear Option - Start Fresh:**

1. **Delete** the current DataTable completely
2. **Create new** DataTable with ONLY the 15 columns listed above
3. **Import** the FOOLPROOF workflow again (fresh import)
4. **Verify** "Keep Only Set" is ON in Format node
5. **Update** DataTable node to use new table
6. **Test** with form submission

This guarantees a clean slate!

---

The key is: **Format Data CLEAN node with keepOnlySet: true** should output ONLY 15 fields, nothing else. If you're seeing more fields in the DataTable, that parameter isn't working or the connection is wrong.
