# 05 — UI/UX Design Specification

## 1. Document Control

*   **Document Title:** UI/UX Design Specification
*   **File:** `docs/05-UI-UX-DESIGN-SPECIFICATION.md`
*   **Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
*   **Version:** 1.0.2
*   **Status:** DRAFT — UI/UX REVIEW
*   **Author:** AI Engineering Agent
*   **Date:** 2026-09-28
*   **Source Baselines:**
    *   `docs/00-PROJECT-DEFINITION.md` (v0.3.0, BASELINE APPROVED)
    *   `docs/01-PRD.md` (v1.1.0, APPROVED)
    *   `docs/02-FRD.md` (v1.0.1, APPROVED)
    *   `docs/03-TRD.md` (v1.0.1, APPROVED)
    *   `docs/04-SYSTEM-ARCHITECTURE.md` (v1.0.3, APPROVED/FROZEN)

---

## 2. Purpose

This comprehensive UI/UX Design Specification defines the visual, interaction, information-architecture, usability, accessibility, and design-system requirements for the Client application. It dictates what the product should look like, how users navigate it, how information is presented, and how important workflows should feel across all authenticated roles and the public citizen portal. It is a **design specification**, not an implementation plan.

## 3. Source of Truth

This specification adheres strictly to the approved project baselines (Project Definition → PRD → FRD → TRD → System Architecture). UI/UX requirements defined here respect the approved functional boundaries, human-in-the-loop AI processes, hybrid blockchain transaction models, and role-based data visibility. Any design decisions not explicitly defined in upstream documents are labeled as **[UI/UX Proposal]** or **[Design Recommendation]**. 

## 4. Important Architectural Terminology

To maintain consistency across documentation and implementation, the following architectural terms are used:
*   **Client:** React + Vite application (handles UI, routing, state, and presentation).
*   **Server:** Python + FastAPI application (handles API, business logic, background tasks, relayer, indexing).
*   **PostgreSQL:** Operational/queryable application database.
*   **Blockchain:** Tamper-evident audit and lifecycle anchoring layer (Hardhat).
*   **IPFS / Pinata:** Off-chain evidence/document storage.
*   **AI/ML:** In-process Python component inside the Server.
*   **JWT Authentication:** Application authentication and authorization.
*   **Browser Wallet (MetaMask):** Used *only* for specifically approved browser-wallet blockchain operations (Contractor request, Auditor finding).
*   **Server Relayer:** Used for approved administrative/system blockchain operations (Project creation, Officer verification, Admin approval, Completion, AI records).

## 5. Design Goal

The project is a college-level functional prototype, but the UI must look polished, modern, and highly credible. The visual direction combines public-sector transparency, financial/project management, audit compliance, modern SaaS dashboards, trustworthy data visualization, and responsible AI presentation.

**The result must communicate:** Trust, Transparency, Accountability, Evidence, Progress, Financial clarity, Responsible AI, and Auditability.

**Avoid:** Generic college dashboard aesthetics, basic Bootstrap admin panels, neon crypto trading aesthetics, overly futuristic "sci-fi AI" interfaces, dated government website templates, or unnecessarily complex enterprise bloatware.

## 6. Design Principles

1.  **Clarity over decoration:** Visual elements exist to explain data, not to distract. Every border, color, and shadow must serve an informational purpose.
2.  **Data-first presentation:** Dashboards prioritize actionable insights, overdue alerts, and risk flags over empty visual real estate.
3.  **Trust and transparency:** Blockchain and audit trails are presented cleanly, emphasizing immutability without overwhelming the user with cryptography.
4.  **Role-specific experiences:** The Client dynamically adapts navigation, actions, and data density to the authenticated user's exact needs (e.g., Contractor vs. Auditor).
5.  **Human-in-the-loop AI:** AI outputs are always framed as *signals, flags, or anomalies* requiring investigation—never as absolute facts or proof of fraud.
6.  **Evidence before conclusions:** Document IPFS CIDs and physical progress images are linked tightly to the milestone or fund release they justify.
7.  **Consistent interaction patterns:** A primary action (e.g., "Approve Release") looks and behaves the same way everywhere.
8.  **Accessible information hierarchy:** Page layouts naturally guide the eye from macro (Project Budget) to micro (Milestone Status).
9.  **Progressive disclosure:** Complex details (like JSON transaction context or raw AI factor weights) are hidden behind "View Details" or modals to keep primary screens clean.
10. **Responsive usability:** Core dashboard components flow gracefully from desktop down to tablet/mobile, even if desktop is the primary platform.
11. **Clear system status:** The UI distinctly communicates when the Server is processing an action, waiting for MetaMask, or when an upload to IPFS is occurring.
12. **Explainability of risk indicators:** Risk scores must explicitly list their contributing factors (e.g., "60% utilization vs 15% physical progress").

