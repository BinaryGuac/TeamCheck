TeamCheck Project Instructions

---

## 🏗️ Project: TeamCheck — Game Attendance Poll

Project TeamCheck aims at helping a group of sport fanatic to plan the next game day by polling who will be able to attend.
Here's a full summary of everything built and configured for this project:


### 📁 Files
- **`index.html`** — single-page app, no server needed
- See google script at the end of this file

---

### ⚙️ CONFIG block (top of index.html)
```js
GITHUB_OWNER: "BinaryGuac"
GITHUB_REPO:  "TeamCheck"
GAME_DAY:     "sunday"          // repeats every week the same day chosen
WEEKS_AHEAD:  2                   // 3 total columns (this week + 2)
SHEETS_URL:   See bottom
TEAMCHECK VERSION:  "TeamCheck V1.1 2026-05-12"
DEBUG WINDOW: ON/OFF
PLAYERS LIST:      [ "Nick","Ken","Steven","Sung I","Donavon","Jim","Stephen","Frederic","Ian","Dane",
    "Scott","Joel","Luiz","Naim","Leigh", "Sherif","Nicholas","Sebastien","David","Jake", "Chris C","Dylan","Iwan","Robert",
    "Player-25","Player-26","Player-27","Player-28","Player-29","Player-30"]
```

---

### 🎮 Front page Features
- At the top of the page the title is "GAME DAY POLL" with a soccer ball on the left and a goal on the right
- Below the title these instructions: " Select your availability for this coming weekend, following weeks are optional, click CONFIRM once done"
- Read and display the available data when the page loads so the player can see who has already registered
- The display is a table with 4 collumns:
  - column one is the list of players
  - column 2 to 4 the availability of each player for each game day
  - at the top of each date column, display the date in capital Letters as "SUNDAY, MONTH, day"
  - for header style sue: Bebas Neue font, gold color, centered, 1rem / 2px letter-spacing. 
- Player selects their name from a dropdown in the top bar
- CONFIRM button sits inline next to the name selector
- For each of the 3 upcoming Game days, a dropdown lets them pick:
  - `–` (no answer), `⛔ OUT`, or `👤 +1(me)` through `👥 +5`
- Clicking CONFIRM shows a modal depending on the selection of the coming day selection:
  - *"We'll miss You, see you next time"* if the coming day game selection is OUT
  - *"We'll message you to confirm when we have enough players"* if the coming selenction is +1 or more
- Other players' selections show as read-only colored pills
- Total attending count shown in the footer row per date
- Display a version of the index file at the bottom of the page by adding the date of creation of the file YYYY-MM-DD-HH:MM where HH:MM are hours and minute of PST time zone



### 💾 Storage — Google Sheet
- We are using a google sheet to store all player attendance data. 
- The google sheet is accessible by a Google Apps Script
- All the data attendance is stored in the Sheet "Data". 
  - first column contains players names
  - following 3 columns contain attendance per game day of each player
  - the top row is the header of the column: 
     - Name
	 - Date1, Date2 and Date3 where dates actually contain the dates in format yyyy-mm-dd
- Do not wait for activity data logging to complete the data loading, it should be asynchronous.
- Before displaying to the use execute the "date Logic" part to update the data based on the current date. See chapter below for the detailed logic
- When writing back, only writes the updated cells that have been modified by the player to avoid erasing other input
- Writes back on CONFIRM via `PUT` to the same API
- Page **auto-refreshes every 5 minues ** to pick up other players' changes
- Page refreshes after selecting confirm to show who else has registered.
- When reading data from the google sheet, only load the necessary data which should be A1:D31

---

