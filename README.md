# HOTEL-ITSM
Hotel Operations — System Architecture &amp; Multi-Departmental Automation
# ServiceNow IT Service Management (ITSM) Lab
## Hotel Operations — System Architecture & Multi-Departmental Automation

## Objective

> This project demonstrates senior-level ServiceNow platform architecture, implementation, 
> and management for a 250-room boutique hotel (Seneca Technology Solutions client).
>
> As ITSM System Administrator Tier 2, I (Anas Nehri) architected and implemented 
> a production ServiceNow ITSM platform to solve a critical business problem: when 
> IT incidents impact guest-facing operations (reservation system, POS, spa booking, 
> valet management), information must cascade automatically to affected departments 
> within seconds so they can coordinate guest service recovery *before* guests 
> notice the outage.
>
> Implementation includes: ITSM data model design, custom incident tables with 
> business metrics (guest impact tracking, revenue impact calculation), automated 
> multi-department group routing via business rules and workflows, SLA framework 
> aligned to hotel revenue cycles, Knowledge Management system configuration, 
> integration patterns with hotel PMS (Property Management System), RBAC design 
> and role-based field visibility, service catalog customization, and executive 
> reporting dashboards tied to business KPIs.
>
> The system is production-ready and operates in ServiceNow Yokohama release on 
> a Personal Developer Instance (PDI) at developer.servicenow.com.

## Key Skills Demonstrated (Tier 2 CSA Level)

**System Architecture & Design:**
- ITSM data model customization (incident tables, custom fields, relationships)
- Impact/Urgency/Priority business rule logic aligned to hotel metrics
- Multi-stage incident workflow design with conditional branching
- Table relationships and linking strategies (incident ↔ problem ↔ knowledge)

**Workflow Automation:**
- Automated group routing based on affected department(s)
- Business rules for SLA calculation tied to guest impact and revenue thresholds
- Workflow triggers for multi-department notifications (email, in-app)
- Conditional escalation procedures (P1 → manager, P2 → group, P3 → queue)

**Configuration & Customization:**
- User administration and RBAC design (admin, itil, guest services, PMS integration roles)
- Group creation and membership management for hotel departments
- Custom service catalog items aligned to hotel operations
- Field configuration, form layout design, UI policy customization
- Email template creation for incident notifications

**Integration & Data Management:**
- Integration architecture documentation (ServiceNow ↔ hotel PMS)
- Data mapping specifications for guest/department synchronization
- API endpoint design for external system integration
- Configuration of integration middleware and error handling

**Reporting & Analytics:**
- Executive dashboard design (guest impact, revenue protection, SLA compliance)
- Custom report creation tied to hotel business metrics
- Data visualization strategy (KPI widgets, trend analysis)
- Business intelligence insights from incident data

**Platform Administration:**
- Instance configuration and tuning
- Security and compliance configuration (audit logging, field encryption)
- Performance optimization (query optimization, index management)
- Change management and release documentation

## Tools & Technologies Used

- **ServiceNow Platform:** Personal Developer Instance (PDI) — Yokohama release
- **Modules Configured:** Incident Management, Problem Management, Knowledge Management, Service Catalog, ITSM Reports, Workflow Designer, Business Rules, SLA Management, User Administration
- **Integration Patterns:** REST API for PMS connectivity, email integration, webhook-based external notifications
- **Data Modeling:** Custom tables, table extensions, relationships, calculations
- **Automation:** Business rules, workflow orchestration, scheduled jobs
- **Documentation:** Architecture diagrams, integration specifications, configuration runbooks

---

## Part 0 — User & Role Administration (Comprehensive Configuration)

### Objective

Design and configure a multi-tier ServiceNow user structure reflecting hotel 
operations hierarchy, with granular RBAC ensuring:
- Anas Nehri (ITSM Admin): Full platform access, configuration rights
- Wendy Chen (IT Operations Technician): Incident worker, limited configuration
- Boris Kowalski (Front Office Manager): End user, group manager, incident submitter
- Supporting hotel staff: Department-specific access, read-only portal views

### User Records Created

#### **User 1: Anas Nehri — ITSM System Administrator Tier 2**

| Field | Value |
| --- | --- |
| **First Name** | Anas |
| **Last Name** | Nehri |
| **User ID** | anas.nehri |
| **Email** | anas.nehri@senecaTech.com |
| **Phone** | +1-647-555-0142 |
| **Department** | Information Technology |
| **Title** | ITSM System Administrator Tier 2 |
| **Manager** | [IT Director — external role] |
| **Active** | Yes |
| **Roles** | admin, itil, knowledge_manager, sla_admin, report_admin |
| **Groups** | Hotel IT Operations (Manager), Enterprise Admins, ITSM Administrators |
| **Password Policy** | Administrative account (2FA required) |
| **Delegation** | Can delegate to: Wendy Chen (incident management), Matthew Rodriguez (reporting) |

**Role Assignments Justification:**
- `admin` — full platform access for configuration, customization, user management
- `itil` — incident/problem/knowledge record access and modification
- `knowledge_manager` — KB article review, approval, publishing
- `sla_admin` — SLA definition creation, monitoring, breach notification
- `report_admin` — custom dashboard and report creation

**Access Level:** All tables, all configurations, ability to modify data model

---

#### **User 2: Wendy Chen — IT Operations Technician (Tier 1)**

| Field | Value |
| --- | --- |
| **First Name** | Wendy |
| **Last Name** | Chen |
| **User ID** | wendy.chen |
| **Email** | wendy.chen@senecaTech.com |
| **Phone** | +1-647-555-0143 |
| **Department** | Information Technology |
| **Title** | IT Operations Technician |
| **Manager** | Anas Nehri |
| **Active** | Yes |
| **Roles** | itil, knowledge_contributor |
| **Groups** | Hotel IT Operations (Member), Revenue Systems Critical (Member) |
| **Password Policy** | Standard user account (optional 2FA) |
| **Delegation** | Can delegate to: Matthew Rodriguez (manager-level approval) |

**Role Assignments Justification:**
- `itil` — create/update/resolve incident and problem records
- `knowledge_contributor` — submit KB articles, but requires approval before publishing

**Access Level:** 
- Incident table: create, read, update (assigned incidents)
- Problem table: create, read (assigned problems)
- Knowledge table: read-only (with submit capability)
- Reports: read-only (predefined reports only)
- Configuration: None (cannot create business rules, workflows, modify data model)

**Restrictions:**
- Cannot see incidents from other departments
- Cannot modify SLA definitions
- Cannot create new user accounts
- Cannot access admin console

---

#### **User 3: Matthew Rodriguez — IT Operations Manager**

| Field | Value |
| --- | --- |
| **First Name** | Matthew |
| **Last Name** | Rodriguez |
| **User ID** | matthew.rodriguez |
| **Email** | matthew.rodriguez@senecaTech.com |
| **Phone** | +1-647-555-0144 |
| **Department** | Information Technology |
| **Title** | IT Operations Manager |
| **Manager** | [IT Director — external role] |
| **Active** | Yes |
| **Roles** | itil, report_viewer, group_admin |
| **Groups** | Hotel IT Operations (Manager), Revenue Systems Critical (Manager), Guest Services Notifiers (Manager) |
| **Password Policy** | Standard user account (optional 2FA) |
| **Delegation** | Can approve incidents, escalations, and SLA overrides |

**Role Assignments Justification:**
- `itil` — incident/problem management
- `report_viewer` — access to all predefined reports and dashboards
- `group_admin` — manage group membership, assign incident ownership

**Access Level:**
- Incident table: full (all incidents for hotel, manage assignments)
- Problem table: full (create, read, update)
- Reports/Dashboards: all reports
- Configuration: None

---

#### **User 4: Boris Kowalski — Front Office Manager (End User/Guest Services)**

| Field | Value |
| --- | --- |
| **First Name** | Boris |
| **Last Name** | Kowalski |
| **User ID** | boris.kowalski |
| **Email** | boris.kowalski@senecaTech.com |
| **Phone** | +1-647-555-0145 |
| **Department** | Guest Services |
| **Title** | Front Office Manager |
| **Manager** | [Director of Guest Services — external role] |
| **Active** | Yes |
| **Roles** | service_portal_user, group_manager |
| **Groups** | Guest Services Notifiers (Manager), Front Office Team |
| **Password Policy** | Standard user account (no 2FA required) |
| **Delegation** | Can submit incidents on behalf of other front office staff |

**Role Assignments Justification:**
- `service_portal_user` — access to service portal for incident submission, self-service
- `group_manager` — manage Guest Services Notifiers group, coordinate multi-department response

**Access Level:**
- Incident table: submit new incidents, view own incidents, update notifications
- Service Portal: full access (incident submission, knowledge base search, status tracking)
- Reports: read-only (predefined dashboard only: Guest Impact Summary)
- Configuration: None

---

#### **User 5: Priya Desai — Spa & Wellness Director**

| Field | Value |
| --- | --- |
| **First Name** | Priya |
| **Last Name** | Desai |
| **User ID** | priya.desai |
| **Email** | priya.desai@senecaTech.com |
| **Phone** | +1-647-555-0146 |
| **Department** | Spa & Wellness |
| **Title** | Spa & Wellness Director |
| **Manager** | [VP of Guest Services — external role] |
| **Active** | Yes |
| **Roles** | service_portal_user, group_manager |
| **Groups** | Spa & Wellness Team, Guest Services Notifiers |
| **Password Policy** | Standard user account |

**Role Assignments:** Department-level incident submitter and notification recipient

---

#### **User 6: Marcus Johnson — Restaurant & Bar Manager (F&B Director)**

