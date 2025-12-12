# File Upload User Guide

## Introduction

This guide will help you understand how to upload, manage, and organize files in Strapi using the Media Library. Whether you're managing images, documents, or videos, Strapi's upload system provides powerful features for organizing and optimizing your media assets.

## Table of Contents

- [Getting Started](#getting-started)
- [Uploading Files](#uploading-files)
- [Managing Files](#managing-files)
- [Organizing with Folders](#organizing-with-folders)
- [Editing File Information](#editing-file-information)
- [Using Files in Content](#using-files-in-content)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## Getting Started

### Accessing the Media Library

1. Log in to your Strapi admin panel
2. Click on **Media Library** in the left sidebar
3. You'll see all uploaded files organized in a grid or list view

![Media Library](../images/media-library-overview.png)

### Understanding the Interface

The Media Library interface includes:

- **Upload Button**: Add new files
- **View Toggle**: Switch between grid and list views
- **Search Bar**: Find files by name
- **Filter Button**: Filter by file type or folder
- **Sort Options**: Sort by name, date, or size
- **Folder Navigation**: Browse folder structure

## Uploading Files

### Single File Upload

**Method 1: Drag and Drop**

1. Open the Media Library
2. Drag a file from your computer
3. Drop it into the Media Library area
4. Wait for the upload to complete

**Method 2: Browse Files**

1. Click the **Upload** button
2. Click **Browse files**
3. Select one or more files
4. Click **Open**
5. Files will begin uploading automatically

### Multiple File Upload

You can upload multiple files at once:

1. Click the **Upload** button
2. Select multiple files using:
   - **Ctrl+Click** (Windows/Linux) or **Cmd+Click** (Mac) for individual files
   - **Shift+Click** to select a range
3. Click **Open**
4. All selected files upload simultaneously

### Supported File Types

Strapi supports various file types:

**Images:**
- JPEG (.jpg, .jpeg)
- PNG (.png)
- GIF (.gif)
- WebP (.webp)
- SVG (.svg)
- TIFF (.tiff)

**Documents:**
- PDF (.pdf)
- Word (.doc, .docx)
- Excel (.xls, .xlsx)
- PowerPoint (.ppt, .pptx)
- Text (.txt)

**Audio:**
- MP3 (.mp3)
- WAV (.wav)
- OGG (.ogg)

**Video:**
- MP4 (.mp4)
- WebM (.webm)
- MOV (.mov)
- AVI (.avi)

### File Size Limits

Default upload limits:

- **Single File**: 100MB
- **Total per Request**: 250MB

> **Note**: Your administrator can adjust these limits in the configuration.

## Managing Files

### Viewing File Details

To see detailed information about a file:

1. Click on any file in the Media Library
2. A sidebar opens showing:
   - File preview
   - Name
   - Alternative text
   - Caption
   - File size
   - Dimensions (for images)
   - MIME type
   - Upload date
   - File URL

### Editing Files

**Edit File Information:**

1. Click on a file
2. In the details sidebar, click **Edit**
3. Modify:
   - **Name**: Display name
   - **Alternative Text**: For accessibility and SEO
   - **Caption**: Description text
4. Click **Save**

**Replace File:**

1. Click on a file
2. Click **Replace media**
3. Select a new file
4. The file is replaced while maintaining the same ID

### Downloading Files

To download a file:

1. Click on the file
2. Click the **Download** button
3. The file downloads to your computer

Or:

1. Select one or more files using checkboxes
2. Click **Download** in the toolbar
3. Files download as a ZIP (for multiple files)

### Deleting Files

**Delete Single File:**

1. Click on the file
2. Click the **Delete** button (trash icon)
3. Confirm deletion

**Delete Multiple Files:**

1. Check boxes next to files you want to delete
2. Click **Delete** in the toolbar
3. Confirm deletion

> **Warning**: Deleted files cannot be recovered. Files used in published content will be removed from that content.

## Organizing with Folders

### Creating Folders

1. Click **Create Folder** button
2. Enter folder name (e.g., "Product Images")
3. Optionally select a parent folder
4. Click **Create**

### Folder Structure Example

```
Media Library
├── Brand Assets
│   ├── Logos
│   └── Colors
├── Products
│   ├── Electronics
│   │   ├── Phones
│   │   └── Laptops
│   └── Clothing
├── Blog
│   ├── Featured Images
│   └── Inline Images
└── Documents
    ├── PDFs
    └── Presentations
```

### Moving Files to Folders

**Method 1: Drag and Drop**

1. Click and hold a file
2. Drag to the target folder
3. Release to drop

**Method 2: Cut and Paste**

1. Select file(s)
2. Click **Move to**
3. Select destination folder
4. Click **Move**

### Navigating Folders

- Click folder names to navigate into them
- Use **breadcrumb navigation** at the top to go back
- Click **All media** to return to root

### Folder Permissions

Administrators can set folder permissions:

- **View**: See folder contents
- **Upload**: Add files to folder
- **Edit**: Modify files in folder
- **Delete**: Remove files from folder

## Editing File Information

### Why Edit File Information?

Good file information helps:

- **SEO**: Better search engine rankings
- **Accessibility**: Screen readers for visually impaired users
- **Organization**: Find files easier
- **Context**: Understand file purpose

### Alternative Text (Alt Text)

Alt text describes images for:

- Screen readers
- When images fail to load
- Search engine optimization

**Writing Good Alt Text:**

✅ **Good Examples:**
- "Red Nike running shoes on white background"
- "Company team photo at 2024 annual conference"
- "Bar chart showing sales growth 2020-2024"

❌ **Bad Examples:**
- "image1.jpg" (filename)
- "photo" (too vague)
- "Click here to see our products" (not descriptive)

**Best Practices:**
- Be specific and descriptive
- Keep it under 125 characters
- Don't start with "Image of..." or "Photo of..."
- Include important context
- Skip for decorative images

### Captions

Captions provide additional context:

```
Alternative Text: "Golden retriever puppy playing with a ball"
Caption: "Max, our 3-month-old mascot, enjoying his first day at the office"
```

### File Names

**Naming Conventions:**

✅ **Good Names:**
- `product-laptop-macbook-pro-2024.jpg`
- `blog-featured-how-to-guide.png`
- `document-annual-report-2024.pdf`

❌ **Bad Names:**
- `IMG_1234.jpg`
- `Screen Shot 2024-01-01.png`
- `file (1).pdf`

**Tips:**
- Use lowercase
- Separate words with hyphens
- Be descriptive
- Include version/date if relevant
- Avoid special characters

## Using Files in Content

### Adding Images to Content

When creating or editing content:

1. Click the **Media** field
2. Choose from:
   - **Media Library**: Select existing file
   - **Upload**: Add new file
3. Select the file
4. File is linked to your content

### Media Fields

**Single Media:**
- Allows one file
- Example: Featured image, profile photo

**Multiple Media:**
- Allows multiple files
- Example: Product gallery, blog images

### Responsive Images

Strapi automatically generates responsive image formats:

```javascript
{
  "formats": {
    "thumbnail": {
      "url": "/uploads/thumbnail_image.jpg",
      "width": 245,
      "height": 156
    },
    "small": {
      "url": "/uploads/small_image.jpg",
      "width": 500,
      "height": 319
    },
    "medium": {
      "url": "/uploads/medium_image.jpg",
      "width": 750,
      "height": 478
    },
    "large": {
      "url": "/uploads/large_image.jpg",
      "width": 1000,
      "height": 638
    }
  }
}
```

Use appropriate size for your needs:

- **Thumbnail**: Lists, previews
- **Small**: Mobile devices
- **Medium**: Tablets
- **Large**: Desktop
- **Original**: Full resolution

### File URLs

Accessing uploaded files:

**Local Provider:**
```
http://localhost:1337/uploads/filename.jpg
```

**Cloud Provider (S3, Cloudinary, etc.):**
```
https://cdn.example.com/uploads/filename.jpg
```

## Best Practices

### Organizing Files

1. **Use Descriptive Folders**
   ```
   ✅ Products/Electronics/Laptops
   ❌ Folder1/Subfolder2
   ```

2. **Consistent Naming**
   ```
   ✅ product-laptop-dell-xps-15-2024.jpg
   ✅ product-laptop-hp-spectre-x360-2024.jpg
   ❌ IMG_1234.jpg
   ❌ photo.jpg
   ```

3. **Regular Cleanup**
   - Delete unused files monthly
   - Archive old campaign materials
   - Review and update file information

### Optimizing Images

**Before Uploading:**

1. **Resize Images**
   - Max width: 2000px for most uses
   - Use appropriate dimensions for use case

2. **Compress Images**
   - Use tools like TinyPNG, ImageOptim
   - Balance quality vs. file size
   - Aim for < 200KB per image when possible

3. **Choose Right Format**
   - **JPEG**: Photos, complex images
   - **PNG**: Logos, text, transparency needed
   - **WebP**: Best compression, modern browsers
   - **SVG**: Icons, logos, simple graphics

**File Size Guidelines:**

| Use Case | Recommended Size |
|----------|------------------|
| Thumbnail | < 50KB |
| Blog image | < 200KB |
| Hero image | < 500KB |
| Full resolution | < 2MB |

### Accessibility

1. **Always Add Alt Text**
   - Describe what's in the image
   - Include relevant context
   - Keep it concise

2. **Use Descriptive File Names**
   - Help with SEO
   - Easier to find files
   - Better organization

3. **Add Captions When Helpful**
   - Provide context
   - Credit photographers
   - Add relevant information

### Security

1. **Sensitive Files**
   - Don't upload confidential documents
   - Use private folders if available
   - Check file permissions

2. **File Scanning**
   - Scan files before upload
   - Be cautious with files from unknown sources
   - Verify file extensions match content

3. **Copyright**
   - Only upload files you have rights to use
   - Keep track of licenses
   - Credit sources appropriately

## Troubleshooting

### Upload Fails

**Problem**: Upload doesn't complete

**Solutions**:

1. **Check File Size**
   - Reduce file size if too large
   - Split large uploads

2. **Check File Type**
   - Verify file type is supported
   - Try converting to supported format

3. **Check Connection**
   - Verify internet connection
   - Try again with stable connection

4. **Clear Browser Cache**
   - Clear cache and cookies
   - Try different browser

### Can't Find File

**Solutions**:

1. **Use Search**
   - Search by filename
   - Try partial name

2. **Check Filters**
   - Clear any active filters
   - Check all folders

3. **Check Permissions**
   - Verify you have access to folder
   - Contact administrator

### Image Quality Issues

**Problem**: Uploaded images look blurry or pixelated

**Solutions**:

1. **Upload Higher Resolution**
   - Use original high-res image
   - Don't pre-scale too small

2. **Check Image Format**
   - Use JPEG for photos
   - Use PNG for graphics/screenshots
   - Try WebP for better quality+compression

3. **Disable Auto-Optimization**
   - Contact admin to adjust settings
   - Upload original, let Strapi optimize

### File Appears Multiple Times

**Problem**: Same file shows up multiple times

**Solutions**:

1. **Check Upload Status**
   - Wait for upload to complete fully
   - Don't click upload multiple times

2. **Remove Duplicates**
   - Search for filename
   - Keep one, delete duplicates

### Can't Delete File

**Problem**: Delete button is disabled or fails

**Solutions**:

1. **Check Usage**
   - File may be in use by published content
   - Remove from content first
   - Or use "Replace media" instead

2. **Check Permissions**
   - Verify you have delete permission
   - Contact administrator

3. **Try Refreshing**
   - Refresh page
   - Log out and back in

## Getting Help

If you need additional assistance:

1. **Check Admin Panel Help**
   - Look for ? icons
   - Check inline tooltips

2. **Contact Your Administrator**
   - For permissions issues
   - For upload limit increases
   - For technical problems

3. **Strapi Documentation**
   - Visit [docs.strapi.io](https://docs.strapi.io)
   - Search for specific issues

4. **Community Support**
   - [Strapi Discord](https://discord.strapi.io)
   - [Community Forum](https://forum.strapi.io)

---

**Last Updated**: December 12, 2024  
**Version**: Strapi v5.x  
**For**: End Users
