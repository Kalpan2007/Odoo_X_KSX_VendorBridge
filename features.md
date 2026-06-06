# Features

This document summarizes the features VendorBridge is expected to deliver, grouped by role, screen, and business capability.

## Product scope

VendorBridge is not a generic CRUD app. It is a workflow system with strict procurement rules.

The main outcome is to make procurement traceable, structured, and auditable.

## Core features

### Identity and access

- Email and password login
- Signup and forgot-password flow
- Session handling
- Role-based access control
- Secure logout and token refresh

### Dashboard and home

- Pending approvals
- Active RFQs
- Recent purchase orders
- Recent invoices
- Analytics cards
- Quick actions for common tasks

### Vendor management

- Register vendors
- Track vendor status
- Store GST and contact details
- Search and filter vendor records
- Support vendor lifecycle control

### RFQ management

- Create RFQs with title, scope, quantity, and attachments
- Assign vendors to an RFQ
- Set deadlines
- Publish and close RFQs
- Enforce workflow rules before publication

### Quotation management

- Vendor quotation submission
- Editable quotations before deadline
- Side-by-side comparison
- Lowest-price highlighting
- Delivery timeline comparison
- Sorting and filtering

### Approval workflow

- Approve or reject quotations
- Capture approval remarks
- Track approval timeline
- Enforce state transitions
- Write audit logs for each decision

### Purchase orders and invoices

- Generate purchase orders from approved quotations
- Generate invoices from purchase orders
- Calculate tax and totals
- Download invoices as PDF
- Print invoices
- Email invoices
- Track document status

### Activity and notifications

- RFQ notifications
- Approval alerts
- Invoice updates
- Activity timeline
- Immutable audit logs

### Reports and analytics

- Vendor performance analytics
- Procurement statistics
- Spending summaries
- Monthly procurement trends
- Exportable reports

## Role-based feature map

| Role | Primary capabilities |
|---|---|
| Procurement Officer | create RFQs, compare quotations, generate purchase orders, generate invoices |
| Vendor | submit quotations, track RFQ status, view purchase orders |
| Manager / Approver | approve or reject requests, monitor workflow progress |
| Admin | manage users, manage vendors, view analytics |

## Screen map

The UI should support these screens and routes:

- Login and signup
- Dashboard
- Vendor management
- RFQ creation and RFQ detail
- Quotation submission and quotation comparison
- Approval workflow
- Purchase order view
- Invoice generation and invoice detail
- Activity logs and notifications
- Reports and analytics

## Feature rules

- A quotation must never be accepted before it is submitted.
- A vendor must only see their own records.
- An approval must create an audit trail.
- An invoice must be traceable back to the purchase order that generated it.
- Reports must use real procurement data.

## Implementation mapping

| Feature group | Backend module | Frontend area |
|---|---|---|
| Authentication | auth | login, forgot-password |
| Users and roles | users | admin management pages |
| Vendors | vendors | vendors pages |
| RFQs | rfq | rfqs pages |
| Quotations | quotations | quotations and compare pages |
| Approvals | approvals | approvals page |
| Purchase orders | purchase-orders | purchase order pages |
| Invoices | invoices | invoices pages |
| Notifications | notifications | notifications page |
| Audit logs | audit-logs | activity page |
| Reports | reports | reports page |
| File uploads | files | attachment upload flows |

## User experience expectations

- Every screen should answer the user's next action clearly.
- Every important state should be visible without opening a separate debug panel.
- Every list should support filtering, sorting, and pagination when the data volume grows.
- Every destructive action should require deliberate confirmation or be blocked by workflow rules.

## Practical success criteria

The feature set is good enough when a procurement officer can complete the following without leaving the app:

1. Create an RFQ.
2. Invite vendors.
3. Review quotations.
4. Complete approval.
5. Generate the purchase order.
6. Generate the invoice.
7. Share or print the invoice.
8. Review the audit trail and reports.

If that path works cleanly, the application is functioning as an ERP instead of as disconnected forms.

## Future feature ideas

These are reasonable next steps after the core workflow is stable:

- richer vendor scorecards,
- advanced report filters,
- email templates for invoice delivery,
- better comparison visualizations,
- and stronger notification preferences.

Those should be treated as enhancements, not replacements for the core workflow.