# Pharmacy-Sales-Analytics
Interactive Power BI dashboard analyzing European pharmacy sales performance - DataDNA Challenge submission

# 🏥 European Pharmacy Sales Analytics

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-0078D4?style=for-the-badge&logo=microsoft&logoColor=white)
![ZoomCharts](https://img.shields.io/badge/ZoomCharts-00A4EF?style=for-the-badge)

> Interactive Power BI dashboard analyzing pharmacy sales performance across European countries, regions, and product portfolios. Built for the Onyx Data DataDNA Challenge.

[![Live Dashboard](https://img.shields.io/badge/Live%20Dashboard-View%20Here-blue?style=for-the-badge)]([VOTRE_LIEN_ICI](https://app.powerbi.com/view?r=eyJrIjoiZjQ1NTQ0NmItOGE4Yi00MDUwLWJjMzgtMjY0YWJlOWUyMmE5IiwidCI6IjQ2NTRiNmYxLTBlNDctNDU3OS1hOGExLTAyZmU5ZDk0M2M3YiIsImMiOjl9
))
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/kadrimohamed/)

---

## 📊 Preview

![Dashboard Preview](images/Capture d’écran (33).png)

---

## 🎯 Project Overview

### Business Challenge
Analyze sales and profitability for a European pharmacy chain distributor operating across multiple countries. Provide actionable insights for strategic decision-making.

### Key Questions Answered
- ✅ How do revenue, units sold, and margin change over time with seasonal patterns?
- ✅ Which countries and regions contribute the most to total revenue and margin?
- ✅ How does performance vary when drilling down from country → region → pharmacy?
- ✅ Which pharmacies outperform or underperform compared to regional averages?
- ✅ How do Urban, Suburban, and Rural pharmacies differ in profitability?
- ✅ Which product categories and brands generate the most revenue and margin?
- ✅ Are there products with high volume but low margin, or low volume but high margin?
- ✅ How do promoted sales compare to non-promoted sales?
- ✅ What are the visible geographic patterns in sales and profitability?

---

## 🏗️ Dashboard Structure

### 📄 Page 1: Executive Dashboard
High-level overview with key performance indicators and trends.

**Key Visuals:**
- 6 KPI Cards (Revenue, Margin %, YoY %, Units, Transactions)
- Area Chart: Revenue & Margin Trends Over Time
- Column Chart: Revenue by Country
- Stacked Bar Chart: Revenue by Pharmacy Type
- Donut Chart: Promoted vs Non-Promoted Sales
- Ribbon Chart: Top 5 Regions Performance Evolution

![Page 1](images/Capture d’écran (33).png)

---

### 📄 Page 2: Product & Geographic Intelligence
Deep-dive analysis into product performance and geographic patterns.

**Key Visuals:**
- Treemap: Revenue by Category & Brand
- **Scatter Chart: Product Portfolio Analysis (Volume vs Margin)**
  - 4 Quadrants: Stars | Premium | Volume Drivers | Dogs
- Top 10 Products by Revenue
- Geographic Heat Map with Drill-down
- Waterfall Chart: Regional Contribution
- Urban/Suburban/Rural Comparison

![Page 2 - Scatter Analysis](images/Capture d’écran (34).png)
![Page 2 - Map Visual](images/Capture d’écran (35).png)

---

### 📄 Page 3: Advanced Analytics Explorer
Comprehensive data table with full drill-down capabilities.

**Features:**
- **7-Level Hierarchy:** Country → Region → City → Pharmacy → Category → Brand → Product
- **40+ Metrics** including:
  - Sales & Profitability (Revenue, Cost, Margin, Units)
  - Time Intelligence (YoY%, QoQ%, MoM%, YTD, Moving Averages)
  - Benchmarking (vs Region Avg, vs Country Avg)
  - Rankings (Pharmacy Rank, Product Rank, Category Rank)
  - Segmentation (Promoted vs Non-Promoted, Pharmacy Type)
- Rich Conditional Formatting
- Export to Excel Capability

![Page 3](images/Capture d’écran (35).png)

---

## 🛠️ Technical Implementation

### Tools & Technologies
- **Power BI Desktop** - Report development
- **Power Query (M)** - Data transformation
- **DAX** - Calculations and measures (58+ custom measures)
- **ZoomCharts Drill Down Visuals** - Enhanced interactivity
- **PowerPoint** - Custom backgrounds

