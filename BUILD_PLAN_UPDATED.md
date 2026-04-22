# ONBOARDING PORTAL BUILD PLAN (UPDATED)
## Based on Your HubSpot Schema Analysis

---

## PROJECT OVERVIEW

You're building a client-facing onboarding portal with two main sections on the locations landing page:

### **Section A: Onboarding Locations (Top)**
Shows all locations currently being onboarded/set up

**Filter Criteria:** Location Status = Any of:
- Onboarding
- Onboarding (Portal)
- In Set Up
- On Hold
- Paused
- Paused for Lack of Payment

**Data to Display:**
| Field | Internal Name | Type | Purpose |
|-------|---|---|---|
| Location Name | name | string | Which property |
| Location Status | location_status | enumeration | Current stage |
| Campaign Start Date | campaign_start_date | date | When it started |
| Geofence Package Price | geofence_package_price | enumeration | What package |
| Creative - Banner | creative___banner_selection | enumeration | Banner enrolled? |
| Creative - Video | creative___video | enumeration | Video enrolled? |
| Account Rep | account_rep__dropdown_ | enumeration | Point of contact |

**Sortable By:**
- Location Name (A-Z)
- Campaign Start Date (Newest/Oldest)
- Location Status
- Account Rep

---

### **Section B: Active Properties (Bottom)**
Shows all locations actively running campaigns

**Filter Criteria:** Location Status = "Active"

**Data to Display:**
| Field | Internal Name | Type | Purpose |
|-------|---|---|---|
| Location Name | name | string | Which property |
| Location Status | location_status | enumeration | Status |
| Campaign Start Date | campaign_start_date | date | When started |
| Geofence Package Price | geofence_package_price | enumeration | Package tier |
| Creative - Banner | creative___banner_selection | enumeration | Banner? |
| Creative - Video | creative___video | enumeration | Video? |
| GeofenceIQ | geofenceiq | enumeration | Competitor tracking? |
| Package | package | enumeration | Overall package |

**Sortable By:**
- Location Name (A-Z)
- Campaign Start Date (Newest/Oldest)
- Package
- Geofence Price

---

## ENHANCED LOCATION DETAILS PAGE

When user clicks on a location, show comprehensive details:

### **Section 1: Basic Info**
- Location Name
- Location Status
- Campaign Start Date
- Account Rep
- Billing Start Date

### **Section 2: Geofencing Package**
- Package Type (Saver/Standard/Premium/Unlimited/Custom)
- Price
- GeofenceIQ Add-on (if applicable)

**REMOVED PER REQUEST:**
- ~~Management Fee~~ (internal only)
- ~~Media Spend~~ (internal only)

### **Section 3: Creative Package**
- Banner (Enrolled? Which type? Price?)
- Video (Enrolled? Which type? Price?)
- Client Provided Creative (Yes/No/Type)
- Creative Rep assigned
- Volume Discount? (Yes/No)

**REMOVED PER REQUEST:**
- ~~Custom Pricing~~ (showing standard options only)

### **Section 4: Creative Links**
Display live creative assets:
- Banner Link (Evergreen)
- Banner Link (Promo)
- Video Link (Evergreen)
- Video Link (Promo)
- Video Folder Link
- Additional Creative Links

### **Section 5: Add-Ons**
- GeofenceIQ (if enrolled)
- Kurie Playbook (if enrolled)
- Service Tier (if applicable)

### **Section 6: Billing Info**
- Billing Method
- Billing Contacts
- Billing Start Month
- Billing Start Date
- Campaign Duration (Continuous / Predetermined End Date)
- Predetermined End Date (if applicable)

---

## GRAPHQL QUERIES NEEDED

