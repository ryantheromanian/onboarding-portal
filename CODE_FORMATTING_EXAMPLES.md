# Code Formatting & Readability Examples

This document shows BEFORE/AFTER examples for refactoring your modules for better readability.

---

## Example 1: Pricing Module - Formatting

### ❌ BEFORE (Current - Hard to Read)
```html
{% set companies = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}{% set pricingType = { useLegacy: false } %}{% for company in companies %}{% if company.pricing == 'Old' %}{% do pricingType.update({ useLegacy: true }) %}{% endif %}{% endfor %}{# Module-specific options #}{% set mainContent = module.main_content %}
```

### ✅ AFTER (Formatted + Commented)
```html
{# 
  PRICING MODULE - SETUP SECTION
  This section determines which pricing model to display
#}

{# Get user's company associations #}
{% set companies = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}

{# Initialize pricing type - defaults to new pricing model #}
{% set pricingType = {
  useLegacy: false
} %}

{# 
  Check if any company uses legacy pricing
  If so, display legacy pricing cards instead of new ones
#}
{% for company in companies %}
  {% if company.pricing == 'Old' %}
    {% do pricingType.update({ useLegacy: true }) %}
  {% endif %}
{% endfor %}

{# Load module configuration from CMS #}
{% set mainContent = module.main_content %}
{% set productCards = module.product_cards %}
{% set legacyProductCards = module.legacy_product_cards %}
{% set defaultAddons = module.default_add_ons %}
{% set addOnContent = module.add_on_content %}
{% set addOnTables = module.add_on_tables %}
```

---

## Example 2: Location Detail - Macro Documentation

### ❌ BEFORE
```html
{% macro location_detail_table_row(label, value) %}<div class="location-detail__table-row"><span class="location-detail__table-row-label">{{ label }}</span><span class="location-detail__table-row-value">{{ value }}</span></div>{% endmacro %}
```

### ✅ AFTER
```html
{# 
  MACRO: location_detail_table_row
  
  Purpose: Render a single row in the location detail table
  
  Parameters:
    label (string): The field label/title (e.g., "Status", "Address")
    value (string|number): The field value to display
  
  Example Usage:
    {% call location_detail_table_row("Location Status", location.status) %}
  
  Output: Two-column layout with label on left, value on right
#}
{% macro location_detail_table_row(label, value) %}
  <div class="location-detail__table-row">
    <span class="location-detail__table-row-label">
      {{ label }}
    </span>
    <span class="location-detail__table-row-value">
      {{ value }}
    </span>
  </div>
{% endmacro %}
```

---

## Example 3: How to Format Complex Query Results

### ❌ BEFORE
```html
{% set subscriptions = crm_associations(dynamic_page_crm_object.id, 'USER_DEFINED', 31, '', 'subject,hs_pipeline_stage,monthly_price,campaign_start_date').results %}
```

### ✅ AFTER
```html
{# 
  Query: Get all subscriptions/tickets associated with this location
  
  Parameters:
    - dynamic_page_crm_object.id: Current location's HubSpot ID
    - 'USER_DEFINED': Association type (custom in HubSpot)
    - 31: Association type ID (defined in HubSpot settings)
    - Properties: subject, hs_pipeline_stage, monthly_price, campaign_start_date
  
  Note: If subscriptions don't load, verify association ID 31 in HubSpot
#}
{% set subscriptions = crm_associations(
  dynamic_page_crm_object.id,
  'USER_DEFINED',
  31,
  '',
  'subject,hs_pipeline_stage,monthly_price,campaign_start_date'
).results %}
```

---

## Example 4: Conditional Logic - Making It Clear

### ❌ BEFORE
```html
{% if company.pricing == 'Old' %}{% if productCards|length > 0 %}{% for card in productCards %}<div class="card">{% if card.featured %}{{ card.title }} - FEATURED{% else %}{{ card.title }}{% endif %}</div>{% endfor %}{% endif %}{% endif %}
```

### ✅ AFTER
```html
{# Only show product cards for legacy pricing #}
{% if company.pricing == 'Old' %}
  
  {# Only display if cards are configured #}
  {% if productCards|length > 0 %}
    <div class="product-cards-container">
      {% for card in productCards %}
        <div class="card">
          {% if card.featured %}
            <span class="card__badge">FEATURED</span>
          {% endif %}
          <h3 class="card__title">{{ card.title }}</h3>
          {# Add more card content here #}
        </div>
      {% endfor %}
    </div>
  {% else %}
    {# No product cards configured - show message #}
    <p class="message message--info">
      No product cards are configured for this pricing model.
    </p>
  {% endif %}
  
{% endif %}
```

---

## Example 5: Data Table - Before/After Formatting

### ❌ BEFORE (from pricing.module)
```html
<table class="pricing-table"><thead><tr><th>Feature</th><th>Price</th><th>Details</th></tr></thead><tbody>{% for item in items %}<tr><td>{{ item.name }}</td><td>${{ item.price }}</td><td>{{ item.description }}</td></tr>{% endfor %}</tbody></table>
```

### ✅ AFTER
```html
{# 
  PRICING TABLE
  Displays product features, pricing, and details
  This table is generated from pricing module configuration
#}
<div class="table-wrapper">
  <table class="pricing-table">
    
    <!-- Table Header -->
    <thead>
      <tr>
        <th class="pricing-table__header-feature">Feature</th>
        <th class="pricing-table__header-price">Price</th>
        <th class="pricing-table__header-details">Details</th>
      </tr>
    </thead>
    
    <!-- Table Body -->
    <tbody>
      {% for item in items %}
        <tr class="pricing-table__row">
          <td class="pricing-table__cell pricing-table__cell--feature">
            {{ item.name }}
          </td>
          <td class="pricing-table__cell pricing-table__cell--price">
            {% if item.price %}
              ${{ item.price | format_number }}
            {% else %}
              <span class="pricing-table__price--custom">Custom</span>
            {% endif %}
          </td>
          <td class="pricing-table__cell pricing-table__cell--details">
            {{ item.description }}
          </td>
        </tr>
      {% endfor %}
    </tbody>
    
  </table>
</div>
```

