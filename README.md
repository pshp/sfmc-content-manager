# Content Management System – Overview

## What this is

A simple **content manager CloudPage** for SFMC that lets marketers:

- Switch between **categories** (Header, Footer, etc.)
- Manage **localized variants** (country + language + type + version)
- Edit content via a **dynamic form**
- Handle **versioning** (`ContentVersion`) and **status** (`Active` / `Inactive`)

---

## Data extensions

### Config / meta

- **CM_Locale**
  - Which **(CountryCode, LanguageCode)** combinations currently have content variants.
  - Fields: `CountryCode`, `LanguageCode`, `CountryName`, `LanguageName`, `IsDefault`, `CreatedDate`, `IsDeleted`.

- **CM_ContentCategory**
  - High-level **sections** in the UI (left navigation).
  - Fields: `CategoryId`, `CategoryName`, `CreatedDate`, `Status`, `IsDeleted`.

- **CM_ContentDataExtension**
  - Maps a **category** to one or more **content DEs**.
  - Fields: `DataExtensionKey`, `CategoryId`, `CreatedDate`, `ModifiedDate`, `IsDeleted`.

- **CM_ContentFieldConfig**
  - For each `DataExtensionKey`, defines which **fields** are editable and in what **order**.
  - Fields: `DataExtensionKey`, `FieldName`, `SortOrder`, `FieldLength`.

### Content

- One DE per content group, e.g. `Header_Translation_DE`.
- Standard keys:
  - `CountryCode`, `LanguageCode`, `Type`, `Version`, `ContentVersion`
- Status / housekeeping:
  - `Status` (`Active` / `Inactive`), `IsLatest`, `CreatedDate`, `ModifiedDate`
- Plus the actual **content fields** (e.g. `Headline`, `Subline`, `imageURL1`, `CTA_URL`, etc.)
  referenced in `CM_ContentFieldConfig`.

---

## How the CloudPage works

1. **User picks a category**
   → Look up its content DE(s) via `CM_ContentDataExtension`.

2. **User picks locale + variant**
   → Valid combos come from `CM_Locale` + existing rows in the content DE.

3. **Form is built dynamically**
   → Fields come from `CM_ContentFieldConfig` for the chosen `DataExtensionKey`.

4. **On Save**
   - Create or update a row in the content DE for the selected
     `CountryCode + LanguageCode + Type + Version + ContentVersion`.
   - Maintain `IsLatest` and `Status` flags for versioning.

5. **On status changes (activate/deactivate)**
   - Update `Status` and `IsLatest` so there is a clear **current active version** per variant.