## 7. Visual Design System

### 7.1 Color System
Colors must use semantic tokens. Hardcoded hex values should be avoided in implementation.

*   **Primary:** Slate/Indigo blend (Trust, authority, financial stability).
*   **Secondary:** Muted Teal/Cyan (Interactive elements, active states).
*   **Background:** Off-white/slate-gray (Light mode) and deep slate/charcoal (Dark mode).
*   **Surface / Elevated Surface:** White to subtle gray cards; layered for hierarchy.
*   **Border:** Low contrast lines separating table rows and card sections.
*   **Text Primary / Secondary:** High contrast charcoal/white for headers, muted grays for metadata.
*   **Success (Semantic):** Emerald green (Approved, Completed, Confirmed).
*   **Warning (Semantic):** Amber/Orange (Overdue, Pending, Needs Verification).
*   **Danger (Semantic):** Rose/Red (Rejected, Failed, High Risk, System Error).
*   **Info (Semantic):** Sky blue (Informational alerts, tooltips).
*   **AI/Identity (Semantic):** Purple/Violet (Indicates that an insight/indicator originated from the AI system). 

*Note:* AI identity and risk severity must not be confused. Violet identifies the AI source, while severity (how serious the risk is) should use appropriate neutral/positive, warning, or danger semantics. **[UI/UX Proposal]** Avoid making every AI risk item purple and red simultaneously to prevent ambiguous meaning.
*   **Blockchain/Audit (Semantic):** Monospace font with subtle gold/bronze tint for verified on-chain hashes.

### 7.2 Typography
*   **Font Strategy:** A clean, modern sans-serif (e.g., Inter, Roboto, or Plus Jakarta Sans) for UI text; a monospaced font (e.g., JetBrains Mono, Fira Code) for transaction hashes and IDs.
*   **Hierarchy:** `h1` for Page Titles, `h2` for Dashboard Widgets/Cards, `h3` for inner sections.
*   **Numerical/Financial:** Tabular lining numerals must be used for all tables and budget displays to ensure vertical alignment of digits.
*   **Metadata:** Smaller, muted text for timestamps, CIDs, and UUIDs.

### 7.3 Spacing, Border Radius, and Shadows
*   **Spacing:** Follow a strict 4px/8px baseline grid (e.g., `p-4`, `m-2`, `gap-6`).
*   **Border Radius:** 
    *   Cards/Modals: `rounded-xl` or `rounded-2xl` (Soft, modern feel).
    *   Buttons/Inputs: `rounded-md` or `rounded-lg` (Snappy, interactive).
    *   Badges: `rounded-full` (Pill shape).
*   **Shadows / Elevation:** Restrained usage. Subtle diffuse shadows for floating headers and primary action buttons; slightly larger shadows for modals/dropdowns. Dark mode uses border highlights instead of heavy shadows.

### 7.4 Icons
*   **Usage:** Used to reinforce text labels, not replace them (except for common actions like 'Close' or 'Edit' with tooltips).
*   **Style:** Minimalist, consistent stroke weight (e.g., Lucide React, Heroicons). **[Design Recommendation]**

## 8. Design Tokens

Conceptual structure mapping to Tailwind/shadcn:
*   `--color-primary`, `--color-primary-foreground`
*   `--color-background`, `--color-surface`
*   `--color-success`, `--color-warning`, `--color-danger`, `--color-ai`
*   `--radius-sm`, `--radius-md`, `--radius-lg`
*   `--shadow-subtle`, `--shadow-modal`
*   `--font-sans`, `--font-mono`

## 9. Component System

The Client will utilize a reusable component system (e.g., shadcn/ui primitives).