| Field | Value |
| --- | --- |
| **First Name** | Marcus |
| **Last Name** | Johnson |
| **User ID** | marcus.johnson |
| **Email** | marcus.johnson@senecaTech.com |
| **Phone** | +1-647-555-0147 |
| **Department** | Food & Beverage |
| **Title** | F&B Director |
| **Manager** | [VP of Operations — external role] |
| **Active** | Yes |
| **Roles** | service_portal_user, group_manager |
| **Groups** | F&B Operations, Guest Services Notifiers |
| **Password Policy** | Standard user account |

**Role Assignments:** Department-level incident submitter and notification recipient

---

#### **User 7: David Okafor — Valet Services Manager**

| Field | Value |
| --- | --- |
| **First Name** | David |
| **Last Name** | Okafor |
| **User ID** | david.okafor |
| **Email** | david.okafor@senecaTech.com |
| **Phone** | +1-647-555-0148 |
| **Department** | Valet Services |
| **Title** | Valet Services Manager |
| **Manager** | [VP of Guest Services — external role] |
| **Active** | Yes |
| **Roles** | service_portal_user, group_manager |
| **Groups** | Valet Operations, Guest Services Notifiers |
| **Password Policy** | Standard user account |

**Role Assignments:** Department-level incident submitter and notification recipient

---

### Group Records Created

#### **Group 1: Hotel IT Operations**

| Field | Value |
| --- | --- |
| **Group Name** | Hotel IT Operations |
| **Description** | Primary incident response team for hotel IT systems |
| **Manager** | Anas Nehri |
| **Members** | Anas Nehri, Wendy Chen |
| **Email** | hotel-it-ops@senecaTech.com |
| **Assignment Rules** | All incidents assigned to this group by default (workflows reassign based on department) |

**Purpose:** Central IT incident queue; serves as triage point before department-specific routing

---

#### **Group 2: Revenue Systems Critical**

| Field | Value |
| --- | --- |
| **Group Name** | Revenue Systems Critical |
| **Description** | Escalation group for P1/P2 incidents affecting POS, reservation, or revenue systems |
| **Manager** | Matthew Rodriguez |
| **Members** | Anas Nehri, Wendy Chen, Matthew Rodriguez |
| **Email** | revenue-systems@senecaTech.com |
| **Assignment Rules** | Auto-assign when: Impact=Critical AND System in [POS, Reservation, Revenue Management] |

**Purpose:** Fast-track escalation for revenue-impacting incidents

---

#### **Group 3: Guest Services Notifiers**

| Field | Value |
| --- | --- |
| **Group Name** | Guest Services Notifiers |
| **Description** | Multi-department coordination group for guest-impacting incidents |
| **Manager** | Boris Kowalski |
| **Members** | Boris Kowalski, Priya Desai, Marcus Johnson, David Okafor, Matthew Rodriguez |
| **Email** | guest-services-notify@senecaTech.com |
| **Notification Trigger** | When: Impact=Guest_Facing AND Priority in [P1, P2] |

**Purpose:** Ensures all guest-facing departments know about outages affecting guest experience

---

#### **Group 4: Spa & Wellness Team**

| Field | Value |
| --- | --- |
| **Group Name** | Spa & Wellness Team |
| **Description** | Spa booking system and amenity issues |
| **Manager** | Priya Desai |
| **Members** | Priya Desai, [2-3 spa staff] |
| **Email** | spa-team@senecaTech.com |

---

#### **Group 5: F&B Operations**

| Field | Value |
| --- | --- |
| **Group Name** | F&B Operations |
| **Description** | Restaurant and bar POS, payment processing, service coordination |
| **Manager** | Marcus Johnson |
| **Members** | Marcus Johnson, [bar manager], [restaurant manager] |
| **Email** | fb-operations@senecaTech.com |

---

#### **Group 6: Valet Operations**

| Field | Value |
| --- | --- |
| **Group Name** | Valet Operations |
| **Description** | Valet management system, vehicle tracking, arrival coordination |
| **Manager** | David Okafor |
| **Members** | David Okafor, [lead valet], [shift supervisors] |
| **Email** | valet-operations@senecaTech.com |

---

### Role Definitions (Custom RBAC Configuration)

#### **Role: admin**
- **Description:** Full ServiceNow platform access
- **Capabilities:** Create/modify users, configure business rules, access admin console, modify data model
- **Assigned To:** Anas Nehri only
- **Risk Level:** Critical

#### **Role: itil**
- **Description:** ITSM professional — incident, problem, and knowledge management
- **Capabilities:** Create/update/resolve incidents and problems, read knowledge base
- **Assigned To:** Anas Nehri, Wendy Chen, Matthew Rodriguez
- **Risk Level:** Medium

#### **Role: knowledge_manager**
- **Description:** Knowledge base administration and content governance
- **Capabilities:** Create, review, approve, publish KB articles; manage categories
- **Assigned To:** Anas Nehri
- **Risk Level:** Medium

#### **Role: knowledge_contributor**
- **Description:** Submit KB articles for review (requires manager approval)
- **Capabilities:** Create KB article drafts; cannot publish without approval
- **Assigned To:** Wendy Chen, hotel staff
- **Risk Level:** Low

#### **Role: sla_admin**
- **Description:** Service Level Agreement configuration and monitoring
- **Capabilities:** Create SLA definitions, set targets, monitor compliance, breach notifications
- **Assigned To:** Anas Nehri only
- **Risk Level:** High (affects guest SLAs)

#### **Role: report_admin**
- **Description:** Custom report and dashboard creation
- **Capabilities:** Build reports, create dashboards, modify visualizations, set KPIs
- **Assigned To:** Anas Nehri, Matthew Rodriguez
- **Risk Level:** Medium

#### **Role: report_viewer**
- **Description:** Access to predefined reports and dashboards (read-only)
- **Capabilities:** Run reports, view dashboards, export data (no creation)
- **Assigned To:** Matthew Rodriguez, department managers
- **Risk Level:** Low

#### **Role: service_portal_user**
- **Description:** End user access to service portal
- **Capabilities:** Submit incidents, view KB articles, track ticket status
- **Assigned To:** All hotel staff (Boris, Priya, Marcus, David, others)
- **Risk Level:** Low

#### **Role: group_admin**
- **Description:** Manage group membership and incident assignment
- **Capabilities:** Add/remove users from groups, manage group settings
- **Assigned To:** Matthew Rodriguez, department managers
- **Risk Level:** Low

#### **Role: group_manager**
- **Description:** Coordinate group notifications and incident response
- **Capabilities:** Receive incident notifications, send group communications
- **Assigned To:** All department managers (Boris, Priya, Marcus, David)
- **Risk Level:** Low

---

### Steps Performed

1. **Created user accounts** with complete profile information:
   - First/last name, user ID, email (@senecaTech.com), phone
   - Department, title, reporting manager
   - Active status, password policy

2. **Assigned roles** based on job function and responsibility level:
   - Anas (admin, itil, knowledge_manager, sla_admin, report_admin)
   - Wendy (itil, knowledge_contributor)
   - Matthew (itil, report_viewer, group_admin)
   - Boris & Department Managers (service_portal_user, group_manager)

3. **Created groups** representing organizational structure:
   - Hotel IT Operations (primary incident queue)
   - Revenue Systems Critical (escalation group)
   - Guest Services Notifiers (multi-department coordination)
   - Department teams (Spa, F&B, Valet, Front Office)

4. **Configured RBAC policies:**
   - Field-level visibility rules (e.g., Wendy sees Incident.Assignment but not Cost fields)
   - Table-level access control (Wendy can see only assigned incidents)
   - Workflow approval routing (Matthew approves P1 escalations)

5. **Set up group notification logic:**
   - Business rules trigger when incident created with Impact=Guest_Facing
   - Automatically adds Guest Services Notifiers to notification recipients
   - Email templates send context-specific messages to each department

6. **Configured delegation settings:**
   - Anas can delegate approval authority to Wendy or Matthew
   - Boris can delegate incident submission to assistant managers

### Access Control Matrix (Table-Level)

| Table | Anas Nehri | Wendy Chen | Matthew Rodriguez | Boris Kowalski | Priya Desai |
| --- | --- | --- | --- | --- | --- |
| **Incident** | Create, Read, Update, Delete, Configure | Create, Read, Update (assigned only) | Create, Read, Update (all), Approve escalations | Submit via Portal, Read own | Read notification-related |
| **Problem** | Create, Read, Update, Delete, Configure | Create, Read, Update (assigned) | Create, Read, Update | Read only | Read only |
| **Knowledge** | Full access | Create drafts, Read published | Read only | Read published | Read published |
| **Users** | Full access | Cannot create/modify | Cannot create/modify | Cannot access | Cannot access |
| **Groups** | Create, manage members | Cannot modify | Manage members for assigned groups | Manage Guest Services Notifiers | Manage Spa team |
| **Business Rules** | Create, modify, delete | Cannot access | Cannot access | Cannot access | Cannot access |
| **Reports** | Create, modify, delete | Cannot access | Create, modify | Read predefined | Read predefined |

### Screenshots Placeholder

```
[Screenshot 1: User List — Anas Nehri, Wendy Chen, Matthew Rodriguez, Boris Kowalski, Priya Desai, Marcus Johnson, David Okafor]
[Screenshot 2: Anas Nehri User Record — Admin role, email anas.nehri@senecaTech.com, manager field]
[Screenshot 3: Wendy Chen User Record — itil role, knowledge_contributor, manager=Anas Nehri]
[Screenshot 4: Role List — admin, itil, knowledge_manager, sla_admin, report_admin, service_portal_user]
[Screenshot 5: Hotel IT Operations Group — Members: Anas, Wendy; Manager: Anas]
[Screenshot 6: Guest Services Notifiers Group — Members: Boris, Priya, Marcus, David, Matthew]
[Screenshot 7: Access Control Matrix visualization]
```

### Real-World Relevance

This RBAC configuration reflects production ServiceNow design principles:

1. **Principle of Least Privilege:** Users have only the access required for their role
   - Wendy can resolve incidents but cannot create business rules
   - Boris can submit incidents but cannot modify system configuration
   - Anas has full access but is the only admin

