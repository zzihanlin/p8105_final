
<center>

# Project Proposal

</center>

### Group Members

Tasya Dita (tpd2121), Zihan Lin (zl3543), Adeena Moghni (am6592),
Kimberly Palaguachi-Lopez (kp2809), Hanchuan Chen (hc3563)

#### Tentative Project Title

Exploring the Link Between Asthma Trends and Climate Change

### Motivation

Asthma is a co-morbidity for other chronic diseases and also
disproportionately affects those from a lower economic status. In this
project, we want to focus on asthma prevalence/incidence as hotter
temperatures can lead to more pollen, air pollution, and other lung
irritants. Studying these trends will help understand the health risks
in vulnerable populations.

### Intended products

A website containing the analysis and visualizations of our project,
include traditional and interactive charts.

### Data Sources

[Behavioral Risk Factor Surveillance System (BRFSS) Prevalence Data on
Asthma
(2021-2001)](https://www.cdc.gov/asthma/data-visualizations/default.htm),
TIGER/Line Shapefiles, Weather data (from the rnoaa library)

### Analysis/Visualization/Coding Plan

Import BRSS asthma data using read_html() for 2001- 2021 (would add a
year column and merge by state). Clean and tidy data. Write a function
to get monthly temp. readings using at least 10 randomly chosen station
from each state using the rnoaa package and find the seasonal average of
each state using group_by() and summarize(). Merge this data with TIGER
state fips shape files to visually map asthma and temperatures across
all US states and display the health outcome of asthma in the latest
year. Display the distibution of asthma cases from 2001-2021 using box
or violin plots to guide the direction of our analysis to plot weather
temperatures in these states with the most density of asthma cases. Fit
a linear regression between extreme weather temperature (x) and asthma
prevalence/incidence (y). To calculate yearly incidence, we would need
to subtract existing cases from the previous year from the next. We
could retain the beta coefficient and p-values for each state to display
the relationship between these two variables.

### Project Timeline

|          Date          |                   Descriptions                   |
|:----------------------:|:------------------------------------------------:|
|       11/08/2024       |              Complete the proposal               |
| 11/11/2024- 11/15/2024 |          Project review (ZOOM MEETING)           |
| 11/16/2024- 12/05/2024 | Complete project report, webpage, video overview |
| 12/05/2024- 12/07/2024 |              Finish peer assesement              |
|       12/12/2024       |               In-class Discussion                |
| 12/13/2024-12/20/2024  |    Modify project based on discussion results    |
|       12/21/2024       |                     Submit!!                     |
