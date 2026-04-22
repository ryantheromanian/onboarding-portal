# Building Location-Filtered Property Tables

**Purpose:** Answer Danielle's CS team questions automatically  
**Goal:** Show each user only data for their associated locations  
**Status:** Ready to implement

---

## 🎯 Overview

The CS team keeps asking:
- "Which of my properties are active?"
- "What package am I running?"
- "Are we enrolled in creative?"

We'll build **data tables that automatically show each user only their data** by:
1. Using the logged-in user's email
2. Finding their associated company
3. Filtering that company's locations/properties
4. Displaying the relevant property data in a formatted table

---

## 📊 Data Structure You Have

### From HubSpot CRM:
```
Contact (user) 
  ↓ associated to
Company (client)
  ↓ associated to
Property/Location (individual location)
  ↓ has properties like:
    - Location Status (active/inactive)
    - Geofencing Package (yes/no)
    - Creative Banner (yes/no)
    - Creative Video (yes/no)
    - Package Type (Basic/Pro/Enterprise)
```

### What We're Building:
```
User logs in → Portal recognizes their email
→ Queries: User → Company → All Properties
→ Displays table with columns:
   • Location Name
   • Status
   • Geofencing Package
   • Creative Banner
   • Creative Video
   • Package Type
```

---

## 🔧 Technical Implementation

### **Step 1: Create GraphQL Query**

Create new file: `queries/location-properties-summary.graphql`

```graphql
# 
# Query: Location Properties Summary
# Purpose: Get all properties for a user's company with status information
# 
# Parameters:
#   $contactEmail: User's email (from logged-in contact)
#
query LocationPropertiesSummary($contactEmail: String!) {
  CRM {
    contact(uniqueIdentifier: "email", uniqueIdentifierValue: $contactEmail) {
      email
      hs_object_id
      
      # Get the company this contact belongs to
      associations {
        company_collection__primary {
          items {
            hs_object_id
            name
            
            # Get all properties associated with this company
            associations {
              p_properties_collection__property_to_company {
                items {
                  hs_object_id
                  
                  # Basic info
                  property_name
                  property_address
                  property_city
                  property_state
                  
                  # The properties Danielle cares about
                  property_status          # "Active", "Inactive", "Pending"
                  geofencing_package       # Boolean or text: "Active", "Inactive"
                  creative_banner          # Boolean or text
                  creative_video           # Boolean or text
                  package_type             # "Basic", "Pro", "Enterprise"
                  
                  # For reference
                  property_date_opened
                }
              }
            }
          }
        }
      }
    }
  }
}
```

### **Step 2: Create the Module**

Create folder: `modules/location-properties-summary.module/`

**File: module.html**
```html
{# 
  MODULE: Location Properties Summary
  
  Purpose: Display all properties/locations for the user's company
           with their enrollment status for key services
  
  Use Cases:
    - CS team can see which locations are active
    - Users can verify their package type
    - Enrollment status for creative services visible at a glance
  
  Data Source: location-properties-summary.graphql
  
  Author: [Your Name]
  Last Updated: [Date]
#}

{% from '/onboarding-portal/modules/mjtw_partials.html' import
  properties_summary_table
%}

{# 
  SECTION: Properties Summary Header
#}
<div class="module">
  <div class="constrain">
    
    <div class="module__header constrain--7">
      <h1>{{ module.page_title | default('Your Locations & Services') }}</h1>
      <p>{{ module.page_description | default('Below is a summary of all locations associated with your account and their current service enrollments.') }}</p>
    </div>

    {# 
      SECTION: Properties Table
      Display all properties with their status information
    #}
    <div class="properties-summary constrain--8">
      
      {% set company = module.data_query.data.CRM.contact.associations.company_collection__primary.items[0] %}
      {% set properties = company.associations.p_properties_collection__property_to_company.items %}
      
      {% if properties|length > 0 %}
        
        {# Properties table is available #}
        {{ properties_summary_table(properties) }}
        
      {% else %}
        
        {# No properties found #}
        <div class="message message--info">
          <h3>No Locations Found</h3>
          <p>Your account doesn't have any locations yet. Contact support to add your first location.</p>
        </div>
        
      {% endif %}
      
    </div>

  </div>
</div>
```