### 📅 Date logic
- This section explains how to handle the transition when a game day is over
- When game day is over (the day after, no early rollover) its relevant data shall be removed from the google sheet for this to happen follow the directions below
- when loading the player data from google sheet do the following sequence to ensure freshness of the data
 - Read Date1 (= Cell B1)
    - if Date1 is today or in the future then proceed with user inputs as described in the feature chapter
    - if Date1 is exactly any date strictly before today	
		- Copy entire player data in local memory
	    - erase data from colcolumn B 
		- Shift data from column C to column B (B1:B31 = old C1:C31)
		- Shift data from column D to column C (C1:C31 = old D1:D31, D1:D31 = empty)
		- Add a new date in Cell D1 as being a week after the date in updated Cell C1
		- save back the entire player data to google sheet
		- display the new data to the user
		- while doing this process ensure that in the status display at the top of the page, it shows "Updating Data for the new week" and add a log to the debug window

---


### 📊 Logging Activity Data — Google Sheets
- The goal is to keep a record of when the page is being accessed and relevant data to identify who is accessing
- Logging data is stored in the sheet "Log"
- Log on **page load**: timestamp, use "PageLoad" for the player name, IP address (add api call to get it)
- Logs on **CONFIRM**: timestamp(use California time zone) , player name, attendance selections, event, user agent, screen size, referrer, IP address (add api call to get it)
- for the IP fetch use ipify in an asynchronous way, initialize with value 0.0.0.0 wait for 3 seconds if no answer still execute the log.
- do not wait for the response to log data load the page and display to the use. 
- see technical directions at the bottom of the file for implementation

### 📊 Sequencing
- At page load, first load the data from google script and then request ip address from ipify 
- as data is loading display message that data is loading
- Display the data to the user, the table header row shall come from the google sheet
- once page is loaded record logging data to the log info sheet
- wait for user input
- once user press confirm: store player data in google sheet then record log data then send message to user

---

### Hosting
- the page will be hosted on github and the link will be
https://binaryguac.github.io/TeamCheck


---

### 🎨 Design
- Dark green header (`#0a1a0c` → `#1a3d1e`) with gold accent
- Table displaying player list and Alternating White/dark  alternating stripes for the table body
- Gold CONFIRM button inline in the who-bar "CONFIRM"
- Fonts: Bebas Neue (headings) + DM Sans (body)
- Subtitle: *"coming weekend is required, following weeks are optional"*
- add a soccer ball favicon using a small inline SVG encoded as a data URI 

### Development
- During development use the development script to not alter the production google sheet
- just create a page that will be used locally
- you can load the page hosted on github for initial start before making modifications
- For dev version only add a floating debug panel usign dbg() calls along the code

---

# Google Script
Here is the code of the Googlesheet 

For the production URL:
https://script.google.com/macros/s/AKfycbyDU3O6gydWsDEMbLVeY524Zryv7zu8MJpS-GQR_Ts6nOS20XZ67TPYl2D0aWHZ9KyBnw/exec

For the Development:
https://script.google.com/macros/s/AKfycbwbN8WbWX2dtUyBEGyYMwA_cn2JtlnfvSOt1x6-qJ43lkinAZ20PkDjGP1wBkitj9GCTg/exec
Deployment ID
AKfycbwbN8WbWX2dtUyBEGyYMwA_cn2JtlnfvSOt1x6-qJ43lkinAZ20PkDjGP1wBkitj9GCTg

# Change Log

| Date        | Version  | Change                          |
|-------------|----------|---------------------------------|
| 2026-05-01  | 1.0      | Initial Launch                  |
| 2026-05-17  | 1.1      | Automatic Date transition       |
|             |          | Performance Optimization        |
| ------------|------    | --------------------------------|




// ═══════════════════════════════════════════════════════════
//  Google Apps Script — Read & Write a range
//  Deploy as: Web App → Execute as: Me → Who has access: Anyone
// ═══════════════════════════════════════════════════════════

// ── READ a range ────────────────────────────────────────────
function doGet(e) {
  try {
    var sheetName = e.parameter.sheet || "Data";
    var range     = e.parameter.range || "A1:D10";

    var ss     = SpreadsheetApp.getActiveSpreadsheet();
    var sheet  = ss.getSheetByName(sheetName);

    if (!sheet) {
      return jsonResponse({ status: "error", message: "Sheet not found: " + sheetName });
    }

    var values = sheet.getRange(range).getValues();

    return jsonResponse({
      status: "ok",
      sheet:  sheet.getName(),
      range:  range,
      values: values
    });

  } catch(err) {
    return jsonResponse({ status: "error", message: err.toString() });
  }
}

