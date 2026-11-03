Sync Apple Health Data to REST API
Send health data to a REST API endpoint.

Last updated: August 23, 2026

REST API Automation Guide

REST API automations allow you to automatically export your health data to any web service that accepts HTTP POST requests. This is ideal for integrating with custom backends, third-party APIs, or webhooks.

Overview

REST API automations send your health data to a specified URL endpoint using HTTP POST requests. The automation can send data in JSON or CSV format, with configurable headers for authentication and custom metadata.

Use Cases:

Integrating with custom backend services
Sending data to webhooks
Syncing with third-party APIs
Building custom dashboards or analytics platforms
Key Features:

Supports both JSON and CSV formats
Custom HTTP headers for authentication
Configurable request timeout
Manually export historical data
Limitations

Health Data Access: Apps are not allowed to access health data while iPhone is locked. Automations will only run during periods when your device is unlocked. This is a limitation imposed by Apple which cannot be circumvented. More information
Background Processing: iOS limits background processing to preserve battery life. Automations rely on Background App Refresh and may not run immediately if:
Background App Refresh is disabled for the app
The device is in Low Power Mode
The device has been inactive for extended periods
System resources are constrained
Multiple apps are competing for background execution time
Performance

Keep in mind that iOS is optimized for short-running tasks on a mobile device with very tight performance constraints. Background tasks will typically need to complete within 30 seconds and are limited in how much memory they can consume. Health Auto Export allows a large degree of flexibility and customizability, and this in turn requires taking time to understand how certain configurations will affect app performance and outcomes.

Configuration: Automations that produce large amounts of data can cause the system to kill the process and result in automations not running in the background. The following configurations can produce large amounts of data:
Automations configured to export all health metrics.
Recommendation: only select health metrics that have saved data in Apple Health and only select data you actually plan to use. Even empty data types will have a performance impact. You can also consider splitting selected health metrics across multiple automations, which makes it easier for the system to handle.
Automations using time groupings in seconds or minutes, or with data summarization off. Such fine-grained queries can take a long time to run and clash with system limitations.
Recommendation: while it may seem ideal to have the most detailed data possible, consider whether that level of detail is necessary for each metric or data type. Consider multiple automations with different settings.
When exporting outdoor workouts, such as cycling, running, hiking, etc with route data, GPS and associated health metrics data can produce large payloads.
Payload size: Especially when using REST API export, consider that large payloads can cause server errors. Make sure your backend is configured to handle payloads of possibly several hundred megabytes to avoid errors.
Sync frequency: Add the Automations widget to your Home Screen to help ensure automations run successfully in the background (see Automations Widget Setup Guide).
Prerequisites

A valid URL endpoint that accepts HTTP POST requests
Authentication credentials (if required by your endpoint)
Network connectivity to reach your endpoint
Configuration

Open the sidebar, then tap Automations. Tap New Automation and select "REST API" as the Automation Type.

Automation Name

Enter a descriptive name for your automation (e.g., "My Backend API", "Webhook Integration").

Notifications

Configure when you want to receive notifications:

Notify on Cache Update - Receive a notification when cached data is updated
Notify When Run - Receive a notification each time the automation executes
Schedule Reminders — Optional prompts to unlock and open the app. See Automations Overview. These are separate from Notify When Run.
URL Configuration

