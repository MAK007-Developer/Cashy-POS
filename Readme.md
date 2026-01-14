# ✨ Cashy: Business Made Easy

> Simple, direct, and reassuring for busy owners.

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
