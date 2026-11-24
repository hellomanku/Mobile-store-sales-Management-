# 📱 Mayank Sahu Mobile Store - Project Execution & User Guide
**Final Year College Project — Sales, Inventory, Comparison, Billing & Mobile Authentication System**  
**Developer:** Mayank Sahu  

---

## 📖 Table of Contents
1. [Project Overview](#1-project-overview)
2. [Step-by-Step: How to Run the Project](#2-step-by-step-how-to-run-the-project)
   - [Method 1: Direct Browser Launch (Quickest)](#method-1-direct-browser-launch-quickest)
   - [Method 2: Standalone Authentication Suite (`auth.html`)](#method-2-standalone-authentication-suite-authhtml)
   - [Method 3: VS Code Live Server](#method-3-vs-code-live-server)
   - [Method 4: Node.js / Python Local Server](#method-4-nodejs--python-local-server)
3. [Mobile Number-Based Authentication System](#3-mobile-number-based-authentication-system)
   - [A. Login Interface (Password & Instant Mobile OTP)](#a-login-interface)
   - [B. Registration Page with Mobile OTP Verification](#b-customer-registration-page)
   - [C. Forgot Password Page (3-Step Mobile Recovery)](#c-forgot-password-page)
   - [D. Simulated SMS Gateway & Push Notifications](#d-simulated-sms-gateway--push-notification-toast)
4. [Login Credentials & Roles Reference](#4-login-credentials--roles-reference)
5. [Complete Store Feature Walkthrough](#5-complete-store-feature-walkthrough)
   - [Customer Workflow](#1-customer-workflow)
   - [Manager Workflow](#2-manager-workflow)
6. [Database Architecture & Persistence](#6-database-architecture--persistence)
7. [Troubleshooting & Bug Fixes](#7-troubleshooting--bug-fixes)

---

## 1. Project Overview
The **Mayank Sahu Mobile Store** is an enterprise-grade mobile store management application built using vanilla web technologies (HTML5, CSS3, JavaScript ES6+). It operates with zero mandatory external dependencies, saving state persistently using browser `LocalStorage`.

The project includes an **End-to-End Mobile Number-Based Authentication Suite** featuring:
- **Dual-mode Login** (Password Login + Instant Passwordless Mobile OTP Login).
- **Customer Registration** with live mobile phone validation and OTP verification.
- **Forgot Password Recovery** with a 3-step mobile OTP verification wizard.
- **Simulated SMS Gateway** displaying realistic smartphone push notifications with 1-click **Auto-fill OTP**, 60-second countdown timers, and attempt limiters.

---

## 2. Step-by-Step: How to Run the Project

### Method 1: Direct Browser Launch (Quickest)
1. Open File Explorer in Windows.
2. Navigate to: `M:\data Analytics\MobileSalesSystem`
3. Double-click on `index.html` (or right-click -> **Open with** -> **Google Chrome** / **Microsoft Edge** / **Firefox**).
4. The application will immediately load with the unified authentication portal.

---

### Method 2: Standalone Authentication Suite (`auth.html`)
To test or present the authentication system in isolation:
1. Double-click on `auth.html` in `M:\data Analytics\MobileSalesSystem`.
2. This opens the **Dedicated Authentication Suite** featuring:
   - Login, Registration, and Forgot Password cards.
   - An **Interactive Virtual Smartphone Device** on the right side that displays incoming SMS messages in real time!
   - A **Live Database Accounts Explorer** displaying all accounts in `localStorage`.
   - A 1-click button to jump directly into the full store (`index.html`).

---

### Method 3: VS Code Live Server
1. Open Visual Studio Code.
2. Click **File** -> **Open Folder...** -> select `M:\data Analytics\MobileSalesSystem`.
3. If not already installed, install the **Live Server** extension (by Ritwick Dey).
4. Right-click on `index.html` or `auth.html` and click **"Open with Live Server"**.
5. Your browser will open automatically at `http://127.0.0.1:5500/index.html`.

---

### Method 4: Node.js / Python Local Server
#### Using Node.js `npx serve`:
```bash
cd "M:\data Analytics\MobileSalesSystem"
npx serve .
```
Open `http://localhost:3000`.

#### Using Python 3:
```bash
cd "M:\data Analytics\MobileSalesSystem"
python -m http.server 8000
```
Open `http://localhost:8000`.

---

## 3. Mobile Number-Based Authentication System

### A. Login Interface
- **Dual Authentication Modes:**
  1. **🔑 Password Login:** Enter Username, Customer Name, or 10-digit Mobile Number + Password.
     - Includes Show/Hide password toggle (eye icon).
     - Direct link to "Forgot Password?".
  2. **📱 Mobile OTP Login (Passwordless):**
     - Enter 10-digit mobile number with `+91` country prefix.
     - Click **Send OTP** -> receives 6-digit code via simulated SMS.
     - Enter 6-digit code or click **⚡ Auto-fill OTP** on the notification.
     - Logs the user in directly!
- **Role Tabs:** Easily toggle between **Store Manager** and **Customer Portal**.
- **Quick Demo Fill Buttons:** One-click pre-filling for Manager (`mayank` / `admin123`) or Customer (`9876543210` / `pass123`).

---

### B. Customer Registration Page
Accessed via *"Don't have an account? Create an Account"* on the login screen.
1. **Full Name:** Customer's real name.
2. **Mobile Number Verification:**
   - User inputs 10-digit mobile number.
   - Clicks **📲 Send OTP**.
   - Validates that the number is not already registered.
   - Dynamic OTP panel expands with a 6-digit input box and 60-second resend countdown.
   - Enters code and clicks **Verify** -> displays **✅ Mobile Verified** badge and locks phone number.
3. **Password & Strength Meter:**
   - Real-time password strength meter (Weak -> Medium -> Strong) as user types.
   - Confirm Password field with mismatch prevention.
4. **Account Creation:**
   - Saves account permanently into `DB.customers` in `localStorage`.
   - Automatically signs the customer in and opens the Customer Portal.

---

### C. Forgot Password Page
Accessed via *"Forgot Password?"* on the login screen. Uses an intuitive 3-step recovery wizard:
- **Step 1 (Mobile Verification):**
  - Enter registered 10-digit mobile number.
  - Verifies that an account exists for this mobile number.
  - Generates a 6-digit reset OTP and dispatches it via SMS Gateway.
- **Step 2 (OTP Verification):**
  - Enter 6-digit verification code.
  - Validates code against the active session (5-minute expiration, max 3 attempts).
- **Step 3 (Set New Password):**
  - Enter new password and confirm password.
  - Updates the password in `localStorage`.
  - Automatically redirects back to Login with the mobile number pre-filled.

---

### D. Simulated SMS Gateway & Push Notification Toast
- Generates secure random 6-digit numeric OTPs.
- Floating push notification toast appears at the top-right corner designed like a smartphone notification:
  ```
  💬 Messages • Just Now
  Mayank Sahu Mobile Store: Your verification code for [Purpose] is: [ 482910 ]
  Valid for 5 minutes.
  [⚡ Auto-fill OTP] [Dismiss]
  ```
- Clicking **⚡ Auto-fill OTP** automatically pastes the code into the active OTP field and flashes it green for seamless demonstrations!
- Includes pleasant two-tone synthesized notification audio chime via Web Audio API.

---

## 4. Login Credentials & Roles Reference

| Role | Username / Identifier | Registered Mobile | Password | Login Options |
| :--- | :--- | :--- | :--- | :--- |
| **Store Manager** | `mayank` | `9988776655` | `admin123` | Password Login or Mobile OTP Login |
| **Customer (Rahul)** | `Rahul Verma` | `9876543210` | `pass123` | Password Login or Mobile OTP Login |
| **Customer (Priya)** | `Priya Sharma` | `9812345678` | `pass123` | Password Login or Mobile OTP Login |
| **Customer (Deepak)**| `Deepak Joshi` | `9871122334` | `pass123` | Password Login or Mobile OTP Login |
| **New Customer** | Registered Name | User's 10-digit Mobile | User's Password | Verified via SMS OTP during Registration |

---

## 5. Complete Store Feature Walkthrough

### 1. Customer Workflow:
1. **Login:** Log in via Password or Mobile OTP.
2. **Promotions Banner:** Active discounts created by the manager appear dynamically. Click to view promo details.
3. **Browse & Filter:** Search by model, brand, price slider, and RAM filter.
4. **Side-by-Side Mobile Comparison:**
   - Check the **Compare** checkbox on 2 to 4 mobiles.
   - Click **Compare Mobiles** to view a full specification matrix (Price, RAM, Storage, Camera, Battery, OS, Features).
5. **Purchase Order & Billing:**
   - Click **Buy Now** -> verify pre-filled customer details.
   - Click **Confirm Purchase & Generate Bill**.
   - **Bill Editing:** As per project guidelines, only the **Customer Name is editable** on the bill; all phone specs and prices are locked read-only.
   - Click **Print Receipt** for formatted invoice printing.
6. **Live Request to Manager:** Order is dispatched to the manager's incoming requests queue in real time.

---

### 2. Manager Workflow:
1. **Login:** Log in with `mayank` / `admin123` or Manager OTP `9988776655`.
2. **Low Stock Notification:**
   - Any phone with **less than 3 units** triggers an urgent amber alert banner on the manager's welcome screen.
3. **Incoming Customer Requests:**
   - Real-time table of customer orders with **Approve & Dispatch** or **Cancel** controls.
4. **Distributor Ordering:**
   - Order stock from distributors (Samsung Direct, Apple Logistics, etc.).
   - Receiving shipments updates store inventory counts automatically.
5. **Promotions Manager:** Create discounts and promotional copy displayed on customer welcome screens.
6. **Sales Analytics:** Visual interactive charts displaying sales broken down by phone models and monthly sales trends.

---

## 6. Database Architecture & Persistence
- **Storage Engine:** Browser `localStorage` (Key: `MAYANK_SAHU_MOBILE_STORE_DB_V2`).
- **Core Collections:**
  - `managers`: Store manager accounts with usernames, phones, credentials.
  - `customers`: Registered customer accounts with verified mobile numbers.
  - `mobiles`: Store catalog, stock quantities, and technical specifications.
  - `sales`: Customer orders and completed transactions.
  - `orders`: Re-stocking purchase orders placed to distributors.
  - `promotions`: Marketing campaigns and seasonal discount banners.
- **Backup & Reset:** The Manager portal includes buttons to **Export JSON Database Backup** and **Reset to Default Demo State**.

---

## 7. Troubleshooting & Bug Fixes

### Error: `Uncaught Error at setLoginRole (auth.js:18:21) at onclick (index.html:56:110)`
- **Root Cause:** In earlier versions, `DBManager.init()` in `data.js` was invoked before `managerInventoryList` and `currentActiveRole` were declared, triggering a Temporal Dead Zone error.
- **Resolution:** All state variables are now declared with global scope before `DBManager.init()` runs, and defensive fallback checks are implemented in `auth.js`.
