# YOUWARE Project Guide

## Development Commands
- **Preview locally:** `npx serve` from the project root to host the static `index.html` file for manual testing.
- **Formatting:** Maintain the existing formatting within `index.html`; no automated linting or formatting scripts are configured.

## Architecture Overview
- **Single-page application:** All markup, styling, and logic live inside `index.html`. The interface gates access behind a welcome screen that collects the staff name and ID before revealing the medication tracker dashboard.
- **State management:** The app keeps two in-memory collections, `medications` and `withdrawHistory`, that power the medication table and transaction history modal.
- **Persistence layer:** Data is persisted via the platform-provided `window.storage` API under the key `ot-medications`. Auto-save runs every 30 seconds and on key actions (add/edit/withdraw/return/delete/sign-out) to keep the payload `{ medications, withdrawHistory, lastSave }` up to date.
- **Key UI flows:**
  - **Medication CRUD:** Users can add, edit, and delete entries with optional custom locations. Edits record a history transaction describing field-level changes.
  - **Transactions:** Withdraw and return actions prompt for required details (patient metadata for withdrawals) and push structured entries to `withdrawHistory`.
  - **History modal:** Provides filter controls (medication, patient, date range, type) and supports printing or targeted deletions (by date range or keyword) with password protection.
  - **Printing:** The medication table and history modal both offer print-friendly renderings via dynamically assembled HTML documents.

## Data Model Notes
- **Medications:** Each entry stores `{ name, dosage, pharmacyCode, mainStock, quantity, expirationDate, location }`. Expiration status drives table styling and ordering.
- **Transactions (`withdrawHistory`):** Withdrawal entries capture patient info, quantities before/after, staff details, and timestamps. Returns and edit records follow the same structure with context-specific fields (`changes` string for edits).
- **Security controls:** Destructive actions (deleting medications or history records) require the hard-coded password `De2255` and include attempt limits.
