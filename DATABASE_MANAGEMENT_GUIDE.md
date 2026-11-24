# 📱 Mayank Sahu Mobile Store — Database Architecture & Management Guide
**Academic Project:** Mobile Sales, Inventory & Comparison Management System  
**Developer:** Mayank Sahu  
**Course:** Bachelor of Computer Applications / B.Tech Computer Science (Final Year Major Project)  

---

## 1. System & Database Architecture Overview

The system uses a **Dual-Layer Database Architecture** designed for high reliability, zero-setup portability, and academic viva evaluation:

```
+--------------------------------------------------------------------------------+
|                         Mayank Sahu Mobile Store                               |
+------------------------------------+-------------------------------------------+
|      👤 Customer Portal            |           👔 Store Manager Portal         |
|  - Browse & Compare Smartphones    |  - Real-Time Orders Queue (Approve/Reject)|
|  - Submit Order Request            |  - Inventory & Stock Threshold (<3 Units) |
|  - Auto-Generated Tax Invoice      |  - 🗄️ Database Management Console        |
+------------------------------------+-------------------------------------------+
                                      |
                     [ JSON Data Sync Pipeline ]
                                      |
                                      v
+--------------------------------------------------------------------------------+
|                   LAYER 1: Client Database Engine (Default)                    |
|             Browser LocalStorage Engine (`MAYANK_SAHU_MOBILE_STORE_DB_V2`)     |
|   • Instant zero-dependency setup (runs in Chrome, Edge, Firefox, Safari)      |
|   • Complete data persistence across account switches, tab closing & reloads   |
|   • Integrated Backup Export (.JSON) & Restore (.JSON) with 1-click reset      |
+--------------------------------------------------------------------------------+
                                      |
                                      v
+--------------------------------------------------------------------------------+
|                   LAYER 2: Standalone REST API Backend (Optional)              |
|                     Node.js / Express Server (`backend/server.js`)              |
|   • REST Endpoints: GET /api/orders, POST /api/orders, PUT /api/orders/:id    |
|   • Demonstrates client-server separation for advanced viva requirements       |
+--------------------------------------------------------------------------------+
```

---

## 2. Customer Order to Manager Dashboard Lifecycle

Here is the exact lifecycle of how an order placed from a customer account is transmitted to the manager's dashboard:

```
[Customer Portal]
   1. Customer selects mobile model & quantity (e.g. 1x OnePlus 12).
   2. Fills customer name, phone number, and delivery address.
   3. Clicks "✅ Confirm Purchase & Submit Order Request".
   4. Local inventory is decremented by the requested quantity.
   5. A unique Customer Order Request record is generated:
      {
        orderNo: "ORD-REQ-110",
        customer: "Priya Sharma",
        status: "Pending Approval"
      }
   6. Data is committed immediately to the database via `DBManager.save()`.
   7. Customer is immediately shown their official Invoice (with editable name only).
          |
          |  (Persistent Storage Sync)
          v
[Manager Portal (Mayank Sahu)]
   1. Store Manager Mayank Sahu logs in (username: `mayank`, password: `admin123`).
   2. The Manager Dashboard automatically queries all records where `status === 'Pending Approval'`.
   3. A live alert badge appears: "🔔 1 New Request Pending".
   4. The order appears in the "Incoming Customer Order Requests" table with:
      - Customer Name, Phone, and Delivery Address
      - Requested Model and Total Price (including 18% GST)
      - Date & Timestamp
      - "⏳ Pending Approval" status badge
   5. Manager makes a decision:
      • "✓ Approve": Marks order as "🚚 Approved & Dispatched" and logs completion.
      • "👁️ Invoice": Opens full invoice receipt preview in a modal dialog.
      • "✕ Reject": Marks order as "❌ Cancelled" and automatically RESTORES the stock back to store inventory!
```

---

## 3. How to Manage the Database

### Method A: Via the Built-In Manager Portal UI (Recommended)
Store Manager Mayank Sahu has full graphical database management tools inside the application:

1. **Log in as Manager**:
   - Go to the Login Screen.
   - Click the **👔 Store Manager** tab.
   - Use username: `mayank` | password: `admin123`.
