# Form & Field Operations Platform Plan

## Vision
Build a field operations platform with:
- Form builder (drag-and-drop + selectable controls)
- Site and worker management
- Group-based form assignment
- Task assignment and tracking
- Sign/submit/review workflow
- Offline-first sync
- Dashboard analytics

## Core Modules (MVP)
1. Authentication and role-based access (Admin, Manager, Worker)
2. Organization setup (Company, Sites, Teams, Workers)
3. Form Builder (text, checkbox, radio, yes/no, image, signature, notes, header/footer)
4. Form Runtime (draft, complete, sign, submit)
5. Review & Approval workflow for administrators
6. Task/Action Items assignment with due dates and statuses
7. Dashboard and reporting
8. Offline sync queue and conflict handling

## Suggested Architecture
- **Backend**: ASP.NET Core (Razor Pages + Web API)
- **Frontend Admin**: Razor Pages (.cshtml) with JavaScript for builder interactions
- **Mobile/Field**: PWA or native app consuming ASP.NET Core APIs
- **Database**: SQL Server or PostgreSQL via EF Core
- **Storage**: Blob/S3-compatible for photos, signatures, attachments
- **Realtime/Notifications**: SignalR + push notification service
- **Background Jobs**: Hangfire for reminders and retry sync tasks

## High-Level Data Model
- users, roles, permissions
- companies, sites, teams, team_members
- form_templates, form_versions, form_assignments
- form_submissions, submission_answers, submission_signatures
- tasks, task_assignments, task_comments
- assets, asset_logs
- certificates, worker_certificates
- incidents
- sync_events, audit_logs

## Workflow Design
### Form lifecycle
1. Admin creates form template
2. Admin publishes a version
3. Admin assigns form to site/team/workers
4. Worker fills form and signs
5. Submission sent to admin for review
6. Admin approves/rejects and optionally generates tasks

### Task lifecycle
1. Task created manually or from submission rule
2. Assigned to worker/team
3. Worker updates status and adds evidence
4. Manager verifies completion

## Offline Sync Strategy
- Local store for drafts and unsent actions
- Each item includes `local_id`, `server_id`, `updated_at`, `sync_status`
- Sync statuses: `pending`, `syncing`, `synced`, `failed`, `conflict`
- Retry with exponential backoff
- Conflict policy:
  - Approved records are server-authoritative
  - Drafts can use last-write-wins or manual merge screen

## Phased Delivery Plan
### Phase 1 (Weeks 1-2): Foundation
- Product scope, UX map, RBAC matrix
- DB schema and API contracts
- DevOps and CI/CD setup

### Phase 2 (Weeks 3-4): Org + Users
- Login/invite flows
- Site, team, worker CRUD
- Role-based navigation

### Phase 3 (Weeks 5-7): Form Builder
- Control palette and drag/drop canvas
- Validation rules and conditional logic
- Save draft template and publish versions

### Phase 4 (Weeks 8-9): Form Runtime + Signatures
- Render form from schema
- Draft/save/resume submit flows
- Signature capture and attachments

### Phase 5 (Weeks 10-11): Tasks + Notifications
- Task assignment and tracking
- Notification center and reminders

### Phase 6 (Weeks 12-13): Offline Sync
- Local queue + background sync
- Conflict handling UX

### Phase 7 (Weeks 14-16): Dashboard + Hardening
- KPI dashboards, filters, exports
- Security tests, performance tests, UAT

## Security & Compliance
- ASP.NET Core Identity + JWT/cookies
- Per-site and per-role authorization
- Full audit trail for form/task updates
- Immutable approved submissions (with amendment flow)
- Encryption in transit and at rest
- File upload scanning and type restrictions

## Razor Pages Implementation Notes
- Use Razor Pages for CRUD-heavy admin screens
- Use JavaScript components within `.cshtml` for rich controls:
  - Drag/drop: SortableJS
  - Signature: SignaturePad
  - Image annotation: Fabric.js/Konva
- Persist form definitions as JSON schema in DB
- Keep rendering engine server-safe and version-aware

## Risks & Mitigations
- **Risk**: Complex dynamic builder UX in server-rendered pages
  - **Mitigation**: Hybrid Razor + JS architecture
- **Risk**: Offline data conflicts
  - **Mitigation**: Clear sync states and explicit conflict resolution
- **Risk**: Scope creep (assets/certificates/incidents all at once)
  - **Mitigation**: Strict MVP + phased feature gates

## Success Metrics
- Form completion rate
- Average time to submit form
- Task closure time
- Overdue task ratio
- Sync failure rate
- Active workers/week

