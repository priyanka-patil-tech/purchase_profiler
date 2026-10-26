# 🧾 Costco Multi-Account Spend Dashboard

An offline-first analytical platform that securely consolidates three years of multi-account Costco receipts, gas fill-ups, and online orders into a unified private dashboard. By automatically normalizing manufacturer coupon offsets and tracking true unit-pricing metrics locally, it uncovers deep spending insights that standard bank statements completely miss.

---

## 🚀 Quick Start Guide

### Step 1: Extract Your Purchase History
Because modern browsers use strict security layers, you have two ways to safely download your transaction history directly from Costco's servers:

#### Method A: The Drag-and-Drop Bookmarklet (Easiest)
1. Open your local dashboard file (`index.html`) in your browser.
2. Drag the **🚀 Drag to Bookmarks (Costco Universal Downloader)** button directly up onto your browser's **Bookmarks Bar**.
3. Navigate your browser tab straight to **[https://costco.com](https://costco.com)** and log completely into your customer account profile.
4. Once logged in and on the homepage or order status screen, **click the bookmark** you just saved in your toolbar.
5. An alert will display showing how many receipts and online orders were found, and a consolidated `.json` tracking file will automatically save to your computer.

#### Method B: The Native Console Alternative (Fallback)
If your browser settings block the bookmark link from running, follow these fallback steps instead:
1. Log into your profile on **[https://costco.com](https://costco.com)**.
2. Press **`F12`** (or `Cmd + Option + I` on Mac) to open up the browser's Developer Tools panel.
3. Click over onto the **Console** tab at the top of that side panel.
4. Copy the raw extraction block code provided inside your dashboard script parameters, paste it into the prompt line, and hit **Enter**.
5. Your combined data file will immediately download to your machine.

---

### Step 2: Load Your Data into the Dashboard
1. Open your local **`index.html`** file in any standard browser.
2. Click the **📁 Upload JSON Files** action button at the top of the pane.
3. Select one or more of the exported `.json` accounting files you generated from Method A or Method B.
4. The application will instantly ingest, deduplicate, merge, and plot your metrics on charts and datatables!

---

## 🛠️ Main Dashboard Features

*   **🔄 Overlap & Duplicate Protection:** Select duplicate files or upload overlapping timeframes safely. The system filters unique keys via transactional hashing (`transactionBarcode` or `orderNumber`) to keep charts completely accurate without artificial balance inflation.
*   **🌓 Auto Light/Dark System Sync:** The UI natively follows your active operating system color themes. Shifting window canvas modes instantly recolors card plates, layout borders, and Chart.js trend paths.
*   **📈 Smart Coupon Normalization:** Intercepts parent item rows and sequential manufacturer deduction lines automatically. Real-time cost offsets subtract balances directly down to true unit price values prior to updating top-level analytical totals.
*   **🌐 Multi-Channel Segment Filtering:** Seamlessly switch dashboard trendlines, KPI metrics panels, and cost ledger rankings between **In-Warehouse store purchases** and **Costco.com online orders** using integrated interactive ribbon filters.
*   **⛽ Fuel Logistics Dashboard Tracking:** Aggregates newly structured fuel quantity schemas out of transaction nodes. Renders running lifetime metrics via a `Total Fuel Volume` sub-indicator card, and prints chronological gas log entries detailing fill-up dates, volumes (Gallons), and unit pricing ($/Gal) across exactly 5 clean layout columns.

---

## ❓ Troubleshooting & FAQs

#### Error: "Cross-Origin Request Blocked" (CORS)
*   **Why it happens:** You clicked your toolbar scraper bookmark shortcut while looking at a site other than Costco.com. 
*   **The Fix:** Simply point your browser window to `https://costco.com`, verify your session log-in state is active, and click the link again.

#### Error: "Try again after login"
*   **Why it happens:** Costco's cloud servers dropped your current account session cookie parameters, or you have been idling on the site for too long.
*   **The Fix:** Log completely out of your account profile screen on Costco.com, sign right back in to renew your fresh security keys, and trigger your data tool.

---

## 📜 License & Usage Terms

This project is licensed under the **Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0)**. 

* **Hobbyist & Personal Use:** You are completely free to download, modify, and run this dashboard locally for your personal or household Costco accounts.
* **Commercial & Corporate Restriction:** You **may not** use this software, its scraping mechanisms, or its architectural design for commercial purposes, corporate deployment, or within business environments. Processing transactional history metadata extracted from Costco Business Memberships for corporate bookkeeping or automated tax preparation loops is strictly prohibited.

For full legal terms, please review the accompanying `LICENSE.md` file. For professional licensing inquiries, please contact the repository owner.
