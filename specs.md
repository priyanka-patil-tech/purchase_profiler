# Costco Multi-Account Dashboard Specification

## 1. Product and deployment

- The application is a static, single-page dashboard served from `index.html`. Hosting serves the page and its assets; receipt analysis runs in the user's browser.
- The hosted utility is available at [priyanka-patil-tech.github.io/purchase_profiler](https://priyanka-patil-tech.github.io/purchase_profiler).
- Users can also run the page locally in a modern browser.
- Transaction files are read and analyzed in the browser. Receipt data is never uploaded to this project's server or an application backend.
- The Costco export bookmarklet makes requests directly from the user's browser to Costco and downloads the JSON export to the user's device.
- The page uses Chart.js for the monthly trend visualization and GoatCounter for page-view and anonymous interaction analytics.

## 2. User interface

- Show a Costco Multi-Account Dashboard title, purchase-channel filters, file selection, CSV export, and a Costco data-export bookmarklet.
- Place an information button beside the dashboard title. It opens a help panel covering only how to export data from Costco and load the resulting JSON files.
- Place the GitHub repository link in the page footer.
- Display KPI cards, a monthly spending chart, ranked product tables, and a fuel-log table.
- Follow the operating system light/dark color preference and adapt the table layout for narrow screens.

## 3. Data ingestion and normalization

- Accept one or more JSON files through the file input.
- Support arrays of receipt/order records, the combined `{ "warehouseReceipts": [], "onlineOrders": [] }` export, and the supported GraphQL `data.getOnlineOrders.bcOrders` response envelope.
- Identify online orders by `orderTotal`; treat other records as warehouse receipts unless their `receiptType` specifies a different channel.
- Deduplicate records across uploads using the order number or order header ID for online orders, and the transaction barcode or a date/total/membership fallback key for receipts.
- Normalize dates, totals, account references, channel types, and item lists for dashboard calculations.
- Apply warehouse coupon lines as offsets to the preceding eligible item when computing product totals.
- Use an even share of the order total per line when an online order does not provide line-item amounts.
- Keep imported records in page memory for the current session; do not persist receipt data to an application server.

## 4. Dashboard behavior

- Provide filters for All, In-Warehouse, Online, Gas Station, and Car Wash. Filtering updates the chart and insight tables.
- Calculate summary metrics across the ingested records, including spend, trip counts, item quantity, unique item count, account count, and fuel volume.
- Render monthly spending, purchase-count, and item-count trends.
- Rank products by total spend, purchase frequency, and average cost, and show gas fill-ups with date, volume, unit price, total cost, and account reference.
- Enable CSV export after data has loaded. Export rows should respect the selected channel filter.
- The Costco bookmarklet exports warehouse receipts and online orders as a JSON file for loading into the dashboard.

## 5. Analytics and privacy

- Load the GoatCounter counter script from `gc.zgo.at/count.js` and send page views to the configured GoatCounter `/count` endpoint.
- Track page interactions as GoatCounter events using the interaction type and a control identifier or element type.
- Never include receipt contents, uploaded filenames, account identifiers, or input values in analytics events.
- GoatCounter receives anonymous page-view and interaction analytics; it is a separate external service and does not receive receipt data.
- If GoatCounter is unavailable while an event occurs, queue the anonymous event until the counter script loads.

## 6. DOM contract

The following IDs are used by dashboard behavior and must remain available:

```text
#fileInput
#exportCsvBtn
#howToMenuLink
#howToMenu
#filterAll
#filterWarehouse
#filterOnline
#filterGasStation
#filterCarWash
#kpiTotalFuelVolume
#chartHeading
#trendChart
#topSpentTableBody
#topFreqTableBody
#topExpensiveTableBody
#fuelLogTableBody
```

Table rows must retain their displayed column counts:

| Table body | Columns |
|---|---:|
| `#topSpentTableBody` | 5 |
| `#topFreqTableBody` | 5 |
| `#topExpensiveTableBody` | 6 |
| `#fuelLogTableBody` | 5 |