**File: module.css**
```css
/* 
  Location Properties Summary Module Styles
*/

.properties-summary {
  margin: 2rem 0;
}

/* Properties Table */
.properties-table {
  width: 100%;
  border-collapse: collapse;
  margin: 1.5rem 0;
  background: white;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  overflow: hidden;
}

.properties-table thead {
  background: #f5f5f5;
  border-bottom: 2px solid #e0e0e0;
}

.properties-table th {
  padding: 1rem;
  text-align: left;
  font-weight: 600;
  color: #333;
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.properties-table td {
  padding: 1rem;
  border-bottom: 1px solid #f0f0f0;
  font-size: 0.95rem;
}

.properties-table tbody tr:hover {
  background: #fafafa;
}

/* Status Badges */
.status-badge {
  display: inline-block;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.3px;
}

.status-badge--active {
  background: #e8f5e9;
  color: #2e7d32;
}

.status-badge--inactive {
  background: #ffebee;
  color: #c62828;
}

.status-badge--pending {
  background: #fff3e0;
  color: #e65100;
}

/* Enrollment Status */
.enrollment-status {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.enrollment-status__icon {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.7rem;
}

.enrollment-status__icon--active {
  background: #4caf50;
  color: white;
}

.enrollment-status__icon--inactive {
  background: #e0e0e0;
  color: #9e9e9e;
}

/* Responsive - Stack on mobile */
@media (max-width: 768px) {
  
  .properties-table {
    font-size: 0.9rem;
  }
  
  .properties-table th,
  .properties-table td {
    padding: 0.75rem 0.5rem;
  }
  
  .status-badge {
    padding: 0.2rem 0.5rem;
    font-size: 0.7rem;
  }
  
}
```

**File: fields.json**
```json
[
  {
    "id": "page_title",
    "name": "page_title",
    "label": "Page Title",
    "required": false,
    "locked": false,
    "type": "text",
    "default": "Your Locations & Services"
  },
  {
    "id": "page_description",
    "name": "page_description",
    "label": "Page Description",
    "required": false,
    "locked": false,
    "type": "richtext",
    "default": "Below is a summary of all locations associated with your account and their current service enrollments."
  }
]
```

**File: meta.json**
```json
{
  "label": "Location Properties Summary",
  "description": "Displays all properties/locations for a user with their service enrollment status",
  "icon": "https://cdn2.hubspot.net/hubfs/2832391/CMS%20Hub/images/Icon%20Images/Module%20Icons/table.png",
  "display_conditions": []
}
```

---

### **Step 3: Create Reusable Table Partial**

Update: `modules/mjtw_partials.html`

Add this macro at the end:

```html
{# 
  MACRO: properties_summary_table
  
  Purpose: Render table showing all properties with their service status
  
  Parameters:
    properties (list): Array of property objects from GraphQL
    
  Expected Properties:
    - property_name
    - property_address
    - property_status
    - geofencing_package
    - creative_banner
    - creative_video
    - package_type
    
  Output: Formatted HTML table with status badges
#}
{% macro properties_summary_table(properties) %}
  
  <div class="table-wrapper">
    <table class="properties-table">
      
      <!-- Table Header -->
      <thead>
        <tr>
          <th>Location Name</th>
          <th>Address</th>
          <th>Status</th>
          <th>Package Type</th>
          <th>Geofencing</th>
          <th>Creative Banner</th>
          <th>Creative Video</th>
        </tr>
      </thead>
      
      <!-- Table Body -->
      <tbody>
        {% for property in properties %}
          <tr class="properties-table__row">
            
            <!-- Location Name -->
            <td class="properties-table__cell">
              <strong>{{ property.property_name }}</strong>
            </td>
            
            <!-- Address -->
            <td class="properties-table__cell">
              <small>
                {{ property.property_address }}
                {% if property.property_city %}
                  <br>{{ property.property_city }}, {{ property.property_state }}
                {% endif %}
              </small>
            </td>
            
            <!-- Status -->
            <td class="properties-table__cell">
              {% set status_class = property.property_status|lower|replace(' ', '-') %}
              <span class="status-badge status-badge--{{ status_class }}">
                {{ property.property_status }}
              </span>
            </td>
            
            <!-- Package Type -->
            <td class="properties-table__cell">
              {{ property.package_type | default('N/A') }}
            </td>
            
            <!-- Geofencing Enrollment -->
            <td class="properties-table__cell">
              {% if property.geofencing_package %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--active">✓</span>
                  <span>Active</span>
                </div>
              {% else %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--inactive">–</span>
                  <span>Not Enrolled</span>
                </div>
              {% endif %}
            </td>
            
            <!-- Creative Banner -->
            <td class="properties-table__cell">
              {% if property.creative_banner %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--active">✓</span>
                  <span>Active</span>
                </div>
              {% else %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--inactive">–</span>
                  <span>Not Enrolled</span>
                </div>
              {% endif %}
            </td>
            
            <!-- Creative Video -->
            <td class="properties-table__cell">
              {% if property.creative_video %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--active">✓</span>
                  <span>Active</span>
                </div>
              {% else %}
                <div class="enrollment-status">
                  <span class="enrollment-status__icon enrollment-status__icon--inactive">–</span>
                  <span>Not Enrolled</span>
                </div>
              {% endif %}
            </td>
            
          </tr>
        {% endfor %}
      </tbody>
      
    </table>
  </div>
  
{% endmacro %}
```

