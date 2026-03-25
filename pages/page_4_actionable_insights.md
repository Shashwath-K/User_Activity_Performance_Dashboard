# Page 4: Actionable Insights

**Key Question Answered:** Where can I improve efficiency, and what repeatable patterns are destroying my focus?

## Slicers (2 Maximum)
*(Slicers must be different from previous pages)*
1. **Day of Week Profile**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `DayName` (From your Dim Date Table)
     - **Format:** Tile or Checklist.
2. **OS Environment**
   - **Visual to Pick:** `Slicer`
   - **Configuration:**
     - **Field:** `OS`
     - **Format:** Dropdown.

---

## Visual 1: Custom Narrative Text Box
Since Copilot is restricted, we will build our own dynamic narrative using a Text Box and newly created DAX measures.

- **Visual to Pick:** `Text box` (From the Insert ribbon)
- **Configuration:** Write out your narrative sentence, and use the **+ Value** button to insert your dynamic DAX measures.
- **Example Sentence Formatting:** 
  *"My highest focus occurs on **[Top Focus Day]**. The majority of my time goes towards **[Top Project by Time]**, yielding **[Commits on Top Project]** commits in this filtered period."*

### Required DAX Measures to Create:
You must create these 3 exact measures to populate the text box dynamically:

**1. Top Focus Day:**
```dax
Top Focus Day = 
CALCULATE (
    MAX ( 'dim_date'[DayName] ),
    TOPN (
        1,
        ALLSELECTED ( 'dim_date'[DayName] ),
        CALCULATE ( AVERAGE ( 'analytics_user'[FocusScore] ) ),
        DESC
    )
)
```

**2. Top Project by Time:**
```dax
Top Project by Time = 
CALCULATE (
    MAX ( 'analytics_user'[ProjectName] ),
    TOPN (
        1,
        ALLSELECTED ( 'analytics_user'[ProjectName] ),
        CALCULATE ( SUM ( 'analytics_user'[TimeSpentMinutes] ) ),
        DESC
    )
)
```

**3. Commits on Top Project:**
```dax
Commits on Top Project = 
CALCULATE (
    SUM ( 'analytics_user'[Commits] ),
    FILTER(
        ALLSELECTED('analytics_user'),
        'analytics_user'[ProjectName] = [Top Project by Time]
    )
)
```

---

## Visual 2: Distraction Toll Analysis
This compares how much time you bleed to Distraction sessions against how it impacts your actual Focus Score.

- **Visual to Pick:** `Line and clustered column chart`
- **Configuration:**
  - **X-axis:** `DayName` (Sorted by `DayOfWeekNo` from your Dim Date Table).
  - **Column Y-axis:** Add `TimeSpentMinutes`, but open the visual level filter for this specific visual to ONLY include `SessionType = "Distraction"`. 
  - **Line Y-axis:** `FocusScore` (Aggregation: **Average**).
- **Formatting:** Use Red for the Distraction columns and Blue for the Focus Score line for sharp contrast.

---

## Visual 3: Activity Funnel by Priority
Uncover if you are actually spending enough time and generating adequate commits for tasks marked as "High Priority".

- **Visual to Pick:** `Funnel`
- **Configuration:**
  - **Category:** `Priority`
  - **Values:** `TimeSpentMinutes` (Aggregation: **Sum**)
  - **Tooltips:** Add `Commits` (Sum).
- **Formatting:** Ensure it's sorted High to Low to see if the bulk of your time stays at the top of the funnel (High Priority) or leaks into Low Priority.

---

## Visual 4: The Inefficiency Matrix
Identify exactly which Apps or Projects are causing you the most friction (Errors) with the least output (Commits).

- **Visual to Pick:** `Matrix`
- **Configuration:**
  - **Rows:** `AppName`
  - **Values:** 
    1. `ErrorsCount` (Aggregation: **Sum**)
    2. `Commits` (Aggregation: **Sum**)
    3. `LinesOfCode` (Aggregation: **Sum**)
---

## Visual 5: Advanced Actionable Narratives
Add these 3 additional text-based insights to your dashboard to identify "The Switching Tax", "Deep Work Sweet Spots", and "Time Sinks".

### Insight A: The "Switching Tax" (Context Switching)
- **Narrative Sentence:** *"You worked on **[Unique Projects Today]** different projects today. On multi-project days, your Focus Score typically drops by **[Focus Penalty]**."*
- **Required DAX:**
  ```dax
  Unique Projects Today = DISTINCTCOUNT('analytics_user'[ProjectName])
  ```
  ```dax
  Focus Penalty = 
  VAR OverallAvg = CALCULATE(AVERAGE('analytics_user'[FocusScore]), ALLSELECTED('analytics_user'))
  VAR MultiTaskAvg = CALCULATE(AVERAGE('analytics_user'[FocusScore]), FILTER(ALLSELECTED('analytics_user'), [Unique Projects Today] > 1))
  RETURN IF(ISBLANK(MultiTaskAvg) || MultiTaskAvg >= OverallAvg, "0%", FORMAT(DIVIDE(OverallAvg - MultiTaskAvg, OverallAvg, 0), "0%"))
  ```

### Insight B: The "Deep Work Window" (Peak Efficiency)
- **Narrative Sentence:** *"Your 'Deep Work' window is **[Peak Efficiency Hour]**. You are **[Efficiency Boost]%** more productive during this hour compared to your daily average."*
- **Note:** *Requires a Calculated Column `Hour = LEFT('analytics_user'[StartTime], SEARCH(":", 'analytics_user'[StartTime]) - 1)`*
- **Required DAX:**
  ```dax
  Peak Efficiency Hour = 
  CALCULATE (
      MAX ( 'analytics_user'[Hour] ),
      TOPN ( 1, ALLSELECTED ( 'analytics_user'[Hour] ), [Commits Per Minute], DESC )
  )
  ```
  ```dax
  Efficiency Boost = 
  VAR AvgE = CALCULATE([Commits Per Minute], ALLSELECTED('analytics_user'))
  VAR PeakE = CALCULATE([Commits Per Minute], FILTER(ALLSELECTED('analytics_user'), 'analytics_user'[Hour] = [Peak Efficiency Hour]))
  RETURN FORMAT(DIVIDE(PeakE - AvgE, AvgE, 0), "0%")
  ```

### Insight C: The "Time Sink" Detector (Optimize/Reduce)
- **Narrative Sentence:** *"**[Time Sink Project]** is currently your biggest time sink, requiring **[Minutes Per Commit]** minutes for every 1 commit."*
- **Required DAX:**
  ```dax
  Time Sink Project = 
  CALCULATE (
      MAX ( 'analytics_user'[ProjectName] ),
      TOPN ( 1, ALLSELECTED ( 'analytics_user'[ProjectName] ), [Minutes Per Commit], DESC )
  )
  ```
  ```dax
  Minutes Per Commit = DIVIDE(SUM('analytics_user'[TimeSpentMinutes]), SUM('analytics_user'[Commits]), 0)
  ```
