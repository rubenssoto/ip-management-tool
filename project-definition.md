# IP Allowlist Manager — Definition Document (Sheets + Apps Script)

## 1) Executive Summary

A lightweight internal web app for employees to **self-manage the public IP addresses** that must be allowlisted in company security groups. The system uses **Google Apps Script (GAS)** for the backend, **Google Sheets** as the system of record, and a simple web UI built with **HTML + vanilla JavaScript + Tailwind CSS**. Employees can add/update/remove **only their own IPs**; **admins** can view and edit **all** records. Every change is **audited**. **All timestamps are stored in UTC.**

**Primary outcome:** reduce back-and-forth with IT/SecOps and keep allowlists accurate and auditable while staying simple and low‑cost.

---

## 2) Goals

* Provide a simple, secure self-service to manage allowlisted IPs.
* Enforce **per-user visibility** (employees see only their own entries) and **admin oversight**.
* Maintain a complete **audit log** of changes.
* Associate each IP to a **platform** (e.g., Redshift, Metabase) to support app‑specific allowlists.
* Keep the stack minimal (GAS + Sheets + HTML/JS/Tailwind).

---

## 3) Users & Roles

* **Employee (Default User):**

  * Can create, view, update, and delete **only records owned by their email**.
  * Can choose a **platform** from a controlled list when registering an IP.
* **Admin:**

  * Full visibility of **all** records.
  * Can edit any record, reassign ownership, maintain the list of platforms.

**Identity & Access**

* Identity is the Google Workspace email from `Session.getActiveUser()`.
* Access is restricted to the company domain (web app deployed to the domain).

---

## 4) Core Use Cases

1. **Add my current IP to a platform**: User opens the web app, clicks “Detect my IP,” selects a platform (e.g., Redshift), adds an optional label (e.g., “Home”), and saves.
2. **Update an IP entry**: User edits label, notes, or switches the platform (if needed).
3. **Remove an IP entry**: User deletes an entry that’s no longer needed (or admin deactivates it).
4. **Admin view all**: Admin searches/filters and reviews audit entries.

---

## 5) Functional Requirements

* **R1. Ownership & Visibility**: Users can view and modify only rows where `owner_email == current_user_email`.
* **R2. Admin Capabilities**: Admins can list, create, update, delete all records; can change `owner_email`.
* **R3. CRUD**: Create, read, update, delete IP entries with validation.
* **R4. Platform Selection**: **Single‑select** platform from a canonical list (e.g., Redshift, Metabase). If an IP needs multiple platforms, create one row per `(ip, platform)`.
* **R5. Audit**: Every create/update/delete writes an immutable audit row capturing actor, timestamps (UTC), and before/after snapshots.
* **R6. Search & Sort**: UI provides filter (by IP, label, platform, owner) and sorts by `updated_at` desc.
* **R7. IP Validation (v1)**: Accept **IPv4** and **IPv4/CIDR** (e.g., `203.0.113.5`, `203.0.113.0/24`).
* **R8. Uniqueness / Primary Key**: The **primary key** is the composite `(ip, platform)` with `active=TRUE`. No duplicate active rows with the same pair.
* **R9. Status**: Support a boolean `active` flag; deletes in UI may be hard‑delete, admins may also deactivate in sheet.
* **R10. Concurrency Safety**: Writes are serialized with a document lock to avoid race conditions.


---

## 6) Data Model (Google Sheets)

Create a spreadsheet with these tabs and columns. First row holds headers. Use **Data Validation** where suggested. **All date/time fields store UTC.**

### 6.1 IPs (main table)