---

## Example 6: Form Fields - Readable Structure

### ❌ BEFORE
```html
<input type="text" name="email" placeholder="Email" required />
<input type="tel" name="phone" placeholder="Phone" />
<select name="status"><option>Active</option><option>Inactive</option></select>
```

### ✅ AFTER
```html
{# 
  FORM: User Information
  Fields for collecting user contact information
#}

<div class="form-group">
  <label for="user-email" class="form-label">
    Email Address
    <span class="required-indicator">*</span>
  </label>
  <input 
    id="user-email"
    type="email" 
    name="email" 
    placeholder="user@company.com"
    required 
    class="form-input"
  />
  <small class="form-hint">We'll use this to confirm your onboarding</small>
</div>

<div class="form-group">
  <label for="user-phone" class="form-label">
    Phone Number
  </label>
  <input 
    id="user-phone"
    type="tel" 
    name="phone" 
    placeholder="(555) 123-4567"
    class="form-input"
  />
</div>

<div class="form-group">
  <label for="user-status" class="form-label">
    Account Status
    <span class="required-indicator">*</span>
  </label>
  <select 
    id="user-status"
    name="status"
    required 
    class="form-select"
  >
    <option value="">Select Status</option>
    <option value="active">Active</option>
    <option value="inactive">Inactive</option>
    <option value="pending">Pending Approval</option>
  </select>
</div>
```

---

## Example 7: Adding Comments to Complex Logic

### ❌ BEFORE (subscription-detail.module - line ~50-70)
```html
{% set products = { package: null, addOns: [], all: [] } %}{% for lineItem in lineItems %}{% if lineItem.hs_sku %}{% if 'ADDON' in lineItem.hs_sku %}{% do products.addOns.append(lineItem) %}{% else %}{% do products.package.append(lineItem) %}{% endif %}{% do products.all.append(lineItem) %}{% endif %}{% endfor %}
```

### ✅ AFTER
```html
{# 
  PRODUCT CATEGORIZATION
  Separates line items into main package vs. add-ons
  This helps us display them in different sections
#}
{% set products = {
  package: null,
  addOns: [],
  all: []
} %}

{# Iterate through all line items and categorize them #}
{% for lineItem in lineItems %}
  {% if lineItem.hs_sku %}
    
    {# Check if this is an add-on by looking at SKU #}
    {% if 'ADDON' in lineItem.hs_sku %}
      {# This is an add-on product #}
      {% do products.addOns.append(lineItem) %}
    {% else %}
      {# This is the main package #}
      {% do products.package.append(lineItem) %}
    {% endif %}
    
    {# Always add to "all" list for reference #}
    {% do products.all.append(lineItem) %}
    
  {% endif %}
{% endfor %}
```

---

## Implementation Steps

### **For Each Module, Do This:**

1. **Open the module.html file**
2. **Identify "single-line blocks"** (anything that spans multiple concepts on one line)
3. **Break them apart** with line breaks
4. **Add comments** explaining what each section does
5. **Test in portal** to ensure it still renders correctly
6. **Commit with message** like: `Improve readability: pricing.module formatting + comments`

### **Quick Formatting Rules**

```
❌ DON'T DO THIS:
{% set x = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}

✅ DO THIS:
{# Get user's company associations #}
{% set companies = module.data_query.data.CRM.contact.associations.company_collection__primary.items %}
```

```
❌ DON'T DO THIS:
<div class="card"><h2>{{ item.title }}</h2><p>{{ item.description }}</p></div>

✅ DO THIS:
<div class="card">
  <h2 class="card__title">{{ item.title }}</h2>
  <p class="card__description">{{ item.description }}</p>
</div>
```

```
❌ DON'T DO THIS:
{% if x %}{% for y in z %}{{ y }}{% endfor %}{% endif %}

✅ DO THIS:
{% if x %}
  {% for y in z %}
    {{ y }}
  {% endfor %}
{% endif %}
```

---

## Testing Your Changes

After reformatting a module:

1. **View the page in HubSpot** that uses the module
2. **Check these areas:**
   - Text displays correctly
   - Tables render properly
   - Buttons/links work
   - Styling looks the same
   - Mobile responsive (if applicable)

3. **If something breaks:**
   - Check for syntax errors (missing `%}`, `%}`, `}}`)
   - Verify indentation is consistent
   - Roll back with: `git checkout modules/[module]/module.html`

---

## Pro Tips

- **Use VS Code formatting** - Right-click → "Format Document" after editing
- **Install HubSpot extension** - Better syntax highlighting for HubL
- **Test incrementally** - Format one module, commit it, test, then move to next
- **Save often** - Don't reformat 5 modules, then commit
- **Add a "Last Updated" date** in your module header comments

---

## Which Module Should You Start With?

**Start small to build confidence:**

1. **✅ EASY:** sidebar.module (68 lines, mostly navigation)
2. **✅ EASY:** header.module (73 lines, mostly markup)
3. **✅ MEDIUM:** pricing.module (65 lines, some logic)
4. **⚠️ HARD:** subscription-detail.module (645 lines, complex logic)

---

That's it! You now have the pattern. Go format a module and commit it! 🚀