2. **Access the Database Console**:
   - In the manager top navigation bar, click the **"🗄️ Database Management"** pill.
3. **Database Health & Metrics**:
   - Review live metrics: **Storage Engine** (`LocalStorage`), **Total Records**, **Storage Size in KB**, and **Sync Status**.
4. **Export a Database Backup**:
   - Click the blue **"📥 Download Database Backup (.JSON)"** button.
   - The system downloads a complete snapshot file: `Mayank_Sahu_Mobile_Store_DB_Backup_YYYY-MM-DD.json`.
5. **Restore from a Database Backup**:
   - Click **"📤 Restore Database from JSON"**.
   - Select your saved `.json` file.
   - The database engine validates the schema and loads the data with an instant refresh.
6. **Reset Database to Factory Defaults**:
   - Click the red **"🔄 Reset Database to Clean Defaults"** button.
   - Confirms and restores the clean original catalog, distributor orders, and demo requests.
7. **Inspect Raw Tables**:
   - Use the sub-tabs:
     - `[🛒 Customer Orders Table (sales)]`
     - `[📱 Smartphone Catalog Table (mobiles)]`
     - `[📦 Distributor Orders Table (orders)]`
     - `[🎁 Promotions Table (promotions)]`

---

### Method B: Via Browser Developer Tools (F12)
You can directly inspect and verify the stored database inside any browser:
1. Press `F12` or `Right-Click -> Inspect`.
2. Go to the **Application** tab (in Chrome/Edge) or **Storage** tab (in Firefox).
3. Under **Storage** on the left menu, expand **Local Storage** and select `file://` or `http://localhost`.
4. Locate the key: **`MAYANK_SAHU_MOBILE_STORE_DB_V2`**.
5. The `Value` column contains the entire JSON database.

---

### Method C: Via the Optional Node.js REST API Server
If you wish to demonstrate an active backend server to your project examiner:

1. Open PowerShell or Terminal in the project root:
   ```powershell
   cd "m:\data Analytics\MobileSalesSystem"
   ```
2. Install Express & CORS (one-time setup):
   ```bash
   npm install express cors
   ```
3. Start the server:
   ```bash
   node backend/server.js
   ```
4. Access the REST endpoints in your browser or Postman:
   - `http://localhost:5000/api/mobiles` $\rightarrow$ Lists all inventory mobiles
   - `http://localhost:5000/api/orders` $\rightarrow$ Lists all customer orders for the manager
   - `http://localhost:5000/api/database/export` $\rightarrow$ Downloads raw database snapshot

---

## 4. Entity-Relationship (ER) Schema Reference

| Table Name | Description | Key Attributes |
| :--- | :--- | :--- |
| **`managers`** | Authorized store administrator credentials | `id`, `username`, `password`, `name`, `designation` |
| **`mobiles`** | Inventory catalog with technical specs | `id`, `model`, `brand`, `price`, `qty`, `ram`, `storage`, `battery`, `camera`, `display`, `os`, `features` |
| **`sales` (customer_orders)** | Customer purchase orders & requests | `id`, `orderNo`, `billNo`, `customer`, `phone`, `address`, `mobileId`, `modelName`, `qty`, `unitPrice`, `discount`, `gst`, `status`, `date`, `time` |
| **`orders` (distributor_orders)** | Restocking procurement orders | `id`, `orderNo`, `mobileId`, `qty`, `distributor`, `expected`, `status`, `notes` |
| **`promotions`** | Active marketing campaigns | `id`, `title`, `mobileId` (0 for all), `discount`, `validUntil`, `desc` |

---

## 5. College Viva Questions & Answers

**Q1: How does the system handle real-time inventory consistency when a customer orders?**  
*Answer:* When a customer confirms an order, `processCustomerPurchase()` performs an atomic stock check (`if (targetMobile.qty < requestedQty)`). If sufficient, it decrements the mobile stock immediately (`targetMobile.qty -= requestedQty`), creates a customer order request with status `"Pending Approval"`, and commits the transaction to the database via `DBManager.save()`.