### Query 1: Locations by Status (for landing page)
\`\`\`
Get Contact > Associated Company > Associated Locations
Filter by: Location Status (Onboarding/In Setup/On Hold/Paused/Active)
Return: Name, Location_Status, Campaign_Start_Date, Geofence_Package_Price, 
        Creative_Banner, Creative_Video, Account_Rep, Package
\`\`\`

### Query 2: Location Details (for detail page)
\`\`\`
Get Location by ID
Return: ALL properties listed in "Enhanced Location Details Page" section
        EXCLUDE: Management fees, Media spend, Custom pricing details
\`\`\`

---

## FOLDER RESTRUCTURING

Current state:
\`\`\`
/modules (all modules in one folder)
/css (all CSS in root)
/js (all JS in root)
\`\`\`

New structure:
\`\`\`
/modules
  /shared
    /components
      _table-sortable.html (reusable sortable table component)
      _card-status.html (reusable status card)
      _property-display.html (reusable property display)
  /locations
    /onboarding
      locations-onboarding.html (markup)
      locations-onboarding.css (formatted, readable)
      locations-onboarding.js (formatted, readable)
    /active
      locations-active.html
      locations-active.css
      locations-active.js
  /location-details
    location-details.html
    location-details.css
    location-details.js

/css
  /global
    base.css (global resets, typography)
    variables.css (colors, spacing, fonts)
    utilities.css (helper classes)
  /components
    tables.css (table styles - formatted)
    cards.css (card styles - formatted)
    forms.css (form styles - formatted)

/js
  /utils
    table-sorter.js (sortable table logic - reusable)
    data-filter.js (filtering logic)
    api-helpers.js (GraphQL query helpers)
  /modules
    locations.js (locations page logic)
    location-details.js (detail page logic)

/queries
  locations-onboarding.graphql
  locations-active.graphql
  location-details.graphql
\`\`\`

---

## CSS FORMATTING STANDARDS

**BEFORE (one-liner mess):**
\`\`\`css
.table{width:100%;border-collapse:collapse}.table th{background:#f0f0f0;padding:12px;text-align:left;font-weight:600}
\`\`\`

**AFTER (readable, organized):**
\`\`\`css
/* ===========================
   TABLE STYLES
   =========================== */

.table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 2rem;
}

.table th {
  background-color: #f0f0f0;
  padding: 12px;
  text-align: left;
  font-weight: 600;
  border-bottom: 2px solid #ddd;
}

.table td {
  padding: 12px;
  border-bottom: 1px solid #eee;
}

/* Striped rows for readability */
.table tbody tr:nth-child(even) {
  background-color: #fafafa;
}

/* Hover effect for interactivity */
.table tbody tr:hover {
  background-color: #f5f5f5;
}
\`\`\`

---

## JS FORMATTING STANDARDS

**BEFORE (compressed):**
\`\`\`javascript
function sortTable(t,e){var a=document.querySelector(t);if(!a)return;var n=Array.from(a.querySelectorAll("tbody tr"));n.sort((t,e)=>{...});n.forEach(t=>{a.appendChild(t)})}
\`\`\`

**AFTER (readable, documented):**
\`\`\`javascript
/**
 * Sort table by column
 * @param {string} tableSelector - CSS selector for the table
 * @param {number} columnIndex - Index of the column to sort by
 * @param {string} direction - 'asc' or 'desc'
 */
function sortTable(tableSelector, columnIndex, direction = 'asc') {
  // Get the table element
  const table = document.querySelector(tableSelector);
  if (!table) {
    console.error(`Table not found: ${tableSelector}`);
    return;
  }

  // Get all rows from tbody
  const rows = Array.from(table.querySelectorAll('tbody tr'));

  // Sort rows based on column data
  rows.sort((rowA, rowB) => {
    const cellA = rowA.querySelectorAll('td')[columnIndex];
    const cellB = rowB.querySelectorAll('td')[columnIndex];

    const valueA = cellA.textContent.trim();
    const valueB = cellB.textContent.trim();

    // Handle numeric sorting
    if (!isNaN(valueA) && !isNaN(valueB)) {
      return direction === 'asc' 
        ? parseFloat(valueA) - parseFloat(valueB)
        : parseFloat(valueB) - parseFloat(valueA);
    }

    // String sorting
    return direction === 'asc'
      ? valueA.localeCompare(valueB)
      : valueB.localeCompare(valueA);
  });

  // Re-append sorted rows to tbody
  rows.forEach(row => table.appendChild(row));
}
\`\`\`

---

## PROPERTY FIELDS FOR DETAIL PAGE (CLEANED UP)

### Geofencing Section
- geofence_package_price (display price, not fee/spend)
- package (which tier)
- geofenceiq (if enrolled)
- geofenceiq_mrr (cost to show customer)

### Creative Section
- creative___banner_selection (type of banner)
- creative___video (type of video)
- banner___pricing (show price)
- video___pricing (show price)
- client_provided_creative (yes/no)
- creative_rep (who manages it)
- creative_volume_discount_ (yes/no)

**EXCLUDED (internal use only):**
- custom_package_management_fee
- custom_package_media_spend
- custom_banner_pricing
- custom_video_price
- custom_video_pricing

---

## NEXT STEPS

1. ✅ Build GraphQL queries for locations data (without internal fields)
2. ✅ Create reusable table component with sorting
3. ✅ Refactor locations landing page (two sections)
4. ✅ Format CSS files (readable, organized)
5. ✅ Format JS files (documented, reusable)
6. ✅ Build enhanced location details page (no internal fields)
7. ✅ Create build/deploy process
8. ✅ Document everything for team maintenance
