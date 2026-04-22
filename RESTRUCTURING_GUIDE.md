# HubSpot Onboarding Portal - Restructuring & Development Guide

**Project Status:** Analysis Complete | Ready for Restructuring  
**Last Updated:** April 2026  
**Maintainer:** Ryan (Initial) → Future Team Members

---

## 📋 Executive Summary

Your HubSpot CMS portal uses **HubL + HTML + CSS + JavaScript** with a modular architecture that's **already well-organized at the folder level**, but needs:

1. ✅ **Code readability improvements** (lines are too long, need formatting/comments)
2. ✅ **Reusable component extraction** (tables, forms, modals appear in multiple modules)
3. ✅ **Better documentation** for future maintainers
4. ✅ **New feature scaffolding** (location-filtered data tables, onboarding uploads, tickets)
5. ✅ **Data query optimization** (some GraphQL queries can be consolidated)

---

## 🏗️ Current Architecture

### **Folder Structure**
```
onboarding-portal/
├── modules/                    # HubSpot modules (self-contained UI components)
│   ├── _MJTW_BASE.module/      # Base layout & styles
│   ├── dashboard.module/       # Homepage/dashboard
│   ├── header.module/          # Navigation header
│   ├── sidebar.module/         # Sidebar navigation
│   ├── pricing.module/         # Pricing & packages display
│   ├── location-detail.module/ # Single location view
│   ├── locations-listing.module/ # All locations list
│   ├── subscription-detail.module/ # Subscription/ticket details (645 lines!)
│   ├── subscription-setup.module/  # New subscription form
│   ├── user-manager.module/    # User management
│   ├── new-subscription.module/ # New subscription flow
│   └── page-settings.module/   # Page/portal settings
├── queries/                    # GraphQL queries (data fetching)
│   ├── locations.graphql
│   ├── location-subscriptions.graphql
│   ├── properties.graphql
│   ├── pricing.graphql
│   ├── subscriptions.graphql
│   └── ... (14 files total)
├── templates/                  # Page layout templates
│   ├── layouts/
│   │   ├── mjtw-base.html
│   │   └── mjtw-login-base.html
│   └── membership/
│       ├── login.html
│       ├── logout.html
│       └── access-denied.html
├── css/                        # Stylesheets
│   ├── mjtw_styles--desktop.css
│   └── mjtw_styles--mobile.css
├── js/                         # JavaScript
│   └── mjtw_main.js           # (minified/bundled)
├── fonts/                      # Web fonts
└── images/                     # Static assets
```

### **Current Module Structure** (Each .module folder contains)
```
modulename.module/
├── module.html       # HubL template (what displays)
├── module.js         # JavaScript for interactivity (often empty)
├── module.css        # Module-specific styles
├── fields.json       # CMS fields (what can be edited in HubSpot CMS UI)
└── meta.json         # Module metadata
```

---

## 🔍 Code Analysis

### **Module File Sizes**
| Module | HTML Lines | Purpose |
|--------|----------|---------|
| subscription-detail.module | 645 | ⚠️ **TOO LARGE** - needs splitting |
| location-detail.module | 72 | Good size |
| pricing.module | 65 | Good size |
| dashboard.module | 75 | Good size |

### **Key Observations**

#### **1. Code Readability Issues** ❌
- **Single-line HTML blocks** (some lines ~300+ chars)
- **No comments/documentation** explaining logic
- **Mixed concerns** (e.g., subscription-detail handles data fetching, UI rendering, and form submission all in one file)

#### **2. Reusable Components** 🔄
These appear multiple times but aren't abstracted:
- **Data Tables** (pricing tables, subscription tables, property tables)
- **Modals** (edit location, edit property, etc.)
- **Forms** (various input types, validation)
- **Expandable sections** (accordions for deliverables, geofencing, etc.)

#### **3. GraphQL Query Patterns** 📊
Multiple queries fetch similar data:
- `locations.graphql` - Company → Properties
- `locations-v2.graphql` - Duplicate/variant?
- `locations-new-subscription.graphql` - Subset of locations

**Recommendation:** Consolidate queries, use variables for filtering.

#### **4. JavaScript** 🔧
- Main JS is **minified/bundled** (209KB)
- Module-level JS files are **mostly empty** (logic is inline in HTML)
- **Leaflet library** is bundled (used for maps)

---

## ✨ What You Want to Build

### **Phase 1: User-Location Association & Filtered Data** 
**Goal:** Show users only their associated locations and properties

**Data Flow:**
```
User Login → Get User Email → Query Associated Company
→ Query Properties for Company → Filter Data Based on User Role
```

