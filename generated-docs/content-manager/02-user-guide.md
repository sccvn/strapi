# Content Manager - User Guide

Complete guide for content editors and administrators using Strapi Content Manager.

**Version**: 5.31.3  
**Audience**: Content Editors, Administrators, Content Managers

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Managing Content](#managing-content)
3. [Working with Drafts](#working-with-drafts)
4. [Localization (i18n)](#localization-i18n)
5. [Media Management](#media-management)
6. [Relations and Components](#relations-and-components)
7. [Advanced Features](#advanced-features)
8. [Tips and Best Practices](#tips-and-best-practices)
9. [Troubleshooting](#troubleshooting)

---

## Getting Started

### Accessing Content Manager

1. **Login** to Strapi Admin Panel at `http://your-domain/admin`
2. Navigate to **Content Manager** in the left sidebar
3. You'll see a list of available **Content Types** grouped by:
   - **Collection Types** (multiple entries: Articles, Products, etc.)
   - **Single Types** (single entry: Homepage, Settings, etc.)

### Understanding the Interface

```
┌─────────────────────────────────────────────────────────────┐
│  Strapi Admin                                    [User Menu] │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  [Content Manager]                                           │
│    ├─ Collection Types                                       │
│    │   ├─ Articles (42)                                      │
│    │   ├─ Products (128)                                     │
│    │   └─ Categories (15)                                    │
│    │                                                          │
│    └─ Single Types                                           │
│        ├─ Homepage                                           │
│        └─ Global Settings                                    │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

## Managing Content

### Creating New Content

#### For Collection Types (Multiple Entries)

1. **Navigate** to Content Manager → Collection Types → [Content Type]
2. Click **"+ Create new entry"** button (top right)
3. **Fill in the fields**:
   - Required fields are marked with an asterisk (*)
   - Follow field validation rules (character limits, formats, etc.)
4. **Save as Draft** or **Publish**
   - Click **"Save"** to save as draft
   - Click **"Publish"** to make it public immediately

**Example: Creating an Article**

```
Title*: "10 Tips for Better Content Management"
Content: [Rich text editor with formatting options]
Author: [Select from dropdown]
Category: [Select from dropdown]
Featured Image: [Upload or select from media library]
SEO:
  ├─ Meta Title: "Content Management Tips"
  └─ Meta Description: "Learn 10 essential tips..."
  
[Save]  [Publish]
```

#### For Single Types (One Entry)

1. **Navigate** to Content Manager → Single Types → [Content Type]
2. **Edit the existing entry** (created automatically)
3. **Save** or **Publish** your changes

---

### Editing Content

1. **Click on an entry** from the list view
2. **Make your changes** in the edit form
3. **Save** to update the draft
4. **Publish** when ready to make changes live

**Auto-save**: Strapi automatically saves your work every few seconds to prevent data loss.

---

### Deleting Content

#### Delete Single Entry

1. **Open the entry** you want to delete
2. Click **"Delete"** button (usually in top right or bottom)
3. **Confirm deletion** in the popup dialog

⚠️ **Warning**: Deletion is permanent and removes all locales/versions.

#### Bulk Delete

1. **Select multiple entries** using checkboxes in list view
2. Click **"Delete"** in the bulk actions bar
3. **Confirm** to delete all selected entries

---

### Searching and Filtering

#### Search

Use the **search bar** at the top of the list view:
```
🔍 [Search articles...]
```

- Searches in configured searchable fields (usually title, name, etc.)
- Real-time results as you type
- Case-insensitive

#### Filters

Click **"Filters"** button to add advanced filters:

**Available Filter Types**:
- **Text**: Contains, equals, starts with, ends with
- **Number**: Equals, greater than, less than
- **Date**: Before, after, between
- **Boolean**: True/false
- **Relation**: Select related items
- **Enum**: Select from predefined values

**Example Filter**:
```
Title contains "Strapi"
AND
Published date is after "2025-01-01"
AND
Author is "John Doe"
```

---

### Sorting

Click on **column headers** in list view to sort:
- First click: Ascending order (A→Z, 0→9, oldest→newest)
- Second click: Descending order (Z→A, 9→0, newest→oldest)
- Third click: Remove sorting

**Default sort**: Usually by creation date (newest first)

---

## Working with Drafts

Strapi uses a **Draft/Publish workflow** to give you control over content publication.

### Understanding Draft vs Published

| State | Description | Visible to Public |
|-------|-------------|-------------------|
| **Draft** | Work in progress, not public | ❌ No |
| **Published** | Live content, public | ✅ Yes |

### Creating a Draft

1. Create or edit content
2. Click **"Save"** instead of "Publish"
3. Content is saved but not published

**Indicator**: Draft entries show a "Draft" badge in the list view.

---

### Publishing Content

#### Publish Single Entry

1. **Open the draft** entry
2. Click **"Publish"** button
3. Content is now live

**Instant Publishing**: Changes are immediately visible to the public.

#### Publish Multiple Entries (Bulk Publish)

1. **Select entries** to publish (checkboxes in list view)
2. Click **"Publish"** in bulk actions bar
3. **Confirm** to publish all selected entries

---

### Unpublishing Content

Remove content from public view without deleting it:

1. **Open the published** entry
2. Click **"Unpublish"** button
3. Content reverts to draft state

**Use Case**: Temporarily hide outdated or incorrect content.

---

### Modifying Published Content

**Important**: When you edit published content, changes are saved to the **draft version**.

**Workflow**:
1. Edit published content
2. Save changes (creates/updates draft)
3. Publish to make changes live

**Preview**: Use the preview feature to see changes before publishing.

---

## Localization (i18n)

Manage content in multiple languages.

### Prerequisites

- **i18n plugin** must be installed and enabled
- **Locales** configured in Settings → Internationalization
- Content type must have **localization enabled**

### Creating Localized Content

#### Method 1: Create New Locale While Editing

1. **Open an entry**
2. Look for the **locale dropdown** at the top
3. Select **"Create new locale"**
4. Choose the **target locale** (e.g., French, Spanish)
5. **Translate** the content
6. **Save** or **Publish**

**Example**:
```
English (en) ▼  →  [+ Create new locale]
                    → French (fr)
                    → Spanish (es)
                    → German (de)
```

#### Method 2: Duplicate and Translate

1. **Open an entry**
2. Click **locale selector**
3. Choose **locale to create**
4. Content is duplicated
5. **Translate** and save

---

### Switching Between Locales

1. **Open an entry**
2. Use the **locale dropdown** to switch
3. Edit the selected locale
4. **Save** changes

**Locale Indicator**: Current locale is shown in the dropdown and highlighted.

---

### Managing Locales

**View all locales** for an entry:
- Look for "Available in X locales" indicator
- Click to see list of available locales
- Click locale to switch to it

**Delete a locale**:
1. Switch to the locale
2. Click "Delete this locale"
3. Confirm deletion

⚠️ **Note**: Deleting a locale does not delete other locales or the main entry.

---

## Media Management

### Uploading Media

#### While Editing Content

1. **Click media field** (Image, Video, File)
2. Choose upload method:
   - **"From computer"**: Upload new file
   - **"From existing"**: Select from Media Library
   - **"From URL"**: Enter URL to external file
3. **Select/upload** file
4. **Confirm** selection

#### Supported Formats

- **Images**: JPG, PNG, GIF, SVG, WebP
- **Videos**: MP4, WebM, OGG
- **Documents**: PDF, DOC, DOCX, XLS, XLSX
- **Other**: ZIP, CSV, JSON, etc.

#### File Size Limits

- Default max: **50 MB** per file
- Configurable in admin settings
- Large files may require server configuration

---

### Media Library

Access the full **Media Library**:
1. Navigate to **Media Library** in the sidebar
2. Browse, search, filter media
3. Upload, edit, delete files
4. Organize with folders

**Features**:
- Drag-and-drop upload
- Bulk upload
- Image preview and editing
- Metadata management (alt text, caption, etc.)

---

### Best Practices for Media

✅ **Do**:
- Use descriptive file names
- Add alt text for accessibility
- Optimize images before upload (compress, resize)
- Use appropriate formats (WebP for web, PNG for transparency)

❌ **Don't**:
- Upload unnecessarily large files
- Use spaces in file names
- Forget alt text for images
- Upload copyrighted material without permission

---

## Relations and Components

### Working with Relations

Relations connect entries across content types (e.g., Article → Author, Product → Category).

#### One-to-One / Many-to-One Relations

**Example**: Article → Author (one article has one author)

1. **Click the relation field**
2. **Search** for the related entry
3. **Select** from dropdown
4. Related entry is connected

**Clear relation**: Click the "X" button next to the selected item.

---

#### One-to-Many / Many-to-Many Relations

**Example**: Article → Categories (one article can have multiple categories)

1. **Click "Add relation"**
2. **Search/browse** available items
3. **Select** items to add
4. **Repeat** to add more
5. **Remove** by clicking "X" on each item

**Reorder**: Drag and drop items to change order (if ordering is enabled).

---

### Working with Components

Components are reusable content blocks (e.g., SEO block, Call-to-Action, Image Gallery).

#### Single Component

1. **Expand the component** section
2. **Fill in the fields** inside the component
3. Component data is saved with the entry

**Example: SEO Component**
```
SEO
├─ Meta Title: "My Article - My Site"
├─ Meta Description: "This is an article about..."
└─ Meta Image: [Upload image]
```

---

#### Repeatable Components

Add multiple instances of the same component.

1. **Click "+ Add new entry"** for the component
2. **Fill in the fields**
3. **Click "+ Add new entry"** again for another instance
4. **Reorder** by dragging
5. **Delete** by clicking trash icon

**Example: FAQ Component**
```
FAQ Items
├─ [1] Question: "What is Strapi?"
│      Answer: "Strapi is a headless CMS..."
├─ [2] Question: "How do I install it?"
│      Answer: "Run npm create strapi-app..."
└─ [+ Add new entry]
```

---

#### Dynamic Zones

Dynamic zones allow mixing different components.

1. **Click "+ Add component"**
2. **Choose component type** from dropdown
3. **Fill in the component**
4. **Add more components** of different types
5. **Reorder** by dragging

**Example: Page Builder Dynamic Zone**
```
Content Blocks
├─ [Hero Section]
│   ├─ Title: "Welcome"
│   └─ Image: [...]
├─ [Text Block]
│   └─ Content: "Lorem ipsum..."
├─ [Image Gallery]
│   ├─ Image 1: [...]
│   └─ Image 2: [...]
└─ [+ Add component]
    ├─ Hero Section
    ├─ Text Block
    ├─ Image Gallery
    ├─ Call to Action
    └─ Video
```

---

## Advanced Features

### Content History

View and restore previous versions of content.

1. **Open an entry**
2. Click **"History"** tab or button
3. **Browse** previous versions
4. **Preview** a version
5. **Restore** to revert to that version

**Limitations**: History retention depends on configuration (default: 30 days, 100 versions).

---

### Content Preview

Preview content before publishing.

1. **Save** your changes
2. Click **"Preview"** button
3. Preview opens in new tab/window
4. **Review** the content as it will appear
5. **Go back** to make changes if needed

**Configuration**: Preview requires frontend URL configuration.

---

### Cloning/Duplicating Entries

Create a copy of an existing entry.

1. **Open the entry** to clone
2. Click **"Clone"** or "Duplicate" button
3. **Edit** the cloned entry
4. **Save** the new entry

**Use Cases**:
- Create similar content quickly
- Create seasonal variations
- Start from a template entry

---

### Bulk Operations

Perform actions on multiple entries at once.

**Available Bulk Actions**:
- ✅ Publish
- ❌ Unpublish
- 🗑️ Delete

**Steps**:
1. **Select entries** using checkboxes
2. **Click bulk action** button in the bar
3. **Confirm** action

**Tip**: Use filters to narrow down entries before bulk operations.

---

## Tips and Best Practices

### Content Creation

✅ **Best Practices**:
1. **Use descriptive titles** that clearly identify the content
2. **Fill in all required fields** before saving
3. **Add metadata** (SEO, tags, etc.) for better organization
4. **Save drafts frequently** to avoid losing work
5. **Preview before publishing** to catch errors
6. **Use consistent naming conventions** for entries

### Organization

✅ **Keep Content Organized**:
1. Use **categories and tags** consistently
2. Create **template entries** for common content types
3. **Archive old content** instead of deleting (use status fields)
4. Use **descriptive file names** for media
5. **Review and clean up** drafts regularly

### Performance

✅ **Optimize Performance**:
1. **Compress images** before upload
2. **Avoid deeply nested** relations
3. **Use pagination** for large datasets
4. **Delete unused drafts** and media
5. **Use appropriate field types** (e.g., Text vs Rich Text)

### Collaboration

✅ **Team Collaboration**:
1. **Communicate changes** in draft notes or comments
2. **Use consistent workflows** (who creates, reviews, publishes)
3. **Assign ownership** (use creator/updater fields)
4. **Set up roles** and permissions appropriately
5. **Review before publishing** (four-eyes principle)

---

## Troubleshooting

### Cannot Create Content

**Symptoms**: "Create new entry" button is disabled or missing.

**Possible Causes**:
- No permission to create
- Content type configuration issue
- Single type already exists

**Solutions**:
1. ✅ Check user role permissions (Settings → Roles)
2. ✅ Verify content type exists and is configured
3. ✅ For single types, edit the existing entry instead

---

### Cannot Publish Content

**Symptoms**: "Publish" button is disabled or results in error.

**Possible Causes**:
- No publish permission
- Required fields missing
- Validation errors

**Solutions**:
1. ✅ Check role permissions for publish action
2. ✅ Fill in all required fields (marked with *)
3. ✅ Check for validation errors (red text below fields)
4. ✅ Ensure related content exists (for required relations)

---

### Content Not Appearing on Frontend

**Symptoms**: Published content doesn't show on the website.

**Possible Causes**:
- Content not published (still in draft)
- Wrong locale selected
- Cache not cleared
- API permissions not set

**Solutions**:
1. ✅ Verify content is **published** (not draft)
2. ✅ Check correct **locale** is selected
3. ✅ Clear frontend cache
4. ✅ Check API permissions (Settings → Roles → Public → Permissions)
5. ✅ Verify frontend is fetching the correct endpoint

---

### Locale Not Available

**Symptoms**: Cannot create or switch to a specific locale.

**Possible Causes**:
- Locale not enabled in settings
- i18n plugin not installed
- Content type localization disabled

**Solutions**:
1. ✅ Enable locale in Settings → Internationalization
2. ✅ Install i18n plugin if missing
3. ✅ Enable localization for content type (Content-Type Builder)
4. ✅ Restart Strapi if changes don't apply

---

### Upload Fails

**Symptoms**: File upload fails with error.

**Possible Causes**:
- File too large
- Unsupported format
- Server configuration issue
- Storage quota exceeded

**Solutions**:
1. ✅ Check file size (compress if needed)
2. ✅ Verify file format is supported
3. ✅ Check server upload limits (PHP, Nginx, etc.)
4. ✅ Verify storage quota and permissions
5. ✅ Try a different file or format

---

### Cannot Save Changes

**Symptoms**: Save button doesn't work or shows error.

**Possible Causes**:
- Validation errors
- Required fields empty
- Network issue
- Session expired

**Solutions**:
1. ✅ Check for validation errors (red text)
2. ✅ Fill in all required fields
3. ✅ Check internet connection
4. ✅ Refresh page and log in again if session expired
5. ✅ Copy content to clipboard before refreshing (to avoid losing work)

---

### Relation Field Empty

**Symptoms**: Cannot see or select items in relation field.

**Possible Causes**:
- No related content exists
- No permission to view related content
- Relation misconfigured

**Solutions**:
1. ✅ Create entries in the related content type first
2. ✅ Check permissions for the related content type
3. ✅ Verify relation is configured correctly (Content-Type Builder)
4. ✅ Check for filters that might hide items

---

## Keyboard Shortcuts

Boost your productivity with keyboard shortcuts:

| Action | Shortcut (Windows/Linux) | Shortcut (Mac) |
|--------|-------------------------|----------------|
| Save | `Ctrl + S` | `⌘ + S` |
| Publish | `Ctrl + P` | `⌘ + P` |
| Search | `Ctrl + K` | `⌘ + K` |
| Close modal | `Esc` | `Esc` |

---

## Getting Help

- 📚 **Documentation**: https://docs.strapi.io
- 💬 **Community Forum**: https://forum.strapi.io
- 💡 **Discord**: https://discord.strapi.io
- 🐛 **GitHub Issues**: https://github.com/strapi/strapi/issues
- 📧 **Support**: support@strapi.io (Enterprise)

---

**Last Updated**: December 12, 2025  
**Version**: 5.31.3  
**Guide For**: Content Editors and Administrators
