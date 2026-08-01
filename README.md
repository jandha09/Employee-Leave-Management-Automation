# Employee Leave Management Automation

## Overview
Automates the full lifecycle of an employee leave request — from submission to approval to logging — removing the manual back-and-forth between employees, managers, and HR. Requests that fall within an employee's available balance are approved automatically; anything that would exceed their balance is routed to their manager for review. Every request and balance update is logged to Google Sheets, and both employees and managers are notified by email and Slack at each step.

## How It Works
1. **Request Submission** — Employees submit a leave request through an n8n form (name, department, leave type, start/end date, reason, manager email).
2. **Business Day Calculation** — The workflow calculates the number of business days requested (weekends excluded).
3. **Balance Lookup** — The employee's current leave balance is pulled from a "Leave Balances" sheet in Google Sheets.
4. **Auto-Decision**
   - **Within balance →** request is auto-approved, the balance is updated, and the employee + Slack channel are notified.
   - **Exceeds balance →** request is flagged "Pending Manager Review," the manager is emailed for a decision, and both employee and manager get a Slack notification.
5. **Logging** — Every request (approved or pending) is written to a "Leave Requests" sheet with a unique Request ID for tracking.

## Key Concepts Demonstrated
- Conditional business logic (balance-based auto-approval vs. escalation)
- Multi-branch notification routing (email + Slack, employee + manager)
- Google Sheets as a lightweight balance/database system
- Business-day-aware date calculations

**Google Sheet structure required:**
- **Leave Balances** tab — columns: `Employee Email`, `Remaining Balance`
- **Leave Requests** tab — columns: `Request ID`, `Employee Name`, `Employee Email`, `Department`, `Leave Type`, `Start Date`, `End Date`, `Days Requested`, `Reason`, `Manager Email`, `Status`, `Submitted At`

**Credentials needed:** Google Sheets OAuth2, Gmail OAuth2, Slack OAuth2/Bot Token.

## Notes
- Unpaid leave requests bypass the balance check and are always auto-approved (adjust in the "Evaluate Balance" code node if this shouldn't be the case for your org).
- If an employee has no existing record in "Leave Balances," they're treated as having a 0 balance and routed to manager review by default — safer than silently auto-approving an unknown employee.
- This workflow does not currently handle manager approve/deny actions automatically (e.g., via a Slack button); the manager reviews and updates status manually in the sheet. This could be extended with an approval webhook if needed.
