# Hotel Booking Analysis — Guided Project

A Power BI dive into three years of hotel booking data (2018 to 2020), built for Datafied Technologies' Data Sparks guided project track.

## Dashboard

Everything's in `Guided_Project_Simon.pbix`. Open it in Power BI Desktop and click through the visuals and slicers yourself.

No live published Power BI Service link, unfortunately. Couldn't get account/publishing access sorted during this project, so the `.pbix` file is the actual deliverable here.

## Introduction

This project looks at hotel booking records from 2018 to 2020 and turns them into a Power BI dashboard a hotel management team could actually use. The work follows the usual flow for a project like this: understand the problem, pull in the data, clean it up, model it properly, then build out the visuals and metrics on top.

## Problem Statement

Hotel management needs a clearer picture of how bookings behave, cancellations in particular, so they can make better calls on pricing, marketing spend, and operations. Right now that picture doesn't exist in a usable form. As the data analyst on this, the job was to take the raw booking records, clean them up, build a proper data model, and surface the metrics and visuals that actually help with those decisions.

## Data Sourcing

Two sources went into this:

**Hotel Booking Data**, three years of reservations (2018 to 2020): hotel type, cancellations, lead time, length of stay, guest makeup, market segment, distribution channel, room type, deposit type, reservation status dates.

**Country Reference Data**, a country/continent/region lookup (ISO-alpha3 codes) from the UN Statistics Division, used to turn the booking data's raw country codes into actual country names.

## Data Transformation and Cleaning

All done in Power Query:

1. Combined the three yearly sheets into one table.
2. Removed exact duplicate rows so bookings don't get double counted.
3. Arrival date came in as three separate columns (year, month name, day) in the source, so I rebuilt it as one proper Date field.
4. Recoded the 1/0 flags: `is_canceled` became "Canceled"/"Not Canceled", `is_repeated_guest` became "Repeat guest"/"New guest".
5. Agent and Company had a lot of NULLs. Replaced those with empty values rather than dropping the rows.
6. A few columns that should've been numbers (`booking_changes`, `days_in_waiting_list`, `total_of_special_requests`) loaded in as text, which quietly breaks AVERAGE/MAX in DAX. Fixed the types.
7. Merged in the country reference table on the ISO code for real country names.
8. Found and removed a few broken rows, including one where "hotel" showed up where an actual hotel name should've been. Looked like a stray header row that got pulled in during the append.

## Data Modeling

Went with a simple star schema:

**Hotel Dim** holds the two hotel types with a generated ID, built from untouched raw data per the project brief.

**Location Dim** is the cleaned country lookup (code, name, region, continent).

**Fact table** (Combined Bookings) is the cleaned data, linked to both dimensions, one to many.

## Metrics (DAX)

- Average Days in Waiting
- Maximum Days in Waiting
- Average Booking Changes
- Total Bookings with Special Request (greater than 0)
- Ratio of Total Bookings to Total Cancellations
- Cancellation Rate

## The Dashboard

KPI strip up top, then a monthly booking trend line, top 10 countries by bookings, bookings by market segment, and a cancellation split donut. Two slicers, hotel type and country, make it filterable.
