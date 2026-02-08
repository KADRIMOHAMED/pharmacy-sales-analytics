# 🔧 Technical Documentation - Pharmacy Sales Analytics

## Table of Contents
1. [Data Model Architecture](#data-model-architecture)
2. [DAX Measures Reference](#dax-measures-reference)
3. [Visual Configuration](#visual-configuration)
4. [Performance Optimization](#performance-optimization)
5. [Troubleshooting](#troubleshooting)

---

## 1. Data Model Architecture

### Schema Type
**Star Schema** - Optimized for analytical queries and reporting

### Fact Table

#### T_FactSales
**Grain:** One row per sales transaction per product per pharmacy per day

| Column Name | Data Type | Description | Sample Value |
|------------|-----------|-------------|--------------|
| SalesID | String | Unique transaction identifier | "SAL-2023-001234" |
| DateKey | Int64 | Foreign key to T_DimDate | 20230115 |
| PharmacyID | String | Foreign key to T_DimPharmacy | "PH-FR-001" |
| ProductID | String | Foreign key to T_DimProduct | "PRD-12345" |
| UnitsSold | Int64 | Quantity of units sold | 25 |
| RevenueEUR | Double | Sales revenue in EUR | 450.00 |
| CostEUR | Double | Cost of goods sold in EUR | 320.00 |
| MarginEUR | Double | Profit margin (Revenue - Cost) | 130.00 |
| PromoFlag | String | Promotion indicator (Y/N) | "Y" |

**Row Count:** ~500,000 transactions
**Date Range:** 2020-01-01 to 2025-12-31
**Storage Mode:** Import

---

### Dimension Tables

#### T_DimDate
**Grain:** One row per calendar day

| Column Name | Data Type | Description | Format |
|------------|-----------|-------------|--------|
| DateKey | Int64 | Primary key | 20230115 |
| Date | DateTime | Calendar date | 2023-01-15 |
| Year | Int64 | Year number | 2023 |
| Quarter | Int64 | Quarter number (1-4) | 1 |
| MonthNumber | Int64 | Month number (1-12) | 1 |
| MonthName | String | Month name | "January" |
| YearMonth | DateTime | First day of month | 2023-01-01 |

**Row Count:** 2,192 days (6 years)
**Marked as Date Table:** ✅ Yes (on Date column)
**Hierarchy:** Year → Quarter → Month → Date

---

#### T_DimPharmacy
**Grain:** One row per pharmacy location

| Column Name | Data Type | Description | Sample Value |
|------------|-----------|-------------|--------------|
| PharmacyID | String | Primary key | "PH-FR-001" |
| PharmacyName | String | Pharmacy name | "Pharmacie Central Paris" |
| Country | String | Country code/name | "France" |
| Region | String | Administrative region | "Île-de-France" |
| City | String | City name | "Paris" |
| PharmacyType | String | Urban/Suburban/Rural | "Urban" |
| OpenDate | DateTime | Opening date | 2015-03-20 |
| StoreSizeBand | String | Size category | "Large" |
| Latitude | Double | GPS coordinate | 48.8566 |
| Longitude | Double | GPS coordinate | 2.3522 |

**Row Count:** ~150 pharmacies
**Geographic Hierarchy:** Country → Region → City → PharmacyName

---

#### T_DimProduct
**Grain:** One row per product SKU

| Column Name | Data Type | Description | Sample Value |
|------------|-----------|-------------|--------------|
| ProductID | String | Primary key | "PRD-12345" |
| ProductName | String | Product name | "Aspirin 500mg 20ct" |
| Category | String | Product category | "Pain Relief" |
| Brand | String | Brand name | "Bayer" |
| IsGeneric | String | Generic indicator (Y/N) | "N" |
| PackSize | String | Package size | "20 tablets" |
| ListPriceEUR | Double | Retail price | 8.99 |
| StandardCostEUR | Double | Base cost | 4.50 |
| LaunchDate | DateTime | Product launch date | 2010-05-15 |
| IsDiscontinued | String | Discontinued flag (Y/N) | "N" |
| DiscontinuedDate | DateTime | Discontinuation date | NULL |

**Row Count:** ~800 products
**Product Hierarchy:** Category → Brand → ProductName

---

### Relationships

| From Table | From Column | To Table | To Column | Cardinality | Cross Filter | Active |
|-----------|-------------|----------|-----------|-------------|--------------|--------|
| T_FactSales | DateKey | T_DimDate | DateKey | Many-to-One | Single | ✅ Yes |
| T_FactSales | PharmacyID | T_DimPharmacy | PharmacyID | Many-to-One | Single | ✅ Yes |
| T_FactSales | ProductID | T_DimProduct | ProductID | Many-to-One | Single | ✅ Yes |

**Relationship Count:** 3 main relationships + 5 auto-generated for LocalDateTable

**Referential Integrity:** Assumed (not enforced for performance)

---

## 2. DAX Measures Reference

### Measure Organization Structure

All measures are stored in the **`_Measures`** table (calculated table with hidden placeholder column).

#### Display Folder Hierarchy:
```
_Measures
├── 📊 Base Metrics (8 measures)
├── 📅 Time Intelligence (12 measures)
├── 🏪 Pharmacy Type Analysis (9 measures)
├── 🎁 Promotion Analysis (9 measures)
├── 🎯 Comparative Analysis (8 measures)
│   └── Benchmarking (2 measures)
├── 💊 Product Performance (5 measures)
│   └── Product Analysis (2 measures)
├── 🌍 Geographic Analysis (4 measures)
├── 🏆 Rankings (3 measures)
└── Total: 58 measures
```

---

### Key Measure Patterns

#### Pattern 1: Basic Aggregation
```dax
Total Revenue = 
SUM(T_FactSales[RevenueEUR])
```
**Purpose:** Simple aggregation from fact table
**Performance:** ⚡ Excellent (storage engine)
**Used in:** KPI cards, all charts

---

#### Pattern 2: Ratio Calculation with Safety
```dax
Margin % = 
DIVIDE([Total Margin], [Total Revenue], 0)
```
**Purpose:** Calculate percentage with divide-by-zero protection
**Performance:** ⚡ Excellent
**Alternative:** Could use conditional, but DIVIDE is cleaner

---

#### Pattern 3: Time Intelligence - Standard
```dax
Revenue YTD = 
TOTALYTD([Total Revenue], T_DimDate[Date])
```
**Purpose:** Year-to-date accumulation
**Requirements:** 
- T_DimDate marked as Date Table
- Contiguous date range
**Performance:** ⚡⚡ Good (requires date table scan)

---

#### Pattern 4: Time Intelligence - Prior Period
```dax
Revenue PY = 
CALCULATE(
    [Total Revenue],
    DATEADD(T_DimDate[Date], -1, YEAR)
)
```
**Purpose:** Same period last year
**Alternative functions:** SAMEPERIODLASTYEAR, PARALLELPERIOD
**Performance:** ⚡⚡ Good

---

#### Pattern 5: Conditional Aggregation
```dax
Revenue - Promoted = 
CALCULATE([Total Revenue], T_FactSales[PromoFlag] = "Y")
```
**Purpose:** Filter context modification
**Performance:** ⚡⚡ Good (filter on fact table)
**Pattern used:** 18 measures (Pharmacy Type, Promotion analysis)

---

#### Pattern 6: Dynamic Segmentation with Variables
```dax
Product Segment = 
VAR MedianUnits = 
    CALCULATE(
        MEDIAN(T_FactSales[UnitsSold]),
        ALL(T_DimProduct)
    )
VAR CurrentUnits = [Total Units Sold]
VAR CurrentMargin = [Margin %]
VAR Result = 
    SWITCH(
        TRUE(),
        CurrentUnits >= MedianUnits && CurrentMargin >= 0.15, 
            "⭐ Stars (High Vol, High Margin)",
        CurrentUnits < MedianUnits && CurrentMargin >= 0.15, 
            "💎 Premium (Low Vol, High Margin)",
        CurrentUnits >= MedianUnits && CurrentMargin < 0.15, 
            "⚠️ Volume Drivers (High Vol, Low Margin)",
        CurrentUnits < MedianUnits && CurrentMargin < 0.15, 
            "❌ Dogs (Low Vol, Low Margin)",
        BLANK()
    )
RETURN Result
```
**Purpose:** BCG Matrix-style classification
**Complexity:** 🔴 Complex (multiple variables, conditional logic)
**Performance:** ⚡⚡⚡ Moderate (requires full scan for median)
**Optimization:** Consider pre-calculating median if performance issues

---

#### Pattern 7: Contextual Benchmarking
```dax
Revenue vs Region Avg = 
VAR CurrentRevenue = [Total Revenue]
VAR RegionalAverage = 
    CALCULATE(
        [Total Revenue],
        ALLEXCEPT(T_DimPharmacy, T_DimPharmacy[Region])
    )
RETURN
    CurrentRevenue - RegionalAverage
```
**Purpose:** Compare individual entity to peer group average
**Context:** Removes all filters except Region
**Performance:** ⚡⚡⚡ Moderate (requires recalculation at different granularity)
**Used in:** Benchmarking visuals, tooltips

---

#### Pattern 8: Ranking with RANKX
```dax
Pharmacy Rank by Revenue = 
RANKX(
    ALL(T_DimPharmacy[PharmacyName]),
    [Total Revenue],
    ,
    DESC,
    DENSE
)
```
**Purpose:** Assign rank position
**Parameters:**
- ALL: Reset filters for ranking universe
- DESC: Highest revenue = Rank 1
- DENSE: No gaps in ranking (1,2,3 not 1,2,4)
**Performance:** ⚡⚡⚡ Moderate (requires sorting all entities)

---

#### Pattern 9: Moving Average
```dax
Revenue Moving Average 3M = 
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(T_DimDate[Date], LASTDATE(T_DimDate[Date]), -3, MONTH)
) / 3
```
**Purpose:** Smooth trend line
**Window:** Rolling 3 months
**Performance:** ⚡⚡⚡ Moderate (requires date range calculation)
**Visualization:** Line chart overlay on actual revenue

---

#### Pattern 10: Seasonal Index
```dax
Seasonal Index = 
VAR AvgMonthlyRevenue = 
    CALCULATE(
        [Total Revenue],
        ALLEXCEPT(T_DimDate, T_DimDate[Year])
    ) / 12
RETURN
    DIVIDE([Total Revenue], AvgMonthlyRevenue, 0)
```
**Purpose:** Identify seasonal patterns
**Interpretation:**
- 1.0 = Average month
- > 1.0 = Above average (peak season)
- < 1.0 = Below average (low season)
**Performance:** ⚡⚡⚡ Moderate

---

### Performance Characteristics

| Measure Category | Count | Avg Complexity | Storage Engine | Formula Engine | Query Time |
|-----------------|-------|----------------|----------------|----------------|------------|
| Base Metrics | 8 | Simple | ✅ | ❌ | < 10ms |
| Time Intelligence | 12 | Medium | ✅ | ✅ | 10-50ms |
| Conditional Filters | 18 | Simple | ✅ | ❌ | < 20ms |
| Benchmarking | 2 | Complex | ❌ | ✅ | 50-100ms |
| Rankings | 3 | Complex | ❌ | ✅ | 100-200ms |
| Segmentation | 7 | Complex | ❌ | ✅ | 50-150ms |

**Total Average Query Time:** ~30-50ms per visual (acceptable)

---

## 3. Visual Configuration

### Page 1: Executive Dashboard

#### KPI Cards (6 cards)
**Visual Type:** Card
**Configuration:**
```
Data:
- Fields: Single measure ([Total Revenue], etc.)
- Format: Custom (€#,##0 or 0.00%)

Formatting:
- Callout Value: 32pt, Segoe UI Bold
- Category Label: 12pt, Segoe UI Regular
- Background: White with subtle shadow
- Border: None
- Padding: 15px all sides

Size: 200px × 120px each
Layout: 6 columns across top (1/6 width each)
```

#### Area Chart: Revenue & Margin Trends
**Visual Type:** Area Chart
**Configuration:**
```
Axes:
- X-axis: T_DimDate[Date] (continuous)
- Y-axis (Primary): [Total Revenue]
- Y-axis (Secondary): [Margin %]
- Series: [Total Margin]

Formatting:
- Colors: Revenue=#0078D4, Margin=#107C10
- Transparency: 40% for areas
- Line width: 2px
- Grid lines: Horizontal only
- Legend: Top right

Interactions:
- Drill-down: Year → Quarter → Month
- Tooltip: Custom (includes [Revenue YoY %], [Seasonal Index])

Size: 800px × 400px
```

#### Scatter Chart (Page 2): Volume vs Margin
**Visual Type:** Scatter Chart
**Configuration:**
```
Axes:
- X-axis: [Total Units Sold] (Log scale enabled)
- Y-axis: [Margin %]
- Details: T_DimProduct[ProductName]
- Size: [Total Revenue]
- Legend: T_DimProduct[Category]

Formatting:
- Bubble opacity: 70%
- Size range: 10-50px radius
- Grid lines: Both axes
- Reference lines:
  - Vertical: Median([Total Units Sold])
  - Horizontal: Constant value 0.15 (15%)

Quadrant Labels (via text boxes):
- Q1: "⭐ Stars" (top right)
- Q2: "💎 Premium" (top left)
- Q3: "⚠️ Volume Drivers" (bottom right)
- Q4: "❌ Dogs" (bottom left)

Size: 900px × 600px
```

#### Map Visual
**Visual Type:** Filled Map (Azure Maps)
**Configuration:**
```
Location: T_DimPharmacy[Latitude], T_DimPharmacy[Longitude]
Bubble:
- Size: [Total Revenue]
- Color: [Margin %] (gradient scale)

Color Scale:
- Minimum (0%): #A80000 (Red)
- Center (15%): #FFB900 (Yellow)
- Maximum (30%+): #107C10 (Green)

Tooltip (custom):
Fields:
- T_DimPharmacy[PharmacyName]
- T_DimPharmacy[Country] | [Region] | [City]
- [Total Revenue]
- [Margin %]
- [Revenue vs Region Avg] (with conditional icons)
- T_DimPharmacy[PharmacyType]

Map Settings:
- Zoom: Auto
- Map style: Road
- Bubble size range: 5-40px

Size: 800px × 600px
```

---

### Page 3: Advanced Analytics Table

#### Matrix Configuration
**Visual Type:** Matrix
**Configuration:**
```
Rows (7-level hierarchy):
1. T_DimPharmacy[Country]
2. T_DimPharmacy[Region]
3. T_DimPharmacy[City]
4. T_DimPharmacy[PharmacyName]
5. T_DimProduct[Category]
6. T_DimProduct[Brand]
7. T_DimProduct[ProductName]

Values (40+ columns organized in groups):

Group A: Volume Metrics
- [Total Units Sold] (Data bars, Blue)
- [Number of Transactions] (Data bars, Gray)

Group B: Financial Metrics
- [Total Revenue] (Data bars, Blue, €#,##0)
- [Total Cost] (Data bars, Orange, €#,##0)
- [Total Margin] (Data bars, Green, €#,##0)
- [Margin %] (Icons + Color scale, 0.00%)

Group C: Time Intelligence
- [Revenue YTD]
- [Revenue PY]
- [Revenue YoY %] (Icons, 0.00%)

Group D: Benchmarking
- [Revenue vs Region Avg] (Icons + Colors)
- [Pharmacy Rank by Revenue]

Formatting:
Row Headers:
- Font: Segoe UI 10pt
- Indentation: 10px per level
- +/- icons: Enabled
- Subtotals: At each level

Column Headers:
- Font: Segoe UI Semibold 9pt
- Background: #F3F2F1
- Word wrap: Enabled

Values:
- Font: Segoe UI 9pt
- Alignment: Right for numbers
- Data bars: Max width 80% of cell

Conditional Formatting Rules:

Margin % (Color Scale):
- < 10%: #A80000 (Red)
- 10-15%: #FFB900 (Yellow)
- > 15%: #107C10 (Green)

Revenue YoY % (Icons):
- < -5%: ↓↓ Red
- -5% to 5%: ↔ Gray
- > 5%: ↑ Green
- > 10%: ↑↑ Dark Green

Revenue vs Region Avg (Background):
- Negative: Light red (#FFE6E6)
- ±5%: White
- Positive: Light green (#E6FFE6)

Interactions:
- Expand/Collapse: All levels
- Drill-down: Click on row header
- Export: Excel enabled
- Column resize: Manual
- Frozen rows: Header row
- Frozen columns: First column (hierarchy)

Size: Full page width × 80% height
```

---

## 4. Performance Optimization

### Model Optimizations

#### 1. Relationships
✅ **What we did:**
- All relationships are Many-to-One (optimal for star schema)
- Single direction cross-filtering (bi-directional avoided)
- No circular dependencies
- Referential integrity assumed (not enforced for performance)

❌ **Avoided:**
- Many-to-Many relationships
- Bi-directional cross-filtering (except where absolutely necessary)
- Composite models (Import only)

---

#### 2. Data Types
✅ **Optimized types:**
- Integer keys (Int64) instead of String where possible
- DateTime for dates (not String)
- Double for currency (not Decimal for better compression)
- String only where necessary

**Compression ratio achieved:** ~10:1 (500K rows = ~5MB compressed)

---

#### 3. Calculated Columns vs Measures
✅ **Strategy:**
- All calculations in Measures (not calculated columns)
- Exception: MarginEUR in fact table (pre-calculated from source)
- Reason: Measures use formula engine, more flexible, better compression

❌ **Avoided:**
- Calculated columns for aggregations
- Unnecessary columns in fact table

---

#### 4. Aggregations
**Not implemented** (dataset size doesn't warrant it)
- If scaling to 10M+ rows, consider:
  - Pre-aggregated tables at Month/Product level
  - Aggregation tables at Country/Category level

---

### Query Optimizations

#### 1. DAX Patterns
✅ **Efficient patterns used:**
```dax
// ✅ GOOD: Variable usage
Revenue YoY % = 
VAR CurrentRevenue = [Total Revenue]
VAR PYRevenue = [Revenue PY]
RETURN DIVIDE(CurrentRevenue - PYRevenue, PYRevenue, 0)

// ❌ BAD: Repeated calculations
Revenue YoY % = 
DIVIDE([Total Revenue] - [Revenue PY], [Revenue PY], 0)
// [Revenue PY] calculated twice!
```

✅ **Context transition optimization:**
```dax
// ✅ GOOD: Filter directly on table
Revenue - Urban = 
CALCULATE([Total Revenue], T_DimPharmacy[PharmacyType] = "Urban")

// ❌ BAD: Using FILTER function unnecessarily
Revenue - Urban = 
CALCULATE([Total Revenue], FILTER(T_DimPharmacy, T_DimPharmacy[PharmacyType] = "Urban"))
```

---

#### 2. Visual-level Optimizations
✅ **Implemented:**
- Max 10 visuals per page
- Top N filters on large datasets (Top 10 products, Top 5 regions)
- Interaction disabled where not needed
- Tooltips only load on hover (not pre-rendered)

---

#### 3. Slicer Optimizations
✅ **Configuration:**
- Single select where possible (faster than multi-select)
- "Select All" enabled for user convenience
- Sync slicers only where necessary (Date, Country)
- List slicers for < 20 items, Dropdown for > 20 items

---

### Refresh Performance

**Current Performance:**
- Full refresh: ~45 seconds
- Incremental refresh: Not configured (dataset small enough)

**Refresh Strategy:**
```
Scheduled Refresh:
- Frequency: Daily at 2:00 AM UTC
- Incremental: Not needed yet
- Partitioning: Single partition per table

Future optimization (if needed):
- Partition T_FactSales by Year
- Incremental refresh: Last 2 years, keep 6 years
```

---

## 5. Troubleshooting

### Common Issues and Solutions

#### Issue 1: Revenue PY showing BLANK
**Symptoms:** [Revenue PY] returns no values

**Causes:**
1. Date table doesn't cover prior year
2. Date table not marked as Date Table
3. Relationship not active

**Solution:**
```dax
// Diagnostic measure
Date Range Check = 
"Min: " & MIN(T_DimDate[Date]) & 
" | Max: " & MAX(T_DimDate[Date]) & 
" | Has PY: " & IF(
    CALCULATE(COUNTROWS(T_DimDate), T_DimDate[Year] = YEAR(TODAY()) - 1) > 0,
    "Yes",
    "No"
)
```

**Fix:**
1. Extend T_DimDate to cover all required years
2. Mark T_DimDate as Date Table (right-click → Mark as Date Table)
3. Use DATEADD instead of SAMEPERIODLASTYEAR

---

#### Issue 2: Slow Matrix Performance
**Symptoms:** Page 3 matrix takes > 5 seconds to load

**Causes:**
1. Too many expanded levels
2. Too many measures in values
3. Complex conditional formatting

**Solutions:**
1. Collapse all levels by default
2. Use bookmarks to show common views
3. Limit to 20-30 columns maximum
4. Disable conditional formatting on rarely viewed columns
5. Consider removing some rankings/benchmarking from default view

---

#### Issue 3: Scatter Chart Bubbles Overlapping
**Symptoms:** Can't see all products

**Solutions:**
1. Enable log scale on X-axis
2. Add jitter with slight random offset
3. Use drill-down to Category level
4. Add slicer to filter to specific categories
5. Increase chart size

---

#### Issue 4: Map Not Loading Pharmacies
**Symptoms:** Map visual blank or only partial data

**Causes:**
1. Latitude/Longitude columns wrong format
2. Location geocoding failed
3. Too many points for rendering

**Solutions:**
1. Verify Lat/Long are Decimal Degrees
2. Check data quality (no NULLs, valid ranges)
3. Use bubble map instead of filled map
4. Aggregate to City level if > 1000 pharmacies

---

#### Issue 5: Cross-filtering Not Working
**Symptoms:** Clicking on visual doesn't filter others

**Solutions:**
1. Check Edit Interactions (Format → Edit Interactions)
2. Verify slicers are on correct fields
3. Ensure relationships are active
4. Check if "Highlight" is set instead of "Filter"

---

### Performance Metrics

**Target Metrics:**
- Page load: < 3 seconds
- Visual render: < 1 second
- Slicer interaction: < 500ms
- Drill-down: < 1 second
- Export Excel: < 5 seconds

**Actual Performance (tested on dataset):**
- Page 1 load: ~2.1 seconds ✅
- Page 2 load: ~2.8 seconds ✅
- Page 3 load: ~4.2 seconds ⚠️ (acceptable given complexity)
- Slicer interaction: ~300ms ✅
- Drill-down: ~800ms ✅

---

### Debug Mode Tips

**Enable Performance Analyzer:**
1. View → Performance Analyzer → Start Recording
2. Interact with visuals
3. Review DAX query times
4. Identify bottlenecks

**Check Query Plans:**
```dax
// Add to measure temporarily to see execution
[Your Measure] & 
" | Evaluation: " & NOW()
```

**DAX Studio Analysis:**
```
1. Connect DAX Studio to Power BI file
2. Run query trace while interacting
3. Review Server Timings tab
4. Identify slow queries
5. Optimize measures
```

---

### Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-02-08 | Initial documentation |
| 1.1 | TBD | Performance optimizations based on user feedback |

---

### Support

For questions or issues:
1. Check this documentation first
2. Review [insights-summary.md](insights-summary.md) for business context
3. Consult DAX measure files in `/dax-measures/`
4. Open an issue on GitHub
5. Contact: [your.email@example.com]

---

**Last Updated:** February 8, 2026
**Maintained by:** [Your Name]
**Version:** 1.0
