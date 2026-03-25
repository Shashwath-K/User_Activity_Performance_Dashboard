# Page 3: Segmentation (WakaTime Style)

**Key Question Answered:** Which specific environments (Programming Languages, Operating Systems, Devices) am I spending the most time in, and how do they impact my productivity?

## Slicers (2 Maximum)
*(Slicers must be different from Pages 1 & 2)*
1. **Session Type Slicer**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `SessionType` ("Deep Work", "Distraction")
     - **Format:** Dropdown or List.
2. **Project Type Slicer**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `ProjectType`
     - **Format:** Tile or Checklist.

---

## Key Performance Indicators (KPIs)
These 5 KPI cards break down your environment and raw output volume.
- **Visual to Pick:** `Card` (Add 5 separate instances)
- **Cards Configuration:**
  1. **Total Time Spent:** Use `TimeSpentMinutes`. Aggregation: **Sum**. (Consistent with Page 1 & 2)
  2. **Unique Languages Used:** Shows the breadth of your stack.
     ```dax
     Unique Languages = DISTINCTCOUNT('github_repos'[Language])
     ```
  3. **Average Productivity Score:** Use `ProductivityScore`. Aggregation: **Average**. (Consistent with Page 1)
  4. **Top OS by Focus:** Identifies your most focused operating system.
     ```dax
     Top OS by Focus = 
     CALCULATE (
         MAX ( 'analytics_user'[OS] ),
         TOPN ( 1, ALLSELECTED ( 'analytics_user'[OS] ), CALCULATE ( AVERAGE ( 'analytics_user'[FocusScore] ) ), DESC )
     )
     ```
  5. **Total Raw Output (LOC):** The total lines of code produced.
     - **Field:** `LinesOfCode`. Aggregation: **Sum**.

---

## Visual 1: Time Spent by Programming Language
Much like WakaTime, this breaks down exactly which languages are dominating your development schedule.

- **Visual to Pick:** `Clustered bar chart`
- **Configuration:**
  - **Y-axis:** `Language` (You must drag this from your `github_repos.csv` dimension table, which should be linked to `user_analytics_cleaned.csv` via the ProjectName/RepoName relationship!).
  - **X-axis:** `TimeSpentMinutes` (Aggregation: **Sum**).
  - **Tooltips:** Add `LinesOfCode` and `Commits` to see output volume per language.
- **Formatting:** Sort descending by Time Spent so your primary languages are at the top.

---

## Visual 2: Time Spent by Operating System
A clean breakdown of your time footprint across different operating systems.

- **Visual to Pick:** `Clustered column chart`
- **Configuration:**
  - **X-axis:** `OS` (e.g., Windows 11, macOS, Android).
  - **Y-axis:** `TimeSpentMinutes` (Aggregation: **Sum**).
- **Formatting:** Keep the columns uniquely colored to represent the OS brand (e.g., Light Blue for Windows, Green for Android).

---

## Visual 3: Activity Output by Category
While Page 2 looked at Category *over time*, this visual calculates your raw coding deliverables across different task categories (e.g., App Development vs Research).

- **Visual to Pick:** `Line and clustered column chart`
- **Configuration:**
  - **X-axis:** `Category`
  - **Column Y-axis:** `LinesOfCode` (Aggregation: **Sum**).
  - **Line Y-axis:** `Commits` (Aggregation: **Sum**).
- **Formatting:** Use subtle grey bars for `LinesOfCode` and a bright, bold line (e.g., Orange or Purple) for the `Commits` to emphasize shipped code over raw characters typing.

---

## Visual 4: Environmental Productivity Heatmap (Matrix)
This matrix shows you at a glance which exact Device and OS combo leads to the highest Focus and Productivity metrics.

- **Visual to Pick:** `Matrix`
- **Configuration:**
  - **Rows:** `Device`
  - **Columns:** `OS`
  - **Values:** 
    1. `FocusScore` (Aggregation: **Average**)
    2. `ProductivityScore` (Aggregation: **Average**)
- **Formatting:** Turn on "Background color" conditional formatting for both Values so that top-tier Focus environments highlight instantly in Green, and poor environments show up in Red.