**New Tables Needed:**
- Active Properties (filtered by user)
- Packages Enrolled (Location → Products)
- Creative Status (Location → Creative Banner/Video properties)
- Geofencing Status (Location → Geofencing Package property)

### **Phase 2: Onboarding Form Improvements**
**Goal:** Two-way portal (submit + upload deliverables)

**Features:**
- File upload capability (deliverables)
- Form validation
- Progress tracking
- Email confirmation

### **Phase 3: Ticket Submission** (Future)
**Goal:** Integrated ticket creation directly from portal

---

## 🎯 Recommended Restructuring

### **Step 1: Improve Code Readability** (Immediate)

#### **Current Problem Example:**
```html
{% set companies = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}{% for company in companies %}{% if company.pricing == 'Old' %}{% do pricingType.update({ useLegacy: true }) %}{% endif %}{% endfor %}
```

#### **After Restructuring:**
```html
{# 
  Determine if company uses legacy pricing model
  This affects which product cards are displayed
#}
{% set companies = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}
{% set pricingType = { useLegacy: false } %}

{% for company in companies %}
  {% if company.pricing == 'Old' %}
    {% do pricingType.update({ useLegacy: true }) %}
  {% endif %}
{% endfor %}
```

### **Step 2: Extract Reusable Partials**

Create `/modules/partials/` folder with common components:

```
partials/
├── _data-table.html        # Reusable data table component
├── _modal.html             # Modal template
├── _form-field.html        # Form input wrapper
├── _expander.html          # Accordion/expander component
├── _location-card.html     # Location summary card
└── _product-enrollment.html # Product enrollment display
```

### **Step 3: Organize CSS by Component**

```
css/
├── base/
│   ├── reset.css           # Normalize, base styles
│   ├── variables.css       # Color, spacing, font variables
│   └── typography.css      # Font styles
├── components/
│   ├── card.css            # Card component
│   ├── button.css          # Button styles
│   ├── table.css           # Table styles
│   ├── form.css            # Form elements
│   ├── modal.css           # Modal styles
│   └── expandable.css      # Accordion/expander
├── layouts/
│   ├── header.css          # Header layout
│   ├── sidebar.css         # Sidebar layout
│   └── main.css            # Main content area
└── utilities/
    ├── spacing.css         # Margin/padding utilities
    ├── visibility.css      # Display utilities
    └── responsive.css      # Mobile/responsive helpers
```

### **Step 4: Consolidate GraphQL Queries**

**Current:** 14 separate .graphql files  
**Better:** ~5 consolidated queries with variables

```
queries/
├── contact-full-data.graphql   # Gets user + company + properties
├── location-detail.graphql     # Single location with subscriptions
├── pricing-products.graphql    # Available products/pricing
├── location-properties.graphql # Location-specific properties
└── user-associations.graphql   # User role/permission data
```

### **Step 5: Create New Module Structure**

```
modules/
├── data-tables.module/         # Reusable data table templates
│   ├── module.html
│   ├── module.css
│   └── fields.json
├── onboarding-form.module/     # Two-way onboarding
│   ├── module.html
│   ├── module.js              # Form handling
│   ├── module.css
│   └── fields.json
├── location-properties.module/ # NEW - Shows properties per location
│   ├── module.html
│   ├── module.css
│   └── fields.json
└── ticket-submission.module/   # NEW - Ticket creation form
    ├── module.html
    ├── module.js
    ├── module.css
    └── fields.json
```

---

## 📝 Documentation Standards

### **Every Module Needs:**

**1. Module Header Comment** (in module.html)
```html
{# 
  Module: Location Detail
  Purpose: Display single location information and associated subscriptions
  
  Data Requirements:
    - dynamic_page_crm_object: The location object being displayed
    - Required properties: id, name, address, status
  
  Features:
    - Location summary card
    - Subscription list table
    - Edit location modal
  
  Author: [Your name]
  Last Updated: [Date]
#}
```

**2. Variable Documentation**
```html
{# Query: Get subscriptions for this location #}
{% set subscriptions = crm_associations(
  dynamic_page_crm_object.id, 
  'USER_DEFINED', 
  31,           {# Association type ID #}
  '',
  'subject,hs_pipeline_stage,monthly_price,campaign_start_date'
) %}
```

**3. README for Complex Modules**
```
location-detail.module/
├── README.md           ← Document non-obvious logic here
├── module.html
├── module.css
├── module.js
├── fields.json
└── meta.json
```

