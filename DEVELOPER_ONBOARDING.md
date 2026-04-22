# Quick Start Checklist for Next Developer

**Use this when someone needs to take over this project.**

---

## 📦 What You're Inheriting

A **HubSpot CMS portal** built with **HubL + HTML + CSS + JavaScript** that:
- Displays customer onboarding information
- Shows subscription/product details
- Manages location information
- Soon: Will allow file uploads and ticket submission

**Repository:** https://github.com/ryantheromanian/onboarding-portal  
**Tech Stack:** HubL, HTML5, CSS3, JavaScript (ES6+), GraphQL  
**No dependencies needed** - works directly in HubSpot CMS

---

## 🎯 Before Your First Day

### **Required Setup (1 hour)**
- [ ] Access to HubSpot account (developer/admin)
- [ ] Git access to repository
- [ ] Text editor installed (VS Code recommended)
- [ ] VS Code HubSpot extension installed
- [ ] Read `RESTRUCTURING_GUIDE.md` (20 min)

### **Orientation (1-2 hours)**
- [ ] Clone the repo: `git clone https://github.com/ryantheromanian/onboarding-portal.git`
- [ ] Review `README.md` in the repo
- [ ] Review `RESTRUCTURING_GUIDE.md` for architecture overview
- [ ] Visit the portal in HubSpot and browse a few pages
- [ ] Open 2-3 module files to understand structure
- [ ] Ask questions about what confuses you

---

## 🗂️ Folder Structure Quick Reference

```
modules/              ← Where the pages/components live
├── _MJTW_BASE.module
├── dashboard.module
├── pricing.module
├── location-detail.module
└── (12 total modules)

Each module has 5 files:
  module.html       ← The display template (edit here)
  module.css        ← Styling (edit here)
  module.js         ← Interactivity (usually empty, edit if needed)
  fields.json       ← CMS UI fields (edit if adding options)
  meta.json         ← Module metadata (rarely edit)

queries/              ← GraphQL data queries
├── locations.graphql
├── pricing.graphql
└── (14 total)

templates/            ← Page layouts
css/                  ← Global & component styles
js/                   ← Global JavaScript
fonts/, images/       ← Assets
```

---

## 👨‍💻 Common Tasks You'll Do

### **Add a New Column to a Data Table**

**Example:** Add "Enrollment Date" to properties table

1. Open `queries/location-properties-summary.graphql`
2. Add the field name to the query:
   ```graphql
   property_enrollment_date    ← Add this line
   ```
3. Open `modules/mjtw_partials.html`
4. Find the `properties_summary_table` macro
5. Add a `<th>` header and `<td>` cell in the table
6. Test in portal

### **Update Styling**

**Example:** Change button colors

1. Open `css/mjtw_styles--desktop.css`
2. Find the CSS class (search for "button" or "cta-btn")
3. Change the color values
4. Verify on desktop
5. Check `css/mjtw_styles--mobile.css` too

### **Add a New Page/Module**

1. Duplicate an existing module folder (e.g., copy `pricing.module` → `new-feature.module`)
2. Edit `module.html` with your content
3. Update `fields.json` if needed
4. Create matching GraphQL query if needed
5. Test on a staging page first
6. Commit to Git

### **Fix a Bug**

1. Identify which page/module has the issue
2. Open the module file
3. Check module.html, module.css, module.js
4. Make a test change
5. Reload the page in HubSpot
6. Once fixed, commit with clear message

---

## 🔍 Understanding HubL

**HubL** is Jinja2-like templating. Quick examples:

```html
<!-- Variables -->
{{ variable_name }}

<!-- Conditionals -->
{% if condition %}
  Show this
{% endif %}

<!-- Loops -->
{% for item in items %}
  {{ item.name }}
{% endfor %}

<!-- Comments -->
{# This won't show in output #}

<!-- Setting variables -->
{% set my_var = "value" %}

<!-- Filters (transformations) -->
{{ date_string | strftime('%B %d, %Y') }}
```

More: https://developers.hubspot.com/docs/cms/hubl/intro

---

## 🧪 Testing Checklist

**Before committing code:**

- [ ] Viewed the page in HubSpot that uses your module
- [ ] Checked on desktop
- [ ] Checked on mobile (if applicable)
- [ ] Tested all interactive elements (buttons, links, forms)
- [ ] No console errors (F12 → Console tab)
- [ ] Data displays correctly
- [ ] Styling looks right
- [ ] Compared with design/requirements

---

## 📝 Git Workflow

