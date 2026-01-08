# FlexiPage Templates Repository

This repository contains FlexiPage templates for use with the Salesforce DX Templates library.

## Repository Structure

```
flexipage-templates/
├── README.md (this file)
├── RecordPage/
│   └── _flexipage.flexipage-meta.xml
├── AppPage/
│   └── _flexipage.flexipage-meta.xml
└── HomePage/
    └── _flexipage.flexipage-meta.xml
```

## Usage

### With Salesforce DX Templates

```typescript
import { TemplateService, TemplateType } from '@salesforce/templates';

const templateService = TemplateService.getInstance();

await templateService.create(
  TemplateType.Flexipage,
  {
    flexipagename: 'Account_Record_Page',
    template: 'RecordPage',
    flexipageTemplatesGitRepo: 'https://github.com/YOUR_USERNAME/flexipage-templates',
    outputdir: './force-app/main/default/flexipages',
    apiversion: '61.0',
    masterlabel: 'Account Record Page',
    description: 'Custom record page for Account object'
  }
);
```

### CLI Usage

```bash
sf force:template:create flexipage \
  --flexipagename Account_Record_Page \
  --template RecordPage \
  --flexipage-templates-git-repo https://github.com/YOUR_USERNAME/flexipage-templates \
  --outputdir force-app/main/default/flexipages \
  --masterlabel "Account Record Page"
```

## Available Templates

### RecordPage
Standard record page template with a three-column layout.

**Best for:**
- Object detail pages
- Custom record layouts
- Related lists and highlights panels

**Usage:**
```typescript
{
  template: 'RecordPage',
  flexipagename: 'Custom_Account_Page'
}
```

### AppPage
Application page template with flexible regions.

**Best for:**
- Utility pages
- Dashboard pages
- Custom app screens

**Usage:**
```typescript
{
  template: 'AppPage',
  flexipagename: 'Sales_Dashboard'
}
```

### HomePage
Home page template with default components.

**Best for:**
- Custom home pages
- Landing pages
- Overview screens

**Usage:**
```typescript
{
  template: 'HomePage',
  flexipagename: 'Sales_Home'
}
```

## Template Variables

All templates support the following EJS variables:

- `flexipagename` - The name of the FlexiPage
- `masterlabel` - The display label (defaults to flexipagename)
- `description` - The page description
- `apiVersion` - The Salesforce API version
- `template` - The template type (RecordPage, AppPage, or HomePage)

## Customizing Templates

### Adding Components

Edit the `.flexipage-meta.xml` files to add Lightning components:

```xml
<flexiPageRegions>
  <name>region1</name>
  <type>Region</type>
  <itemInstances>
    <componentInstance>
      <componentName>force:recordDetailShortcut</componentName>
      <identifier>force_recordDetailShortcut</identifier>
    </componentInstance>
  </itemInstances>
</flexiPageRegions>
```

### Creating New Templates

1. Create a new directory with the template name
2. Add a `_flexipage.flexipage-meta.xml` file
3. Update the main README with documentation
4. Commit and push

## Template Customization Tips

### 1. Use Template Variables

```xml
<label><%= masterlabel %></label>
<description><%= description %></description>
```

### 2. Conditional Content

```xml
<% if (template === 'RecordPage') { %>
  <!-- Record-specific components -->
<% } %>
```

### 3. Dynamic Component Names

```xml
<componentName>force:record<%= componentType %></componentName>
```

## Contributing

To contribute new templates:

1. Fork this repository
2. Create a new branch for your template
3. Add your template directory with documentation
4. Submit a pull request

## License

BSD 3-Clause License - See LICENSE file

## Resources

- [FlexiPage Metadata Documentation](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_flexipage.htm)
- [Lightning App Builder Guide](https://help.salesforce.com/s/articleView?id=sf.lightning_app_builder_overview.htm)
- [Salesforce DX Templates](https://github.com/forcedotcom/salesforcedx-templates)

