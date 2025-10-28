# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains a **Lead Intelligence System kickoff form** for Source Angel ApS. The form is a standalone HTML application that collects client information for lead generation system setup.

## Architecture

### Single-Page Application Structure
- **Self-contained HTML file**: All HTML, CSS, and JavaScript in one file (`kickoff_form_ultra_clean_no_comms_hosting_leads_phases.html`)
- **No build process**: Direct deployment - open file in browser or serve via web server
- **No dependencies**: Vanilla JavaScript, no external libraries or frameworks

### Key Components

#### 1. Form Data Collection
The form collects structured data across multiple sections:
- Contact information
- Job site preferences and priorities (The Hub, Jobindex, LinkedIn, TechSavvy, Crunchbase)
- Ideal Customer Profile (ICP) criteria:
  - Company size (employee count ranges)
  - Industry types (tech startups, agencies, fintech, SaaS)
  - Geography (Denmark, Scandinavia, EU)
  - IT position requirements
  - Deal-breaker criteria (e.g., Eastern European teams)
- Lead examples (10-20 target companies)
- Funding preferences and sources
- Contact person priorities (CTO, VP Engineering, etc.)
- HubSpot integration settings
- Communication preferences

#### 2. Webhook Integration
- **Endpoint**: `https://n8n.aigrowthadvisors.cc/webhook/lead-intel-kickoff`
- **Method**: POST with JSON payload
- **Fallback**: Data saved to localStorage for resilience
- **No authentication**: Webhook should handle security

#### 3. UI Features
- Dark theme with gradient background
- Real-time progress bar tracking form completion
- Dynamic field visibility (e.g., "other site" checkbox reveals text input)
- Form validation with required field checking
- Success/error alerts
- Mobile-responsive design (grid collapses at 780px)

## Working with This File

### Testing Locally
```bash
# Option 1: Open directly in browser
open kickoff_form_ultra_clean_no_comms_hosting_leads_phases.html

# Option 2: Serve with simple HTTP server
python3 -m http.server 8000
# Then open http://localhost:8000/kickoff_form_ultra_clean_no_comms_hosting_leads_phases.html

# Option 3: Use any web server
npx serve .
```

### Configuration Changes

#### Webhook URL
Located at line 306:
```javascript
const WEBHOOK_URL = 'https://n8n.aigrowthadvisors.cc/webhook/lead-intel-kickoff';
```

#### Required Fields
Defined at line 344 in submit handler:
```javascript
const required = ['contactName','leadExamples'];
```

#### Styling Variables
CSS custom properties at lines 9-25:
```css
:root {
  --bg: #0b1220;      /* page background */
  --card: #0f172a;    /* container */
  --accent: #22c55e;  /* primary green */
  --accent2: #2dd4bf; /* teal */
  /* ... */
}
```

### Form Data Structure

The submitted payload follows this structure:
```javascript
{
  submittedAt: ISO timestamp,
  referralSource: URL param 'ref' or 'direct',
  userAgent: Browser UA string,
  contact: { name: string },
  jobsites: string[],
  otherSite: string,
  icp: {
    minEmployees, maxEmployees,
    industries: string[],
    geografi, minPositions,
    dealbreaker, positionTypes,
    otherDealbreakers
  },
  leadExamples: string,
  funding: string,
  fundingSources: string[],
  hubspot: { api, leadHandling, tag },
  comm: { channel, group, tech },
  notes: string
}
```

## Language

- **UI Language**: Danish (da)
- **Page Title**: "Kickoff Information — Lead Intelligence System"
- All labels, placeholders, and messages are in Danish

## Deployment

This is a static file with no build process:
1. Upload to any web server or hosting platform
2. Ensure webhook endpoint is accessible
3. Optionally add URL parameter `?ref=source` for tracking referral sources
4. No SSL/HTTPS required for the form itself, but webhook should use HTTPS

## Common Modifications

### Adding Form Fields
1. Add HTML input in appropriate section (lines 101-288)
2. Update FormData extraction in submit handler (lines 356-380)
3. Update data structure sent to webhook
4. Consider adding to required fields array if mandatory

### Changing Validation
- Modify required fields array at line 344
- Add custom validation logic before line 354 (FormData creation)
- Update error messages as needed

### Styling Adjustments
- Modify CSS variables in `:root` (lines 9-25)
- Adjust responsive breakpoint at line 82 (currently 780px)
- Modify spacing/sizing in component classes (lines 33-82)

## Security Considerations

- **No authentication**: Form is publicly accessible
- **No CSRF protection**: Consider adding token if needed
- **Input sanitization**: Handled on webhook/backend side
- **localStorage usage**: Client-side only, no sensitive data persistence
- **API keys**: Form asks about HubSpot API but doesn't transmit it (asks for secure channel)

## Integration Points

### n8n Workflow
The form integrates with an n8n workflow at the webhook endpoint. Expected n8n workflow should:
1. Receive JSON payload
2. Validate data structure
3. Store in database or Google Sheets
4. Optionally trigger HubSpot integration based on `leadHandling` preference
5. Send confirmation email (optional)

### HubSpot Integration
Three handling modes supported:
1. **Google Sheet manual**: Export for manual review
2. **HubSpot direct**: Automatic contact creation
3. **HubSpot with tag**: Auto-tag with custom label

## No Repository Features

This repository currently contains:
- ❌ No version control (.git)
- ❌ No package manager (no package.json, requirements.txt, etc.)
- ❌ No build tools
- ❌ No testing framework
- ❌ No CI/CD
- ❌ No dependency management

If the project grows, consider adding:
- Git for version control
- Separate CSS/JS files for maintainability
- TypeScript for type safety
- Form validation library
- Testing framework (Playwright, Vitest)
- Build process for minification
