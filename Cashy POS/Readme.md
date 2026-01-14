"Cashy: Business Made Easy."
•	The Vibe: Simple, direct, and reassuring for busy owners.
Product Requirements Document (PRD)

Project Name: Generic Retail Point of Sale (POS) System
Version: 1.0 (MVP)
Status: Approved for Development

1. Executive Summary
The goal is to develop a robust, single-terminal Desktop Point of Sale application using    C .NET (WinForms)    and    SQL Server. The system addresses critical pain points in local retail environments: lack of stock tracking, outdated user interfaces, and slow operational workflows. The core value proposition is an atomic, reliable checkout process that ensures inventory integrity in real-time.

2. Project Scope 

In-Scope:   
 Single-terminal desktop deployment.
 Role-Based Access Control (Manager vs. Cashier).
 Inventory Management (CRUD).
 Sales Transaction Processing (Atomic Invoicing).
 Shift Reporting.
 Auto-provisioning of the database (Bootstrapper).

 Out-of-Scope (v1.0):   
 Multi-terminal concurrency (Cloud Sync).
 E-commerce integration.
 Mobile app interface.

 3. User Personas
| Persona | Role | Key Responsibilities | Pain Points |
| --- | --- | --- | --- |
|    The Cashier    | End User | Scans items, processes payments, prints receipts. | Wants to avoid "menu diving" during rush hour; fears making mistakes with bills. |
|    The Manager    | Admin | Updates prices, adds new stock, views audit reports. | Needs to trust that cash in the drawer matches the sales report; needs to prevent theft. |

4. Functional Requirements (FR) 

These requirements define the specific behaviors of the system.
 4.1. Authentication & Authorization
•	FR-AUTH-01:    The system must require a secure login (Username/Password) upon startup.
•	FR-AUTH-02:    The system must support two distinct roles: `Admin` (Manager) and `User` (Cashier).
•	FR-AUTH-03:    Passwords must be hashed (e.g., SHA-256 with Salt) before storage.    Plain text storage is strictly forbidden.    

 4.2. Inventory Management (Manager Only)
•	FR-INV-01:    Managers must be able to Create, Read, Update, and Delete (CRUD) product records. 
•	FR-INV-02:    Managers must be able to adjust Stock Quantity manually (e.g., for restock).
•	FR-INV-03:    Cashiers must have    Read-Only    access to the Product Catalog (Search/View Price). 
 4.3. Sales & Transactions (The Core Loop)
•	FR-SALE-01:    The system must allow users to add items to a "Cart" via Barcode Scan or Manual Search.
•	FR-SALE-02:    The system must calculate the Total Amount (Sum of `LineItem.Price  Quantity`) in real-time.
•	FR-SALE-03: (CRITICAL) The "Finalize Sale" action must execute as an Atomic Database Transaction that:

1. Inserts the `Invoice` record.
2. Inserts all `InvoiceLineItem` records.
3. Updates (Decrements) the `StockQuantity` for every product sold.
4. Commits only if all steps succeed. 
  FR-SALE-04:    If the transaction fails (e.g., Insufficient Stock), the system must Rollback and alert the user.





 4.4. Reporting
•	FR-RPT-01:    The system must generate a "Shift Report" showing Total Sales and Cash vs. Card breakdown.
•	FR-RPT-02:    Usage statistics (e.g., checkout speed) must be logged to a `UsageStats` table.

5. Non-Functional Requirements (NFR) 
These constraints define "how well" the system performs.
Category	Requirement	Metric/Constraint
Performance	NFR-PERF-01	The FinalizeSale transaction must complete end-to-end in $\le 500$ milliseconds under normal load. 8
Security	NFR-SEC-01	Cashiers must be technically restricted (at the Business Logic level) from deleting Invoices. 9
Reliability	NFR-REL-01	The system must be capable of recovering from a "Cold Start" (No DB) using the Auto-Migration Bootstrapper. 10
Usability	NFR-USE-01	The Checkout UI must be touch-friendly with button targets $\ge 44x44$ pixels.
Maintainability	NFR-MAIN-01	Error logs must be written to the Windows Event Viewer with a distinct Source Name (e.g., "MyPOS"). 11


6. System Architecture 
 6.1. Architectural Pattern
The system follows a 3-Tier Monolithic Architecture tailored for Desktop:
1. Presentation Tier: C WinForms (User Interface).
2. Application Tier: C Business Logic Libraries (BLL) & Services.
3. Data Tier: SQL Server (accessed via ADO.NET). 

 6.2. Technology Stack
    Language: C .NET 8.0 (or latest LTS).
    UI Framework: Windows Forms (WinForms).
    Database:  SQL Server Express / LocalDB.
    Data Access:  ADO.NET (System.Data.SqlClient).
    Logging:  `System.Diagnostics.EventLog`.
7. Development & Deployment Strategy 
 7.1. Version Control
•	Strategy: Gitflow.
•	Branching: `main` (Production), `develop` (Integration), `feature/xyz` (Task specific).

 7.2. Installation & Update
•	Installer: ClickOnce   Deployment.
•	Update Policy:  The application checks for updates on startup.
•	First-Run Logic:  The application checks for the existence of the database. If missing, it runs the embedded `.sql` Bootstrapper script.

 7.3. Testing Strategy 
•	Unit Tests:    Validate Business Logic (e.g., Total Calculation, RBAC checks).
•	Integration Tests:    Validate the ADO.NET `SQL Transaction` logic (Commit/Rollback).
•	Performance Tests:  Use BenchmarkDotNet to verify NFR-PERF-01.

 8. User Interface Guidelines (Wireframe Concept)
•	Layout: Split screen. Left side = "Cart/Line Items". Right side = "Product Grid" (Quick Select) and "Numpad".
    Color Coding:   
•	Green: Finalize/Pay actions.
•	Red: Destructive actions (Void Item) - Manager Authorization may be required.
•	High Contrast: Black text on White/Light Grey background for readability.
## 🚀 Getting Started

### Prerequisites
- Windows 10 or later
- .NET 8.0 SDK
- SQL Server Express / LocalDB
- Visual Studio 2022 (recommended)

### Installation
1. Clone the repository: `git clone https://github.com/your-org/cashy-pos.git`
2. Open `Cashy.sln` in Visual Studio
3. Restore NuGet packages: `dotnet restore`
4. Build the solution: `dotnet build`
5. Run the application—the Bootstrapper will auto-provision the database on first launch

## 📋 Project Structure

```
Cashy/
├── Cashy.Presentation/       # WinForms UI
├── Cashy.Business/           # BLL & Services
├── Cashy.Data/               # ADO.NET Data Access
├── Cashy.Tests/              # Unit & Integration Tests
├── Database/                 # SQL scripts & Bootstrapper
└── README.md
```

## 🧪 Testing

Run tests to validate business logic and transaction integrity:

```bash
dotnet test Cashy.Tests.csproj
```

Performance benchmarks verify NFR-PERF-01 (≤500ms finalization):

```bash
dotnet run --project Cashy.Benchmarks --configuration Release
```

## 📦 Deployment

The application uses **ClickOnce Deployment** for seamless updates. Users receive notifications on startup if a newer version is available.

## 🔒 Security & Compliance

- ✅ Passwords hashed with SHA-256 + Salt
- ✅ Role-based access control enforced at Business Logic layer
- ✅ All errors logged to Windows Event Viewer (`Source: "Cashy"`)
- ✅ Atomic transactions prevent inventory corruption

## 📞 Support

For issues or feature requests, open a GitHub issue or contact the development team.

---

**Version:** 1.0 (MVP) | **Status:** Approved for Development