*   **Button / Icon Button:** Primary, Secondary, Outline, Ghost, and Destructive variants. Must support `isLoading` states (spinner replaces icon).
*   **Input / Select / Date Picker / Textarea:** Standard form elements with explicit error states, focus rings, and help text.
*   **File Upload:** Drag-and-drop zone with progress bar and success/error indicators.
*   **Search / Filter:** Debounced inputs mapping to table parameters.
*   **Tabs:** Clean underlining or pill-based indicators to switch views without routing.
*   **Status Badge:** Pill-shaped, semantically colored (e.g., Green "Approved", Amber "Pending", Red "Rejected").
*   **Risk Badge:** Purple background with AI icon (e.g., "High Risk", "Anomaly").
*   **Metric Card:** Title, large numerical value, and secondary trend/context text.
*   **Progress Bar:** Fills horizontally, colored semantically (Green for healthy budget, Red for overrun).
*   **Alert / Toast:** In-page contextual warnings / slide-in ephemeral notifications.
*   **Modal / Dialog / Drawer:** Used for complex interactions (e.g., Submitting Audit Finding, Approving Funds) to avoid page navigation.
*   **Data Table:** Sortable, paginated columns. Includes row-level actions (dropdown menu `...`).
*   **Timeline / Activity Feed:** Vertical stepper showing lifecycle events with timestamps and actor details.
*   **Transaction Card:** Specialized card showing Tx Hash (truncated with copy button), block confirmation status, and a link to the explorer.
*   **Document/Evidence Card:** Shows file type icon, CID, filename, and download/view action.
*   **Empty/Loading/Error States:** Skeleton loaders for data fetching; friendly graphics/text for empty tables.

## 10. Application Shell

The global authenticated layout consists of:
*   **Sidebar (Left):** Primary role-based navigation. Collapsible on smaller screens. Includes platform logo and current role indicator.
*   **Top Navigation:** Breadcrumbs for deep navigation, Theme Toggle (Light/Dark), Notification Bell with unread indicator, User Avatar/Profile Dropdown.
*   **Content Area:** Scrollable main view with max-width constraints for readability on ultrawide monitors.
*   **Wallet Status Indicator (Contextual):** For Contractor/Auditor roles, a small pill in the header showing MetaMask connection status (Connected: `0x123...abc`).

## 11. Role-Based UI Overview

The sidebar navigation and available dashboard widgets change dynamically based on the JWT's `role` claim. 
*   **Platform Admin:** Focuses on users and roles.
*   **Government Admin:** Focuses on portfolio health, budgets, and pending approvals.
*   **Department Officer:** Focuses on milestone verification queues and progress.
*   **Contractor:** Focuses on assigned projects and fund request statuses.
*   **Auditor:** Focuses on AI flags, escalations, and audit reports.
*   **Citizen (Unauthenticated):** Public portal layout, no sidebar, top-nav only.

## 12. Platform Admin UI

*   **Primary Objective:** System-level user administration.
*   **Navigation:** Dashboard, Users, System Settings.
*   **Key Workflows:** 
    *   Activating/Deactivating users.
    *   Changing user roles.
*   **Visual Focus:** Data tables with quick-action toggles (Activate/Deactivate) and role assignment comboboxes.

## 13. Government Admin UI

*   **Primary Objective:** Project governance and financial oversight.
*   **Navigation:** Dashboard, Projects, Approvals Queue, Escalations.
*   **Dashboard Priorities:** 
    *   Total Budget Allocated vs. Disbursed (Metric Cards).
    *   Projects at Risk (List of projects with High AI Risk Scores).
    *   Pending Fund Release Approvals (Actionable table).
*   **Key Workflows:**
    *   **Project Creation:** Multi-step form (Details, Budget, Initial Milestones).
    *   **Fund Release Approval:** Drawer/Modal displaying Contractor's request, Officer's verification, uploaded Evidence (CIDs), and AI pre-analysis. Contains explicit "Approve" (Server Relayer action) and "Reject" buttons.
    *   **Escalate to Auditor:** Contextual action on a project/milestone to manually trigger an Auditor investigation (generates `escalationId`).

## 14. Officer / Engineer UI

*   **Primary Objective:** Validating physical progress on the ground.
*   **Navigation:** Dashboard, Assigned Projects, Verification Queue.
*   **Dashboard Priorities:**
    *   Milestones nearing deadlines.
    *   Contractor updates awaiting verification.
*   **Key Workflows:**
    *   **Milestone Verification:** Form to review Contractor's physical progress notes/evidence, input independent Officer notes, and click "Verify Milestone" (Server Relayer action).

## 15. Contractor UI

