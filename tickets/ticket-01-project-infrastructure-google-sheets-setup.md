# TICKET #1: Project Infrastructure & Google Sheets Data Model Setup

**Priority:** Critical | **Story Points:** 5 | **Estimated Time:** 4-6 hours

## Overview
Create the foundational Google Sheets data model and initial project infrastructure for the IP Allowlist Manager application.

## Google Sheets Setup

### Spreadsheet Name: `IP-Allowlist-Manager-Database`

Create a new Google Sheets document with exactly this name. This will serve as your system of record.

### Required Sheets (Tabs)

#### 1. Sheet: `IPs` (Main Data Table)
**Column Headers (Row 1):**
- A1: `owner_email`
- B1: `ip` 
- C1: `platform`
- D1: `label`
- E1: `notes`
- F1: `created_at`
- G1: `created_by`
- H1: `updated_at`
- I1: `updated_by`
- J1: `active`

**Data Validation Rules:**
- Column A (`owner_email`): Data validation > Text contains > `@yourcompany.com` (replace with actual domain)
- Column B (`ip`): Custom formula validation for IPv4/CIDR format
- Column C (`platform`): List from range `Platforms!A:A`
- Column J (`active`): List of items: `TRUE,FALSE`

#### 2. Sheet: `Admins`
**Column Headers (Row 1):**
- A1: `email`

**Sample Data:**
- A2: `admin@yourcompany.com`
- A3: `it-admin@yourcompany.com`

#### 3. Sheet: `Platforms`
**Column Headers (Row 1):**
- A1: `platform`
- B1: `description`

**Sample Data:**
- A2: `Redshift`, B2: `Data warehouse access`
- A3: `Metabase`, B3: `Analytics dashboard`
- A4: `VPN`, B4: `Company VPN access`
- A5: `Staging-DB`, B5: `Staging database access`

#### 4. Sheet: `Audit`
**Column Headers (Row 1):**
- A1: `timestamp`
- B1: `actor_email`
- C1: `action`
- D1: `ip`
- E1: `platform`
- F1: `details_json`

## Tasks Checklist

### ✅ Spreadsheet Creation
- [X] Create new Google Sheets: `IP-Allowlist-Manager-Database`
- [X] Create 4 sheets with exact names: `IPs`, `Admins`, `Platforms`, `Audit`
- [X] Add column headers to all sheets as specified
- [X] Format headers (bold, background color, freeze row 1)

### ✅ Data Validation Setup
- [X] Set up email domain validation for `IPs.owner_email`
- [X] Create platform dropdown reference to `Platforms` sheet
- [X] Set up TRUE/FALSE validation for `active` column
- [X] Create custom IPv4/CIDR validation formula for IP column

### ✅ Sheet Protection
- [X] Protect row 1 (headers) in all sheets
- [X] Make `Audit` sheet append-only (protect all existing data)
- [X] Set appropriate edit permissions for protected ranges

### ✅ Conditional Formatting
- [X] Add red background for rows where `active = FALSE`
- [X] Add green left border for recently updated rows (updated within 7 days)
- [X] Alternate row colors for better readability

### ✅ Sample Data
- [X] Add sample admin emails to `Admins` sheet
- [X] Add initial platforms to `Platforms` sheet
- [X] Add 2-3 sample IP entries for testing

### ✅ Sharing & Permissions
- [X] Share spreadsheet with development team
- [X] Set proper edit/view permissions
- [ X] Note the Spreadsheet ID for Apps Script integration

## Deliverables

1. **Google Sheets Database** - Fully configured spreadsheet
2. **Spreadsheet ID** - Document the ID for backend integration
3. **Configuration Documentation** - Record all validation rules and formulas used
4. **Test Data** - Sample records for initial development

## Validation Rules Reference

### IPv4/CIDR Validation Formula
**Requirement:** Create a custom Google Sheets formula that validates IPv4 addresses and CIDR notation. The formula should use REGEXMATCH functions to check for valid IPv4 format (xxx.xxx.xxx.xxx) and CIDR format (xxx.xxx.xxx.xxx/xx) where each octet is between 0-255 and CIDR suffix is between 0-32.

### Email Domain Validation
**Requirement:** Create a Google Sheets formula that validates email addresses are from the company domain. The formula should use REGEXMATCH to ensure emails follow the pattern of valid email format ending with @yourcompany.com (replace with actual domain).

## Definition of Done
- ✅ All 4 sheets created with proper structure
- ✅ Data validation prevents invalid entries
- ✅ Sheet protection configured correctly  
- ✅ Conditional formatting improves readability
- ✅ Sample data exists for development testing
- ✅ Spreadsheet shared with appropriate permissions
- ✅ Spreadsheet ID documented for backend integration

## Next Steps
Once complete, move to Ticket #2 for backend Google Apps Script development.