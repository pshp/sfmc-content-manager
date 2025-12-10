# DMC Content Manager

Small CloudPages app to manage localised content variants in SFMC.

Content is organised by:
- Category
- Country
- Language
- Type
- Version
- ContentVersion (multiple per variant, one **active**)

---

## 1. Master & Config Data Extensions

- **DMC_ContentCategory**
  - List of content categories (e.g. Welcome Email, Reset Password).

- **DMC_ContentDataExtensions**
  - One row per managed **Content Data Extension** (see section 2).
  - Links a CategoryId to a DataExtensionKey
  - Optional label / sort order for UI.

- **DMC_Locale**
  - Master list of allowed locales.
  - Controls which Country/Language variants can exist.

- **DMC_Type**
  - Master list of allowed **Type** values for variants.
  - Example: `Newsletter`, `Localty`, `Non-Loyalty`, etc.

- **DMC_Version**
  - Master list of allowed **Version** values.
  - Example: `Standard`, `Summer`, `Christmas`, etc.

- **DMC_ContentFieldConfig**
  - Field config for each content DE.
  - Drives which fields show on the form and in what order.

---

## 2. Content Data Extensions (per message/asset)

For each content DE you manage via the app:

- Required “identity” fields:
  - CategoryId
  - CountryCode
  - LanguageCode
  - Type
  - Version
  - ContentVersion
  - IsActive (marks the live ContentVersion for that variant)

- Plus any content fields configured in **DMC_ContentFieldConfig**
  (e.g. Subject, Preheader, HTMLBody, TextBody, etc.).

---

## 3. CloudPages

### Page 1 – Variant overview (ID `3637`)

- **Inputs (querystring)**
  - `cat` = CategoryId
  - `de`  = DataExtensionKey

- **What it does**
  - Loads the content DE configured in **DMC_ContentDataExtensions**.
  - Shows all variants (Country / Language / Type / Version) for that DE.
  - Lets you:
    - Add a variant (using allowed locales from **DMC_Locale**, types/versions from **DMC_Type** / **DMC_Version**).
    - Soft-delete a variant.
    - Click a variant to open Page 2.

### Page 2 – Variant detail & versions (ID `3638`)

- **Inputs (querystring/form)**
  - Context: `cat`, `de`, `country`, `lang`, `type`, `version`
  - Versioning: `cv` (ContentVersion), `id` (`_CustomObjectKey`)
  - Action: `action = activate | deactivate | save`

- **What it does**
  - Loads all rows (ContentVersions) for that single variant.
  - Builds the edit form from **DMC_ContentFieldConfig**.
  - Actions:
    - `save` – updates fields on the selected row (`id`).
    - `activate` – sets this row `IsActive = 1` and all other rows for the same variant to `0`.
    - `deactivate` – sets this row `IsActive = 0` (kept as history).

---

## 4. Flow

1. Admin maintains master tables:
   - Categories, content DE mapping, locales, types, versions, field config.
2. User opens **Page 1** (`cat` + `de`) → sees variants.
3. User selects or adds a variant → goes to **Page 2**.
4. User edits fields, creates new ContentVersions, and marks one as active.