**Q2: What happens if the manager rejects or cancels a customer order?**  
*Answer:* If the manager cancels an order via the dashboard (`cancelCustomerOrder(orderId)`), the system safely restores the reserved stock (`mob.qty += order.qty`) back into store inventory and updates the order status to `"Cancelled"`, ensuring zero inventory leakage.

**Q3: How are low-stock alerts triggered for the manager?**  
*Answer:* Whenever an item's quantity drops below 3 units (e.g. following customer sales), the `checkLowStockAlerts()` routine triggers a high-priority banner on manager login, prompting Mayank Sahu to create distributor purchase orders.





# 📱 Mayank Sahu Mobile Store — Complete Project Run Guide
**Project Name:** Intelligent Mobile Sales, Inventory, Comparison & Billing Management System  
**Developer:** Mayank Sahu  
**Project Category:** Final Year College Academic Project (BCA / B.Tech / MCA / IT)  
**Document:** `guide.md`  

---

## 📑 Table of Contents
1. [Project Overview](#1-project-overview)
2. [Prerequisites](#2-prerequisites)
3. [Step-by-Step: How to Run the Project](#3-step-by-step-how-to-run-the-project)
   - [Method 1: Direct Browser Launch (Easiest & Recommended)](#method-1-direct-browser-launch-easiest--recommended)
   - [Method 2: Using VS Code Live Server](#method-2-using-vs-code-live-server)
   - [Method 3: Using Python HTTP Server](#method-3-using-python-http-server)
   - [Method 4: Running with Node.js Backend Server](#method-4-running-with-nodejs-backend-server)
4. [User Accounts & Login Credentials](#4-user-accounts--login-credentials)
5. [Step-by-Step Live Testing Walkthrough (For College Demo & Viva)](#5-step-by-step-live-testing-walkthrough-for-college-demo--viva)
   - [Scenario A: Placing an Order as Customer & Approving as Manager](#scenario-a-placing-an-order-as-customer--approving-as-manager)
   - [Scenario B: Low Stock Warning (< 3 Units) Demonstration](#scenario-b-low-stock-warning--3-units-demonstration)
   - [Scenario C: Multi-Device Specification Comparison](#scenario-c-multi-device-specification-comparison)
   - [Scenario D: Customer Bill Generation & Name-Only Editing](#scenario-d-customer-bill-generation--name-only-editing)
   - [Scenario E: Distributor Restocking Order & Inventory Update](#scenario-e-distributor-restocking-order--inventory-update)
   - [Scenario F: Database Management (Backup, Restore & Reset)](#scenario-f-database-management-backup-restore--reset)
6. [Project File Architecture](#6-project-file-architecture)
7. [Frequently Asked Questions & Troubleshooting](#7-frequently-asked-questions--troubleshooting)

---

## 1. Project Overview

**Mayank Sahu Mobile Store** is an enterprise-grade web application developed as a major academic project. It provides:
- **Dual Portal Security:** Independent workspaces for **Store Manager (Mayank Sahu)** and **Customers**.
- **Real-Time Order Queue:** Orders placed by customers immediately appear on the Store Manager's dashboard for review, approval, and dispatch.
- **Stock Automation & Safety Alerts:** Prominently alerts the manager upon login if any smartphone has dropped below the safety threshold of **3 units**.
- **Distributor Logistics:** Manager creates procurement purchase orders; confirming delivery automatically updates store inventory stock.
- **Specification Comparison Matrix:** Dynamic comparison tool allowing customers to compare 2–4 smartphones side-by-side with criterion toggles (Price, RAM, Storage, Camera, Battery, Display, OS) and best-value spec highlighting.
- **Invoice & Tax Compliance:** Auto-generates standard tax receipts with 18% GST calculation. As per business rules, customers can **edit only their own name** on the invoice—all technical and monetary fields remain strictly read-only.
- **Persistent Database Engine:** Browser-backed `LocalStorage` engine (`DBManager`) prevents data loss across browser tabs, reloads, or restarts, coupled with a 1-click JSON backup, restore, and factory reset console.

---

## 2. Prerequisites

| Requirement | Details |
| :--- | :--- |
| **Operating System** | Windows 10/11, macOS, or Linux |
| **Web Browser** | Google Chrome, Microsoft Edge, Mozilla Firefox, Brave, or Safari |
| **Software Needed** | **None** for basic run. Optional: VS Code, Python, or Node.js |

---

## 3. Step-by-Step: How to Run the Project

You can run this project using any of the 4 methods below. **Method 1 is the quickest and requires zero software installation.**

### Method 1: Direct Browser Launch (Easiest & Recommended)
> **Best for:** Instant preview, university presentations, and quick offline evaluation.

1. Open your File Explorer on your computer.
2. Navigate to the project directory:
   ```
   m:\data Analytics\MobileSalesSystem
   ```
3. Locate the file named **`index.html`**.
4. **Double-click `index.html`** (or right-click $\rightarrow$ **Open with** $\rightarrow$ **Google Chrome** or **Microsoft Edge**).
5. The application will immediately launch in your browser at `file:///.../index.html`.
6. All features, database storage, and customer-to-manager workflows will work out of the box!

---

### Method 2: Using VS Code Live Server
> **Best for:** Developers working inside Visual Studio Code.

1. Open **Visual Studio Code**.
2. Click **File** $\rightarrow$ **Open Folder...** and select:
   ```
   m:\data Analytics\MobileSalesSystem
   ```
3. If not already installed, install the **Live Server** extension by Ritwick Dey (Extension ID: `ritwickdey.LiveServer`).
4. In the left file explorer of VS Code, right-click on **`index.html`**.
5. Click **"Open with Live Server"** (or click *Go Live* in the bottom status bar).
6. Your browser will automatically open at:
   ```
   http://127.0.0.1:5500/index.html
   ```

---

### Method 3: Using Python HTTP Server
> **Best for:** Running a lightweight local web server via command line.

1. Open **PowerShell** or **Command Prompt (CMD)**.
2. Change directory to the project folder:
   ```powershell
   cd "m:\data Analytics\MobileSalesSystem"
   ```
3. Start Python's built-in web server:
   ```powershell
   python -m http.server 8000
   ```
4. Open your browser and navigate to:
   ```
   http://localhost:8000
   ```

---

### Method 4: Running with Node.js Backend Server
> **Best for:** Demonstrating a complete client-server REST API architecture to university examiners.

1. Ensure **Node.js** is installed on your machine (`node -v`).
2. Open PowerShell or Command Prompt.
3. Change directory to the project folder:
   ```powershell
   cd "m:\data Analytics\MobileSalesSystem"
   ```
4. Install backend dependencies (one-time command):
   ```powershell
   npm install express cors
   ```
5. Start the backend REST API server:
   ```powershell
   node backend/server.js
   ```
6. The terminal will display:
   ```
   =======================================================
   Mayank Sahu Mobile Store — Backend API Server
   Server running at: http://localhost:5000
   API Endpoints:
   - GET  /api/mobiles     -> List all mobiles
   - GET  /api/orders      -> List incoming orders for manager
   - POST /api/orders      -> Submit customer order request
   - PUT  /api/orders/:id/status -> Approve or cancel order
   =======================================================
   ```
7. Open `index.html` in your browser as described in Method 1 or 2.

---

## 4. User Accounts & Login Credentials

The project features a dedicated login gateway that cleanly disappears upon authentication. Use the credentials below:

```
+-----------------------------------------------------------------------------------------+
| ROLE                 | USERNAME            | PASSWORD   | ACCESS LEVEL                  |
+----------------------+---------------------+------------+-------------------------------+
| 👔 Store Manager     | mayank              | admin123   | Full Store & Managerial Admin |
| 👤 Customer Account  | Any Name (e.g. Joy) | pass123    | Shopping, Buying, & Invoices  |
+-----------------------------------------------------------------------------------------+
```

### How Login Navigation Works:
1. When opening the app, you will see the **Login Screen**.
2. Click **👔 Store Manager** or **👤 Customer Portal** tab to switch roles.
3. Notice that username and password automatically pre-fill for demo convenience.
4. Click **"Enter Application Portal →"** (or press `Enter` on your keyboard).
5. The login box will smoothly fade out, the top store navigation bar will appear, and you will be routed directly to your portal dashboard!
6. To switch users at any time, click the **🚪 Logout** button at the top right.

---

## 5. Step-by-Step Live Testing Walkthrough (For College Demo & Viva)

Follow these scenarios during your project viva or demonstration:

### Scenario A: Placing an Order as Customer & Approving as Manager
> **Demonstrates:** Real-time customer order transmission to manager dashboard.

1. **Log in as Customer:**
   - On the login screen, select **👤 Customer Portal**.
   - Enter your name: e.g. `Akash Gupta`.
   - Enter password: `pass123`.
   - Click **Enter Application Portal →**.
2. **Place an Order:**
   - In the navigation bar, click **🛒 Buy & Checkout**.
   - Select a smartphone: e.g. **OnePlus 12 (OnePlus) — ₹64,999**.
   - Set Quantity: `1`.
   - Fill in contact details:
     - Phone: `9876543210`
     - Address: `Flat 14, Galaxy Heights, Mumbai`
   - Select promotion (optional): *Festive Mega Discount (15% OFF)*.
   - Observe that the grand total calculates automatically with 18% GST.
   - Click **"✅ Confirm Purchase & Submit Order Request →"**.
3. **Customer Confirmation:**
   - A success popup appears stating order request `ORD-REQ-XXX` has been forwarded to Store Manager Mayank Sahu.
   - You are taken to the **🧾 My Official Invoice** tab showing your bill.
4. **Log out as Customer:**
   - Click **🚪 Logout** at the top right.
5. **Log in as Store Manager (Mayank Sahu):**
   - Select **👔 Store Manager**.
   - Username: `mayank` | Password: `admin123`.
   - Click **Enter Application Portal →**.
6. **Verify on Manager Dashboard:**
   - Look at the top card: **🔔 Incoming Customer Order Requests**.
   - You will see a live alert badge: `🔔 1 New Request Pending`.
   - In the table, Akash Gupta's order is highlighted at the top with status **`⏳ Pending Approval`**!
7. **Manager Action:**
   - Click **"👁️ Invoice"** to inspect Akash's tax receipt.
   - Click **"✓ Approve"** to approve and dispatch the order!
   - Status updates to **`🚚 Approved & Dispatched`**, and store revenue updates automatically.

---

### Scenario B: Low Stock Warning (< 3 Units) Demonstration
> **Demonstrates:** Automated inventory safety threshold alerts.

1. Log in as Store Manager: `mayank` / `admin123`.
2. Notice the yellow banner at the top of the dashboard:
   - **⚠️ Low Inventory Threshold Warning (Less than 3 in store):**  
     Lists devices with stock below 3 units (e.g. *Pixel 8 Pro* with 2 units, *Realme GT 5* with 1 unit).
3. If an item drops to 0 units, a red **🚨 CRITICAL: Out of Stock Alert!** banner appears.
4. When any customer purchases items bringing remaining stock under 3, this banner updates dynamically on the next login!

---

### Scenario C: Multi-Device Specification Comparison
> **Demonstrates:** Dynamic comparison matrix with criteria selection and spec highlighting.

1. Log in as Customer: `Rahul Verma` / `pass123`.
2. On the **📱 Browse Smartphones** tab, check the **"Compare"** checkbox on 2 or 3 smartphones (e.g., *Galaxy S24 Ultra*, *iPhone 15 Pro*, and *OnePlus 12*).
3. Click the **⚖️ Compare Devices** tab.
4. View the side-by-side comparison matrix.
5. Toggle criteria chips on/off (e.g., click *Price*, *RAM*, *Camera*, *Battery*).
6. Notice that the **best specification in each row is highlighted in green** with a `★ Best` badge (lowest price, highest RAM, highest battery, etc.).
7. Click **"🛒 Buy This"** directly from the comparison column to initiate checkout!

---

### Scenario D: Customer Bill Generation & Name-Only Editing
> **Demonstrates:** Security and business logic where customer can update only their name on the bill.

1. Log in as Customer, select a mobile, and complete purchase.
2. In the **🧾 My Official Invoice** tab, observe:
   - Invoice Number, Order ID, Date & Time.
   - Device Model, RAM, Storage, OS, Quantity, Unit Price, GST, and Grand Total are **completely read-only**.
3. Click the button: **"✏️ Edit Customer Name Only"**.
4. A prompt appears asking for your updated name. Enter a new name: e.g. `Rahul M. Verma`.
5. The invoice updates the Customer Name field immediately.
6. Click **"🖨️ Print Invoice Receipt"** to trigger standard browser printing formatted cleanly with print CSS.

---

### Scenario E: Distributor Restocking Order & Inventory Update
> **Demonstrates:** B2B procurement workflow and stock replenishment.

1. Log in as Store Manager: `mayank` / `admin123`.
2. Click the **📦 Distributor Restocking** tab.
3. Select a device: e.g. **Pixel 8 Pro (Current Stock: 2)**.
4. Quantity: `15`.
5. Distributor Name: `Apex Global Telecom Logistics`.
6. Delivery Date: Select any upcoming date.
7. Click **"📤 Dispatch Purchase Order"**.
8. In the **Distributor Order History** table below, the order appears with status `⏳ Pending Arrival`.
9. Click **"📦 Mark Received (+15)"**.
10. Confirm delivery $\rightarrow$ Stock for Pixel 8 Pro automatically jumps from 2 to 17 units, and the low-stock alert disappears!

---

### Scenario F: Database Management (Backup, Restore & Reset)
> **Demonstrates:** LocalStorage persistence, backup export, and factory reset.

1. Log in as Store Manager: `mayank` / `admin123`.
2. Click the **🗄️ Database Management** tab.
3. Observe live database metrics: Storage engine (`LocalStorage`), record count, storage size.
4. Click **"📥 Download Database Backup (.JSON)"** $\rightarrow$ A complete JSON file downloads to your computer.
5. Click **"🔄 Reset Database to Clean Defaults"** $\rightarrow$ Restores clean demo data.
6. Click **"📤 Restore Database from JSON"** $\rightarrow$ Select your saved JSON file to restore all your data instantly.

---

## 6. Project File Architecture

```
MobileSalesSystem/
├── index.html                   (Main UI: Login, Manager Portal & Customer Portal)
├── guide.md                     (This comprehensive execution & testing guide)
├── DATABASE_MANAGEMENT_GUIDE.md (Detailed database & ER schema documentation)
├── css/
│   └── styles.css               (CSS Variables, Animations, Components, & Print Styles)
├── js/
│   ├── data.js                  (Persistent LocalStorage Engine & In-Memory Database)
│   ├── utils.js                 (Currency Formatters, Screen Routing, & Modal Handlers)
│   ├── auth.js                  (Role Switcher, Login Transitions, & Session Termination)
│   ├── manager.js               (Manager Dashboard, Incoming Orders, Catalog CRUD, & Analytics)
│   └── customer.js              (Browse Catalog, Compare Matrix, Checkout, & Invoice Generator)
└── backend/
    └── server.js                (Optional Node.js / Express REST API Server)
```

---

## 7. Frequently Asked Questions & Troubleshooting

### Q1: Do I need to install Node.js or run any terminal commands?
**No.** The project is completely self-contained. You can simply double-click `index.html` to open it in any web browser.

### Q2: Why does data stay saved even after I refresh the page?
The application uses the built-in **`DBManager`** engine which serializes all changes to browser `LocalStorage` under the key `MAYANK_SAHU_MOBILE_STORE_DB_V2`.

### Q3: How do I completely clear all data and start fresh?
Log in as Store Manager (`mayank` / `admin123`), go to the **🗄️ Database Management** tab, and click **"🔄 Reset Database to Clean Defaults"**.

### Q4: Can I test on a mobile phone or tablet?
Yes! The CSS layout is fully responsive and adjusts seamlessly across desktop monitors, laptops, tablets, and smartphone viewports.

---

*Academic Project Developed with care by **Mayank Sahu** • Final Year College Major Project*