// ── WRITE a range ────────────────────────────────────────────
function doPost(e) {
  try {
    var data = JSON.parse(e.postData.contents);

    // ── Append-row logging → "Log" sheet ────────────────────
    if (data.timestamp !== undefined && data.values === undefined) {
      var ss        = SpreadsheetApp.getActiveSpreadsheet();
      var logSheet  = ss.getSheetByName("Log");
      if (!logSheet) {
        return jsonResponse({ status: "error", message: "Sheet 'Log' not found" });
      }
      logSheet.appendRow([
        data.timestamp   || '',
        data.player      || '',
        data.selections  || '',
        data.ua          || '',
        data.screen      || '',
        data.timezone    || '',
        data.lang        || '',
        data.platform    || '',
        data.cores       || '',
        data.mem         || '',
        data.gpu         || '',
        data.canvas2d    || '',
        data.touchPoints || '',
        data.plugins     || '',
        data.ip          || '',
      ]);
      return jsonResponse({ status: "ok", action: "appended", sheet: "Log" });
    }

    // ── Range write → "Data" sheet ───────────────────────────
    var range  = data.range || "A1";
    var values = data.values; // 2D array

    if (!values || !Array.isArray(values)) {
      return jsonResponse({ status: "error", message: "Missing or invalid 'values' array" });
    }

    var ss    = SpreadsheetApp.getActiveSpreadsheet();
    var sheet = ss.getSheetByName("Data");

    if (!sheet) {
      return jsonResponse({ status: "error", message: "Sheet 'Data' not found" });
    }

    var numRows = values.length;
    var numCols = values[0].length;
    sheet.getRange(range).offset(0, 0, numRows, numCols).setValues(values);

    return jsonResponse({
      status:  "ok",
      action:  "written",
      sheet:   "Data",
      range:   range,
      rows:    numRows,
      cols:    numCols
    });

  } catch(err) {
    return jsonResponse({ status: "error", message: err.toString() });
  }
}

// ── Helper ───────────────────────────────────────────────────
function jsonResponse(obj) {
  return ContentService
    .createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}


## 🔧 Google Apps Script — Fetch Fix (CORS / Redirect)

### Root Cause
Apps Script Web Apps return a **HTTP 302 redirect** before serving JSON. Additionally, sending `Content-Type: application/json` triggers a **CORS preflight** (`OPTIONS`) request that Apps Script does not handle — silently killing writes.

---

### GET (read)
```js
const resp = await fetch(SHEETS_URL + "?sheet=Data&range=A1:D31", {
  redirect: "follow"   // ← required — follow the 302 redirect Apps Script issues
});
const json = JSON.parse(await resp.text());
```

---

### POST (write)
```js
const resp = await fetch(SHEETS_URL, {
  method:   "POST",
  redirect: "follow",  // ← follow the redirect
  body:     JSON.stringify(payload)
  // ← NO Content-Type header — omitting it avoids CORS preflight
  //   Apps Script reads body via e.postData.contents and JSON.parse() works fine
});
const json = JSON.parse(await resp.text());
```

---

### POST (fire-and-forget logging)
```js
fetch(SHEETS_URL, {
  method: "POST",
  mode:   "no-cors",   // ← skips preflight entirely; response is opaque (unreadable)
  body:   JSON.stringify(logPayload)
  // Use only when you don't need to confirm success (logging)
});
```

---

### Rules of thumb
| Scenario | Solution |
|---|---|
| GET fails with "Failed to fetch" | Add `redirect: "follow"` |
| POST write silently fails | Remove `Content-Type` header |
| Logging / fire-and-forget POST | Use `mode: "no-cors"` |
| Need to confirm POST succeeded | No `Content-Type`, with `redirect: "follow"`, parse `resp.text()` not `resp.json()` |