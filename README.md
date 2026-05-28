# Excel-Driven Revit Sheet & View Generator

A Dynamo + Python automation workflow for Autodesk Revit that creates sheets from Excel data and automatically places views on sheets using user-defined coordinates.

---

# Overview

This project was developed as a BIM automation exercise to explore how repetitive documentation workflows in Revit can be automated using Dynamo, Python, and the Revit API.

The workflow reads sheet and view information from Excel, creates missing sheets, places views on sheets, and generates a detailed automation report.

The main goal of the project was to better understand:

* Revit API workflows
* Excel-driven BIM automation
* batch documentation generation
* reusable automation logic
* QA/QC validation systems
* viewport placement workflows

---

# Features

## Automated Sheet Creation

The tool can automatically:

* create Revit sheets
* assign sheet numbers
* assign sheet names
* reuse existing sheets

---

## Automated View Placement

The workflow can:

* search views by name
* place views onto sheets
* place multiple section views on the same sheet
* control viewport placement using X/Y coordinates from Excel

---

## Excel-Driven Workflow

The entire workflow is controlled from Excel.

Example input:

| Sheet Number | Sheet Name        | View Name | X  | Y  |
| ------------ | ----------------- | --------- | -- | -- |
| A-101        | Ground Floor Plan | Level 1   | 1  | 1  |
| A-301        | Sections          | Section 1 | -1 | -1 |
| A-301        | Sections          | Section 2 | -1 | 0  |
| A-301        | Sections          | Section 3 | -1 | 1  |

---

# QA/QC & Validation

The script includes validation logic for:

* empty sheet numbers
* empty sheet names
* empty view names
* duplicate sheets
* already placed views
* missing title blocks
* missing views

---

# Workflow

Excel
↓
Dynamo
↓
Python Script
↓
Revit API
↓
Sheet Creation
↓
View Placement
↓
Automation Report

---

# Technologies Used

* Autodesk Revit
* Dynamo
* Python
* Revit API
* Excel Integration

---

# Skills Demonstrated

* BIM automation
* Revit API scripting
* batch documentation workflows
* Excel-driven automation
* viewport placement systems
* reusable scripting logic
* QA/QC validation
* production workflow thinking

---

# Future Improvements

Planned future improvements include:

* automatic viewport spacing
* bounding-box based auto-layout
* title block selection from Excel
* automatic viewport scaling
* sheet template integration
* WPF user interface
* Autodesk Construction Cloud integration

---

# Learning Notes

This project was developed as part of a personal learning process focused on BIM automation and digital construction workflows.

Although it started as a small prototype exercise, the workflow evolved into a reusable documentation automation system capable of handling real-world BIM coordination tasks.
