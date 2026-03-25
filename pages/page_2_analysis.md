# Page 2: Analysis & Trends

**Key Question Answered:** How do my categories (coding, social, browser) and system resources impact my output over time?

## Slicers (2 Maximum)
*(Slicers must be different from Page 1: Date Range, Device)*
1. **Category Selection**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `Category`
     - **Format:** Dropdown or List (Single or Multi-select depending on preference).
2. **Project Name Selector**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `ProjectName`
     - **Format:** Dropdown with "Select All" enabled.

---

## Key Performance Indicators (KPIs)
These 5 KPI cards should be placed at the top of the page for high-level quality and system analysis.
- **Visual to Pick:** `Card` (Add 5 separate instances)
- **Cards Configuration:**
  1. **Total Time Spent:** Use `TimeSpentMinutes`. Aggregation: **Sum**. (Consistent with Page 1)
  2. **Total Errors Reported:** Use `ErrorsCount`. Aggregation: **Sum**.
  3. **Average Focus Score:** Use `FocusScore`. Aggregation: **Average**. (Consistent with Page 1)
  4. **Performance Efficiency:** Measures how many commits you produce relative to code volume.
     ```dax
     Performance Efficiency = DIVIDE(SUM('analytics_user'[Commits]) * 1000, SUM('analytics_user'[LinesOfCode]), 0)
     ```
  5. **Peak CPU Impact:** The highest CPU usage recorded in this filter context.
     - **Field:** `CPUUsagePercent`. Aggregation: **Max**.

---

## Visual 1: Cumulative Time Spent by Category
This visual tracks the volume of time invested across broader categories (like App Development, Coding, Social) over month periods.

- **Visual to Pick:** `Stacked area chart`
- **Configuration:**
  - **X-axis:** `MonthName` (From your Dim Date Table! Make sure you go into Data View, select `MonthName` column, and apply "Sort by Column" -> `MonthNumber` so they correctly order Jan through Dec).
  - **Y-axis:** `TimeSpentMinutes` (Aggregation: **Sum**).
  - **Legend:** `Category`
- **Formatting:** Sort X-axis by `MonthNumber` (Ascending). Use distinct but complementary colors for your categories (e.g., Tech/Coding categories in cool blues/teals, Distractions in warmer colors).

---

## Visual 2: System Load vs. Productivity Correlation
You wanted to avoid scatter plots, so this overlay effectively visualizes if heavy system usage (CPU/RAM) occurs during highly productive phases.

- **Visual to Pick:** `Line and clustered column chart`
- **Configuration:**
  - **X-axis:** `Date` (From Dim Date Table, uncheck hierarchy).
  - **Column Y-axis:** Add `CPUUsagePercent` (Aggregation: **Average**) and `RAMUsagePercent` (Aggregation: **Average**).
  - **Line Y-axis:** Add `ProductivityScore` (Aggregation: **Average**).
- **Formatting:** Keep the columns (CPU/RAM) visually subdued (like light grey and faint yellow) so that the prominent Line (Productivity Score) easily pops out for visual analysis.

---

## Visual 3: Direct Output vs Errors by App
This visual contrasts the volume of code output against the volume of errors encountered based on the application being used.

- **Visual to Pick:** `Clustered bar chart`
- **Configuration:**
  - **Y-axis:** `AppName`
  - **X-axis:** Add `LinesOfCode` (Aggregation: **Sum**) and `ErrorsCount` (Aggregation: **Sum**).
- **Formatting:** Make LinesOfCode a distinct "positive" color like blue, and ErrorsCount a "warning" color like red to easily identify which environments yield the highest error rates per line of code. Sort by LinesOfCode (Descending).

---

## Visual 4: In-Depth Project Performance Breakdown
A tabular format lets us dive deep into specific metrics across all projects tracked over the time range.

- **Visual to Pick:** `Matrix`
- **Configuration:**
  - **Rows:** `ProjectName`
  - **Values:** Add the following (in order):
    1. `TimeSpentMinutes` (Aggregation: **Sum**)
    2. `LinesOfCode` (Aggregation: **Sum**)
    3. `Commits` (Aggregation: **Sum**)
    4. `ErrorsCount` (Aggregation: **Sum**)
    5. `ProductivityScore` (Aggregation: **Average**)
- **Formatting:** Apply Conditional Formatting (Background Color or Data Bars) to the "ProductivityScore" column to quickly spot highly productive projects (green) vs low ones (red).