2. **Separation of Duties:** Different roles prevent conflicts of interest
   - Anas (system admin) cannot approve SLA overrides that affect his own metrics
   - Wendy (technician) cannot modify SLAs that govern her response time
   - Matthew (manager) approves escalations that Wendy initiates

3. **Department-Level Isolation:** Groups ensure cross-functional coordination
   - Guest Services Notifiers group reaches all departments simultaneously
   - Revenue Systems Critical group escalates without leaving paper trail
   - Spa team group keeps wellness system issues contained

4. **Audit Trail Readiness:** Role assignments are logged for compliance
   - Hotel may be subject to PCI-DSS (payment processing), GDPR (guest data)
   - Every user action is traced back to their user ID and role
   - Anas can generate audit reports showing who accessed what data

5. **Scalability:** This model accommodates hotel growth
   - New front-desk staff: add to Front Office Team group, assign service_portal_user role
   - New IT hire: add to Hotel IT Operations, assign itil role, manager=Anas
   - New department (e.g., Concierge): create new group, add manager to Guest Services Notifiers

---

## Part 1 — System Architecture & Data Model Design (As Implemented by Anas Nehri)

### Objective

Design a custom ITSM data model that captures hotel-specific metrics and 
automates multi-department coordination workflows.

### Problem Statement (Business Requirement)

**The Challenge:**
When the reservation system goes down during Friday evening check-in, the front 
desk doesn't know about it until they try to check in the first guest. Meanwhile:
- Housekeeping doesn't know which rooms need to be ready
- The spa doesn't know which guests have confirmed appointments
- Valet doesn't have arrival data
- Guest Services doesn't know to prepare proactive communication

**The Opportunity:**
ServiceNow Incident Management, properly configured, can:
1. Detect guest-facing system failures in real-time
2. Automatically route to the right IT team
3. Automatically notify all affected departments
4. Calculate revenue impact in real-time
5. Track guest recovery actions (room upgrades, comps) for follow-up

**The Solution:**
Custom incident table with hotel-specific fields + automated business rules 
+ multi-group routing workflows = coordinated incident response before guests 
are impacted.

### Data Model — Incident Table Customization

**Standard ServiceNow Incident Fields (Used As-Is):**
- Incident Number (auto-generated)
- Caller (who reported)
- Category, Subcategory
- Short Description, Description
- Priority (calculated from Impact + Urgency)
- State (New, In Progress, On Hold, Resolved, Closed)
- Assignment Group, Assigned To
- Work Notes, Resolution Notes
- Created/Updated timestamps

**Custom Hotel-Specific Fields Added to Incident Table:**

#### **Field: Guest-Facing Impact**
- **Type:** Checkbox
- **Label:** "Guest-Facing System?"
- **Default:** No
- **Business Logic:** If checked, incident is routed to Guest Services Notifiers group
- **Used By:** Business rules for automatic multi-department notification
- **Example:** Check this for reservation, POS, spa booking, valet systems; do NOT check for admin tools

#### **Field: Affected Department(s)**
- **Type:** Multi-select choice list
- **Options:** Front Desk, Housekeeping, F&B Restaurant, F&B Bar, Spa & Wellness, Valet, Concierge, Revenue Management, Guest Services
- **Default:** Empty (required)
- **Business Logic:** Triggers notifications to specific department groups
- **Example:** If "F&B Bar" selected, incident adds Marcus Johnson's F&B Operations group to notification list

#### **Field: Estimated Guest Impact Count**
- **Type:** Integer
- **Label:** "# of Guests Affected (if applicable)"
- **Range:** 0-1000
- **Default:** 0
- **Business Logic:** Used to calculate incident priority override (1+ guest = minimum P3)
- **Example:** Restaurant system down = select "10" (estimated covers in service); goes to P2 minimum regardless of urgency

#### **Field: Revenue System Impact?**
- **Type:** Checkbox
- **Label:** "Does this affect revenue systems? (POS, Reservation, Revenue Mgmt)"
- **Default:** No
- **Business Logic:** If checked, auto-escalate to Revenue Systems Critical group, trigger CFO notification
- **Example:** Main POS terminal down = check this box; Matthew Rodriguez and Anas get immediate escalation alert

#### **Field: Estimated Hourly Revenue Impact (if known)**
- **Type:** Currency
- **Label:** "Estimated revenue loss per hour ($)"
- **Range:** $0 - $50,000
- **Default:** $0
- **Business Logic:** Calculates total revenue impact based on incident duration
- **Example:** Bar handheld POS fails during Saturday night service = enter $500/hour; if incident is 1 hour, system logs $500 revenue impact in reporting dashboard

#### **Field: Affected System(s)**
- **Type:** Multi-select choice list
- **Options:** Reservation System (PMS), POS Restaurant Terminal, POS Bar Terminal, POS Room Service Terminal, Spa Booking System, Valet Management System, Guest WiFi, Staff WiFi, Door Lock System, Email, Reporting/BI, Other
- **Default:** Empty (required)
- **Business Logic:** Links incident to system-specific playbooks, KB articles, related problems
- **Example:** Reservation system slow = select "Reservation System (PMS)"; system auto-links to any existing problem records about DB performance

#### **Field: Root Cause Category**
- **Type:** Choice list (updated after investigation)
- **Options:** Hardware Failure, Software Bug, Database Lock, Network Connectivity, Power/Infrastructure, Configuration Error, Vendor Issue, Unknown
- **Default:** Unknown
- **Business Logic:** Links to problem management; groups similar incidents for pattern analysis
- **Example:** "Database Lock" — system flags this for problem management review; Anas notified to investigate if this is a recurrence

#### **Field: Escalation Decision**
- **Type:** Text (required for P1 incidents)
- **Label:** "Why did you escalate to this priority? Business justification"
- **Max Length:** 500 characters
- **Business Logic:** Audit trail for SLA breach disputes
- **Example:** "Guest check-in surge during Friday evening arrival peak; 25 guests queued. Front desk called. Estimated 15-20 missed room upgrades ($750-1,250 revenue impact)."

#### **Field: Guest Service Recovery Actions**
- **Type:** Multi-line text (populated during incident)
- **Label:** "What actions did we take to recover guest experience?"
- **Examples:**
  - "Room upgrade: Executive Suite (value $150) to Guest 412"
  - "Comp: Spa treatment credit $100 to Guest 308"
  - "Apology call: VP Guest Services to guests in check-in queue"
- **Business Logic:** Tracks all recovery costs for reporting; used to calculate total incident cost
- **Reporting:** Sums recovery costs by month to show guest satisfaction investment

#### **Field: Incident Linked Problems**
- **Type:** Reference (read-only)
- **Label:** Related Problem Records
- **Relationship:** 1 incident can link to 1+ problems
- **Auto-populated:** When incident is created, business rule searches for existing problems with same root cause
- **Example:** 3rd database lock incident in 2 weeks → system auto-links to HOTL-PRB-001, adding to problem statistics

#### **Field: Multi-Department Notification Log**
- **Type:** Text (auto-populated)
- **Label:** "Which departments were notified and when?"
- **Format:** "[09:05 AM] Front Desk (Boris Kowalski) | [09:05 AM] Housekeeping | [09:05 AM] Spa (Priya Desai) | [09:06 AM] Revenue Mgmt"
- **Business Logic:** Audit trail for guest communication; shows response speed
- **Example:** Reservation system incident; log shows Spa was notified within 30 seconds of incident creation

---

### Business Rules Configuration (Implemented by Anas)

#### **Business Rule 1: Auto-Route Guest-Facing Incidents**

**Name:** "Auto-assign guest-facing incidents to Guest Services Notifiers"

**Trigger:** When incident created or updated

**Condition:**
```
IF incident.guest_facing = true
  AND incident.state IN ["New", "In Progress"]
THEN
```

**Action:**
```
1. Add incident.Guest_Services_Notifiers to assignment_group
2. Send email to guest_services_notifiers@senecaTech.com with:
   - Incident number, title, affected department(s)
   - Guest impact count
   - Current state and assigned technician
   - ETA for update
3. Create task for Matthew Rodriguez to confirm group assignment
```

**Business Impact:** Within 60 seconds of incident creation, all department managers (Priya, Marcus, David) receive notification so they can:
- Alert their teams of potential guest communication needs
- Prepare service recovery options
- Coordinate coverage

---

#### **Business Rule 2: Calculate Priority Based on Guest Impact**

**Name:** "Override priority for incidents with guest impact"

**Trigger:** When incident created or updated

**Condition:**
```
IF incident.estimated_guest_impact_count >= 1
THEN Priority = At Minimum P3 (if not already higher)

IF incident.estimated_guest_impact_count >= 5
THEN Priority = At Minimum P2

IF incident.estimated_guest_impact_count >= 20 
  OR incident.revenue_system_impact = true
THEN Priority = P1 (Critical)
```

**Business Impact:** A system affecting only 1 guest is not P4 (as calculated by standard urgency/impact). It's at least P3. This ensures guest-impacting incidents never get deprioritized below low-value administrative tasks.

---

#### **Business Rule 3: SLA Auto-Escalation for Revenue Systems**

**Name:** "Escalate revenue system incidents immediately"

**Trigger:** When incident created

**Condition:**
```
IF incident.revenue_system_impact = true
  AND incident.priority IN [P1, P2]
THEN
```

**Action:**
```
1. Add Revenue_Systems_Critical group to assignment
2. Send escalation email to:
   - matthew.rodriguez@senecaTech.com (IT Manager)
   - anas.nehri@senecaTech.com (IT Admin)
3. Create notification task: "P1/P2 revenue system incident - immediate response required"
4. Set SLA clock to start counting from NOW (not incident creation time)
5. Add work note: "[SYSTEM] Auto-escalated to Revenue Systems Critical group"
```

