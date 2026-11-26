# n8n Task Reminder Automation

Automates daily email reminders for tasks marked **Pending** in Google Sheets.

## Workflow

`Daily Schedule Trigger (09:00 IST) - Google Sheets (read tasks) - IF (Task Status = Pending) - Gmail (send reminder)`

- Runs every day at 9:00 AM (Asia/Kolkata).
- Reads rows from the `Tasks` sheet.
- Sends an email only for rows whose `Task Status` is exactly `Pending`.
- Uses the employee email, name, task name, and due date from the row.
- Completed tasks are ignored.

## Files

- `workflow.json` — Importable n8n workflow
- `sample-data.csv` — Sample task rows for testing
- `screenshots/` — Added all screenshots.

## Prerequisites

1. An n8n instance (cloud or self-hosted).
2. A Google Sheet containing these column headers exactly:
   - `Task ID`
   - `Task Name`
   - `Assigned Employee`
   - `Employee Email`
   - `Due Date`
   - `Task Status`
3. Google Sheets OAuth credential configured in n8n.
4. Gmail OAuth credential configured in n8n. The sender account will be the Gmail account connected to this credential.

## Setup

### 1. Prepare Google Sheets

- Create a spreadsheet and a worksheet named `Tasks or Sheet1`.
- Import `sample-data.csv` into the sheet (or paste the rows).
- Ensure the first row is the column headers.
- Replace sample addresses with actual employee email addresses before testing.

### 2. Import workflow

1. In n8n, open **Workflows**.
2. Choose **Import from File** and select `workflow.json`.
3. Open **Read Tasks from Google Sheets**:
   - Select your Google Sheets credential.
   - Replace `YOUR_GOOGLE_SHEET_ID` with the spreadsheet ID (the string between `/d/` and `/edit` in its URL).
   - Select worksheet `Tasks` (or the actual worksheet name).
   - Confirm the node reads all rows.
4. Open **Send Reminder Email** and select your Gmail OAuth credential.
5. Save the workflow.

### 3. Test before activating

1. Temporarily execute the workflow manually, or click **Execute Workflow**.
2. Verify the Sheets node returns rows.
3. Verify the IF node's **true** output contains only Pending rows.
4. Verify Gmail sends to the expected employee email addresses.
5. Use your **own email address** in sample rows for safe testing.
6. Check the email's subject, task name, and due date formatting.

> The workflow's email date uses the source value from the sheet. For consistent display, store dates as `26-Nov-2026` or use an n8n date expression if your sheet returns ISO dates.

### 4. Activate

- Set the Schedule Trigger to daily at **09:00**.
- Confirm the workflow timezone is `Asia/Kolkata` (workflow settings).
- Activate the workflow. It will run automatically each day.

## Reminder email template

**Subject:** Reminder – Complete API documentation

Hi Rahul Sharma,

This is a reminder that your task "Complete API documentation" is still pending.

Due Date: 26-Nov-2026

Please complete the task before the due date.

Thanks,  
Task Reminder Automation

## Notes / troubleshooting

- The JSON is a workflow template; credentials are not embedded. Set up OAuth credentials in your n8n instance after importing.
- The Google Sheet ID is a placeholder and must be replaced.
- Google Sheets columns should match the header names exactly.
- Confirm the Gmail account is authorized to send messages.
- If dates appear in another format, adjust the email expression to format the date, based on the actual value returned by the Sheets node.
- A reminder is sent for every Pending row each run, including tasks whose due date is in the future or past. If you want overdue-only reminders or deduplication, add date conditions or a reminder-sent column.
