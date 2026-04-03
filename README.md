# Northwind Products — SAP Fiori Application

An SAP Fiori Elements List Report application built on SAP Business Technology Platform (BTP), consuming the public Northwind OData V2 service. The app displays a searchable, filterable list of products with a drill-down Object Page for detailed product information.

---
## Live Demo

🔗 [Open Application](https://be444841trial.launchpad.cfapps.us10.hana.ondemand.com/a2295dd7-8a71-42ef-bf4b-a5e3e206700e.cominternnorthwindappnorthwindapp.cominternnorthwindappnorthwindapp-0.0.1)

> Note: The application is hosted on SAP BTP Trial. Performance may vary slightly due to shared resources.

---

## Architecture Overview

```
[Northwind OData Service]
        |
        v
[BTP Destination: "Northwind"]
        |
        v
[Fiori App (SAPUI5 - List Report + Object Page)]
        |
        v
[HTML5 Application Repository]
        |
        v
[Managed Application Router]
        |
        v
[XSUAA Authentication]
        |
        v
[Cloud Foundry Space: dev]
```

- **Destination**: Configured in BTP Cockpit to point to `https://services.odata.org`, enabling BAS to discover and consume the Northwind OData service.
- **BAS**: Used to scaffold, develop, and preview the Fiori application using the SAP Fiori generator.
- **Cloud Foundry**: Hosts the deployed application via the HTML5 Application Repository and Managed Application Router — no standalone CF app instance is required.

---

## Setup Instructions

### Prerequisites
- SAP Universal ID (free): https://account.sap.com/core/create/register
- SAP BTP Trial account: https://account.hanatrial.ondemand.com
- Google Chrome or Microsoft Edge

### Step 1 — BTP Trial Account
1. Log in to https://account.hanatrial.ondemand.com
2. Enter your Trial subaccount
3. Verify Cloud Foundry Environment is enabled with a `dev` space

### Step 2 — Subscribe to Services
1. Navigate to **Services → Service Marketplace**
2. Subscribe to **SAP Business Application Studio** (trial plan)
3. Navigate to **Services → Instances and Subscriptions → Create**
4. Create a **Destination Service** instance (lite plan)

### Step 3 — Assign Role Collections
1. Navigate to **Security → Users**
2. Select your user
3. Assign **Business_Application_Studio_Developer** role collection

### Step 4 — Configure Northwind Destination
1. Navigate to **Connectivity → Destinations**
2. Create a new destination with:
   - **Name**: `Northwind`
   - **Type**: `HTTP`
   - **URL**: `https://services.odata.org`
   - **Proxy Type**: `Internet`
   - **Authentication**: `NoAuthentication`
3. Add Additional Properties:
   - `WebIDEEnabled` = `true`
   - `WebIDEUsage` = `odata_gen`
4. Click **Check Connection** to verify

### Step 5 — Create Fiori Application in BAS
1. Launch BAS from Subscriptions
2. Create a Dev Space of type **SAP Fiori** named `FioriDev`
3. Open Command Palette → `Fiori: Open Application Generator`
4. Select **List Report Page** template
5. Data Source: **Connect to an OData Service**
   - URL: `https://services.odata.org/V2/Northwind/Northwind.svc/`
6. Main Entity: **Products**
7. Project attributes:
   - Module name: `northwindapp`
   - Application title: `Northwind Products`
   - Namespace: `com.intern.northwindapp`

### Step 6 — Run Preview
1. Open Terminal in BAS
2. Run: `npm run start`
3. Open via **Ports: Get External URL** → port 8080

### Step 7 — Deploy to Cloud Foundry
```bash
cf login -a https://api.cf.us10-001.hana.ondemand.com
cd /home/user/projects/northwindapp
mbt build
cf deploy mta_archives/cominternnorthwindappnorthwindapp_0.0.1.mtar
```

---

## OData Entity Used

**Entity Set**: `Products` from the Northwind OData V2 service (`/V2/Northwind/Northwind.svc/`)

**Reason**: The Products entity provides a rich set of meaningful fields (ProductID, ProductName, UnitPrice, UnitsInStock, QuantityPerUnit, Discontinued) that demonstrate real-world inventory management use cases — making it ideal for a List Report with drill-down capability.

**Columns displayed**:
- ProductID
- ProductName
- UnitPrice
- UnitsInStock

**Service URL**: https://services.odata.org/V2/Northwind/Northwind.svc/

---

## Challenges Faced

### 1. "Cannot login w/o session" error when opening app preview
**Problem**: Opening the external BAS URL in a new browser tab showed a session error.  
**Solution**: Used `Fiori: Preview Application` from the Command Palette and accessed the app via `Simple Browser: Show` inside BAS, which maintains the authenticated session.

### 2. Object Page showing no data
**Problem**: The Object Page was generated but displayed no fields.  
**Solution**: Manually added OData annotations (`UI.LineItem`, `UI.HeaderInfo`, `UI.FieldGroup`, `UI.Facets`) to `webapp/annotations/annotation.xml` to define the List Report columns and Object Page sections.

### 3. SAP Build Work Zone subscription issue (OIDC trust)
**Problem**: Initial subscription failed with `OIDC trust missing` error.  
**Solution**: After retrying and ensuring correct subaccount configuration, the subscription was successfully completed, and the application was integrated into SAP Build Work Zone Launchpad.

### 4. Postman POST request type error
**Problem**: POST request to OData service failed with `Cannot convert Edm.Double to Edm.Decimal`.  
**Solution**: Changed the `Price` field value from a JSON number (`29.99`) to a JSON string (`"29.99"`) to match the OData Edm.Decimal type expectation.

---

## Bonus Tasks Completed

| #  | Task | Status |
|----|------|--------|
| B1 | Deploy to Cloud Foundry | Completed (Application successfully deployed and running) |
| B2 | Test & Validate via Postman ($top, $filter, $select, $orderby) | Completed (Queries tested on live OData endpoint) |
| B3 | XSUAA Authentication | Enabled (Application secured with authentication) |
| B4 | Fiori Object Page | Implemented (Navigation to detailed view enabled) |
| B5 | OData POST Write Operation via Postman | Tested (POST request executed successfully) |
| B6 | SAP Build Work Zone | Configured (App integrated into Launchpad site) |

---

## Screenshots

All screenshots are located in the `/docs` folder:

| Task | File | Description |
|------|------|-------------|
| D1 | [GitHub Repository](https://github.com/mohamedbayoumi-dev/Northwind_Products) | GitHub project source code |
| D2 | `docs/Deliverable D2` | Northwind destination configured in BTP Cockpit with successful Check Connection |
| D3 | `docs/D3 — BAS preview.png` | Fiori List Report running in BAS preview showing live Northwind product data |
| B1 | `docs/B1 — Deploy to CF.png` | Application Deployed to Cloud Foundry |
| B2 | `docs/B2 — Postman Queries/$top.png` | Postman GET request with $top=5 |
| B2 | `docs/B2 — Postman Queries/$filter.png` | Postman GET request with $filter=UnitPrice gt 20 |
| B2 | `docs/B2 — Postman Queries/$select.png` | Postman GET request with $select |
| B2 | `docs/B2 — Postman Queries/$orderby.png` | Postman GET request with $orderby=UnitPrice desc |
| B3 | `docs/B3 — SAP Login Screen (XSUAA).png` | SAp Login Screen (XSUAA) |
| B4 | `docs/B4 — Object Page.png` | Object Page showing detailed product information (Chai) |
| B5 | `docs/B5 — POST Request.png` | OData POST Request (Create Product) |
| B6 | `docs/B6 — Workzone Integration.png` | Integration with SAP Build Work Zone |
| `docs/cf-environment.webp` | Extra,Cloud Foundry Environment Status |

---

## Deployed Application

The application was built using SAP Business Application Studio and deployed to SAP BTP Cloud Foundry using the Multi-Target Application (MTA) build and deployment process.

### Deployment Details
- **CF API Endpoint**: `https://api.cf.us10-001.hana.ondemand.com`
- **Organization (Org)**: `be444841trial`
- **Space**: `dev`
- **HTML5 Application Name**: `cominternnorthwindappnorthwindapp`

### Application Access
- **SAP Build Work Zone (Launchpad)**  
  🔗 [Open Fiori Application](https://be444841trial.launchpad.cfapps.us10.hana.ondemand.com/a2295dd7-8a71-42ef-bf4b-a5e3e206700e.cominternnorthwindappnorthwindapp.cominternnorthwindappnorthwindapp-0.0.1)

> Note: The application is secured using XSUAA authentication. A login prompt will appear before accessing the application.

### Status
✔️ The application is successfully deployed and running in the Cloud Foundry environment.

---

## Technical Stack

- SAP Fiori Elements (List Report + Object Page)
- SAPUI5
- OData V2
- SAP Business Application Studio
- SAP BTP Cloud Foundry
- HTML5 Application Repository
- XSUAA Authentication

---

## Author

Mohamed Bayoumy  
Full Stack Developer | SAP BTP Enthusiast

---