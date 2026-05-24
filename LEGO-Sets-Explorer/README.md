# Power BI Projects 📊

### 2. LEGO Sets Explorer Dashboard

**Dataset:** Maven Analytics — LEGO Sets  
**Tool:** Power BI Desktop  
**Pages:** 2

---

#### Overview

This interactive Power BI report allows users to explore over 4,385 LEGO sets by price, theme, age range, and category. The report was designed with a strong focus on **user experience** — giving a non-technical audience full control over filtering and exploration without needing any data knowledge.

The dashboard goes beyond standard filtering by incorporating advanced Power BI features including custom image tooltips, numeric range parameters, decomposition trees, and bookmark-driven page navigation.

---

### Page 1 — LEGO Set Explorer

<img width="1317" height="741" alt="image" src="https://github.com/user-attachments/assets/9a068d36-8d00-4bcf-b50a-d9c8f983d425" />

#### What It Does

A fully interactive product explorer where users can:

- **Set a maximum price** using a numeric range parameter slicer — the table instantly filters to only show sets within budget
- **Filter by Theme Group, Theme, and Age Range** using three independent slicers
- **Click on any Set ID** in the table to see a detail card on the right showing the set's price, number of pieces, age rating, and release year
- **Hover over any set name** to see a custom image tooltip showing a photo of that LEGO set
- **Reset all filters** with a single Reset Filter button built using bookmark actions
- **Navigate to the Decomposition Tree** using the Explore Filters button with page navigation


<img width="1320" height="731" alt="image" src="https://github.com/user-attachments/assets/13e3bb92-e454-40d6-87f7-648aabe36d83" />


#### Key Insights

- **4,385 total LEGO sets** analysed across all themes with an average price of **$45** and average piece count of **411**
- The **World Map set (31203-1)** is the largest Art set with **11,695 pieces** priced at **$250**, targeting adult collectors
- Licensed themes such as **Star Wars (388 sets)** and **Marvel Super Heroes (156 sets)** dominate the Licensed theme group, confirming strong commercial focus on franchise partnerships

---

### Page 2 — Decomposition Tree

<img width="1308" height="738" alt="image" src="https://github.com/user-attachments/assets/3a1ac73f-9833-4efe-ab86-1db788d28a20" />


#### What It Does

Accessed via the **Explore Filters** button on Page 1, this page uses Power BI's built-in Decomposition Tree visual to let users drill down through the LEGO set hierarchy:

**Total Sets → Category → Theme Group → Theme → Set Name**

Users can explore exactly how the total set count breaks down at each level — for example drilling from Licensed → Marvel Super Heroes → individual set names.

A **Back button** built with bookmark actions returns the user to Page 1 without losing their filter context.


---

#### DAX Measures & Calculations

**Max Price Filter** — controls which sets are visible based on the numeric range parameter:

```dax
Max Price Filter = 
IF(
    [Average Price] <= 'Max Price'[Max Price Value],
    1,
    0
)
```

> This measure works with a numeric range parameter to dynamically show or hide rows. When the user moves the price slicer, `Max Price Value` updates and this measure re-evaluates for every row — returning 1 (show) or 0 (hide). It is used as a visual-level filter set to show only rows where the result equals 1.

---

**Selected Age** — shows the age rating of a selected set in the detail card:

```dax
Selected Age = 
IF(
    HASONEVALUE(lego_sets[Age]),
    MAX(lego_sets[Age]),
    "-"
)
```

> `HASONEVALUE()` checks whether exactly one row is selected in context — i.e. the user has clicked on a specific set. If yes, it returns that set's age rating. If multiple sets are selected or none, it returns "-" to keep the card clean and avoid showing misleading aggregated values.

---

**Total Sets** — counts unique LEGO sets:

```dax
Total Sets = DISTINCTCOUNT(lego_sets[set_id])
```

> `DISTINCTCOUNT()` is used instead of `COUNT()` to ensure each set is counted once regardless of how many times its ID appears in the dataset. This gives an accurate total that responds correctly to all slicer selections.

---


---

*Dataset source: [Maven Analytics](https://mavenanalytics.io)*
