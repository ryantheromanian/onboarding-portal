# IMPLEMENTATION GUIDE
## Onboarding Portal Locations Pages Restructuring

---

## 📋 TABLE OF CONTENTS

1. [Overview](#overview)
2. [File Organization](#file-organization)
3. [CSS Formatting & Breakdown](#css-formatting--breakdown)
4. [GraphQL Integration](#graphql-integration)
5. [Module Templates](#module-templates)
6. [JavaScript Structure](#javascript-structure)
7. [Step-by-Step Implementation](#step-by-step-implementation)
8. [Testing Checklist](#testing-checklist)
9. [Future Maintenance](#future-maintenance)

---

## OVERVIEW

You're restructuring your onboarding portal to:
1. **Improve readability** - Break CSS/JS one-liners into organized, documented code
2. **Add new features** - Location property tables (Onboarding vs Active)
3. **Enhance user experience** - Cleaner, customer-facing location details page
4. **Enable maintainability** - Clear folder structure and documentation

---

## FILE ORGANIZATION

### Current Structure (Messy)
```
/modules
  locations.html (all on one line)
  location-details.html (all on one line)
  pricing.html
  subscriptions.html
  ...

/css
  mjw_styles__desktop.css (all one line - 50KB+)
  mjw_styles__mobile.css (all one line)
  
/js
  main.js (209KB - minified)
```

### New Structure (Organized)
```
/modules
  /shared
    /components
      _table-sortable.html (reusable sortable table)
      _status-badge.html (reusable status display)
      _property-row.html (reusable property display)
    
  /locations
    /landing (combined onboarding + active)
      locations.html (main page markup)
      locations.css (all styles - FORMATTED & READABLE)
      locations.js (all JS logic - DOCUMENTED)
    
    /details
      location-details.html (detail page markup)
      location-details.css (detail page styles)
      location-details.js (detail page logic)

/css
  /global
    base.css (resets, typography, general)
    variables.css (colors, fonts, spacing constants)
    utilities.css (helper classes)
  
  /components
    tables.css (FORMATTED, with clear sections)
    status-badges.css (FORMATTED)
    cards.css (FORMATTED)

/js
  /utils
    table-sorter.js (reusable sorting function)
    data-filter.js (filtering logic)
    api-client.js (GraphQL client)
    date-formatter.js (date utilities)
  
  /modules
    locations.js (locations page logic)
    location-details.js (detail page logic)

/queries
  locations-onboarding.graphql
  locations-active.graphql
  location-details.graphql

/docs
  STRUCTURE.md (folder structure explained)
  STYLE_GUIDE.md (CSS naming conventions)
  JS_GUIDE.md (JavaScript patterns)
  MAINTENANCE.md (how to update things)
```

---

## CSS FORMATTING & BREAKDOWN

### The Problem
Your current CSS file is 50KB+ of minified one-liners:
```css
.table{width:100%;border-collapse:collapse}.table th{background:#f0f0f0;padding:12px;text-align:left;font-weight:600}.table td{padding:12px;border-bottom:1px solid #eee}
```

### The Solution: Break into organized sections

**File: `/css/components/tables.css`**

```css
/* ===========================
   TABLE COMPONENT STYLES
   Author: [Your Name]
   Last Updated: 2026-04-22
   =========================== */

/* Import variables for consistency */
@import '../global/variables.css';

/* ========== BASE TABLE STYLES ========== */

.table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 2rem;
  background-color: #fff;
  border-radius: 6px;
  overflow: hidden;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

/* ========== TABLE HEADER STYLES ========== */

.table th {
  background-color: #f0f0f0;
  color: #333;
  padding: 12px 16px;
  text-align: left;
  font-weight: 600;
  font-size: 0.95rem;
  border-bottom: 2px solid #ddd;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

/* Sortable column headers */
.table th.sortable {
  cursor: pointer;
  user-select: none;
  position: relative;
  padding-right: 24px;
}

.table th.sortable:hover {
  background-color: #e8e8e8;
}

/* Sort indicator */
.table th.sortable::after {
  content: '⇅';
  position: absolute;
  right: 8px;
  opacity: 0.4;
  font-size: 0.85rem;
}

.table th.sort-asc::after {
  content: '↑';
  opacity: 1;
  color: #0066cc;
}

.table th.sort-desc::after {
  content: '↓';
  opacity: 1;
  color: #0066cc;
}

/* ========== TABLE BODY STYLES ========== */

.table td {
  padding: 12px 16px;
  border-bottom: 1px solid #eee;
  color: #555;
  font-size: 0.9rem;
}

/* Alternating row colors for readability */
.table tbody tr:nth-child(even) {
  background-color: #fafafa;
}

/* Hover effect */
.table tbody tr:hover {
  background-color: #f5f5f5;
  transition: background-color 0.2s ease;
}

/* Last row - no border */
.table tbody tr:last-child td {
  border-bottom: none;
}

/* ========== STATUS COLUMN ========== */

.table .status-cell {
  font-weight: 500;
}

.table .status-cell.active {
  color: #28a745;
}

.table .status-cell.onboarding {
  color: #ff9800;
}

.table .status-cell.paused {
  color: #dc3545;
}

.table .status-cell.setup {
  color: #0066cc;
}

/* ========== PRICE COLUMN ========== */

.table .price-cell {
  font-weight: 600;
  color: #0066cc;
  font-size: 0.95rem;
}

/* ========== ACTIONS COLUMN ========== */

.table .actions-cell {
  text-align: center;
}

.table .actions-cell a {
  display: inline-block;
  padding: 6px 12px;
  margin: 0 4px;
  background-color: #0066cc;
  color: white;
  text-decoration: none;
  border-radius: 4px;
  font-size: 0.85rem;
  transition: background-color 0.2s ease;
}

.table .actions-cell a:hover {
  background-color: #0052a3;
}

/* ========== RESPONSIVE ========== */

@media (max-width: 768px) {
  .table {
    font-size: 0.85rem;
  }
  
  .table th,
  .table td {
    padding: 8px 12px;
  }
  
  .table th {
    font-size: 0.85rem;
  }
}

@media (max-width: 480px) {
  .table {
    display: block;
    overflow-x: auto;
  }
  
  .table th,
  .table td {
    padding: 6px 10px;
    font-size: 0.8rem;
  }
}
```

---

## GRAPHQL INTEGRATION

### Setup Your API Client

**File: `/js/utils/api-client.js`**

```javascript
/**
 * HubSpot GraphQL API Client
 * Handles all communication with HubSpot CRM data
 */

class HubSpotAPIClient {
  constructor(apiKey, apiEndpoint) {
    this.apiKey = apiKey;
    this.apiEndpoint = apiEndpoint || 'https://api.hubapi.com/crm/v3/graphql';
  }

  /**
   * Execute a GraphQL query
   * @param {string} query - GraphQL query string
   * @param {object} variables - Query variables
   * @returns {Promise} - Query result
   */
  async executeQuery(query, variables = {}) {
    try {
      const response = await fetch(this.apiEndpoint, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${this.apiKey}`,
        },
        body: JSON.stringify({
          query,
          variables,
        }),
      });

      if (!response.ok) {
        throw new Error(`API Error: ${response.status}`);
      }

      const data = await response.json();

      if (data.errors) {
        console.error('GraphQL Errors:', data.errors);
        throw new Error(data.errors[0].message);
      }

      return data.data;
    } catch (error) {
      console.error('Query Execution Error:', error);
      throw error;
    }
  }

  /**
   * Get onboarding locations for a company
   * @param {string} companyId - Company ID
   * @returns {Promise} - Array of onboarding locations
   */
  async getOnboardingLocations(companyId) {
    const query = require('../queries/locations-onboarding.graphql');
    return this.executeQuery(query, { companyId });
  }

  /**
   * Get active locations for a company
   * @param {string} companyId - Company ID
   * @returns {Promise} - Array of active locations
   */
  async getActiveLocations(companyId) {
    const query = require('../queries/locations-active.graphql');
    return this.executeQuery(query, { companyId });
  }

  /**
   * Get detailed info for a specific location
   * @param {string} locationId - Location ID
   * @returns {Promise} - Location details
   */
  async getLocationDetails(locationId) {
    const query = require('../queries/location-details.graphql');
    return this.executeQuery(query, { locationId });
  }
}

// Initialize and export
const apiClient = new HubSpotAPIClient(
  window.HubSpotAPI_KEY, // Set this in your template
  window.HubSpotAPI_ENDPOINT
);

export default apiClient;
```

---

## MODULE TEMPLATES

### Locations Landing Page Template

**File: `/modules/locations/landing/locations.html`**

```html
{% extends "./base-layouts/template.html" %}

{% block body %}
<div class="locations-wrapper">
  
  <!-- Page Header -->
  <div class="page-header">
    <h1>My Properties</h1>
    <p class="page-subtitle">Manage your locations and services</p>
  </div>

  <!-- Section A: Onboarding Locations -->
  <section class="locations-section onboarding-section">
    <div class="section-header">
      <h2 class="section-title">Properties Being Onboarded</h2>
      <p class="section-subtitle">
        These properties are currently in setup, on hold, or paused
      </p>
    </div>
    
    <div class="table-container" id="onboarding-table-container">
      <div class="loading-spinner">Loading...</div>
    </div>
  </section>

  <!-- Section B: Active Properties -->
  <section class="locations-section active-section">
    <div class="section-header">
      <h2 class="section-title">Active Properties</h2>
      <p class="section-subtitle">
        These properties are currently live and running campaigns
      </p>
    </div>
    
    <div class="table-container" id="active-table-container">
      <div class="loading-spinner">Loading...</div>
    </div>
  </section>

</div>
{% endblock %}
```

---

## JAVASCRIPT STRUCTURE

### Locations Page Logic

**File: `/js/modules/locations.js`**

```javascript
/**
 * Locations Page Logic
 * Handles loading, sorting, and filtering of location data
 */

import apiClient from '../utils/api-client.js';
import { sortTableByColumn } from '../utils/table-sorter.js';
import { formatDate } from '../utils/date-formatter.js';

class LocationsPage {
  constructor() {
    this.companyId = window.CURRENT_COMPANY_ID; // Set in template
    this.onboardingLocations = [];
    this.activeLocations = [];
  }

  /**
   * Initialize the page - load data and render
   */
  async init() {
    try {
      await this.loadData();
      this.renderTables();
      this.attachEventListeners();
    } catch (error) {
      this.showError('Failed to load locations data');
      console.error(error);
    }
  }

  /**
   * Load location data from HubSpot API
   */
  async loadData() {
    // Load onboarding locations
    const onboardingData = await apiClient.getOnboardingLocations(this.companyId);
    this.onboardingLocations = onboardingData.locations.edges.map(e => e.node);

    // Load active locations
    const activeData = await apiClient.getActiveLocations(this.companyId);
    this.activeLocations = activeData.locations.edges.map(e => e.node);
  }

  /**
   * Render both tables
   */
  renderTables() {
    this.renderOnboardingTable();
    this.renderActiveTable();
  }

  /**
   * Render onboarding locations table
   */
  renderOnboardingTable() {
    const container = document.getElementById('onboarding-table-container');
    
    if (this.onboardingLocations.length === 0) {
      container.innerHTML = '<p class="no-data">No properties currently onboarding.</p>';
      return;
    }

    const html = `
      <table class="table" id="onboarding-table">
        <thead>
          <tr>
            <th class="sortable" data-column="name">Location Name</th>
            <th class="sortable" data-column="status">Status</th>
            <th class="sortable" data-column="date">Campaign Start Date</th>
            <th>Geofencing Package</th>
            <th>Creative</th>
            <th>Account Rep</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          ${this.onboardingLocations.map(loc => this.renderLocationRow(loc)).join('')}
        </tbody>
      </table>
    `;
    
    container.innerHTML = html;
  }

  /**
   * Render active locations table
   */
  renderActiveTable() {
    const container = document.getElementById('active-table-container');
    
    if (this.activeLocations.length === 0) {
      container.innerHTML = '<p class="no-data">No active properties at this time.</p>';
      return;
    }

    const html = `
      <table class="table" id="active-table">
        <thead>
          <tr>
            <th class="sortable" data-column="name">Location Name</th>
            <th class="sortable" data-column="status">Status</th>
            <th class="sortable" data-column="date">Campaign Start Date</th>
            <th>Package</th>
            <th>Geofencing</th>
            <th>Creative</th>
            <th>Account Rep</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          ${this.activeLocations.map(loc => this.renderActiveLocationRow(loc)).join('')}
        </tbody>
      </table>
    `;
    
    container.innerHTML = html;
  }

  /**
   * Render a row for onboarding location
   */
  renderLocationRow(location) {
    const creative = [];
    if (location.creative___banner_selection !== 'None') creative.push('Banner');
    if (location.creative___video !== 'None') creative.push('Video');
    const creativeText = creative.length > 0 ? creative.join(', ') : 'None';

    return `
      <tr class="location-row" data-location-id="${location.id}">
        <td class="name-cell">${location.name}</td>
        <td class="status-cell ${location.location_status.toLowerCase().replace(/\s+/g, '-')}">
          ${location.location_status}
        </td>
        <td class="date-cell">${formatDate(location.campaign_start_date)}</td>
        <td class="package-cell">${location.geofence_package_price || 'N/A'}</td>
        <td class="creative-cell">${creativeText}</td>
        <td class="rep-cell">${location.account_rep__dropdown_ || 'Unassigned'}</td>
        <td class="actions-cell">
          <a href="/location-details?id=${location.id}" class="btn-view">View Details</a>
        </td>
      </tr>
    `;
  }

  /**
   * Render a row for active location
   */
  renderActiveLocationRow(location) {
    const creative = [];
    if (location.creative___banner_selection !== 'None') creative.push('Banner');
    if (location.creative___video !== 'None') creative.push('Video');
    const creativeText = creative.length > 0 ? creative.join(', ') : 'None';

    return `
      <tr class="location-row" data-location-id="${location.id}">
        <td class="name-cell">${location.name}</td>
        <td class="status-cell active">Active</td>
        <td class="date-cell">${formatDate(location.campaign_start_date)}</td>
        <td class="package-cell">${location.package || 'N/A'}</td>
        <td class="geo-cell">${location.geofenceiq !== 'None' ? 'Yes' : 'No'}</td>
        <td class="creative-cell">${creativeText}</td>
        <td class="rep-cell">${location.account_rep__dropdown_ || 'Unassigned'}</td>
        <td class="actions-cell">
          <a href="/location-details?id=${location.id}" class="btn-view">View Details</a>
        </td>
      </tr>
    `;
  }

  /**
   * Attach event listeners
   */
  attachEventListeners() {
    // Sortable column headers
    document.querySelectorAll('.table th.sortable').forEach(header => {
      header.addEventListener('click', (e) => {
        const column = e.target.dataset.column;
        const table = e.target.closest('table');
        sortTableByColumn(table, column);
      });
    });

    // Row click to view details
    document.querySelectorAll('.location-row').forEach(row => {
      row.addEventListener('click', (e) => {
        if (e.target.classList.contains('btn-view')) return; // Don't trigger on button click
        const locationId = row.dataset.locationId;
        window.location.href = `/location-details?id=${locationId}`;
      });
    });
  }

  /**
   * Show error message
   */
  showError(message) {
    const container = document.querySelector('.locations-wrapper');
    const errorHtml = `
      <div class="error-banner">
        <strong>Error:</strong> ${message}
        <button onclick="location.reload()">Reload Page</button>
      </div>
    `;
    container.insertAdjacentHTML('afterbegin', errorHtml);
  }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', () => {
  const page = new LocationsPage();
  page.init();
});
```

---

## STEP-BY-STEP IMPLEMENTATION

### Phase 1: Folder Restructuring (1-2 hours)
1. Create new folder structure as outlined
2. Move existing CSS/JS files to new locations
3. Update all imports/references

### Phase 2: CSS Formatting (2-3 hours)
1. Break `mjw_styles__desktop.css` into organized sections
2. Create `/css/global/variables.css` for colors, fonts, spacing
3. Create component CSS files (tables, cards, forms)
4. Test for styling regressions

### Phase 3: GraphQL Integration (1-2 hours)
1. Create GraphQL query files
2. Create API client (`api-client.js`)
3. Test queries in HubSpot sandbox
4. Add error handling

### Phase 4: Module Refactoring (3-4 hours)
1. Refactor locations page (split onboarding/active)
2. Create sortable table component
3. Refactor location-details page
4. Add proper error handling

### Phase 5: Testing & Deployment (2-3 hours)
1. Test on desktop/mobile
2. Test sorting, filtering, navigation
3. Performance check
4. Deploy to production

---

## TESTING CHECKLIST

- [ ] All CSS loads correctly (no styling regressions)
- [ ] Tables render with correct data
- [ ] Sorting works on all columns
- [ ] Status badges display correct colors
- [ ] Links navigate to correct location details page
- [ ] Responsive design works on mobile
- [ ] GraphQL queries return correct data
- [ ] Error messages display if API fails
- [ ] Date formatting is consistent
- [ ] Performance is acceptable (<2s load time)

---

## FUTURE MAINTENANCE

### Adding a New Location Property

1. **Add to GraphQL query** - `/queries/location-details.graphql`
2. **Add to HTML template** - Update the relevant section
3. **Update CSS** - Add styling if needed
4. **Update JS** - Update the rendering logic if needed
5. **Document** - Update this guide

### Adding a New Table Column

1. **Update GraphQL query** - Add field to query
2. **Update HTML table header** - Add `<th>` with sortable class if needed
3. **Update rendering logic** - Add `<td>` in renderLocationRow()
4. **Add CSS styling** - Create `.column-name-cell` class
5. **Test sorting** - Verify sortable works

### Reporting Issues

If someone reports an issue:
1. Check the browser console for JavaScript errors
2. Check the network tab for failed API calls
3. Verify HubSpot API is responding
4. Check if property name matches internal field name

---

## DOCUMENTATION FILES TO CREATE

1. **STRUCTURE.md** - Folder structure explained
2. **STYLE_GUIDE.md** - CSS naming conventions
3. **JS_GUIDE.md** - JavaScript patterns and conventions
4. **API_GUIDE.md** - How to add new GraphQL queries
5. **TROUBLESHOOTING.md** - Common issues and fixes

---

## HANDOFF TO TEAM

When you hire someone new:
1. Point them to `/docs/STRUCTURE.md`
2. Have them read `/docs/STYLE_GUIDE.md`
3. Have them make a small change (add a new field to location details)
4. Review their code for consistency

---

## SUCCESS METRICS

✅ Code is readable and maintainable  
✅ CSS files are broken into logical sections  
✅ JavaScript is documented with JSDoc comments  
✅ Anyone can find what they need in 5 minutes  
✅ Anyone can make a change without breaking things  
✅ CS team can see what properties are active vs onboarding  
✅ Customers see customer-facing data only (no internal fees/costs)  

