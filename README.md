# intelligent-lead-agent

Minimal, dark-mode web form for collecting kickoff inputs for a Lead Intelligence System. Built for Source Angel ApS.

## 🚀 Quick Start

### Option 1: Open Directly in Browser
```bash
open web/index.html
```

### Option 2: Serve with HTTP Server
```bash
# Using Python
python3 -m http.server 8000
# Then open http://localhost:8000/web/

# Using Node.js
npx serve web
# Then open http://localhost:3000/
```

### Option 3: Deploy to Vercel
```bash
vercel deploy
```

## 📋 Project Structure

```
intelligent-lead-agent/
├── web/
│   └── index.html          # Main form (standalone, no build needed)
├── n8n/
│   └── lead-intel-example.json  # n8n workflow template
├── README.md              # This file
├── CLAUDE.md              # AI assistant context
├── .gitignore             # Git ignore rules
└── vercel.json            # Vercel deployment config
```

## ⚙️ Configuration

### Webhook URL

The form POSTs JSON data to an n8n webhook. To change the endpoint, edit line 378 in `web/index.html`:

```javascript
const WEBHOOK_URL = 'https://n8n.svenarnarsson.com/webhook/sa1';
```

**Production Recommendation:** Use environment variable injection during deployment to avoid hardcoding webhook URLs.

### CORS Configuration (n8n)

Your n8n webhook must allow CORS from the domain hosting this form. In n8n:

1. Open your webhook node
2. Under "Options" → "Response Headers"
3. Add:
   ```
   Access-Control-Allow-Origin: https://yourdomain.com
   Access-Control-Allow-Methods: POST, OPTIONS
   Access-Control-Allow-Headers: Content-Type
   ```

For development, you can use `*` for origin, but **always restrict in production**.

## 📊 JSON Payload Schema

The form submits the following JSON structure:

```json
{
  "submittedAt": "2025-10-28T11:22:33.456Z",
  "referralSource": "direct",
  "userAgent": "Mozilla/5.0...",
  "contact": {
    "name": "Henning Hørstrup"
  },
  "jobsites": ["The Hub", "LinkedIn Jobs", "Andet"],
  "jobsitePriority": "1. The Hub, 2. LinkedIn...",
  "otherSite": "ExampleJobBoard",
  "icp": {
    "minEmployees": "10-20",
    "maxEmployees": "200",
    "industries": ["Teknologistartups", "SaaS-virksomheder"],
    "otherIndustry": "Custom industry description",
    "geografi": "Danmark",
    "minPositions": "2",
    "dealbreaker": "Automatisk udelukket",
    "positionTypes": "Senior Dev, Backend",
    "otherDealbreakers": "No nonprofits"
  },
  "leadExamples": "1. Synthesia...\n2. Lunar...",
  "funding": "Ja",
  "fundingSources": ["TechSavvy", "Crunchbase"],
  "hubspot": {
    "api": "Ja - vil dele via sikker kanal",
    "leadHandling": "Google Sheet manuel",
    "tag": "AI-qualified"
  },
  "comm": {
    "channel": "Slack",
    "group": "ai-team@sourceangel.dk",
    "tech": "Name <email@domain>"
  },
  "notes": "Any additional context..."
}
```

### Field Descriptions

| Field Path | Type | Required | Description |
|-----------|------|----------|-------------|
| `submittedAt` | string (ISO 8601) | Yes | Submission timestamp |
| `referralSource` | string | Yes | URL param `?ref=` or 'direct' |
| `userAgent` | string | Yes | Browser user agent |
| `contact.name` | string | **Yes** | Client contact name |
| `jobsites` | string[] | No | Selected job boards |
| `jobsitePriority` | string | No | Priority ordering of job sites |
| `otherSite` | string | Conditional | Required if "Andet site" checkbox selected |
| `icp.*` | object | Partial | Ideal Customer Profile criteria |
| `leadExamples` | string | **Yes** | 10-20 company examples |
| `hubspot.*` | object | Partial | HubSpot integration settings |
| `comm.*` | object | No | Communication preferences |
| `notes` | string | No | Additional comments |

## 🧪 Testing Guide

### Manual Testing

**Quick Test with Auto-Fill:**
- Click the purple 🧪 **"Udfyld testdata (TEST)"** button, or press **Ctrl+Shift+T**
- All fields populate with clearly marked placeholder data (⚠️ TEST / TESTDATA labels)
- Perfect for quick demos and testing the complete submission flow

1. **Happy Path Test**
   - Use auto-fill button/shortcut or manually fill required fields: `contactName`, `leadExamples`
   - Submit form → should show success message
   - Check DevTools Network tab for POST to webhook

2. **Validation Test**
   - Leave `contactName` empty
   - Try to submit → should show inline error + focus field
   - Fill it in → error should clear automatically

3. **Dynamic Field Test**
   - Check "Andet site" checkbox
   - Leave text field empty and submit
   - Should show "Angiv venligst navn på andre sites" error
   - Uncheck checkbox → field should hide and clear error

4. **Network Error Test**
   - Open DevTools → Network tab → Enable offline mode
   - Submit form → should show error with retry button
   - Check localStorage for saved data
   - Re-enable network → click "Prøv igen" → should succeed

### Automated Testing (Optional)

```bash
# Install pa11y for accessibility testing
npm install -g pa11y-ci

# Run accessibility audit
pa11y-ci web/index.html
```