### **Module README Template**
```markdown
# Location Detail Module

## Purpose
Displays all information about a single location/property.

## Data Flow
1. User clicks location from listing
2. Dynamic page loads with `dynamic_page_crm_object` = location
3. Module queries associated subscriptions
4. Displays location info + subscription table

## Key Variables
- `dynamic_page_crm_object`: The location object
- `subscriptions`: List of active subscriptions for this location
- `isAdmin`: Boolean - can user edit this location?

## Editing This Module
- **To add a new property field:** Edit module.html line XXX
- **To change styling:** See module.css
- **To add form validation:** Update module.js

## Troubleshooting
- If subscriptions don't show: Check GraphQL association ID (31)
- If styles break: Check CSS selector specificity
```

---

## 🚀 Implementation Roadmap

### **Week 1: Code Readability**
- [ ] Reformat all HTML to multi-line (max 80 chars per line)
- [ ] Add comments explaining data flow in each module
- [ ] Clean up unused CSS rules
- [ ] Test all pages still work

### **Week 2: Extract Partials**
- [ ] Create `/modules/partials/` folder
- [ ] Extract reusable components (table, modal, form-field)
- [ ] Update modules to use new partials
- [ ] Test functionality

### **Week 3: CSS Reorganization**
- [ ] Split CSS into component files
- [ ] Create variables file for colors/spacing
- [ ] Test responsive design
- [ ] Verify no visual regressions

### **Week 4: New Features**
- [ ] Build location-properties module (shows active properties)
- [ ] Build onboarding-form improvements (file uploads)
- [ ] Create documentation for future developers
- [ ] Deploy & test

---

## 💡 FAQ & Quick Reference

### **Q: How do I add a new property to the location display?**

**A:** 
1. Add property name to GraphQL query (queries/location-detail.graphql)
2. Add field to table in location-detail.module/module.html
3. Style if needed in module.css
4. Test on staging

### **Q: How do I create a new data table (like "Active Packages")?**

**A:** 
1. Copy `pricing.module` → `new-module.module`
2. Update GraphQL query to fetch package data
3. Replace table structure with your data
4. Update styling
5. Add to module in page

### **Q: How do I filter data to show only user's locations?**

**A:**
1. In GraphQL query, add user email as variable
2. Query returns: User → Company → Properties (user already filtered by CRM)
3. In module HTML, iterate with: `{% for property in company.properties %}`

### **Q: Can I see what the developer before me wrote?**

**A:** Check Git history!
```bash
git log --oneline modules/subscription-detail.module/
git show <commit-hash>
```

### **Q: How do I test locally before pushing?**

**A:** 
1. Create a branch: `git checkout -b feature/my-change`
2. Make changes
3. Preview in HubSpot CMS UI on DEV portal
4. Commit: `git add . && git commit -m "Clear description"`
5. Create pull request for review

---

## 🔧 Tools & Resources

### **HubSpot Documentation**
- [HubL Reference](https://developers.hubspot.com/docs/cms/hubl/intro)
- [CRM Associations Function](https://developers.hubspot.com/docs/cms/hubl/functions#crm-associations)
- [GraphQL API](https://developers.hubspot.com/docs/cms/data-fetching)

### **Git Basics for Portal Maintenance**
```bash
# See what changed
git status

# See changes in detail
git diff modules/pricing.module/

# Revert a file if you broke it
git checkout modules/pricing.module/module.html

# See history of a file
git log -p modules/pricing.module/module.html
```

### **Common HubL Patterns**

**Loop through data:**
```html
{% for item in items %}
  {{ item.name }}
{% endfor %}
```

**Conditional display:**
```html
{% if user.isAdmin %}
  <button>Edit</button>
{% endif %}
```

**Format dates:**
```html
{{ timestamp | strftime('%B %d, %Y') }}
```

**Output JSON for debugging:**
```html
<!-- Uncomment to debug -->
<!-- {{ variableName | tojson }} -->
```

---

## ✅ Before You Start

1. **Backup:** Ensure Git is tracking your changes
2. **Test Pages:** List all pages that use each module
3. **Browser Test:** Test in Chrome, Firefox, Safari, Mobile
4. **Document:** Add comments as you refactor
5. **Commit Often:** Small commits are easier to review/revert

---

## 📞 Handoff Checklist for Future Maintainers

When handing off to a team member, ensure they can:

- [ ] Clone the repo and understand folder structure
- [ ] Explain what each module does
- [ ] Add a new field to a module without breaking it
- [ ] Update CSS styling
- [ ] Create a new GraphQL query
- [ ] Test changes on staging
- [ ] Deploy to production
- [ ] Rollback if needed

If they can't do all these, **add more documentation** before they start solo.

---

**Ready to start? Let's begin with Week 1: Code Readability!**
