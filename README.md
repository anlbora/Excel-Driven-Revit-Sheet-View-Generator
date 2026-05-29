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
'''python

    import clr
    
    clr.AddReference("RevitServices")
    from RevitServices.Persistence import DocumentManager
    from RevitServices.Transactions import TransactionManager
    
    clr.AddReference("RevitAPI")
    from Autodesk.Revit.DB import (
        FilteredElementCollector,
        BuiltInCategory,
        ViewSheet,
        View,
        Viewport,
        XYZ
    )
    
    doc = DocumentManager.Instance.CurrentDBDocument
    excel_data = IN[0]
    
    report = [["Sheet Number", "Sheet Name", "View Name", "X", "Y", "Status", "Message"]]
    
    title_blocks = (
        FilteredElementCollector(doc)
        .OfCategory(BuiltInCategory.OST_TitleBlocks)
        .WhereElementIsElementType()
        .ToElements()
    )
    
    if len(title_blocks) == 0:
        report.append(["", "", "", "", "", "ERROR", "No title block type found"])
        OUT = report
    
    else:
        title_block_id = title_blocks[0].Id

    def get_existing_sheet(sheet_number):
        sheets = FilteredElementCollector(doc).OfClass(ViewSheet).ToElements()
        for sheet in sheets:
            if sheet.SheetNumber == sheet_number:
                return sheet
        return None

    def get_view_by_name(view_name):
        views = FilteredElementCollector(doc).OfClass(View).ToElements()
        for view in views:
            if view.Name == view_name and not view.IsTemplate and view.CanBePrinted:
                return view
        return None

    def is_view_already_placed(view):
        viewports = FilteredElementCollector(doc).OfClass(Viewport).ToElements()
        for viewport in viewports:
            if viewport.ViewId == view.Id:
                return True
        return False

    rows = excel_data[1:]

    TransactionManager.Instance.EnsureInTransaction(doc)

    for row in rows:

        sheet_number = str(row[0]).strip() if len(row) > 0 and row[0] else ""
        sheet_name = str(row[1]).strip() if len(row) > 1 and row[1] else ""
        view_name = str(row[2]).strip() if len(row) > 2 and row[2] else ""

        x = float(row[3]) if len(row) > 3 and row[3] else 1
        y = float(row[4]) if len(row) > 4 and row[4] else 1

        if sheet_number == "":
            report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "Empty sheet number"])
            continue

        if sheet_name == "":
            report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "Empty sheet name"])
            continue

        if view_name == "":
            report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "Empty view name"])
            continue

        sheet = get_existing_sheet(sheet_number)

        if sheet is None:
            try:
                sheet = ViewSheet.Create(doc, title_block_id)
                sheet.SheetNumber = sheet_number
                sheet.Name = sheet_name
                sheet_message = "Sheet created"
            except Exception as e:
                report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "Could not create sheet: " + str(e)])
                continue
        else:
            sheet_message = "Existing sheet used"

        view = get_view_by_name(view_name)

        if view is None:
            report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "View not found"])
            continue

        if is_view_already_placed(view):
            report.append([sheet_number, sheet_name, view_name, x, y, "SKIPPED", "View already placed on another sheet"])
            continue

        try:
            Viewport.Create(
                doc,
                sheet.Id,
                view.Id,
                XYZ(x, y, 0)
            )

            report.append([
                sheet_number,
                sheet_name,
                view_name,
                x,
                y,
                "PLACED",
                sheet_message + " | View placed successfully"
            ])

        except Exception as e:
            report.append([sheet_number, sheet_name, view_name, x, y, "ERROR", "Could not place view: " + str(e)])

    TransactionManager.Instance.TransactionTaskDone()

    OUT = report

'''

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