**Make a change:**
```bash
# 1. Create a new branch
git checkout -b feature/your-feature-name

# 2. Make your changes
# 3. View what you changed
git status

# 4. Commit
git add .
git commit -m "Clear description of what you changed"

# 5. Push to GitHub
git push origin feature/your-feature-name

# 6. Create Pull Request on GitHub (ask for review)
```

**If you mess up:**
```bash
# Undo a file
git checkout modules/pricing.module/module.html

# See changes in a file
git diff modules/pricing.module/module.html

# See commit history
git log -p modules/pricing.module/
```

---

## 🆘 When You're Stuck

### **Module isn't displaying**
1. Check for syntax errors (missing `%}`, `}}`)
2. Verify indentation is consistent
3. Check browser console (F12)
4. Revert the file: `git checkout [filepath]`

### **Data isn't showing**
1. Check GraphQL query returns data (test in HubSpot GraphQL explorer)
2. Verify variable name matches: `{{ variable_name }}`
3. Check for typos in property names
4. Use `{{ variable | tojson }}` to debug

### **Styling looks wrong**
1. Inspect element (F12 → Inspector)
2. Check which CSS file has the rule
3. Look for specificity issues
4. Test on mobile too

### **I need to understand a module**
1. Read the top comments in module.html
2. Check `RESTRUCTURING_GUIDE.md` for patterns
3. Look at similar modules for comparison
4. Check `CODE_FORMATTING_EXAMPLES.md` for before/after

---

## 📚 Documentation Files

**In the repository, read these (in order):**

1. **README.md** - Project overview (if exists)
2. **RESTRUCTURING_GUIDE.md** - Architecture & standards
3. **CODE_FORMATTING_EXAMPLES.md** - Before/after examples
4. **BUILDING_PROPERTY_TABLES.md** - How to build data tables

---

## 🚀 Your First Week

### **Day 1:** Get oriented
- [ ] Set up access and tools
- [ ] Read the documentation
- [ ] Browse a few pages
- [ ] Understand folder structure

### **Day 2-3:** Make a small change
- [ ] Pick an easy module (sidebar or header)
- [ ] Change some text or styling
- [ ] Create a branch, commit, test
- [ ] Get comfortable with the workflow

### **Day 4-5:** Bigger feature
- [ ] Work on something from the team's backlog
- [ ] Ask questions
- [ ] Document what you learn
- [ ] Update this checklist for the next person

---

## 💡 Things to Remember

✅ **DO:**
- Test your changes in the portal before committing
- Write clear commit messages
- Ask questions if confused
- Add comments to complex code
- Break changes into small commits
- Update this checklist if you learn something

❌ **DON'T:**
- Edit files directly in HubSpot (use Git, pull to HubSpot)
- Commit without testing
- Push to `main` without a review
- Delete files unless you know what they do
- Assume property names - verify in HubSpot
- Ignore browser console errors

---

## 📞 Key Contacts & Resources

**HubSpot Support**
- https://docs.hubspot.com/
- https://developers.hubspot.com/docs/cms

**HubL Documentation**
- https://developers.hubspot.com/docs/cms/hubl/intro

**GraphQL Reference**
- https://developers.hubspot.com/docs/cms/data-fetching

**Your Team:**
- Ryan (original developer) - Ask about design decisions
- Danielle (CS team) - Ask about user needs
- Developer team - For code review & pair programming

---

## 🎓 Skill Progression

**Level 1 - Novice (Week 1-2)**
- [ ] Can change text/styling in existing modules
- [ ] Understand folder structure
- [ ] Can create a branch and commit

**Level 2 - Intermediate (Week 3-4)**
- [ ] Can duplicate and modify modules
- [ ] Understand HubL basics
- [ ] Can debug simple issues

**Level 3 - Advanced (Month 2+)**
- [ ] Can write new GraphQL queries
- [ ] Build new modules from scratch
- [ ] Mentor next person

---

## 📋 Handoff Checklist

**When YOU hand this off to the next person:**

- [ ] Update this checklist with new learnings
- [ ] Add/remove documentation as needed
- [ ] List any current known issues
- [ ] Note any in-progress features
- [ ] Update contact information
- [ ] Add new common tasks you figured out
- [ ] Mark anything as "deprecated" that's no longer used

---

## ✨ Last Notes

This codebase is **well-structured** and **easy to work with** once you understand the patterns. Take time to read and understand rather than just jumping in.

The previous developer (Ryan) did a good job organizing modules and queries. Your job is to:
1. **Maintain** that organization
2. **Improve** documentation
3. **Add** new features cleanly
4. **Leave it better** than you found it

**You've got this!** 🚀

---

**Questions? Check the documentation first, then ask your team.**