*   **Primary Objective:** Executing work and requesting funds.
*   **Navigation:** Dashboard, My Projects, Wallet Connection.
*   **Dashboard Priorities:**
    *   Active Milestones.
    *   Status of submitted Fund Release Requests (Pending, Verified, Approved, Rejected).
*   **Key Workflows:**
    *   **Submit Fund Release:** Form requiring milestone selection, physical progress description, and IPFS evidence upload. 
    *   **Wallet Interaction:** Clicking "Submit Request" triggers MetaMask. The UI must show a "Waiting for Signature" modal. Once signed and mined, it shows "Validating with Server," leading to a Success toast.

## 16. Auditor UI

*   **Primary Objective:** Independent investigation of anomalies.
*   **Navigation:** Dashboard, Risk Queue, Escalations, My Findings.
*   **Dashboard Priorities:**
    *   AI Flags awaiting review (sorted by Risk Score).
    *   Government Admin escalations.
*   **Key Workflows:**
    *   **Investigation View:** Split-screen or multi-card layout showing the Anomaly (e.g., "Mismatch: 80% funds released, 20% physical progress"), relevant transaction history, and IPFS evidence.
    *   **Record Finding:** Form to input investigation conclusions, attach an audit report (IPFS), and submit. Triggers MetaMask to sign the `AuditFindingRecorded` transaction.

## 17. Citizen Portal

*   **Primary Objective:** Public transparency.
*   **Navigation:** Home, Public Projects, Search.
*   **Visual Focus:** Clean, accessible, unauthenticated layout. Emphasizes charts (budget vs spent) and timelines to make public financial/progress information understandable to a non-technical citizen.
*   **Key Workflows:**
    *   **Project Details:** View what the project is, allocated budget, fund utilization, physical progress, and milestone status.
    *   **Audit Timeline:** Vertical timeline showing public fund-event history and published audit information (Project Created, Funds Released, Audit Findings).
*   **Restrictions:** The portal must remain unauthenticated and read-only. Explicitly do NOT expose:
    *   internal AI risk factors
    *   private operational workflow data
    *   restricted IPFS evidence
    *   unauthorized user information
    *   contractor identity (where approved requirements exclude it)

## 18. Information Architecture & Navigation Hierarchy

```text
Authenticated Shell
 ├── Dashboard (Role specific widgets)
 ├── Projects
 │    ├── Project List
 │    └── Project Detail (Tabs: Overview, Milestones, Finances, Audit Trail)
 ├── Tasks / Queues
 │    ├── Approvals (Gov Admin)
 │    ├── Verifications (Officer)
 │    └── Risk Queue (Auditor)
 ├── System (Platform Admin)
 │    └── Users
 └── Profile & Settings

Public Portal (Citizen)
 ├── Landing Page (Search & Global Stats)
 └── Public Project Detail (Overview, Public Timeline, Public Audit Summary)
```

## 19. Screen Inventory