---

## 📝 How Filtering Works

### **User-Location Association Flow**

```
1. User logs into portal
   ↓
2. Contact email is captured: {{ contact.email }}
   ↓
3. GraphQL query: location-properties-summary.graphql
   - Takes: $contactEmail = "user@company.com"
   - HubSpot automatically filters to:
     - Contact with that email
     - Company associated with that contact
     - All Properties associated with that company
   ↓
4. In module.html, we access:
   {% set company = module.data_query.data.CRM.contact.associations.company_collection__primary.items[0] %}
   {% set properties = company.associations.p_properties_collection__property_to_company.items %}
   ↓
5. Loop through properties, display in table
   - Each user ONLY sees their company's properties
   - No way for them to see other companies' data
```

### **Security Note**
✅ This is secure because:
- HubSpot handles the authentication
- GraphQL respects HubSpot CRM permissions
- Each contact email is unique
- They can only query data associated with their company

---

## 🧪 Testing Your Implementation

### **Test Checklist:**

1. **✅ Login as different users**
   - User A logs in → sees Company A's properties
   - User B logs in → sees Company B's properties
   - No data leakage between companies

2. **✅ Verify table displays correctly**
   - Headers visible
   - All columns show
   - Data populates properly
   - Status badges display correctly

3. **✅ Check responsive design**
   - Desktop: Full table view
   - Tablet: Table readable
   - Mobile: Consider horizontal scroll or card layout

4. **✅ Verify data accuracy**
   - Status matches what's in CRM
   - Package type is correct
   - Enrollment statuses are accurate

5. **✅ Test edge cases**
   - User with 0 properties → Shows helpful message
   - User with 1 property → Works
   - User with 50 properties → Table loads, no performance issues

---

## 🚀 Expansion Ideas

Once this works, you can build similar tables for:

### **Active Subscriptions Table**
```
Query: Get user → company → all subscriptions
Columns:
  - Subscription Name
  - Start Date
  - Renewal Date
  - Status
  - Monthly Price
```

### **Deliverables Tracker Table**
```
Query: Get user → company → all tickets (as deliverables)
Columns:
  - Project Name
  - Task
  - Due Date
  - Status (not started / in progress / complete)
  - Assigned To
```

### **Support Tickets Table**
```
Query: Get user → company → all support tickets
Columns:
  - Ticket ID
  - Subject
  - Status
  - Created Date
  - Last Updated
  - Priority
```

---

## 📚 Reference

### **HubL Functions Used**
- `module.data_query.data` - Access GraphQL query results
- `associations.field_collection__association` - Navigate relationships
- `.items` - Array of results
- `|default('fallback')` - Show fallback if empty
- `|lower`, `|replace()` - String filters

### **Files You'll Need to Update/Create**
```
✅ queries/location-properties-summary.graphql (NEW)
✅ modules/location-properties-summary.module/ (NEW)
   ├── module.html
   ├── module.css
   ├── fields.json
   └── meta.json
✅ modules/mjtw_partials.html (UPDATE - add macro)
```

---

## 🎬 Next Steps

1. **Create the GraphQL query file** (location-properties-summary.graphql)
2. **Create the module folder** and files
3. **Test the GraphQL query** in HubSpot GraphQL explorer
4. **Add module to a test page** in your portal
5. **Test as different users** to verify filtering works
6. **Refine styling** based on your design
7. **Deploy to production**

---

**Need help?** Check the RESTRUCTURING_GUIDE.md for Git commands and testing procedures!