## ♿ Accessibility Checklist

- ✅ All form fields have associated `<label>` elements
- ✅ Required fields marked with `aria-required="true"`
- ✅ Error messages linked via `aria-describedby`
- ✅ Invalid states managed with `aria-invalid`
- ✅ Success/error alerts use `aria-live="polite"`
- ✅ Keyboard navigation fully supported
- ✅ Focus states visible (2px outline in accent color)
- ✅ Color contrast meets WCAG AA standards
- ✅ Form groups use appropriate ARIA roles

### Screen Reader Testing

Test with:
- **macOS:** VoiceOver (Cmd + F5)
- **Windows:** NVDA (free) or JAWS
- **Mobile:** TalkBack (Android) or VoiceOver (iOS)

## 🚢 Deployment

### Vercel Deployment

1. **Install Vercel CLI** (if not already installed)
   ```bash
   npm install -g vercel
   ```

2. **Deploy**
   ```bash
   vercel deploy
   ```

3. **Production Deployment**
   ```bash
   vercel --prod
   ```

The `vercel.json` configuration automatically serves the `web/` directory as the site root.

### GitHub Pages

1. **Enable GitHub Pages** in repository settings
2. **Set source** to `main` branch / `web` folder
3. **Access** at `https://yourusername.github.io/intelligent-lead-agent/`

### Static Hosting (Netlify, Cloudflare Pages, etc.)

Simply upload the `web/` directory or point to it in your build settings. No build process required.

## 🔧 Development

### Key Features

- **Zero Build Process:** Standalone HTML file, no dependencies
- **Dark Theme:** Minimal design with gradient backgrounds
- **Real-time Progress:** Visual progress bar tracks form completion
- **Smart Validation:** Per-field inline errors + top-level banner
- **Resilient:** localStorage backup + retry mechanism on network failure
- **Accessible:** WCAG AA compliant with full keyboard navigation
- **Mobile-Friendly:** Responsive design (breakpoint at 780px)
- **Test Data Auto-Fill:** One-click button or keyboard shortcut (Ctrl+Shift+T) to fill form with clearly marked placeholder data

### Customization

#### Changing Colors

Edit CSS variables at line 9-24 in `web/index.html`:

```css
:root {
  --bg: #0b1220;        /* page background */
  --accent: #22c55e;    /* primary green */
  --accent2: #2dd4bf;   /* teal */
  --danger: #ef4444;    /* error red */
  /* ... */
}
```

#### Adding Form Fields

1. Add HTML input in appropriate section
2. Update FormData extraction (lines 529-563)
3. If required, add to validation logic (lines 398-436)
4. Add error span if needed: `<span id="fieldName-error" class="error-text" role="alert">Error message</span>`

#### Changing Validation Rules

Edit the `validateForm()` function starting at line 398. Current required fields:
- `contactName`
- `leadExamples`
- `otherSiteName` (only when "Andet site" checkbox is checked)

## 🔍 Troubleshooting

### Form Doesn't Submit

1. **Check webhook URL** (line 378) is correct and accessible
2. **Verify CORS** headers on n8n webhook
3. **Check browser console** for errors
4. **Test webhook** separately with curl:
   ```bash
   curl -X POST https://n8n.svenarnarsson.com/webhook/sa1 \
     -H "Content-Type: application/json" \
     -d '{"test": "data"}'
   ```

### Validation Not Working

1. **Ensure field has ID** matching error span ID pattern (`fieldName-error`)
2. **Check field has `required` attribute** if validation is mandatory
3. **Verify error span exists** with correct ID in HTML
4. **Check browser console** for JavaScript errors

### Retry Button Not Appearing

The retry button only appears if:
1. Form submission failed (network error or non-2xx response)
2. localStorage contains saved data less than 24 hours old
3. Check browser localStorage: `localStorage.getItem('kickoffData')`

### Accessibility Issues

1. **Test with keyboard only:** Tab through all fields, submit with Enter
2. **Check focus visibility:** Should see 2px teal outline
3. **Test with screen reader:** Errors should be announced
4. **Verify color contrast:** Use browser DevTools contrast checker

## 📚 n8n Workflow Setup

See `n8n/lead-intel-example.json` for a sample workflow. Import it to n8n:

1. Open n8n → Workflows → Import from File
2. Select `n8n/lead-intel-example.json`
3. Configure CORS headers (see Configuration section)
4. Activate workflow
5. Test with form submission

## 🤝 Contributing

This is a private project for Source Angel ApS. For internal development:

1. **Clone repo:**
   ```bash
   git clone https://github.com/yourusername/intelligent-lead-agent.git
   ```

2. **Make changes** to `web/index.html`

3. **Test locally** (see Testing Guide above)

4. **Commit & push:**
   ```bash
   git add .
   git commit -m "feat: describe your changes"
   git push origin main
   ```

5. **Deploy** to Vercel automatically via Git integration

## 📄 License

Private project for Source Angel ApS. All rights reserved.

## 📞 Support

For questions or issues:
- **Technical:** Contact development team
- **n8n Setup:** Refer to [n8n documentation](https://docs.n8n.io/)
- **Vercel Deployment:** See [Vercel docs](https://vercel.com/docs)

---

**Built with ❤️ for Source Angel ApS** | Lead Intelligence System