*   **AUTH-01:** Login (Role: Any, Purpose: Authenticate user via credentials)
*   **ADMIN-01:** Platform Admin Dashboard (Role: Platform Admin, Purpose: System overview)
*   **ADMIN-02:** User Management (Role: Platform Admin, Purpose: Activate/deactivate users and assign roles via data table)
*   **ADMIN-03:** System Settings (Role: Platform Admin, Purpose: Manage global configs)
*   **DASH-GA:** Government Admin Dashboard (Role: Gov Admin, Purpose: Portfolio health, budgets, pending approvals)
*   **DASH-OFF:** Officer Dashboard (Role: Officer, Purpose: Verification queue, physical progress monitoring)
*   **DASH-CON:** Contractor Dashboard (Role: Contractor, Purpose: Assigned projects, milestone status, pending fund requests)
*   **DASH-AUD:** Auditor Dashboard (Role: Auditor, Purpose: AI flags, escalations, audit tracking)
*   **PROJ-01:** Project List (Role: Gov Admin/Officer/Contractor/Auditor, Purpose: Filterable data table of projects)
*   **PROJ-02:** Project Detail (Role: Gov Admin/Officer/Contractor/Auditor, Purpose: Complex tabbed view containing Overview, Milestones, Finances, Audit Trail)
*   **PROJ-03:** Project Creation (Role: Gov Admin, Purpose: Multi-step creation workflow)
*   **MILE-01:** Milestone Management / Verification (Role: Officer, Purpose: Form to verify Contractor updates; can be a modal/drawer in Project Detail)
*   **FUND-01:** Fund Release Request (Role: Contractor, Purpose: Wallet-triggered submission form)
*   **FUND-02:** Fund Release Approval (Role: Gov Admin, Purpose: Server-Relayed approval dialog/modal)
*   **FUND-03:** Fund Release History (Role: Gov Admin/Auditor, Purpose: Tab or sub-view showing past fund transactions)
*   **DOC-01:** Evidence / Documents (Role: Gov Admin/Officer/Auditor, Purpose: Sub-view or modal displaying IPFS CIDs and files)
*   **RISK-01:** AI Risk / Anomaly Investigation (Role: Auditor, Purpose: Split-screen view of anomaly details and history)
*   **RISK-02:** Auditor Escalation View (Role: Auditor, Purpose: Manual escalations from Gov Admin)
*   **RISK-03:** Audit Finding (Role: Auditor, Purpose: Wallet-triggered form to submit investigation conclusions)
*   **AUDIT-01:** Blockchain Audit Trail (Role: Admin/Auditor, Purpose: Tabbed sub-view showing immutable event history)
*   **NOTIF-01:** Notifications / Notification Center (Role: Any, Purpose: Dropdown or drawer showing in-app alerts)
*   **PROF-01:** [UI/UX Proposal] Profile / Account View (Role: Any, Purpose: Manage personal account details within app shell)
*   **PUB-01:** Citizen Landing Page (Role: Citizen, Purpose: Unauthenticated public project search and global stats)
*   **PUB-02:** Citizen Project Detail (Role: Citizen, Purpose: View public budget, physical progress, and milestones)
*   **PUB-03:** Citizen Public Audit Timeline / Summary (Role: Citizen, Purpose: View published audit summaries and fund-event history)

## 20. Demo-Critical Screens

The following screens require the highest visual polish for the viva/demonstration:
1.  **Login:** First impression; must look secure and modern.
2.  **Government Admin Dashboard:** Must impressively visualize the portfolio health, budget metrics, and immediately highlight the AI's utility by showing "Projects at Risk."
3.  **Project Detail (Admin/Auditor view):** The core hub combining relational data, progress bars, and the blockchain audit timeline.
4.  **AI Risk Dashboard / Investigation View:** Must clearly explain *why* an anomaly was flagged (Explainable AI factors) without looking like a black box.
5.  **Fund Lifecycle (Approval Modal):** Shows the intersection of Contractor evidence, Officer verification, and Admin authorization.
6.  **Citizen Project View:** Demonstrates the end-goal of public transparency in a highly accessible format.

## 21. Data Visualization

*   **Budget vs Utilization:** Horizontal stacked progress bars or minimal Donut charts.
*   **Financial vs Physical Progress:** Dual horizontal bars side-by-side to instantly highlight discrepancies (e.g., Blue bar for Financial 80%, Green bar for Physical 20%).
*   **Risk Distribution:** Heatmaps or scatter plots (if data volume permits) on the Auditor dashboard, or simply sorted metric cards.
*   **Fund Lifecycle Timeline:** Vertical stepper with icons (Checkmark = Confirmed, Spinner = Pending, Exclamation = Anomaly).

## 22. AI / Risk UX

*   **Risk Presentation:** AI outputs are presented under headers like "AI Anomaly Analysis" or "System Risk Indicators."
*   **Risk Score (0-100):** The UI must support a 0-100 risk score where severity can be visually represented (e.g., via a circular progress ring). Final numerical thresholds must be determined during AI/ML calibration and implementation.
*   **Risk Severity Bands [UI/UX Proposal]:** Example severity bands (0–30 Low, 31–70 Medium, 71–100 High) may be shown in designs, but the UI must not hard-code these as an upstream project requirement.
*   **Contributing Factors:** Bulleted list or pill tags explaining the score (e.g., `Factor: Duplicate Invoice Detected`, `Factor: Budget Overrun Trajectory`).
*   **Terminology:** Never use "Fraud Confirmed." Use "High Risk Anomaly," "Review Recommended," "Unusual Pattern."

## 23. Blockchain UX

*   **Progressive Disclosure:** Normal users see "✔ Confirmed on Blockchain". Clicking it expands to show the `on_chain_id`, `Tx Hash`, and Block Number.
*   **Monospace formatting:** All hashes (`0x...`) use monospace fonts.
*   **Visual Indicators:** A subtle "Chain" or "Lock" icon next to lifecycle events denotes that it is anchored.
*   **Wallet Interaction:** When MetaMask is triggered, the Client UI must be overlaid with a non-dismissible (except by cancellation) modal stating "Please confirm the transaction in your browser wallet."