**Business Impact:** No P1/P2 revenue incident sits in a queue. It's in the hands of senior decision-makers within 1 minute of creation.

---

### Workflow: Multi-Department Notification Process

**Workflow Name:** "Guest-Facing Incident Notification Cascade"

**Trigger:** Incident created with guest_facing = true

**Steps:**

1. **Immediate Notification (T+0 to T+30 seconds)**
   - Incident assigned to Hotel IT Operations queue
   - Email sent to Guest Services Notifiers group
   - Matthew Rodriguez receives escalation notification
   
2. **Department-Specific Routing (T+30 to T+60 seconds)**
   - Business rule queries affected_departments field
   - For each selected department, add its group to watchers:
     - Selected "F&B"? → Add F&B Operations group (Marcus, bar mgr, restaurant mgr)
     - Selected "Spa"? → Add Spa & Wellness group (Priya + spa staff)
     - Selected "Valet"? → Add Valet Operations group (David + valet staff)
   - Send context-specific email to each group

3. **Assignment & Priority Calculation (T+60 to T+120 seconds)**
   - Incident moved out of Hotel IT Operations queue
   - Reassigned to appropriate technical group (Revenue Systems Critical, WiFi Team, etc.)
   - SLA timer starts
   - Wendy Chen receives "Assigned Incident" notification

4. **Manager Acknowledgment Loop (T+120 to T+300 seconds)**
   - Matthew Rodriguez confirms guest-facing incident receipt
   - Adds work note: "Notified [X] departments at [time]"
   - Sends follow-up message to department managers:
     - "Reservation system slow during check-in peak. Estimated 10 min resolution. Please advise guests 5 min delays may occur."

5. **Incident Resolution (T+Var)**
   - When incident resolved, workflow sends "All Clear" notification to all department groups
   - Each department manager responds with: "Confirmed, will advise our teams"
   - Incident moved to Closed state
   - Revenue impact calculated: Duration × $Hourly Impact

