# Retail Sales & Inventory Analytics with Tableau

A business intelligence portfolio project that transforms a synthetic
retail Excel dataset into Tableau dashboards for sales and inventory
analysis.

**Project status:** In progress — dashboard layouts created; final
calculation validation and publishing pending.

## Project Overview

This project models the reporting needs of a fictional single-branch
spare-parts retailer in Sri Lanka.

The retailer's Excel reporting baseline is extended into visual
dashboards intended to help users investigate sales performance,
gross profit, stock availability, and reorder priorities.

All business records are synthetic. Monetary values are in Sri Lankan
Rupees (LKR).

## Business Questions

- How do revenue and gross profit change over time?
- Which categories and products generate the most revenue?
- How many distinct customer invoices were recorded?
- What is the value of inventory at the latest month end?
- Which products are at or below their reorder points?
- How does month-end inventory value change over time?

## Tools

| Tool | Purpose |
|---|---|
| Tableau Desktop Public Edition | Dashboard development and calculations |
| Microsoft Excel | Source dataset and reporting baseline |
| GitHub | Project documentation and version history |

## Dashboard Previews

The screenshots below show the current development version.
See the validation checklist for outstanding corrections.

### Sales Overview

<img width="1500" height="1100" alt="Sales Overview (2)" src="https://github.com/user-attachments/assets/94077db3-8bf6-49f5-8423-8566dc43bf68" />

The sales dashboard contains:

- Total revenue
- Gross profit
- Gross margin
- Distinct invoice count
- Monthly revenue trend
- Revenue by category
- Leading products by revenue

### Inventory Overview

<img width="1450" height="1000" alt="inventory Report (2)" src="https://github.com/user-attachments/assets/9f138acb-d0b8-4da4-8dff-407c578bde96" />

The inventory dashboard is designed to contain:

- Latest month-end stock units
- Latest month-end inventory value
- Number of products requiring reorder review
- Product-level reorder list
- Inventory value by category
- Monthly inventory value trend

## Dataset

| Attribute | Details |
|---|---|
| Business scenario | Fictional spare-parts retailer |
| Reporting period | January 2024–December 2025 |
| Currency | LKR |
| Products | 40 |
| Sales lines | 7,395 |
| Distinct invoices | 3,805 |
| Inventory snapshots | 960 |
| Purchase orders | 316 |
| Inventory reporting frequency | Month end |

### Source Tables

| Table | Grain |
|---|---|
| Sales | One product line on an invoice |
| Products | One product |
| Inventory | One product at one month end |
| Purchases | One purchase order for one product |

The Excel workbook also includes a Legacy Monthly Report for
reconciling sales totals.

An invoice can contain multiple sales lines, so invoice counts use
COUNTD rather than a count of sales rows.

## Data Model

The beginner implementation uses separate Tableau data sources:

| Tableau source | Relationship |
|---|---|
| Sales Model | Sales related to Products using Product ID |
| Inventory Model | Inventory related to Products using Product ID |

Sales transactions and inventory snapshots have different levels
of detail. Keeping them in separate reporting sources avoids
multiplying inventory values through a transaction-level join.

Purchase data is included in the dataset for a future supplier
performance dashboard.

## KPI Definitions

| KPI | Definition |
|---|---|
| Revenue | Quantity × Selling Price × (1 − Discount Rate), rounded per line |
| COGS | Quantity × Unit Cost at Sale |
| Gross Profit | Revenue − COGS |
| Gross Margin | Total Gross Profit ÷ Total Revenue |
| Orders | Distinct count of Invoice ID |
| Inventory Value | Closing Stock × Product Unit Cost |
| Products to Reorder | Distinct products with Closing Stock ≤ Reorder Point |

Gross profit excludes operating expenses and is not net profit.

Inventory valuation uses the dataset's constant standard unit costs.
It does not implement FIFO or another inventory costing method.

## Tableau Calculations

### Revenue

    ROUND(
        [Quantity] * [Selling Price] * (1 - [Discount Rate]),
        2
    )

### COGS

    [Quantity] * [Unit Cost at Sale]

### Gross Profit

    [Revenue] - [COGS]

