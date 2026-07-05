Google Sheets to PostgreSQL Pipeline
Hey! This is a simple, automated Python pipeline that pulls data directly from a Google Sheet, cleans up common spreadsheet messiness using Pandas, and drops it neatly into a PostgreSQL database.

It’s built to handle those annoying formatting edge cases that usually crash database uploads (like hidden spaces in headers or ghost columns).

🛠 What It Does
Grabs Live Data: Connects directly to Google Sheets using a Google Service account—no manual CSV exporting required.

Smart Schema Creation: Checks your database and automatically creates a dedicated energy schema if it isn’t already there.

Fixes Spreadsheet Messiness:

Strips out accidental blank spaces at the beginning or end of column names.

Drops those annoying "ghost columns" (empty columns that happen when someone accidentally clicks or formats blank cells in Excel/Sheets).

Automatically deduplicates column names so PostgreSQL won't throw an error.

Efficient Uploads: Uses batch processing (chunksize=1000) so it won't hog your system memory if the dataset grows.

🚀 Setup & Requirements
1. Install Dependencies
You'll need a few libraries to handle the data processing, Google API connection, and database driver:

Bash
pip install gspread pandas numpy sqlalchemy psycopg2-binary
2. Google Sheets Authentication
Because the script connects programmatically, you need to tell Google it's safe:

Grab a Service Account .json key file from your Google Cloud Console.

Put it in this project's root folder and name it credentials.json.

Open your target Google Sheet in your browser, click Share, and invite the client_email address listed inside your JSON file (Viewer permissions are fine).

3. Database Connection
Make sure your script or environment has access to your DB_URI. It should look like this:

Python
DB_URI = "postgresql+psycopg2://user:password@host:port/dbname"
📂 How It Runs
When you execute clean.py, it follows a quick 3-step lifecycle:

Extract: Logs into Google Sheets, scans the worksheets, and pulls the target data (Edwin-CF & CM) into a Pandas DataFrame.

Transform: Strips the header whitespace, kills the blank columns, and fixes duplicate headers to keep the database happy.

Load: Connects to Postgres, makes sure the schema exists, and uses to_sql with if_exists="replace" to refresh the target table (energy.edwin_cf_cm) with fresh data.

At the very end, it runs a quick SELECT * test query just to print out the final result and confirm the data safely made it across the finish line!