### Data Model
- **Architecture:** Star Schema
- **Fact Table:** T_FactSales (daily transactions)
- **Dimension Tables:** 
  - T_DimDate (with full date hierarchy)
  - T_DimPharmacy (geographic & type attributes)
  - T_DimProduct (category, brand, generic flag)
- **Relationships:** 8 active relationships (Many-to-One)

![Data Model](images/Capture d’écran (36).png)

### DAX Measures Categories

#### 📊 Base Metrics (8 measures)
- Total Revenue, Total Cost, Total Margin
- Total Units Sold, Number of Transactions
- Margin %, Average Transaction Value, Average Units per Transaction

#### 📅 Time Intelligence (12 measures)
- Revenue YTD, MTD, QTD
- Revenue PY (Previous Year), YoY Change, YoY %
- Revenue Moving Average 3M, Seasonal Index
- MoM Growth %, QoQ Growth %

#### 🏪 Pharmacy Type Analysis (9 measures)
- Revenue/Margin/Units by Urban, Suburban, Rural

#### 🎁 Promotion Analysis (9 measures)
- Revenue/Margin/Units for Promoted vs Non-Promoted
- Promo Impact %, Margin % comparisons

#### 🎯 Comparative Analysis (8 measures)
- Revenue/Margin/Units per Pharmacy
- Benchmarking (vs Region Avg, vs Country Avg)
- Counts (Pharmacies, Products, Countries)

#### 💊 Product Performance (5 measures)
- Revenue/Cost/Margin per Unit
- High Volume Low Margin Products
- Revenue Share %

#### 🌍 Geographic Analysis (4 measures)
- Regional Contribution %, Country Contribution %
- Top Country, Top Region

#### 🏆 Rankings (3 measures)
- Pharmacy Rank by Revenue
- Product Rank by Revenue
- Category Rank by Margin

---

## 💡 Key Insights Delivered

### 🎯 Product Portfolio Optimization
- **47 products** (32% of catalog) identified as **High Volume / Low Margin**
  - Generate **€2.5M revenue** (35% of total)
  - Average margin of only **8%** (below 15% target)
  - **Action:** Cost optimization or pricing strategy revision
  - **Potential Impact:** +2% margin = +€50K profit

- **23 products** (16% of catalog) identified as **Low Volume / High Margin (Premium)**
  - Generate **€890K revenue** (12% of total)
  - Exceptional margin of **28%**
  - **Opportunity:** Low visibility limiting volume
  - **Action:** Increase in-store visibility and marketing
  - **Potential Impact:** +50% volume = +€125K profit

### 🌍 Geographic Performance
- **Country-level patterns** reveal concentration of profitability
- **Regional variations** show performance differences within countries
- **Pharmacy-level benchmarking** identifies top and bottom performers
- **Urban/Suburban/Rural** distinct performance characteristics

### 🎁 Promotion Effectiveness
- Quantified impact of promotions on volume and margin
- Margin % comparison between promoted and non-promoted sales
- Revenue contribution from promotional activities

---

## 📂 Repository Structure
```
pharmacy-sales-analytics/
├── README.md                          # This file
├── LICENSE                            # MIT License
├── .gitignore                         # Git ignore rules
├── reports/
│   └── Pharmacy-Sales-Analytics.pbix  # Power BI report file
├── images/
│   ├── dashboard-page1.png            # Screenshots
│   ├── dashboard-page2.png
│   ├── dashboard-page3.png
│   ├── scatter-analysis.png
│   ├── map-visual.png
│   └── data-model.png
├── dax-measures/
│   ├── base-metrics.txt               # DAX formulas
│   ├── time-intelligence.txt
│   ├── product-analysis.txt
│   └── geographic-analysis.txt
├── documentation/
│   ├── technical-documentation.md     # Detailed technical docs
│   ├── insights-summary.md            # Business insights
│   └── challenge-brief.md             # Original challenge brief
└── resources/
    ├── color-palette.md               # Design system
    └── design-guidelines.md
```

---

## 🚀 How to Use

