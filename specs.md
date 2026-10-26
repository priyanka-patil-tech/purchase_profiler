# Spec-Driven Development (SDD) Specification: Costco Dashboard Data Engine & UI

## 1. Automated Behavioral Contract (Gherkin / BDD)

```gherkin
Feature: Multi-Account File Ingestion and Deduplication
  As a User with multiple Costco memberships
  I want to upload duplicate, overlapping, or multi-channel receipt JSON exports
  So that my warehouse and online metrics are consolidated without artificial inflation

  Background:
    Given an empty global dataset state variable "globalReceiptsList"

  Scenario Outline: Ingesting overlapping files filters out duplicate records
    When file <File_A> is processed by the ingestion engine
    And file <File_B> is subsequently processed by the ingestion engine
    Then the internal array "globalReceiptsList" should contain exactly <Final_Count> records
    And the DOM element "#kpiTotalSpent" text content should exactly match <Expected_Spent_String>

    Examples:

      | File_A            | File_B            | Final_Count | Expected_Spent_String |
      | "redmond_q3.json" | "redmond_q3.json" | 34          | "\$1,450.22"            |
      | "redmond_q3.json" | "kirkland_q3.json"| 52          | "\$2,891.10"            |

  Scenario: Sequential Coupon Normalization
    Given a receipt contains consecutive item lines:

      | itemNumber | itemDescription01 | amount | unit |
      | "112218"   | "HAAGEN DAZS"     | 13.99  | 1    |
      | "388447"   | "/  112218"       | -3.50  | -1   |
    When the normalization engine processes the receipt item array
    Then the containing receipt row item object "amount" value should update to 10.49
    And product "112218" total spent should be computed as exactly 10.49
    And product "112218" times bought should be computed as exactly 1

  Scenario: Fuel Ingestion Analysis
    Given a Gas Station receipt containing fuel logging metadata properties:

      | itemNumber | itemDescription01 | amount | unit | fuelUnitQuantity | itemUnitPriceAmount |
      | "800599"   | "REGULAR GAS"     | 59.63  | 1    | 11.469           | 5.199               |
    When the ingestion processor updates runtime calculations
    Then the total fuel volume state value should increment by exactly 11.469
    And DOM element "#kpiTotalFuelVolume" text content should match "11.469 Gal"
    And a transaction record row should be cleanly appended to "#fuelLogTableBody" containing exactly 5 columns
```

---

## 2. Strict Input/Output Schema Contracts (JSON Schema)

The engine layer must maintain a fully conformant parsing alignment against this absolute structural layout model, supporting wrapped GraphQL response envelopes for both warehouse receipts and online order histories.

```json
{
  "\$schema": "https://json-schema.org",
  "title": "CostcoUnifiedState",
  "type": "object",
  "required": ["globalReceiptsList", "currentFilterMode"],
  "properties": {
    "currentFilterMode": {
      "type": "string",
      "enum": ["all", "In-Warehouse", "Online", "Gas Station", "Car Wash"]
    },
    "globalReceiptsList": {
      "type": "array",
      "items": {
        "type": "object",
        "anyOf": [
          {
            "required": ["membershipNumber", "warehouseName", "receiptType", "total", "transactionBarcode"],
            "properties": {
              "transactionBarcode": { "type": ["string", "null"] },
              "total": { "type": "number" },
              "itemArray": {
                "type": "array",
                "items": {
                  "type": "object",
                  "required": ["itemNumber", "itemDescription01", "amount", "unit"],
                  "properties": {
                    "fuelUnitQuantity": { "type": ["number", "null"] },
                    "itemUnitPriceAmount": { "type": ["number", "null"] }
                  }
                }
              }
            }
          },
          {
            "required": ["orderHeaderId", "orderPlacedDate", "orderNumber", "orderTotal", "orderLineItems"],
            "properties": {
              "orderNumber": { "type": "string" },
              "orderTotal": { "type": "number" },
              "emailAddress": { "type": "string" },
              "orderLineItems": {
                "type": "array",
                "items": {
                  "type": "object",
                  "required": ["itemNumber", "itemDescription"],
                  "properties": {
                    "itemNumber": { "type": "string" },
                    "itemDescription": { "type": "string" }
                  }
                }
              }
            }
          }
        ]
      }
    }
  }
}
```

---

## 3. DOM Structural Invariants (HTML Contract)

Any compilation pass must guarantee that these query indicators remain present, unmutated, and output exact table column dimensions:

```typescript
interface DashboardDOMSignature {
  "#fileInput":           HTMLInputElement;
  "#exportCsvBtn":       HTMLButtonElement;
  "#chartHeading":       HTMLHeadingElement;
  "#trendChart":         HTMLCanvasElement;
  
  // KPI Metrics Panel Assertions
  "#kpiTotalFuelVolume": HTMLSpanElement;

  // Table Heading Assertions
  "#tSpentHeading":      HTMLHeadingElement;
  "#tFreqHeading":       HTMLHeadingElement;
  "#tExpHeading":        HTMLHeadingElement;
  "#tFuelHeading":       HTMLHeadingElement;

  // Table Body Assertions (Strict Column Dimension Signatures)
  "#topSpentTableBody":  HTMLTableSectionElement; // Must generate exactly 5 <td> children per row line
  "#topFreqTableBody":   HTMLTableSectionElement; // Must generate exactly 5 <td> children per row line
  "#topExpensiveTableBody": HTMLTableSectionElement; // Must generate exactly 6 <td> children per row line
  "#fuelLogTableBody":   HTMLTableSectionElement; // Must generate exactly 5 <td> children per row line
}
```

---

## 4. UI Invariant Matrix (CSS Tokens & States)

```typescript
type ActiveTheme = 'light' | 'dark';

interface UIThemeMatrix {
  backgroundColor: (theme: ActiveTheme) => theme === 'dark' ? '#0f172a' : '#f8fafc';
  cardBackground:  (theme: ActiveTheme) => theme === 'dark' ? '#1e293b' : '#ffffff';
  tableHeaderBg:   (theme: ActiveTheme) => theme === 'dark' ? '#151f32' : '#f1f5f9';
}

interface ResponsiveBreakpointInvariants {
  viewportWidthLessThan1024px: {
    ".tables-grid": "grid-template-columns: 1fr";
    "header":       "flex-direction: column";
  };
}
```

---

## 5. Embedded Costco Scraper Bookmarklet Payload

The toolbar bookmark shortcut executes parallel asynchronous network transformations against Costco's secure cloud service gateways (`https://costco.com`). 

It harvests authentication parameters from active `localStorage` tokens to safely scrape data across two separate endpoints:
1. **Warehouse Receipts Ledger:** Queries the `receiptsWithCounts` node utilizing a three-year calendar fallback window formatted as `MM/DD/YYYY`.
2. **Online Orders Repository:** Queries the `getOnlineOrders` node schema utilizing sanitized query variables formatted in standard ISO `YYYY-MM-DD` syntax.

If explicit line-item costs are omitted by the online API gateway response loop, the local engine data transformer layer will automatically apply a fallback algorithm that evenly distributes the total transaction value across the order elements to protect top-level math aggregates.