## 24. IPFS / Evidence UX

*   **Upload:** Drag-and-drop zone. During upload, show a progress bar. 
*   **Display:** Once uploaded, display a "Document Card" containing the filename, file size, a PDF/Image icon, and the generated IPFS CID (truncated).
*   **Security:** UI must note that uploaded evidence is public/unencrypted for transparency purposes.

## 25. Forms and Workflows

*   **Validation:** Inline red text for errors (e.g., "Budget must be greater than 0"). Validated using a schema-based validation approach. Candidate implementation library: Zod. **[Implementation Option]**
*   **Destructive/Critical Actions:** Fund approvals, rejections, or audit finding submissions require a confirmation modal ("Are you sure you want to approve $50,000?").
*   **Submission State:** Buttons disable and show a loading spinner during API calls to prevent double-submission.

## 26. Notifications

*   **In-App Bell Icon:** Top right corner. Red dot for unread.
*   **Dropdown List:** Shows recent business events ("Project XYZ approved," "Milestone Overdue").
*   **Deep Links:** Clicking a notification navigates directly to the relevant Project Detail or Approval view.

## 27. Loading / Empty / Error / Success States

*   **Loading:** Skeleton loaders (pulsing gray blocks) for large dashboard widgets or tables. Spinners for small buttons.
*   **Empty:** Friendly illustrations or muted icons indicating "No projects found," "No pending approvals," or "Queue empty. Great job!"
*   **Success:** Temporary (3-5 seconds) slide-in Toast notifications at the bottom right ("Transaction Confirmed," "Project Saved").
*   **Error:** Slide-in Red Toast for generic API errors. Specific inline errors for form validation. If a Server-Relayed transaction fails, the modal displays the exact revert reason in a red alert box.

## 28. Dark Mode

*   **Strategy:** Semantic token switching. Deep slate backgrounds (`bg-slate-950`), elevated cards (`bg-slate-900`), border highlights (`border-slate-800`).
*   **Text:** High contrast `text-slate-100` for readability.
*   **Semantic Colors:** Adjust success/warning/danger tones to be slightly muted/pastel in dark mode to prevent retinal burn (e.g., swap glaring red for a softer rose/coral).

## 29. Responsive Design

*   **Desktop First:** Optimized for 1080p+ screens. Dashboards utilize CSS Grid for multi-column layouts.
*   **Tablet:** Sidebars collapse into icons. Multi-column grids drop to 2-column or 1-column.
*   **Mobile:** Top navigation introduces a Hamburger menu. Data tables switch to stacked card layouts or allow horizontal scrolling. Complex charts render as simplified summary metrics.

## 30. Accessibility

*   **Keyboard Navigation:** All interactive elements (buttons, links, inputs) must be focusable with explicit `focus-visible:ring` outlines.
*   **Color Independence:** Do not rely solely on red/green. A "Rejected" badge must be Red AND say "Rejected" (or have an X icon).
*   **Contrast:** Ensure WCAG AA compliance (4.5:1 ratio) for text against backgrounds.

## 31. Motion and Micro-interactions

*   **Restrained:** No bouncy, excessive animations.
*   **Transitions [UI/UX Proposal]:** Fast (150ms-200ms) ease-in-out fades for modals appearing, tooltips hovering, and page routing.
*   **Progressive Loading:** Staggered fade-ins for dashboard widgets to make the initial load feel performant.

## 32. UI Libraries and External References (Candidate Selection)

The project will be built using the approved Client stack (React, Vite, Tailwind CSS, shadcn/ui).
**[Implementation Options to Evaluate Later - DO NOT INSTALL YET]**:
*   *Icons:* `lucide-react` (native to shadcn/ui).
*   *Charting:* `recharts` or `chart.js` (clean, responsive SVGs).
*   *Tables:* `@tanstack/react-table` (headless UI for sorting/pagination).
*   *Forms:* `react-hook-form` + `zod` (seamless validation).
*   *Animation:* `framer-motion` (for simple layout transitions).
*   *State/Data Fetching:* `react-query` or RTK Query.

## 33. Design Anti-Patterns