Enter the full URL where you want to send your health data. This should be a complete URL including the protocol (http:// or https://).

Example URLs:

https://api.example.com/health-data
https://webhook.site/your-unique-id
http://localhost:3000/api/health
Note: The URL must be valid and accessible from your device. Invalid URLs will prevent the automation from running.

Request Timeout

Select a timeout interval for HTTP requests. This determines how long the app will wait for a response before considering the request failed.

HTTP Headers

Add custom HTTP headers for authentication or metadata. Common use cases include:

API keys: X-API-Key: your-api-key
Authorization tokens: Authorization: Bearer your-token
Content type overrides: Content-Type: application/json
To add headers:

Tap "Add Headers"
Enter the header key in the left field
Enter the header value in the right field
Repeat for additional headers
Important: Each header key must have a corresponding value. Empty headers will be ignored.

Data Type Settings

Data Type

Select which type of health data to export:

Health Metrics - Steps, heart rate, sleep, and other health measurements
Workouts - Exercise and fitness activities
Symptoms - Health symptoms and conditions
ECG - Electrocardiogram readings
Heart Rate Notifications - High/low heart rate events
State of Mind - Mood and mental state entries (iOS 18.0+)
Cycle Tracking - Menstrual cycle and reproductive health data
Medications - Medication logs and adherence (iOS 26.0+)
Health Metrics Configuration

When Health Metrics is selected:

Select Health Metrics - Choose which specific metrics to include. You can select all available metrics or choose specific ones.

Tip: Selecting only the metrics you need can improve processing time and reduce data size.

Preferred Sources - Configure which data sources take priority when multiple sources provide the same metric.

Workout Configuration

When Workouts is selected:

Include Route Data - Toggle ON to include routes for workouts that have location data.

Include Workout Metrics - Toggle ON to include health metrics collected during workouts (heart rate, calories, etc.).

Time Grouping (Workout Metrics) - When using Export Version 2 and Include Workout Metrics is enabled:

Minutes - Groups workout metrics by minute
Seconds - Groups workout metrics by second
Export Settings

Export Format

Select the format for your exported data:

JSON format - Provides detailed data structures with nested objects. Best for APIs, databases, and applications that need structured data. JSON format includes more detailed information for complex data types like sleep phases and AFib readings.
CSV format - Provides tabular data that can be easily imported into spreadsheet applications. Best for simple data analysis or when your endpoint expects CSV data.
Note: The Content-Type header is automatically set to application/json for JSON exports and multipart/form-data for CSV exports.

Export Version

Select an Export Version. Versioning allows transitioning between updated versions of the export at your own pace and minimizes breaking changes to workflows.

Version 1 - Legacy format, use if you have existing workflows that depend on this format
Version 2 - Current format with enhanced workout data and more detailed metadata options
Date Range

Select when data should be exported:

Default - Syncs data for the full previous day plus data up to the current date and time
Since Last Sync - On each sync, exports all data since the last time the export ran up until the current date and time
Today - Syncs all data for the current date up to the current time
Yesterday - Syncs all data for the full previous day
Previous 7 Days - Syncs data for the full previous seven days
Summarize Data

When using JSON format with Health Metrics data type, toggle Summarize Data ON or OFF.

ON - Provides aggregated data summaries
OFF - Provides disaggregated data where possible, showing individual data points
Note: This setting only applies to JSON format with Health Metrics. Data is always aggregated when using CSV format or when multiple metrics are selected.

Time Grouping

When using JSON format with Summarize Data enabled, select how data should be aggregated.

Note: CSV format always aggregates data. Minute and second-level aggregation can significantly increase processing time and data size.

Batch requests and large payloads

When using JSON format, toggle Batch Requests ON to send data in multiple HTTP requests instead of one large payload.

ON — Spreads data over multiple requests. Use when your endpoint has payload size limits, timeouts on large bodies, or you process data incrementally.
OFF — Sends all data in a single request. Suitable for smaller exports and simple webhooks.
When to enable batching:

Many health metrics selected, long date ranges, or fine time grouping (minutes/seconds)
Summarize Data is off and you export disaggregated health metrics
Your server returns errors or timeouts on large POST bodies
Notes:

Batch requests apply only to REST API + JSON (not CSV).
Batching reduces payload size per request but does not remove the need to fetch data on the device; slow health data queries may still appear as warnings in Activity Logs. See Slow queries in Activity Logs for how to speed up runs.
Sync Cadence

Configure how often the automation should upload data:

Select a number and interval.

Testing & Verification

Manual Testing

Tap "Manual Export" in the automation configuration screen
Select a date range
Tap "Export" to send a test request
Check your endpoint to verify the data was received
Viewing Activity Logs

Tap View Activity Logs in the automation configuration screen.
Review runs (grouped, newest first) and expand events within each run.
Warnings (for example, slow health data query) vs errors (HTTP failures, timeouts, or encode messages such as “No health data was found in this date range, so nothing was uploaded.” and “Health data could not be fully read. The device may have locked during the export. Nothing was uploaded.”)—see Automations Overview — Activity Logs.
Successful REST uploads often show a summary with format, data type, export period, and date range included in the run.
Share (toolbar) exports the full App Event Logs diagnostic ZIP for support (same as Settings → Advanced).
Clear removes only this automation’s activity history.
Verifying Data Format

The app automatically includes these headers in each request:

Content-Type - Set based on export format
automation-name - The name of your automation
automation-id - Unique identifier for the automation
automation-aggregation - The selected time grouping
automation-period - The selected date range
session-id - Unique identifier for each request
Troubleshooting

Common Issues

Data Not Received at Endpoint

Verify the endpoint URL is correct
Check that your endpoint accepts POST requests
Review authentication headers
Check endpoint logs for incoming requests
Verify network connectivity
Review Activity Logs for HTTP status codes and per-day failures
Tips and Best Practices

Automatic Syncing:
Charge your device and use iPhone Mirroring
When your device is being charged iOS places less restrictions on device performance, so data can sync more frequently
By using iPhone Mirroring, your device behaves in the same way as if it were unlocked. This means health data is accessible by Health Auto Export in order to run automated actions
Performance:
Use appropriate time grouping to balance detail vs. data size
Select only the metrics you need
Enable Batch Requests for large JSON payloads (see Batch requests and large payloads)
Watch Activity Logs for slow-query warnings; reduce metrics or use coarser grouping if runs are slow
Reliability:
Set appropriate timeout values based on your endpoint's response time
Monitor Activity Logs regularly
Data Format:
Use JSON for structured data and APIs
Use CSV for simple data analysis or spreadsheet integration
Consider batch requests for large datasets or separate processing