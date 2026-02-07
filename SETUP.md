# Google Sheets Tracking Setup Guide

This guide will help you set up Google Sheets tracking for your Valentine's app to log when someone clicks "Yes".

## Prerequisites
- A Google Account
- The Valentine's app deployed on GitHub Pages

## Step 1: Create a Google Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Click **"+ Blank"** to create a new spreadsheet
3. Name it something like **"Valentine Responses"**
4. Set up the following column headers in Row 1:
   - **A1**: `Timestamp`
   - **B1**: `Response`
   - **C1**: `User Agent`

Your sheet should look like this:

```
| Timestamp           | Response | User Agent         |
|---------------------|----------|--------------------|
|                     |          |                    |
```

## Step 2: Create the Apps Script

1. In your Google Sheet, click **Extensions** > **Apps Script**
2. Delete any default code in the editor
3. Copy and paste the following code:

```javascript
function doGet(e) {
  return handleRequest(e);
}

function doPost(e) {
  return handleRequest(e);
}

function handleRequest(e) {
  try {
    // Get the active spreadsheet
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    
    // Extract parameters
    const params = e.parameter;
    const response = params.response || 'yes';
    const userAgent = params.ua || 'Unknown';
    const timestamp = new Date();
    
    // Append data to sheet
    sheet.appendRow([timestamp, response, userAgent]);
    
    // Return success response
    return ContentService.createTextOutput(
      JSON.stringify({ success: true, message: 'Data logged successfully' })
    ).setMimeType(ContentService.MimeType.JSON);
    
  } catch (error) {
    // Return error response
    return ContentService.createTextOutput(
      JSON.stringify({ success: false, error: error.toString() })
    ).setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Click **Save** (💾 icon) or press `Ctrl+S` / `Cmd+S`
5. Name your project (e.g., "Valentine Tracker")

## Step 3: Deploy as Web App

1. Click **Deploy** > **New deployment**
2. Click the gear icon ⚙️ next to "Select type"
3. Choose **Web app**
4. Configure the deployment:
   - **Description**: "Valentine Response Tracker" (or any description)
   - **Execute as**: **Me** (your Google account)
   - **Who has access**: **Anyone** (important for public access)
5. Click **Deploy**
6. You may need to authorize the script:
   - Click **Authorize access**
   - Choose your Google account
   - Click **Advanced** if you see a warning
   - Click **Go to [Project Name] (unsafe)** - this is safe because it's your own script
   - Click **Allow**
7. **Copy the Web app URL** - it will look like:
   ```
   https://script.google.com/macros/s/AKfycbz.../exec
   ```
   
   ⚠️ **IMPORTANT**: Save this URL - you'll need it for the next step!

## Step 4: Update Your index.html

1. Open your `index.html` file
2. Find the section that says `/* EDIT HERE */` (around line 159)
3. Add your Google Apps Script URL as a new configuration variable:

```javascript
/************* EDIT HERE *************/
const YOUR_NAME = "Your Name Here";
const WHATSAPP_NUMBER = "1234567890"; 
// Use international format, no + or spaces
// Example: "14155552671"

const EMAIL_ADDRESS = "youremail@example.com";

// Google Sheets Tracking URL
const TRACKING_URL = "https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec";
/************************************/
```

Replace `YOUR_SCRIPT_ID` with your actual deployment URL from Step 3.

## Step 5: Testing the Webhook

Before deploying, test that your webhook works correctly.

### Using curl (Command Line)

Open your terminal and run:

```bash
curl "https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec?response=yes&ua=TestUserAgent"
```

Replace `YOUR_SCRIPT_ID` with your actual script ID.

**Expected Response:**
```json
{"success":true,"message":"Data logged successfully"}
```

### Using Your Browser

Simply paste this URL in your browser (replace YOUR_SCRIPT_ID):
```
https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec?response=yes&ua=BrowserTest
```

You should see a JSON response, and a new row should appear in your Google Sheet.

### Verify in Google Sheets

1. Go back to your Google Sheet
2. You should see new rows with:
   - Timestamp (when the test was run)
   - Response ("yes")
   - User Agent ("TestUserAgent" or "BrowserTest")

## Step 6: Deploy Your Updated App

1. Commit and push your changes to GitHub
2. GitHub Pages will automatically deploy the update
3. Test by visiting your live app and clicking "Yes"
4. Check your Google Sheet to verify the data is logged

## Troubleshooting

### No data appearing in the sheet

1. **Check the deployment URL**: Make sure you copied the entire URL from the deployment step
2. **Verify permissions**: The script should be set to "Anyone" can access
3. **Check the script**: Make sure the Apps Script code was pasted correctly
4. **Look at execution logs**: In Apps Script, click **Executions** to see if requests are coming through

### CORS errors

- Make sure your deployment is set to "Anyone" can access
- Redeploy the script if you changed any settings

### Script not authorized

- Go back to Apps Script deployment settings
- Reauthorize the script with your Google account

## Privacy & Data Collection Notice

This tracking is completely transparent to users clicking "Yes" on your Valentine's app. Consider:
- Adding a privacy notice to your app
- Informing users that responses are being logged
- Complying with any applicable privacy regulations (GDPR, CCPA, etc.)

## Sheet Data Format

Your Google Sheet will collect data in this format:

| Column | Name       | Description                                      | Example                          |
|--------|------------|--------------------------------------------------|----------------------------------|
| A      | Timestamp  | Date and time when response was recorded         | 2/14/2024 10:30:45 AM           |
| B      | Response   | The response value (always "yes" for this app)   | yes                              |
| C      | User Agent | Browser/device information of the user           | Mozilla/5.0 (Windows NT 10.0...) |

## Managing Your Data

### Viewing Responses
- Simply open your Google Sheet to see all responses in real-time

### Exporting Data
1. In Google Sheets, click **File** > **Download**
2. Choose your preferred format (Excel, CSV, PDF, etc.)

### Clearing Data
- Select the rows you want to delete (not the header row)
- Right-click and choose **Delete rows**

## Advanced: Multiple Response Types

If you want to track both "yes" and "no" responses, you can update the tracking code in `index.html` to send different response values. However, note that the current app moves the "No" button, so clicking it would be difficult to track!

## Support

If you encounter issues:
1. Check the Apps Script execution logs
2. Verify your deployment settings
3. Test with curl to isolate the problem
4. Ensure your TRACKING_URL in index.html is correct