**Do NOT Implement:**
*   Excessive gradients or "glassmorphism" panels that harm readability.
*   Walls of disconnected KPI cards with no context.
*   "Black box" AI scores without contributing factors.
*   Cryptocurrency aesthetics (neon green, laser eyes, token tickers).
*   Tables displaying raw UUIDs taking up 50% of the screen width (truncate or hide).

## 34. UI Security / Privacy Presentation

*   **Conditional Rendering:** Elements a user lacks permission for are hidden entirely, not just disabled (e.g., an Officer never sees the "Approve Funds" button).
*   **Token Expiry:** If the JWT expires, the user is cleanly redirected to the Login page with a toast message: "Session expired, please log in again."
*   **Public Portal Restrictions:** The Citizen view explicitly omits the Authentication top-nav logic, Contractor profile links, and internal document metadata.

## 35. Design States and State Machines

*   **Project Status UI Mapping:** `Draft` (Gray) -> `Active` (Blue) -> `Completed` (Green).
*   **Fund Release Status UI Mapping:** `Pending` (Amber) -> `Verified` (Teal) -> `Approved` (Green) OR `Rejected` (Red).
*   **AI Risk Flag UI Mapping:** `No Flag` -> `Flagged` -> `Investigated`. 
    *   *AI-originated indicators* use a Violet AI icon/accent.
    *   *Risk severity* uses separate semantic treatment: Low/normal → neutral/positive; Medium → warning; High → danger.
    *   *A high-risk AI flag* may therefore display a Violet AI indicator together with a Red/High-Severity indicator. (Do not define Purple/Red as one combined semantic category).

## 36. UI Traceability

| UI/UX Requirement | Source Document | ID / Section |
| :--- | :--- | :--- |
| Six specific role dashboards | PRD / FRD | Actor Profiles Section |
| Hybrid wallet interaction (Contractor/Auditor) | System Architecture | Sections 15 & 18 |
| Explainable AI Risk Score & Factors | TRD | Section 1 (AI/ML) |
| Public transparent Citizen portal | PRD | Scope / Features Section |
| Dark mode semantic tokens | Project Definition | Section 4 (Baseline) |
| Evidence IPFS upload with CIDs | FRD | Document/Evidence Section |

## 37. UI Acceptance Criteria

*   [ ] The application shell provides clear navigation for all 6 roles.
*   [ ] Dark mode and Light mode are supported via semantic Tailwind tokens.
*   [ ] A visual distinction exists between Server-Relayed actions (e.g., Approve) and Wallet-Signed actions (e.g., Submit Request).
*   [ ] AI insights are presented as Risk Signals with factors, not facts.
*   [ ] The Citizen portal exposes no restricted operational data.
*   [ ] Data tables support empty states, loading skeletons, and pagination.
*   [ ] Blockchain references (CIDs, Tx Hashes) are accessible but progressively disclosed.
*   [ ] Important actions (approvals, rejections, findings) use confirmation modals.

## 38. Deferred UI Decisions

*   Exact icon package (`lucide-react` vs `heroicons`).
*   Exact charting library (`recharts` vs others).
*   Specific pixel values for breakpoints (will default to Tailwind standard `sm`, `md`, `lg`, `xl`).

## 39. Scope Control

The UI specification explicitly **excludes**:
*   Mobile native app layouts (React Native/Flutter).
*   Real biometric or government SSO integrations.
*   Complex GIS (Geographic Information Systems) mapping for physical progress (simple text/images are approved).
*   Fully custom CSS outside the Tailwind/shadcn ecosystem.

## 40. UI/UX Dependencies / Issues Requiring Review

*   *No contradictions with the PRD, FRD, TRD, or System Architecture (v1.0.3) were discovered during the drafting of this specification.*

## 41. Change History

| Version | Date | Author | Summary |
| :--- | :--- | :--- | :--- |
| 1.0.2 | 2026-09-28 | AI Engineering Agent | Clarified AI origin vs severity semantics, clarified Zod as an implementation option, and clarified Profile / Account View as a UI/UX proposal. |
| 1.0.1 | 2026-09-28 | AI Engineering Agent | Refined AI risk presentation semantics, expanded screen inventory, clarified citizen UX, and explicitly distinguished UI/UX proposals from approved requirements. |
| 1.0.0 | 2026-09-28 | AI Engineering Agent | Initial UI/UX Design Specification. |

---
*(End of UI/UX Design Specification)*