### Prerequisites
- Power BI Desktop (Latest version)
- ZoomCharts license (for full interactivity - [get free trial](https://zoomcharts.com/))

### Installation
1. Clone this repository
```bash
   git clone https://github.com/YOUR_USERNAME/pharmacy-sales-analytics.git
```

2. Open `reports/Pharmacy-Sales-Analytics.pbix` in Power BI Desktop

3. If using ZoomCharts visuals, activate your license

4. Explore the dashboard!

### Navigation
- Use the **top navigation buttons** to switch between pages
- **Slicers** on the left panel filter all visuals
- **Click on visuals** to cross-filter (drill-down enabled)
- **Bookmarks** provide pre-defined views
- **Reset Filters** button clears all selections

---

## 📊 Sample DAX Measures

### Product Segment Classification
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

### Year-over-Year Revenue Growth
```dax
Revenue YoY % = 
VAR CurrentRevenue = [Total Revenue]
VAR PreviousYearRevenue = 
    CALCULATE(
        [Total Revenue],
        DATEADD(T_DimDate[Date], -1, YEAR)
    )
RETURN
    DIVIDE(
        CurrentRevenue - PreviousYearRevenue,
        PreviousYearRevenue,
        0
    )
```

### Regional Benchmarking
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

[See all 58 DAX measures →](dax-measures/)

---

## 🎨 Design System

### Color Palette
- **Revenue:** #0078D4 (Blue)
- **Margin:** #107C10 (Green)
- **Cost:** #D83B01 (Orange/Red)
- **Promoted:** #FFB900 (Gold)
- **Non-Promoted:** #8A8886 (Gray)

### Typography
- **Headings:** Segoe UI Bold, 24pt
- **KPIs:** Segoe UI Bold, 20pt
- **Labels:** Segoe UI Regular, 10pt

[Full design guidelines →](resources/design-guidelines.md)

---

## 📈 Performance Optimizations

- ✅ Star schema data model for optimal query performance
- ✅ All calculations in DAX measures (not calculated columns where possible)
- ✅ Import mode for fast rendering
- ✅ Optimized visuals (8-10 per page max)
- ✅ Aggregations pre-calculated
- ✅ Relationships properly configured (Many-to-One)

---

## 🏆 Challenge Information

This project was built for the **Onyx Data DataDNA Challenge** & **ZoomCharts Mini Challenge**.

### Judging Criteria
- **Intuitive (15 pts):** Clear, visually appealing, tells a story
- **Interactive (15 pts):** Engaging navigation, cross-filtering, drill-down
- **Insightful (15 pts):** Accurate data, meaningful insights, optimized performance



---

## 📚 Documentation

- [📖 Technical Documentation](documentation/technical-documentation.md)
- [💡 Business Insights Summary](documentation/insights-summary.md)
- [📋 Original Challenge Brief](documentation/challenge-brief.md)
- [🎨 Design Guidelines](resources/design-guidelines.md)

---

## 🤝 Contributing

While this is a challenge submission, feedback and suggestions are welcome!

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add some improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- **[Onyx Data](https://www.linkedin.com/company/onyxdata/)** for organizing the DataDNA Challenge
- **[ZoomCharts](https://zoomcharts.com/)** for providing drill-down visual tools and mentorship
- **Power BI Community** for inspiration and support

---

## 📞 Contact

**Your Name**
- LinkedIn: [Your Profile](https://www.linkedin.com/in/kadrimohamed/)
- Twitter: [@YourHandle]([YOUR_TWITTER](https://x.com/Kadri__med))
- Portfolio: [Your Website](https://github.com/KADRIMOHAMED)
- Email: kadrimed95@gmail.com

---

## 🌟 If you found this project helpful, please give it a star! ⭐

---

**Built with ❤️ using Power BI, DAX, and ZoomCharts**

[![Made with Power BI](https://img.shields.io/badge/Made%20with-Power%20BI-F2C811?style=flat-square&logo=powerbi)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/DAX-Measures%2058+-0078D4?style=flat-square)](https://dax.guide/)
[![ZoomCharts](https://img.shields.io/badge/Enhanced%20with-ZoomCharts-00A4EF?style=flat-square)](https://zoomcharts.com/)
```

---

### **📄 .gitignore**

Créez un fichier `.gitignore` :
```
# Power BI
*.pbix.tmp
*.pbix~

# Windows
Thumbs.db
Desktop.ini

# macOS
.DS_Store

# Temp files
~$*
*.tmp

# Logs
*.log

# Private/Sensitive
credentials.txt
connection-strings.txt

# Large files (use Git LFS if needed)
*.pbix
# Note: You may want to remove .pbix from gitignore 
# and use Git LFS instead for version control
```

---

### **📄 LICENSE**

Créez un fichier `LICENSE` (MIT License recommandée) :
```
MIT License

Copyright (c) 2026 KADRI MOHAMED

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