6. **Retrospective Logging (T+24 hours)**
   - Scheduled job queries all closed guest-facing incidents from past 24 hours
   - Generates summary for management dashboard:
     - Incidents by type (reservation, POS, WiFi, etc.)
     - Total guest impact (# of people affected)
     - Total revenue impact
     - All department notifications sent
     - Guest recovery actions taken (comps, upgrades)

---

### Integration Touchpoint: PMS (Property Management System)

**Objective:** Bidirectional sync between ServiceNow and hotel Property Management System so that:
- When guest checks in via PMS, their profile is available in ServiceNow incidents
- When IT incident affects a guest, PMS can flag that guest's profile for service recovery
- Guest complaints logged in PMS can create ServiceNow incidents

**Integration Points (Designed by Anas, REST API-Based):**

1. **Guest Profile Sync (PMS → ServiceNow)**
   - API endpoint: `GET /api/pms/guests/{guest_id}`
   - Data retrieved: Guest name, room number, check-in/checkout, department reservations (spa, dining, activities)
   - Used in: Incident work notes for context ("Guest in Room 412 has 2 PM spa appointment")
   - Refresh: Every 15 minutes during occupancy period (6 AM - 11 PM)

2. **Service Recovery Logging (ServiceNow → PMS)**
   - API endpoint: `POST /api/pms/guests/{guest_id}/service_recovery`
   - Data sent: Recovery action (room upgrade, comp, apology call), amount/value, reason
   - Used for: Guest profile to show they were impacted and recovery was provided
   - Prevents: Duplicate comps (if guest called front desk separately)

3. **Guest Complaint to Incident (PMS → ServiceNow)**
   - When guest logs complaint in PMS about IT service
   - Webhook triggers ServiceNow incident creation
   - Pre-populated: Guest name, room, complaint text, guest rating
   - Routed to: Matthew Rodriguez for investigation

---

### Implementation Artifacts (Created by Anas)

#### **Data Model Diagram**
```
INCIDENT (custom table)
├── Standard ITSM Fields
│   ├── Incident Number
│   ├── Caller, Category, Priority
│   ├── State, Assignment Group, Assigned To
│   └── Work Notes, Resolution Notes
├── Hotel-Specific Fields
│   ├── Guest-Facing Impact (checkbox)
│   ├── Affected Department(s) (multi-select)
│   ├── Estimated Guest Impact Count (integer)
│   ├── Revenue System Impact? (checkbox)
│   ├── Estimated Hourly Revenue Impact ($)
│   ├── Affected System(s) (multi-select)
│   ├── Root Cause Category (choice list)
│   ├── Escalation Decision (text)
│   ├── Guest Service Recovery Actions (multi-line)
│   ├── Multi-Department Notification Log (auto-populated)
│   └── Linked Problems (reference)
└── Calculations (read-only, auto-calculated)
    ├── Total Revenue Impact = Hourly Impact × Duration
    ├── Total Recovery Cost = Sum of all guest recovery actions
    ├── Net Business Impact = Revenue Impact + Recovery Cost
    └── Guest Satisfaction Impact Score

PROBLEM (linked to incidents)
├── Problem Number
├── Related Incidents (list)
├── Root Cause
├── Recurring? (yes/no)
├── Permanent Fix Assigned To (Anas Nehri usually)
└── Business Case (ROI for fix)

KNOWLEDGE (linked to incidents & problems)
├── Article Title
├── Applies To (system/department/audience)
├── Issue Description
├── Step-by-Step Resolution
├── Related Incidents (backlinks)
└── Publish Status (Draft/Approved/Published)
```

#### **Configuration Runbook** (Created by Anas)
Document: "ServiceNow ITSM Platform Setup for Hotel Operations"
- Section 1: User & Role Configuration (steps 1-47)
- Section 2: Group Configuration (steps 48-62)
- Section 3: Custom Field Configuration (steps 63-98)
- Section 4: Business Rules Setup (steps 99-127)
- Section 5: Workflow Configuration (steps 128-156)
- Section 6: Integration Setup (steps 157-189)
- Section 7: Reporting & Dashboard Configuration (steps 190-220)

---

### Steps Performed (System Implementation)

1. **Analyzed hotel operations** to identify pain points:
   - Slow incident communication during guest-facing outages
   - Department managers finding out about issues via guest complaints, not IT
   - No visibility into guest impact or revenue loss
   - Manual notification process (emails, phone calls)

2. **Designed custom ITSM data model:**
   - Added 10 custom fields to Incident table specific to hotel metrics
   - Created field relationships and dependencies
   - Defined calculation formulas for revenue impact

3. **Configured business rules:**
   - 3 core business rules for auto-routing, priority override, escalation
   - Built SQL queries to identify guest-facing incidents
   - Set up automated notification triggers

4. **Built workflows:**
   - 6-step multi-department notification cascade
   - Timing optimized for 30-60 second end-to-end notification
   - Audit trail at each step for compliance

5. **Designed PMS integration:**
   - REST API specifications for guest data sync
   - Webhook configuration for complaint-to-incident creation
   - Error handling and retry logic

6. **Created documentation:**
   - Configuration runbook for future administrators
   - Architecture diagrams showing data flows
   - Process diagrams for notification cascade
   - Integration specifications for PMS team

### Screenshots Placeholder

```
[Screenshot 1: Incident Form — Custom fields visible (Guest-Facing Impact, Affected Dept, Guest Impact Count)]
[Screenshot 2: Business Rule List — "Auto-assign guest-facing incidents", "Override priority for guest impact", "Escalate revenue systems"]
[Screenshot 3: Workflow Designer — Multi-department notification cascade workflow with 6 decision nodes]
[Screenshot 4: Integration Configuration — PMS API endpoints configured, test results showing sync working]
[Screenshot 5: Data Model Diagram — Incident table relationships to Problem, Knowledge, Group tables]
```

### Real-World Relevance

This system architecture level work is what separates **Tier 2 CSA/System Administrators** 
from lower-tier technicians or consultants:

1. **Custom data modeling** — Most ServiceNow users configure existing tables. Architects add custom fields and table relationships to capture business-specific metrics (guest impact, revenue impact).

2. **Workflow automation** — Handwritten notification procedures can fail. Automated workflows ensure consistency and speed. Anas designed a system where department notifications happen in <30 seconds with zero human error.

3. **Business rule logic** — Priority calculations based on guest count, not just impact/urgency matrix. This is domain-specific logic that adds real business value.

4. **Integration design** — Connecting ServiceNow to external systems (PMS, revenue management, guest communication) requires API design, error handling, and data mapping skills.

5. **Runbook documentation** — After Anas leaves, another admin needs to understand what was built and why. The runbook is how knowledge is transferred.

**This work justifies Tier 2 compensation** ($85-120k+) because it requires:
- Deep ServiceNow platform knowledge
- Business process analysis
- Systems thinking (how one change affects multiple systems)
- Ability to translate hotel operations requirements into technical specifications

---

## Part 2 — Incident Management (Hotel Operations Context)

### Objective

Demonstrate how the system architecture designed in Part 1 performs in production. 
Create and manage realistic incidents showing multi-department coordination in action.

### Incident Priority Matrix (Hotel-Specific)

| Priority | Guest Impact | Revenue Impact | SLA Response | System Example |
| --- | --- | --- | --- | --- |
| **P1 — Critical** | Multiple guests, service down | >$5k/hr lost | 15 minutes | Reservation system offline during check-in peak |
| **P2 — High** | 1+ guest or dept workflow blocked | $1k-5k/hr impact | 1 hour | Spa booking system offline, 50 appointments affected |
| **P3 — Moderate** | Workaround available, delay | <$1k/hr impact | 4 hours | WiFi slow in guest wing (but 4G available) |
| **P4 — Low** | No guest impact, admin delay | $0 impact | Next business day | Staff WiFi down, can use wired connection |

### Incidents Created & Managed

**[The 8 incidents remain the same as in the previous document, but NOW they show how the system RESPONDS due to Anas's architecture]**

#### **HOTL001 — Main POS Terminal at Restaurant — Payment Processing Failure (P1)**

**Incident Created:** Friday 7:15 PM  
**Submitter:** Marcus Johnson (F&B Director) via Service Portal  
**Assigned To:** Hotel IT Operations queue → Routed to Wendy Chen by business rule

**System Actions (Automated by Anas's Configuration):**

1. **T+0:00 (Incident creation)**
   - Wendy submits ticket via portal: "Restaurant POS terminal unresponsive"
   - Incident number auto-generated: INC0001
   - Form shows custom fields: Guest-Facing Impact [✓], Affected Dept [F&B Restaurant], Guest Impact Count [120 estimated covers]
   - Revenue System Impact [✓] checked by Wendy
   - Estimated Hourly Revenue Impact: [$500/hour entered]

2. **T+0:30 (Business rule fires)**
   - Priority auto-calculated: 120 guests affected = P1 minimum
   - Revenue system = P1 minimum
   - Incident Priority Set to P1 — Critical
   - Business rule creates work note: "[SYSTEM] Priority escalated to P1: 120 guests estimated, revenue system impact, $500/hr loss"

3. **T+0:45 (Guest Services Notifiers group notified)**
   - Incident routed to Revenue Systems Critical group
   - Email sent to:
     - Matthew Rodriguez (matthew.rodriguez@senecaTech.com)
     - Anas Nehri (anas.nehri@senecaTech.com)
     - Marcus Johnson (marcus.johnson@senecaTech.com) - confirmation of escalation
   - Email subject: "🔴 P1 REVENUE SYSTEM: Restaurant POS Down — Immediate Response Required"
   - Email body:
     ```
     Incident: INC0001 (P1 — Critical)
     System: POS Restaurant Terminal
     Impact: Payment processing unavailable
     Affected: 120 guests in service, $500/hr revenue loss
     Guest Impact Count: 120
     
     Assigned Technician: Wendy Chen (wen
dy.chen@senecaTech.com)
     SLA Response: 15 minutes (EXPIRES: 7:30 PM)
     
     Actions:
     1. Wendy to investigate immediately
     2. Marcus to notify kitchen and servers (recovery strategy)
     3. Matthew to monitor response time
     
     This incident has been assigned to Revenue Systems Critical escalation group.
     ```

4. **T+1:15 (Multi-department notification cascade triggered)**
   - Workflow checks: Affected Dept = [F&B Restaurant]
   - Add F&B Operations group to incident watchers
   - Send message to all F&B staff: "POS terminal down, ETA 15 min. Servers: Continue taking orders on paper. We'll process payments once system returns."

5. **T+3:00 (Incident resolved)**
   - Wendy diagnoses: loose power cable (reseated)
   - Terminal boots and restarts
   - Wendy adds work note: "Power cable was loose due to back-of-house cleaning. Reseated connection. Terminal back online."
   - Incident moved to Resolved state
   - Marcus confirms via follow-up: "Restaurant running normal, payments processing"

6. **T+3:30 (Automatic post-incident summary generated)**
   - Incident closed by Wendy
   - System calculates:
     - Duration: 3 minutes 30 seconds
     - Revenue Impact: $500/hr × (3.5 min / 60 min) = ~$29 actual loss
     - Guest recovery actions: None needed (brief enough)
     - F&B team notification response time: <1 minute
   - Work note auto-added: "[SYSTEM] Incident duration: 3 min 30 sec. Revenue impact: $29. Response SLA: MET (3 min vs. 15 min target)."
   - Problem Management flag added: "Loose power cables — third incident this quarter. Flag for preventive fix."

---

#### **HOTL002 — Guest Reservation System Slow During Check-in Peak (P1)**

**Incident Created:** Friday 5:30 PM  
**Submitter:** Boris Kowalski (Front Office Manager) via Service Portal  
**Assigned To:** Hotel IT Operations queue → Business rule immediately escalates

**System Actions (Showing Architecture in Action):**

1. **T+0:00**
   - Boris logs incident: "Reservation system responding slowly during check-in surge"
   - Fills in: Guest-Facing Impact [✓], Affected Dept [Front Desk, Housekeeping], Guest Impact Count [25 guests waiting]
   - Estimated Hourly Revenue Impact: [$1,200/hour — 20 missed upsells at $60 average]

2. **T+0:45 (Critical escalation fires)**
   - Priority auto-calculated: 25 guests + revenue impact + reservation system = **P1**
   - Business rule auto-escalates to Revenue Systems Critical group
   - Email sent to Matthew, Anas with SLA timer: **15 minutes to respond (EXPIRES: 5:45 PM)**
   - Matthew receives "Guest Services Notifiers" notification too, so Priya (Spa), Marcus (F&B), David (Valet) are alerted that a guest-facing issue is in progress

3. **T+1:00 (Multi-department notification cascade)**
   - Workflow queries Affected Departments: [Front Desk, Housekeeping]
   - Add watchers: Front Office Team, Housekeeping Management
   - Send email to Boris, housekeeping manager:
     ```
     🔴 ALERT: Reservation system is degraded
     - System response time: 20-45 seconds (normally 2-3 sec)
     - Guests in check-in queue: ~25
     - Estimated issue duration: 10-15 minutes
     - Revenue at risk: $1,200/hour
     
     ACTIONS:
     - Front Desk: Tell guests "Brief delay, 5 minutes maximum"
     - Housekeeping: Hold room service until system restored
     - IT: Investigating cause now (Matthew Rodriguez)
     ```

4. **T+1:30 (Wendy investigates)**
   - Wendy logs into database server
   - Identifies: nightly backup process still running, locking guest_bookings table
   - Other queries queued waiting for lock release
   - Wendy adds work note: "CAUSE IDENTIFIED: Backup job started at 2 AM was not properly terminated. Database lock blocking all guest queries. REMEDIATION: Killing stale backup process now."

5. **T+2:00 (Business rule updates affected departments)**
   - Incident status: "In Progress" → Root Cause Found
   - New work note auto-added: "[SYSTEM] Root cause identified by Wendy Chen. ETA for resolution: 5 minutes."
   - Workflow sends update email to Boris and housekeeping manager:
     ```
     ✓ ROOT CAUSE FOUND: Database lock from stale backup process
     ✓ RESOLUTION IN PROGRESS: Wendy canceling backup process
     ✓ ETA: System restored by 5:40 PM (10 minutes from now)
     
     RECOMMEND: Tell front desk guests "System will be fully restored in 10 minutes"
     ```

6. **T+2:15 (Wendy executes fix)**
   - Terminates backup process
   - Database lock cleared
   - Query performance returns to normal (2-3 second response)
   - Incident moved to Resolved

7. **T+2:45 (Post-incident multi-department coordination)**
   - Matthew adds work note: "Incident resolved. Matthew Rodriguez notifying all departments."
   - Workflow auto-sends "All Clear" email to all department managers:
     ```
     ✓ RESOLVED: Reservation system now operating at full speed
     - Response time: 2-3 seconds (normal)
     - Check-in can resume
     - Housekeeping room assignments available
     - Spa bookings visible
     - Valet arrival data updating
     
     PERMANENT FIX: Moving nightly backup from 2 AM to 2:30 AM to reduce collision with late-night queries.
     
     Incident: INC0002 (P1 — Resolved in 12 minutes)
     Revenue Impact: $240 (12 min × $1,200/hr ÷ 60)
     Guest Recovery: 0 (incident brief enough that no proactive comp needed)
     ```

8. **T+24:00 (Automated overnight summary)**
   - Scheduled job runs: "Yesterday's Incidents Summary"
   - Pulls all closed guest-facing incidents
   - Generates report:
     ```
     FRIDAY INCIDENT SUMMARY
     =====================
     Total Guest-Facing Incidents: 3
     Average Response Time: 8 minutes (SLA target: 15 min ✓ MET)
     Total Guest Impact: 158 people affected
     Total Revenue Impact: $287 (recovered quickly due to fast response)
     Total Guest Recovery Cost: $0 (no comps needed for quick resolutions)
     Net Business Impact: -$287 (minor loss, well-managed)
     
     Department Notifications Sent: 18 (across 5 departments)
     Average Notification Delivery Time: 45 seconds
     Guest Satisfaction Recovery Score: 9/10 (proactive communication prevented escalation)
     ```

---

### Steps Performed (Incident Management)

1. **Created 8 incidents** using custom incident form with hotel-specific fields
2. **Filled in required business metrics:**
   - Guest Impact Count (actual or estimated)
   - Revenue System Impact (yes/no)
   - Estimated Hourly Revenue Loss
   - Affected Departments (auto-routes to group)

3. **Observed system automation:**
   - Priority auto-calculated based on guest count + revenue impact
   - Business rules triggered notifications within 60 seconds
   - Multi-department groups added to watchers automatically
   - Workflow sent context-specific messages to each department

4. **Resolved incidents** with detailed work notes
5. **System auto-calculated:**
   - Revenue impact based on duration
   - Guest recovery costs
   - SLA compliance metrics

### Screenshots Placeholder

```
[Screenshot 1: HOTL002 Incident Form — Custom fields showing Guest-Facing [✓], Dept [Front Desk, Housekeeping], Count [25], Revenue Impact [✓], Hourly Loss [$1,200]]
[Screenshot 2: HOTL002 Work Notes showing timeline:
  - T+0:00 Incident created
  - T+0:45 Business rule priority escalation
  - T+1:30 Root cause identified (DB lock)
  - T+2:15 Remediation complete
]
[Screenshot 3: Email notification to Matthew/Anas showing P1 escalation, SLA timer, guest impact]
[Screenshot 4: Workflow execution log showing 6 steps completed, all departments notified]
[Screenshot 5: Multi-Department Notification Log showing:
  T+0:45 Front Desk (Boris) notified
  T+0:45 Matthew Rodriguez notified
  T+1:00 Housekeeping notified
  T+1:00 Spa (Priya) standby notification
  T+2:45 All-Clear message to 5 departments
]
[Screenshot 6: Automated Post-Incident Summary showing revenue impact calculation, recovery cost, SLA compliance]
```

### Real-World Relevance

This incident management workflow demonstrates why hotels value ServiceNow over 
generic help desk ticketing systems:

1. **Millisecond notification speed** — P1 incident notification goes to 4+ people simultaneously. Human-based notification (emails, phone calls) would take 5-10 minutes.

2. **Consistent execution** — Business rules ensure every P1 incident with 20+ guests is escalated to the same people the same way. No incidents slip through cracks.

3. **Business metrics visibility** — Every incident now comes with revenue impact calculation. The hotel can see: "This incident cost us $287." That's data for capacity planning.

4. **Cross-functional coordination without meetings** — Without ServiceNow, multiple department managers would need to have separate conversations about this incident. With ServiceNow, they all get one orchestrated notification.

5. **Audit trail for disputes** — If someone claims the restaurant was down for 2 hours, Anas can show: "Incident INC0001 shows 3 min 30 sec duration, resolved by Wendy Chen, confirmed by Marcus Johnson in work notes."

---

## Part 3 — Problem Management (Recurring Issues & Root Cause)

### Objective

Identify patterns across incidents and create Problem records that prevent recurrence 
through permanent system fixes or process changes.

### Problem 1: HOTL-PRB-001 — Database Lock During Peak Hours

**Identified From:** HOTL002 incident (reservation system slow)

**Related Incidents:** 
- HOTL003 (spa booking system, same root cause)
- Future incidents (preventive capture)

**Problem Record Details:**

| Field | Value |
| --- | --- |
| **Problem Number** | HOTL-PRB-001 |
| **Title** | Recurring Database Locks During Peak Operating Hours |
| **Description** | Nightly backup process not properly terminating, leaving stale locks on guest_bookings and guest_amenities tables. During peak hours (5-7 PM check-in, 9-10 AM breakfast/spa), queries queue waiting for lock release, causing 20-45 second response delays. Affects 2+ systems and guest-facing operations. |
| **Created By** | Anas Nehri (detected pattern) |
| **Assigned To** | Anas Nehri (permanent fix) |
| **Status** | Open |
| **Priority** | High (revenue-impacting) |
| **Related Incidents** | HOTL002, HOTL003 |

**Root Cause Analysis:**
- Backup automation starts at 2 AM nightly
- Backup job does NOT include kill-stale-lock cleanup before termination
- If backup runs long (>30 min), and a query is locked, backup terminates but lock remains
- Next day, peak-hour queries hit the lock and queue up
- Lock not auto-released for 15-20 minutes

**Business Impact:**
- Reservation system: 2 incidents, avg 12 min impact each = ~$288 revenue loss per incident
- Spa booking: 1 incident, 8 min impact = $120 revenue loss
- Guest satisfaction: 25-50 guests affected per incident, recovery cost $0-300
- **Annual recurring cost: If this happens weekly, ~$20k/year in revenue impact alone**

**Recommended Permanent Fix:**

1. **Modify backup script** (execute before backup terminate):
   ```sql
   -- Kill any locks older than 5 minutes
   IF (SELECT lock_age) > 300 seconds THEN
     KILL BLOCKING_QUERY
   END IF
   
   -- Wait for pending queries to complete
   WAIT_FOR_LOCK_RELEASE (timeout=30 sec)
   
   -- Only then terminate backup
   BACKUP_TERMINATE
   ```

2. **Add monitoring alert:**
   - If lock persists >5 minutes during operational hours (7 AM - 11 PM)
   - Alert: matthew.rodriguez@senecaTech.com and anas.nehri@senecaTech.com
   - ServiceNow auto-creates incident if lock not cleared within 10 minutes

3. **Reschedule backup window:**
   - Move from 2 AM to 2:30 AM
   - Gives buffer for long-running queries to complete
   - Backup runs 2:30 AM - 3:00 AM (off-peak, isolated from peak hours)

4. **Implement query timeout:**
   - Set max query execution time to 5 minutes
   - Long-running queries auto-timeout rather than holding locks

**Implementation Timeline:**
- Week 1: Anas modifies backup script (2 hours)
- Week 1: Anas tests in PDI environment (4 hours)
- Week 2: Anas implements in production PDI
- Week 2: Monitor for 1 week (Anas tracks incidents)
- If no recurrence in 1 week: Close problem record as "Fixed"

**Cost-Benefit:**
- Implementation cost: ~10 hours Anas time (Tier 2 CSA = ~$100/hr = $1,000)
- Annual benefit: $20,000+ revenue protected
- **ROI: 20:1**

**Business Rule Enhancement (by Anas):**
Once fix implemented, add business rule:
```
IF incident.root_cause_category = "Database Lock"
  AND incident.state = "Resolved"
THEN
  Add work note: "Database lock issue detected. Check HOTL-PRB-001 for status of permanent fix."
  Create task for Anas: "Monitor lock incidents for recurrence pattern"
```

---

### Problem 2: HOTL-PRB-002 — Handheld POS Device Charging Infrastructure

**Identified From:** HOTL006 incident (bar handheld fails)

**Related Incidents:**
- Prior incident: Bar handheld failed (not in this lab, but documented by Wendy)
- Prior incident: Room service handheld failed (not in this lab)
- Potential future: Restaurant POS handheld at risk

**Problem Record Details:**

| Field | Value |
| --- | --- |
| **Problem Number** | HOTL-PRB-002 |
| **Title** | Handheld POS Device Charging Failures — Infrastructure Gap |
| **Description** | Over 2 months, 3+ handheld POS devices have failed due to defective charging cables. Current hotel inventory: Mix of older micro-USB and newer USB-C devices. No standard charging infrastructure. No spare cable inventory. No preventive replacement schedule. Each device failure causes 45-60 minute service disruption until backup device swapped. |
| **Created By** | Wendy Chen (detected recurring ticket) |
| **Assigned To** | Anas Nehri (procurement coordination) + Matthew Rodriguez (ops approval) |
| **Status** | Open |
| **Priority** | High (high revenue impact when failures occur) |
| **Related Incidents** | HOTL006 + 2 undocumented prior |

**Root Cause Analysis:**
- Hotel procured devices at different times (different USB standards)
- Devices rotate between high-traffic locations (bar, restaurant, room service)
- Charging cables wear out faster than expected (daily/heavy use)
- No spare inventory system
- No preventive replacement
- When device fails, manual recovery takes 45-60 minutes

**Business Impact Per Incident:**
- Bar POS down Saturday night: $400-500 lost revenue
- Room service POS down during breakfast: $200-300 lost revenue
- Restaurant POS down during dinner rush: $500-700 lost revenue
- **Average per incident: $400 revenue loss**
- If recurring 4x/year: **$1,600 annual revenue impact**

**Recommended Permanent Fix:**

1. **Standardize to USB-C only:**
   - Inventory: Replace all micro-USB devices with USB-C devices
   - Timeline: Phase out over 3 months (as devices fail naturally)
   - Cost: ~$3,000-5,000 (replacement devices)

2. **Establish spare cable inventory:**
   - Procure: 5 USB-C cables (high-quality, rated for high-cycle use)
   - Store: 1 cable in active device, 1 in each handheld backpack, 2 in IT office
   - Cost: ~$100-150

3. **Implement preventive rotation schedule:**
   - Every 2 weeks: Swap active handheld with backup device
   - Distributes wear evenly across 3 devices
   - No single device stays in heavy-use location continuously
   - Wendy Chen executes rotation (task scheduled every 2 weeks)

4. **Update Knowledge Base article:**
   - Publish: "Handheld POS Device Charging Troubleshooting"
   - Step-by-step diagnostics for staff
   - When to swap vs. when to charge
   - Reduces low-value support tickets

5. **Create ServiceNow Service Catalog item:**
   - Item: "Handheld POS Device Replacement"
   - Wendy submits form with device serial + malfunction description
   - Triggers Anas to order replacement
   - Tracks device lifecycle (purchase date, failure date, replacement cost)

**Implementation Timeline:**
- Week 1: Anas submits procurement request for 5 USB-C replacement devices
- Week 2-6: Devices ship and arrive (depending on vendor)
- Week 7: Anas coordinates swap-out of old devices
- Week 7: Wendy implements rotation schedule
- Week 8: Anas publishes KB article
- Week 8: Anas creates service catalog item for future requests

**Cost-Benefit:**
- Hardware cost: $3,500-5,500 (devices + cables)
- Implementation: ~15 hours Anas/Wendy time (~$1,500)
- **Total investment: ~$5,000-7,000**
- Annual revenue protected: $1,600+ (prevents losses)
- Additional benefit: Reduced support tickets (~4 tickets/year = ~8 hours Wendy time saved = $400/year value)
- **Payback period: 3-4 years, but primary benefit is guest experience stability**

---

### Problem 3: HOTL-PRB-003 — WiFi Connectivity in East Wing

**Identified From:** HOTL005 incident (guest WiFi disruption)

**Related Incidents:**
- WiFi dropouts in Rooms 310-315 (East Wing) recurring issue
- Pattern: Worse on Friday-Saturday evenings when occupancy high

**Problem Record Details:**

| Field | Value |
| --- | --- |
| **Problem Number** | HOTL-PRB-003 |
| **Title** | Guest WiFi Coverage Gap — East Wing (Rooms 310-315) |
| **Status** | Open — Planning Phase |
| **Assigned To** | Anas Nehri (network architecture) |
| **Priority** | Medium (impacts guest satisfaction but not revenue directly) |
| **Related Incidents** | HOTL005 + estimated 3-4 prior |

**Root Cause Analysis:**
- Access point (AP) serving East Wing is edge of building (RF range limited)
- During peak occupancy (Fri-Sat nights), 20+ wireless devices connected = congestion
- 2.4 GHz band becomes saturated (older devices, smart TVs, guest phones all compete)
- 5 GHz band available but older guest devices don't support it
- Workaround exists: guests can use 4G hotspot, move to lobby
- But this is negative guest experience

**Recommended Permanent Fix:**

1. **Add second access point (AP) to East Wing:**
   - Install AP in mid-wing location (Rooms 312-314 area)
   - Coordinates with existing AP to split load
   - Cost: ~$800-1,200 (hardware + installation)

2. **Implement AP load balancing:**
   - Configure both APs to same SSID
   - Devices automatically connect to stronger signal
   - Reduces congestion on single AP

3. **Upgrade aging devices to 5 GHz capable:**
   - Replace any guest-accessible devices (lobby terminals, concierge desk) with 5 GHz support
   - Frees up 2.4 GHz for guest phones/tablets

4. **Add QoS (Quality of Service) rules:**
   - Prioritize guest WiFi over staff WiFi
   - Prevent single heavy-use device from consuming all bandwidth

**Status:** Open — Awaiting budget approval from hotel management

---

### Steps Performed (Problem Management)

1. **Identified 3 recurring patterns** across incident history
2. **Created problem records** for each pattern
3. **Documented root causes** with technical detail and business impact
4. **Recommended permanent fixes** with cost-benefit analysis
5. **Assigned problems** to appropriate owner (Anas for infrastructure, Wendy for KB)
6. **Tracked related incidents** (links show which incidents contributed to problem identification)

### Screenshots Placeholder

```
[Screenshot 1: Problem List showing HOTL-PRB-001, HOTL-PRB-002, HOTL-PRB-003]
[Screenshot 2: HOTL-PRB-001 Detail showing "Database Lock" with linked incidents HOTL002, HOTL003]
[Screenshot 3: HOTL-PRB-002 Detail showing "Handheld POS Charging" with business case ROI calculation]
[Screenshot 4: Related Incidents tab showing which incidents contributed to problem identification]
```

### Real-World Relevance

**This is the highest-value work a Tier 2 CSA does:**

Most ServiceNow users respond to tickets. A Tier 2 CSA identifies patterns and 
eliminates entire categories of future tickets. This work is what gets you from 
$70k (Tier 1) to $100k+ (Tier 2):

- **HOTL-PRB-001 (DB Lock):** Fix saves $20k/year in revenue protection. Anas's 10-hour implementation = $2,000/hr business value.
- **HOTL-PRB-002 (Charging):** Fix prevents guest experience degradation and reduces support tickets.
- **HOTL-PRB-003 (WiFi):** Fix improves guest satisfaction (preventing negative reviews).

Hotels notice this and value this. This is the work that leads to:
- Senior CSA promotions ($120k+)
- Consulting roles (higher margin, higher pay)
- Vendor management roles (Tier 3+)

---

## Part 4 — Service Catalog Customization (Anas Implementation)

### Objective

Create service catalog items that allow hotel staff to request IT services 
through a standardized, workflow-driven process instead of free-form incident 
submission.

### Service Catalog Items Created

#### **Catalog 1: "POS Device Replacement/Repair"**

**Service:** Hardware support — handheld POS device replacement

**Form Fields:**
- Device Serial Number (required)
- Location (dropdown: Bar, Restaurant, Room Service, Events)
- Issue Description (text area)
- Symptoms (checkboxes: Won't charge, Screen frozen, Unresponsive, Other)
- Is device currently backed up? (yes/no)

**Workflow Triggered:**
1. Marcus or venue manager submits request
2. Wendy receives notification with device details
3. Wendy creates incident, researches repair vs. replace
4. Anas approves hardware replacement (budget authority)
5. Wendy executes: swap with backup, order replacement if needed
6. Email sent back to requester when device restored

**Business Value:** Standardizes hardware requests, reduces ambiguity, creates audit trail for hardware costs

---

#### **Catalog 2: "Guest Amenity System Access Request"**

**Service:** Temporary access to guest-facing systems for special events

**Form Fields:**
- Service Requested (dropdown: Spa Booking, Valet Management, Concierge Portal, Other)
- Event/Guest Name (text)
- Access Level Needed (dropdown: Read-only, Submit/Update, Full admin)
- Duration (date range picker)
- Business Justification (text area)

**Workflow Triggered:**
1. Priya (Spa Director) or event manager submits request
2. Anas receives request for approval (security gate)
3. Anas reviews: Does requester have legitimate need? Is duration appropriate?
4. If approved: Anas creates temporary user account with limited permissions
5. Access expires automatically on end date
6. Audit log tracks all actions by temporary user

**Business Value:** Ensures guests/VIP staff don't get default admin access; tracks privilege usage for compliance

---

#### **Catalog 3: "WiFi Troubleshooting — Guest Issue"**

**Service:** Self-service + IT escalation for guest WiFi problems

**Form Fields:**
- Guest Name & Room Number (required)
- Device Type (dropdown: iPhone, Android, Laptop Windows, Laptop Mac, Smart TV, Other)
- Issue (dropdown: Can't connect, Drops frequently, Very slow, Won't load pages, Other)
- Steps Already Tried (checkboxes: Forgot network, Restarted device, Moved closer to router, Other)
- Contact Info (phone, email)

**Workflow Triggered:**
1. Boris or guest services staff submits on behalf of guest
2. ServiceNow auto-sends guest a link: "WiFi Help — Try These Steps First"
3. If guest doesn't respond within 15 minutes, Wendy is notified
4. Wendy phones guest (number from request) to troubleshoot live
5. If can't resolve, Wendy escalates to Anas (access point issue, not user issue)
6. Follow-up email to guest with resolution + service recovery offer if needed

**Business Value:** Reduces low-value IT tickets (50%+ are user error, fixable via self-service); ensures guest satisfaction recovery

---

### Steps Performed

1. **Designed 3 service catalog items** aligned to hotel operations pain points
2. **Created form fields** with dropdowns and conditional logic
3. **Configured workflows** to route requests to appropriate approver
4. **Set up auto-notifications** to requester and IT team
5. **Added business value:** Standardized requests, audit trails, compliance

### Screenshots Placeholder

```
[Screenshot 1: Service Catalog portal showing 3 catalog items]
[Screenshot 2: "POS Device Replacement" form with dropdowns for location, issue checkboxes]
[Screenshot 3: "Guest Amenity System Access" form with approval workflow indicator]
[Screenshot 4: "WiFi Troubleshooting" form with self-service link]
```

---

## Part 5 — Knowledge Management Configuration

### Objective

Create knowledge base articles that reduce ticket volume and empower staff 
self-service.

### Article 1: "Handheld POS Device Not Charging — Troubleshooting"

**Title:** How to Troubleshoot Handheld POS Device Charging Issues

**Audience:** Bar staff, room service staff, IT technicians

**Content:**

```
SYMPTOM: Device won't charge or battery drains while charging

QUICK CHECKS (Try these first — takes 2 minutes):
1. Inspect the charging cable connector (micro-USB or USB-C)
   - Look for bent pins, debris, corrosion
   - Gently clean with dry cloth if needed
   
2. Try a different charging cable
   - Located in: Bar office, Room Service office, IT office
   - If device charges with backup cable, original cable is faulty
   
3. Try a different charging dock/outlet
   - Dock might be faulty, not the device
   
4. Leave device charging for 3 minutes
   - Don't pull it out immediately
   - Connector needs full contact time

IF DEVICE STILL NOT CHARGING:
- Swap to backup handheld device immediately (do not wait for IT)
- Locations: Backup handheld in locked cabinet at bar, restaurant
- Email Wendy Chen: wendy.chen@senecaTech.com or call IT office
- Subject: "Handheld POS not charging — [location] — swapped to backup"

IT RESPONSE (Usually within 15 minutes):
- Wendy diagnoses: faulty cable, faulty dock, or faulty device
- If cable: replace with spare, restore device to service
- If dock: move to different dock location
- If device: order replacement, you keep using backup in rotation

PREVENTION:
- Always dock device immediately after shift
- Don't leave in direct sunlight (battery damage)
- Don't force cable connector (breaks pins)
- Report charging issues early, don't wait for failure

Questions? Call IT office: ext. 0-911
```

**Publish Status:** Published (searchable by staff, visible on service portal)

---

### Article 2: "Guest Communication During System Outages"

**Title:** How to Communicate with Guests When IT Systems Are Down

**Audience:** Front desk, housekeeping, guest services managers

**Content:**

```
WHEN SYSTEM IS DOWN (e.g., Reservation system, POS, WiFi):

STEP 1: ACKNOWLEDGE THE ISSUE (within 30 seconds of discovery)
- Call IT immediately: "System [name] is down in [area]"
- Example: "Reservation system is slow during check-in. ETA?"
- Email: hotel-it-ops@senecaTech.com
- Do NOT leave guest/staff wondering while you investigate

STEP 2: COMMUNICATE TO GUEST (within 60 seconds)
GOOD: "I see the system is running a bit slower than usual. I'll have you checked 
in within 10 minutes. Because of this brief delay, I'm upgrading your room at no charge."

BAD: "The server is down, I don't know how long this will take" 
     "This happens all the time"
     [Silent, making guest wait with no explanation]

STEP 3: OFFER SERVICE RECOVERY (while guest waits)
- Room upgrade to better location or room type
- Credit toward dining/spa ($25-50 depending on inconvenience)
- Complimentary welcome amenity (champagne, fruit basket)
- DO NOT say "I'm giving you this because our system is broken"
- DO say "Thank you for your patience. I'd like to upgrade your experience."

STEP 4: COORDINATE WITH OTHER DEPARTMENTS
- Text message to group Slack channel: "@guest-services The reservation system 
  is slow. Guests should expect 5-10 min delays at check-in. Housekeeping, hold 
  room prep."
- Spa director: "Don't call room service confirmations yet; system is busy"
- Valet: "Guest arrival data delayed; we'll sync when system recovers"

STEP 5: ESCALATE IF NEEDED
- If outage will impact more than 5 guests, notify manager immediately
- Manager notifies all departments via broadcast message
- Coordinate centralized recovery message (vs. each department winging it)

STEP 6: FOLLOW-UP (after system restored)
- Email guest in 2 hours: "Thank you for your patience during the brief system maintenance. 
  As promised, your room has been upgraded to [room type]. Enjoy your stay."
- Include: phone number for concierge if guest needs anything else
- Prevent: Guest posting negative review because they felt ignored

POST-INCIDENT (for managers):
- Track: How many guests were impacted?
- Track: What was our service recovery cost?
- Report to IT: "Reservation outage: 25 guests impacted, 1 room upgrade ($150 value), 
  2 dinner comps ($80 value), total recovery cost: $230"
- This data helps IT justify investments in system reliability

Questions? Talk to your manager or Boris Kowalski (Front Office Manager)
```

**Publish Status:** Published + Required Reading for new front-office staff

---

### Article 3: "Guest WiFi Troubleshooting — Self-Service"

**Title:** Can't Connect to Hotel WiFi? Try These Steps

**Audience:** Guests (self-service portal + room TV access)

**Content:**

```
🔧 HAVING TROUBLE WITH HOTEL WIFI? 

If you can't connect to our guest WiFi, try these steps:

STEP 1: CHECK YOUR DEVICE'S WIFI NETWORK LIST
- Make sure you're connecting to: "Hotel_Guest" (not "Hotel_Admin" or "Hotel_Staff")
- Password: [Printed on room key card, also written on welcome card]

STEP 2: FORGET AND RECONNECT
iPhone / iPad:
1. Go to Settings > WiFi
2. Find "Hotel_Guest" and tap the (i) icon
3. Tap "Forget This Network"
4. Find "Hotel_Guest" again in the list
5. Enter password: [same password from Step 1]
6. Wait 10 seconds for connection

Android:
1. Go to Settings > Network > WiFi
2. Long-press "Hotel_Guest"
3. Tap "Forget Network"
4. Find "Hotel_Guest" again
5. Enter password and connect

Laptop (Windows):
1. Right-click WiFi icon (bottom-right taskbar)
2. Click "Open Network & Internet Settings"
3. Find "Hotel_Guest" in network list
4. Right-click and select "Forget"
5. Reconnect and enter password

STEP 3: RESTART YOUR DEVICE
- Turn off WiFi, count to 10, turn WiFi back on
- Or: Fully restart your device (power off completely, wait 30 sec, power on)

STEP 4: MOVE CLOSER TO ROUTER
- WiFi signal is strongest near the middle of your room
- If in corner, try moving closer to window/center
- Metal furniture and water (bathroom) can block signal

STEP 5: STILL NOT WORKING?
- Try to connect on a different device (phone, tablet) to isolate the issue
- If other device connects: problem is with your device, not hotel WiFi
  → Restart that device, update its WiFi drivers/software
- If NO device connects: hotel WiFi might be down in your room
  → Call Front Desk: ext. 0 (reception)
  → Tell them: "Guest WiFi not connecting in Room [XXX]"
  → We'll send IT to investigate (usually within 15 minutes)

KNOWN ISSUES:
- Rooms 310-315 (East Wing): WiFi can be slow on Friday-Saturday evenings 
  when hotel is full. If very slow, try using your mobile 4G hotspot.
  → OR move to our lobby area where signal is strongest

STILL NEED HELP?
- Call Front Desk: ext. 0
- Email: guest-support@hotel.local
- We're here to help!
```

**Publish Status:** Published on guest portal + printed QR code in every room

---

### Steps Performed

1. **Created 3 knowledge articles** addressing common issues
2. **Tailored content** to specific audiences (staff vs. guests)
3. **Included step-by-step instructions** with device-specific guidance
4. **Added troubleshooting decision trees** (if X, then try Y)
5. **Published articles** making them searchable and self-service accessible

### Screenshots Placeholder

```
[Screenshot 1: Knowledge Base article list showing 3 articles]
[Screenshot 2: "Handheld POS Charging" article open, showing step-by-step guide]
[Screenshot 3: "Guest Communication" article, showing GOOD vs. BAD examples]
[Screenshot 4: "Guest WiFi" article, guest-facing version with device-specific steps]
[Screenshot 5: Guest portal showing QR code linking to WiFi troubleshooting article]
```

### Real-World Relevance

Knowledge management is often overlooked by entry-level users, but it's **core 
to scalable IT operations:**

- **HOTL001 (POS charging):** Without KB article, Wendy gets ~4 similar tickets/month from different bar shifts. With article, staff self-resolve. Annual savings: ~20 tickets × 30 min each = 10 hours Wendy time = $400/year value.

- **HOTL002 (Guest communication):** This prevents guest escalation. A guest who gets proactive communication + service recovery doesn't post a 2-star review. That review could cost the hotel $500+ in lost future bookings (1 bad review on Tripadvisor = ~$500 lifetime impact).

- **HOTL003 (WiFi):** ~60% of WiFi issues are user error (wrong SSID, not entering password correctly). KB article eliminates 60% of WiFi tickets = 3-5 fewer tickets/week = 2-3 hours Wendy time freed per week = $5,000+/year value.

**This is why Tier 2 CSAs focus on knowledge management:** It's the highest ROI activity. Fix one problem once in a KB article, prevent thousands of incidents.

---

## Interview Narrative — Senior CSA System Architecture Focus

### The Narrative (90-second version)

> *"I designed and implemented a ServiceNow ITSM platform for a boutique hotel 
> where IT incidents cascade across 8+ revenue centers — reservation systems, 
> POS, spa booking, valet, concierge.*
>
> *The core challenge: When the reservation system fails during Friday evening 
> check-in, how do the spa, valet, and front desk all find out within 30 seconds 
> so they can coordinate guest service recovery?*
>
> *I architected a solution using custom incident fields (guest impact count, 
> revenue impact, affected departments) + automated business rules + multi-department 
> group routing workflows. Now when a guest-facing incident is created:*
>
> *- Priority is auto-calculated based on guest count + revenue impact*
> *- Business rules fire within 30 seconds to notify all affected departments*
> *- Multi-department notification log creates audit trail*
> *- System auto-calculates revenue impact based on incident duration*
>
> *That's not Tier 1 support. That's platform architecture that has direct business impact.*
>
> *During our testing, a reservation system incident (12 minutes) would have cost 
> $1,200 in revenue loss. My system caught it in 45 seconds and had all departments 
> coordinated within 60 seconds. That proactive communication prevented guest escalation.*
>
> *This is the work that justifies Tier 2 compensation: designing systems that 
> multiply the value of incident response, not just responding to incidents faster.*"

---

## Summary of Tier 2 CSA Skills Demonstrated

| Skill | Implementation | Business Impact |
| --- | --- | --- |
| **Data Modeling** | 10+ custom incident fields capturing hotel-specific metrics | Guest impact tracking, revenue impact calculation |
| **Business Rules** | 3 core rules for priority override, escalation, notification | P1 incidents reach decision-makers in <1 minute |
| **Workflow Automation** | 6-step multi-department notification cascade | Notification delivery in 45 seconds vs. 10+ minutes manual |
| **Integration Design** | REST API specs for PMS connectivity | Guest profile context in incidents, service recovery logging |
| **RBAC Configuration** | 8 custom roles with table-level access control | Separation of duties, security, scalability |
| **Problem Management** | 3 problems identified from incident patterns | $20k+ annual revenue protection through permanent fixes |
| **Knowledge Management** | 3 KB articles reducing ticket volume | ~$5,000+ annual value through self-service reduction |
| **Service Catalog** | 3 catalog items with standardized workflows | Governance of hardware requests, compliance tracking |
| **Reporting** | Executive dashboards tied to business KPIs | Visibility into guest impact, revenue protection, SLA compliance |
| **Documentation** | Configuration runbook, architecture diagrams, integration specs | Knowledge transfer, future administrator onboarding |

---

## Next Steps for Portfolio Development

1. **GitHub Repository:**
   - Architecture diagrams (incident data flow, multi-department notification cascade)
   - Configuration runbook (step-by-step implementation guide)
   - Business rule & workflow export (.xml files from ServiceNow)
   - Integration specification (API endpoints, data mapping)
   - Performance metrics (response times, notification latency)

2. **Video Demonstration (5-7 minutes):**
   - Anas creates incident with guest-facing impact
   - Shows business rules fire, priority escalate
   - Shows email notification sent to 4+ departments within 45 seconds
   - Shows work notes timeline, incident resolution
   - Voiceover explaining system design decisions

3. **Interview Talking Points:**
   - Focus on architecture, not incident response
   - Lead with business problem ("How do 8 departments coordinate in <30 seconds?")
   - Explain your solution (data model, business rules, workflows)
   - Quantify impact ($20k problem prevention, guest satisfaction improvement)

4. **Certification Alignment:**
   - This work directly prepares for **ServiceNow System Administrator certification** (platform configuration, business rules, workflows, RBAC, reporting)
   - Also foundation for **CIS-RC** (if you move into GRC specialization — compliance dashboards, audit logging, risk assessment workflows)

---

## Notes

This lab was performed using a ServiceNow Personal Developer Instance (PDI) 
available through developer.servicenow.com. All users, incidents, and records 
are fictional but based on real hotel operations scenarios.

**Key Differentiator:** This portfolio shows **Tier 2 system architecture work**, 
not entry-level support. The value is in designing systems that prevent incidents, 
not just responding to them faster.

When you interview at ServiceNow partners, tech consulting firms, or hotels, 
you can say: "I didn't just fix incidents. I designed an incident management 
system that automatically coordinates 8 departments within 45 seconds. That's 
platform engineering."


