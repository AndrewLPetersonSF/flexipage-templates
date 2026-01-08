# FlexiPage Templates Repository Setup Guide

## Quick Start

Follow these steps to set up and publish your FlexiPage templates repository to GitHub:

### 1. Initialize Git Repository

```bash
cd flexipage-templates-repo
git init
git add .
git commit -m "Initial commit: FlexiPage templates with RecordPage, AppPage, and HomePage"
```

### 2. Create GitHub Repository

1. Go to [GitHub](https://github.com/new)
2. Create a new repository named `flexipage-templates` (or your preferred name)
3. **Important**: Make it **Public** (the template generator requires public repos)
4. Do **NOT** initialize with README, .gitignore, or license (we already have these)

### 3. Push to GitHub

Replace `YOUR_USERNAME` with your GitHub username:

```bash
git remote add origin https://github.com/YOUR_USERNAME/flexipage-templates.git
git branch -M main
git push -u origin main
```

### 4. Verify Repository

Visit your repository URL:
```
https://github.com/YOUR_USERNAME/flexipage-templates
```

You should see:
```
flexipage-templates/
├── README.md
├── .gitignore
├── RecordPage/
│   └── _flexipage.flexipage-meta.xml
├── AppPage/
│   └── _flexipage.flexipage-meta.xml
└── HomePage/
    └── _flexipage.flexipage-meta.xml
```

## Testing the Templates

### Option 1: Using TypeScript/JavaScript

Create a test file `test-generator.ts`:

```typescript
import { TemplateService, TemplateType } from '@salesforce/templates';

async function testFlexipageGenerator() {
  const templateService = TemplateService.getInstance();

  // Test RecordPage
  const result = await templateService.create(
    TemplateType.Flexipage,
    {
      flexipagename: 'Account_Record_Page',
      template: 'RecordPage',
      flexipageTemplatesGitRepo: 'https://github.com/YOUR_USERNAME/flexipage-templates',
      outputdir: './test-output/flexipages',
      apiversion: '61.0',
      masterlabel: 'Account Record Page',
      description: 'Custom record page for Account object',
      internal: true // Skip directory validation for testing
    }
  );

  console.log('✅ FlexiPage created successfully!');
  console.log(result.rawOutput);
}

testFlexipageGenerator().catch(console.error);
```

Run it:
```bash
cd /Users/andrewpeterson/dev/salesforcedx-templates
npm run build
node -r esbuild-register test-generator.ts
```

### Option 2: Using Salesforce CLI (if integrated)

```bash
sf force:template:create flexipage \
  --flexipagename Account_Record_Page \
  --template RecordPage \
  --flexipage-templates-git-repo https://github.com/YOUR_USERNAME/flexipage-templates \
  --outputdir ./test-output/flexipages \
  --masterlabel "Account Record Page" \
  --internal
```

## Customizing Your Templates

### Step 1: Copy Real FlexiPage XML

1. In Salesforce, create a FlexiPage using Lightning App Builder
2. Deploy or retrieve it to your local project
3. Copy the XML content from `force-app/main/default/flexipages/YourPage.flexipage-meta.xml`

### Step 2: Templatize the Content

Replace static values with EJS variables:

**Before:**
```xml
<label>My Hardcoded Label</label>
<description>A specific description</description>
```

**After:**
```xml
<label><%= masterlabel %></label>
<description><%= description %></description>
```

### Step 3: Update and Push

```bash
cd flexipage-templates-repo
git add RecordPage/_flexipage.flexipage-meta.xml
git commit -m "Update RecordPage template with real content"
git push
```

### Step 4: Force Reload in Generator

When testing, use `forceLoadingRemoteRepo: true` to bypass cache:

```typescript
{
  flexipagename: 'Test_Page',
  template: 'RecordPage',
  flexipageTemplatesGitRepo: 'https://github.com/YOUR_USERNAME/flexipage-templates',
  forceLoadingRemoteRepo: true  // 👈 Forces fresh download
}
```

## Adding New Template Types

### Example: Adding a "DashboardPage" Template

1. Create the directory and file:
```bash
mkdir flexipage-templates-repo/DashboardPage
touch flexipage-templates-repo/DashboardPage/_flexipage.flexipage-meta.xml
```

2. Add your FlexiPage XML content

3. Update `flexipageGenerator.ts` to support it:
```typescript
const validTemplates = ['RecordPage', 'AppPage', 'HomePage', 'DashboardPage'];
```

4. Update the TypeScript interface in `types.ts`:
```typescript
export interface FlexipageOptions extends TemplateOptions {
  template: 'RecordPage' | 'AppPage' | 'HomePage' | 'DashboardPage';
  // ...
}
```

5. Commit and push:
```bash
git add .
git commit -m "Add DashboardPage template"
git push
```

## Repository Structure Options

### Option 1: Flat Structure (Current)
```
flexipage-templates/
├── RecordPage/
├── AppPage/
└── HomePage/
```

Simple and straightforward. Good for small number of templates.

### Option 2: Organized by Object
```
flexipage-templates/
├── Account/
│   ├── RecordPage/
│   └── AppPage/
├── Opportunity/
│   ├── RecordPage/
│   └── AppPage/
└── Generic/
    ├── RecordPage/
    ├── AppPage/
    └── HomePage/
```

Better for object-specific templates. Use subdirectory in URL:
```
https://github.com/YOUR_USERNAME/flexipage-templates/tree/main/Account
```

### Option 3: Versioned
```
flexipage-templates/
├── v1/
│   ├── RecordPage/
│   ├── AppPage/
│   └── HomePage/
└── v2/
    ├── RecordPage/
    ├── AppPage/
    └── HomePage/
```

Good for maintaining multiple versions. Use branches or tags instead:
```
https://github.com/YOUR_USERNAME/flexipage-templates/tree/v1.0.0
```

## Environment Variables (Optional)

For easier testing, set an environment variable:

```bash
# In ~/.zshrc or ~/.bashrc
export FLEXIPAGE_TEMPLATES_REPO="https://github.com/YOUR_USERNAME/flexipage-templates"
```

Then in your code:
```typescript
flexipageTemplatesGitRepo: process.env.FLEXIPAGE_TEMPLATES_REPO || 'default-url'
```

## Troubleshooting

### Repository Not Found
**Error**: Cannot retrieve default branch for custom templates repository

**Solutions**:
- Verify the repository is **public**
- Check the URL is correct (copy from browser)
- Ensure you've pushed commits to GitHub

### Template Not Found
**Error**: FlexiPage template "RecordPage" not found

**Solutions**:
- Check directory name matches exactly (case-sensitive)
- Verify files exist: `ls flexipage-templates-repo/RecordPage/`
- Try with `forceLoadingRemoteRepo: true`

### Cache Issues
**Problem**: Old template content appears after updates

**Solutions**:
```bash
# Clear cache
rm -rf ~/.sfdx/custom-templates/

# Or use forceLoadingRemoteRepo flag
```

### Invalid XML
**Error**: XML parsing errors when deploying

**Solutions**:
- Validate XML syntax: `xmllint --noout yourfile.xml`
- Check EJS syntax is correct
- Ensure all `<% ... %>` tags are properly closed

## Next Steps

1. ✅ Set up repository on GitHub
2. ✅ Test with placeholder content
3. 🔄 Copy real FlexiPage XML from Salesforce
4. 🔄 Templatize the content
5. 🔄 Test end-to-end generation
6. 🔄 Add more templates as needed

## Resources

- [FlexiPage Metadata Reference](https://developer.salesforce.com/docs/atlas.en-us.api_meta.meta/api_meta/meta_flexipage.htm)
- [Lightning App Builder](https://help.salesforce.com/s/articleView?id=sf.lightning_app_builder_overview.htm)
- [EJS Template Syntax](https://ejs.co/)

## Support

Questions? Check:
- Main repository: `salesforcedx-templates`
- Generator code: `src/generators/flexipageGenerator.ts`
- Types: `src/utils/types.ts`