| Column        | Type / Example                    | Notes                                                                                 |
| ------------- | --------------------------------- | ------------------------------------------------------------------------------------- |
| `owner_email` | `user@company.com`                | Lowercase; used for per‑user filtering. **Validation:** text contains company domain. |
| `ip`          | `203.0.113.5` or `203.0.113.0/24` | IPv4 or IPv4/CIDR in v1.                                                              |
| `platform`    | `Redshift`                        | **Single value** from **Platforms** list.                                             |
| `label`       | `Home`, `Office`, `Mobile`        | Optional.                                                                             |
| `notes`       | Free text                         | Optional.                                                                             |
| `created_at`  | DateTime (UTC)                    | Auto‑populated by backend.                                                            |
| `created_by`  | `actor@company.com`               | Auto‑populated.                                                                       |
| `updated_at`  | DateTime (UTC)                    | Auto‑populated.                                                                       |
| `updated_by`  | `actor@company.com`               | Auto‑populated.                                                                       |
| `active`      | TRUE/FALSE                        | Default TRUE; admins may toggle.                                                      |

**Primary Key & Constraints**

* **PK:** `(ip, platform)` for rows where `active=TRUE`.
* `platform` must exist in **Platforms**.
* Backend enforces owner‑only edits; admins bypass.

### 6.2 Admins

| Column  | Type / Example      |
| ------- | ------------------- |
| `email` | `admin@company.com` |

> All listed emails are administrators. (Optional: also support a script property `ADMINS` with a comma‑separated list.)

### 6.3 Platforms

| Column                     | Type / Example     | Notes                     |
| -------------------------- | ------------------ | ------------------------- |
| `platform`                 | `Redshift`         | Display name; **unique**. |
| *(optional)* `description` | `Warehouse access` | For admin reference.      |

### 6.4 Audit (append‑only)

| Column         | Type / Example                   | Notes                               |
| -------------- | -------------------------------- | ----------------------------------- |
| `timestamp`    | DateTime (UTC)                   | When the change occurred.           |
| `actor_email`  | `user@company.com`               | Who performed the action.           |
| `action`       | `CREATE` \| `UPDATE` \| `DELETE` | Operation.                          |
| `ip`           | `203.0.113.5`                    | Part of the PK at time of action.   |
| `platform`     | `Redshift`                       | Part of the PK at time of action.   |
| `details_json` | JSON string                      | Before/after snapshot; reason, etc. |

---

## 7) UX & Interaction Requirements

* **Views**:

  * **My IPs** (default): shows only the current user’s records.
  * **All IPs** (admin only): full dataset.
* **CRUD Form**: Fields for IP/CIDR, platform (single‑select), label, notes; owner (admin only).
* **Detect My IP**: Button fetches the client’s current public IP and pre‑fills the form.
* **Search/Filter**: Client‑side text filter over IP/label/platform/owner.
* **Sort**: Default by `updated_at` desc.
* **Styling**: Tailwind CSS for consistent spacing, forms, and tables.
* **Accessibility**: Keyboard navigable; sufficient color contrast.

---

## 8) Validation Rules

* **IPv4** or **IPv4/CIDR** only (prefix 0–32).
* **Composite uniqueness**: No duplicate active `(ip, platform)` rows.
* `owner_email` must match a domain‑allowed pattern.
* `platform` must exist in the **Platforms** list.
* On update, edits must pass the same validation.

---

## 9) Security & Governance

* **Access Control**: Domain‑only access; identity from Google session; server‑side checks on each request.
* **Least Privilege**: Default users limited to their own records; admins only as needed.
* **Audit**: Append‑only audit log across all CRUD events (timestamps in UTC).
* **Sheet Protections**: Protect header rows; optional protected ranges for the Audit sheet to prevent manual edits.
* **Concurrency**: Use a document lock for writes.

---

## 10) Technology Choices

* **Frontend**: HTML + **vanilla JavaScript** + **Tailwind CSS** (served via Apps Script HTMLService as a single‑page UI).
* **Backend**: Google Apps Script services (SpreadsheetApp, HtmlService, PropertiesService, LockService).
* **Identity**: Google Workspace account (domain‑restricted).
* **Storage**: Google Sheets (tabs listed above).
* **Timestamps**: **UTC** for all stored and displayed date/time fields.

**Rationale**: Fast to deliver, minimal maintenance, native auth, appropriate for \~50 employees.
