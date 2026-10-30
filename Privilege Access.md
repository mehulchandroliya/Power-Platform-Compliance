Below is the comprehensive reference list of Microsoft Dataverse system and platform tables where granting Create and/or Write privileges (especially at Organization or Parent: Child Business Unit scope) creates a high risk of privilege escalation, remote code execution, security bypass, or complete system takeover—effectively making the access equivalent to System Administrator or System Customizer.
1. Role Assignment & Security Hierarchy (Direct Privilege Escalation)
Writing or creating records in these tables allows a user to elevate their own permissions or assign administrative roles directly to themselves or others.
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| Role | role | Controls role definitions and security scopes. Modifying or creating records lets users alter security limits or craft custom roles with unconstrained access. |
| System User Roles | systemuserroles | Intersect table mapping users to security roles. Creating or modifying a row here allows assigning System Administrator or System Customizer directly to any user account. |
| Team Roles | teamroles | Intersect table mapping teams to security roles. Writing here allows assigning administrative security roles to a team, elevating all team members. |
| Privilege | privilege | Defines individual security privileges in the system. Direct manipulation alters system-wide permission boundaries. |
| Role Privileges | roleprivileges | Maps privileges to security roles. Modifying this table grants additional permissions to existing custom or baseline roles. |
| Field Security Profile | fieldsecurityprofile | Controls Column-Level Security (CLS). Writing here allows removing restrictions on secured columns (e.g., social security numbers, passwords, financial keys). |
| System User Field Security Profiles | systemuserprofiles | Maps users to column security profiles. Writing here grants access to protected columns. |
| Business Unit | businessunit | Defines the top-level security boundaries and organizational structure. Writing/creating records alters parent-child BU hierarchies and scope inheritance. |
2. Business Logic, Plug-ins & Code Execution (System Code Takeover)
Dataverse executes plugins, custom workflows, and API integrations with system-level backend privileges. Writing or creating records in these tables allows arbitrary server-side code execution.
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| Plug-in Assembly | pluginassembly | Contains compiled .NET DLL binaries. Creating/writing allows uploading custom code that executes on the server under elevated host process context. |
| Plug-in Type | plugintype | Defines classes within compiled plugin assemblies. Writing here registers new logic types for platform execution. |
| SDK Message Processing Step | sdkmessageprocessingstep | Controls when and on which events (Create/Update/Delete of any table) plugins fire. Writing here allows intercepting data across all system operations or altering business logic transactions. |
| SDK Message Processing Step Image | sdkmessageprocessingstepimage | Defines pre- and post-entity images passed to plugins during execution, allowing interception of sensitive record states. |
| Service Endpoint | serviceendpoint | Configures Azure Service Bus, Event Hubs, or Webhooks. Writing here allows exfiltrating Dataverse transaction streams to external endpoints. |
| Custom API | customapi | Defines custom API definitions and request parameters. Creating/writing enables custom unbound or bound platform endpoints. |
| Custom API Request Parameter / Response Property | customapirequestparameter, customapiresponseproperty | Alters input validation and parameter mapping for custom API endpoints. |
3. Automation, Workflows & Power Automate (Logic Bypass)
Dataverse workflows and classic processes execute under the owner's context or system impersonation.
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| Process / Workflow | workflow | Stores Power Automate cloud flows, classic workflows, action processes, and business rules. Creating or modifying records allows executing logic as a System Admin or bypassing validations. |
| Process Trigger | processtrigger | Dictates event conditions that invoke processes. Writing here attaches unauthorized automated logic to critical data events. |
| Workflow Dependency | workflowdependency | Defines dependencies between processes and entities/attributes. Modifying this corrupts or alters execution trees. |
4. Metadata, Schema & Solution Architecture (Structural Administrative Control)
These tables store entity-attribute-relationship (EAR) schemas and solution package components.
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| Entity / Table | entity | Stores table-level system metadata. Creating or editing metadata modifies data structures, ownership types, and security constraints across the platform. |
| Attribute / Column | attribute | Stores column metadata. Writing allows altering field properties, audit logging toggles, or key boundaries. |
| Relationship Metadata | relationship / one-to-many-relationship | Dictates database foreign keys and cascading delete/assign rules across system tables. |
| Solution | solution | Packages customizations, components, and code. Creating/writing solution records allows deploying unmanaged packages that overwrite core security configurations or schema settings. |
| Solution Component | solutioncomponent | Intersect table mapping components to solutions. Writing here forces modified components into existing system solutions. |
| Publisher | publisher | Defines solution publishers and prefix controls (new_, cr123_). Writing allows impersonating official Microsoft or ISV publishers. |
5. Security Identities, Users & Application Access
Tables defining non-interactive system users, service principals, and API access.
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| User / System User | systemuser | Contains environment user records. Creating or modifying user records (e.g., updating email, user status, or application user IDs) allows user impersonation or account takeover. |
| Application User / Application ID | applicationuser | Stores Microsoft Entra ID (Azure AD) App Registrations mapped into Dataverse. Creating/modifying application users allows assigning non-human service principals full administrative roles without MFA. |
| Connection Reference | connectionreference | Links flows and logic apps to embedded authentication credentials. Creating or modifying references allows hijacking existing API connections. |
| Environment Variable Definition | environmentvariabledefinition | Holds configuration keys used by flows and plugins. |
| Environment Variable Value | environmentvariablevalue | Stores runtime environment variable values (e.g., secret endpoints, database connection strings, Azure tenant IDs). Writing here redirects application traffic to malicious endpoints. |
6. System Governance, Auditing & Web Application Controls
| Table Name | Logical / Schema Name | Risk & Elevation Path |
|---|---|---|
| Audit | audit | Stores transaction logs. Direct write access allows tempering with or deleting audit trails to cover malicious changes. |
| Organization | organization | Contains tenant/environment global settings (e.g., enabling/disabling auditing, system maintenance modes, security defaults). Modifying this alters global environment behavior. |
| Web Resource | webresource | Stores HTML, JavaScript, CSS, and image files used in forms/apps. Creating/writing allows injecting XSS scripts into Model-Driven Apps or Portals that run under the session context of System Administrators. |
| Site Map | sitemap | Defines app navigation structures. Modifying this can expose hidden administrative forms or hide critical security controls. |
| System Form | systemform | Stores form and dashboard layouts. Writing allows injecting malicious JavaScript libraries into main entity forms. |
Key Takeaway for Governance & Security Auditing
Standard Dataverse RBAC enforces that none of these listed tables should ever have Organization-level Create or Write privileges assigned in custom security roles for non-admin users. Granting Create or Write privileges on any table in this list creates a privilege escalation path that effectively renders the user a System Administrator.