### Gross Margin

    IF SUM([Revenue]) <> 0 THEN
        SUM([Gross Profit]) / SUM([Revenue])
    END

### Orders

    COUNTD([Invoice ID])

### Inventory Value

    [Closing Stock] * [Unit Cost]

### Latest Snapshot

    [Snapshot Date] = { FIXED : MAX([Snapshot Date]) }

### Stock Status

    IF [Closing Stock] = 0 THEN
        "Out of stock"
    ELSEIF [Closing Stock] <= [Reorder Point] THEN
        "Reorder"
    ELSE
        "Available"
    END

### Products to Reorder

    COUNTD(
        IF [Closing Stock] <= [Reorder Point] THEN
            [Product ID]
        END
    )

Use Product ID from Inventory in the inventory calculation.

## Inventory Date Handling

Current-stock cards must be filtered to the latest snapshot:
31 December 2025.

The inventory trend must retain all monthly snapshots.

Closing stock can be summed across products for one snapshot date.
Summing closing stock across months does not represent current stock.

Reorder candidates should be reviewed alongside open purchase orders
before placing additional orders.

## Validation Targets

The following are reference values from the supplied dataset.
They are not a claim that every current dashboard view has passed
validation.

Sales targets apply to the complete dataset with all categories
included. Inventory targets apply to 31 December 2025 only.

| Metric | Expected Value |
|---|---:|
| Revenue | LKR 41,040,335 |
| COGS | LKR 29,762,620 |
| Gross Profit | LKR 11,277,715 |
| Gross Margin | 27.48% |
| Distinct Invoices | 3,805 |
| Units Sold | 14,787 |
| Latest Stock Units | 1,190 |
| Latest Inventory Value | LKR 3,138,400 |
| Products to Reorder | 8 |

### Validation Checklist

- [ ] Include every category when validating full-dataset sales.
- [ ] Match Tableau revenue and profit against the Excel baseline.
- [ ] Apply the latest-snapshot filter to inventory KPI cards.
- [ ] Confirm reorder count uses the conditional distinct-count formula.
- [ ] Use Snapshot Date on the inventory trend's horizontal axis.
- [ ] Show the reorder list as a product-level table.
- [ ] Use LKR formatting consistently.
- [ ] Set the monthly revenue axis to an appropriate automatic range.
- [ ] Test filters and reset behavior.
- [ ] Replace development screenshots after corrections.

## Excel-to-Tableau Migration

### Starting Point

The Excel workbook provides a monthly sales report and separate
source tables for sales, products, inventory, and purchasing.

### Tableau Reporting

The dashboards organize the data into business-focused views,
combining headline KPIs with category, product, and time-based
analysis.

### Reconciliation

The migration is considered validated when Tableau results match
the Excel baseline under the same filters and reporting dates.

No measured time savings or production performance improvements
are claimed.

## How to Open the Project

1. Download or clone this repository.
2. Open `tableau/Retail_Analytics.twbx` in Tableau Desktop Public Edition.
3. If prompted for a source file, select `data/Retail_Dataset.xlsx`.
4. Open the Sales Overview and Inventory Overview dashboards.
5. Check the reporting dates and active filters before interpreting KPIs.

## Live Dashboard

Tableau Public link: to be added after validation and publishing.

## Planned Improvements

- Complete the dashboard validation checklist.
- Add clearly labelled Category and Brand filters.
- Improve table readability and remove unnecessary scrollbars.
- Add a Supplier Performance dashboard.
- Add SQL reporting views and document their validation.
- Record a short dashboard walkthrough.

## Limitations

- All records are synthetic and do not describe a real company.
- The model represents one retail branch.
- Stock snapshots are monthly rather than real-time.
- Costs are constant standard costs in this teaching dataset.
- Returns, tax, operating expenses, and partial deliveries are absent.
- Enterprise permissions and automated server refresh are not implemented.
- No real-world operational outcomes are claimed.

## AI Assistance

ChatGPT assisted with synthetic dataset generation, learning guidance,
calculation examples, and documentation drafting. Dashboard
implementation and validation should be reviewed and understood
by the project author.
