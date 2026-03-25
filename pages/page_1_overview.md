# Page 1: Overview and Summary

**Key Question Answered:** How productive and focused am I overall, and where is my time going?

## Slicers (2 Maximum)
1. **Date Range Slicer**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `Date` (From your new **Dim Date Table**)
     - **Format:** Set the slicer style to "Between" so you get a slider for a specific date range.
2. **Device Filter**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `Device`
     - **Format:** Set the slicer style to "Dropdown" or "Tile".

---

## Key Performance Indicators (KPIs)
Add 4 Individual KPI Cards at the top of the dashboard.
- **Visual to Pick:** `Card` (Add 4 separate instances)
- **Cards Configuration:**
  1. **Total Time Spent:** Use `TimeSpentMinutes`. Aggregation: **Sum**.
  2. **Average Productivity Score:** Use `ProductivityScore`. Aggregation: **Average**.
  3. **Average Focus Score:** Use `FocusScore`. Aggregation: **Average**.
  4. **Total Commits:** Use `Commits`. Aggregation: **Sum**.
  5. **Top Application by Usage:** Use `Top Application by Usage`. Aggregation: **Max**.
  ``` dax
      Top Application by Usage = 
      CALCULATE (
          MAX ( 'user_analytics_cleaned'[AppName] ),
          TOPN (
              1,
              ALLSELECTED ( 'user_analytics_cleaned'[AppName] ),
              CALCULATE ( SUM ( 'user_analytics_cleaned'[TimeSpentMinutes] ) ),
              DESC
          )
      )
  ```

---

## Visual 1: Daily Productivity & Focus Trend
This visual tracks whether your focus and productivity scores are improving or declining over time.

- **Visual to Pick:** `Line chart`
- **Configuration:**
  - **X-axis:** `Date` (From Dim Date Table, make sure it's sorted by Date).
  - **Y-axis:** Add `ProductivityScore` (Aggregation: **Average**) and `FocusScore` (Aggregation: **Average**).
- **Formatting:** Use strong distinct colors for the lines (e.g., green for Productivity, purple for Focus) so the trend is instantly obvious over time.

---

## Visual 2: Time Distribution by Application
This visual identifies which applications or tools are taking up the majority of your time.

- **Visual to Pick:** `Clustered bar chart`
- **Configuration:**
  - **Y-axis:** `AppName`
  - **X-axis:** `TimeSpentMinutes` (Aggregation: **Sum**).
  - **Tooltips:** Add `Commits` (Sum) and `LinesOfCode` (Sum). You can hover over an app (like VS Code or Jupyter) and see exactly what was produced during that time.
- **Formatting:** Sort the axis by Sum of TimeSpentMinutes (Descending).

---

## Visual 3: Deep Work vs Distraction Over the Week
This chart uncovers your best days for focusing versus being distracted.

- **Visual to Pick:** `Stacked column chart` (Standard, NOT 100%)
- **Configuration:**
  - **X-axis:** `DayName` (From your Dim Date Table! Make sure you go into Data View, select `DayName` column, and apply "Sort by Column" -> `DayOfWeekNo` so they correctly order Monday to Sunday).
  - **Y-axis:** `TimeSpentMinutes` (Aggregation: **Sum**).
  - **Legend:** `SessionType` (Deep Work vs Distraction).
- **Formatting:** Use a solid, focused color (like Dark Blue) for "Deep Work" and a stark contrasting color (like Red or Orange) for "Distraction".

---

## Visual 4: Commits & Code Output by Project
This answers exactly which project is getting the most tangible deliverables.

- **Visual to Pick:** `Line and clustered column chart`
- **Configuration:**
  - **X-axis:** `ProjectName`
  - **Column Y-axis:** `LinesOfCode` (Aggregation: **Sum**).
  - **Line Y-axis:** `Commits` (Aggregation: **Sum**).
- **Formatting:** Keep the columns a subtle gray and emphasize the Commits line in a bold, prominent color to signify "shipped" output.
