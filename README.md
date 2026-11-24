utomated Price Tracker

A Python/Flask scraper and an n8n workflow (both in Docker) that log product prices to Google Sheets and email you when the price drops below a threshold.

How it works
Schedule Trigger -> HTTP Request -> Append row in sheet -> If price < threshold -> Gmail alert
                    (Flask scraper)   (Google Sheets)
Schedule Trigger runs the workflow on a timer.
HTTP Request sends a product URL to the Flask scraper (POST /scrape), which returns the product title and price.
Append row in sheet logs the title and price to a Google Sheet, so you build a price history over time.
If checks whether the price is below your target (set to 52 in the example).
Send a message emails you through Gmail when it is.

The example uses books.toscrape.com, a practice site built for scraping. Swap in your own product URL once it works.

What you need
Docker installed and running
A Google account (for Google Sheets and Gmail)
A Google Sheet with two column headers in row 1: Title: and Price:
Setup
1. Clone the repo
bash
git clone https://github.com/aidan787/n8n-price-tracker.git
cd n8n-price-tracker
2. Start the scraper and n8n

TODO (Aidan): replace this with the exact command(s) you use. For example, docker compose up -d if you have a compose file, or your separate docker run commands.

The scraper container must be named flask-scraper, listen on port 5001, and share a Docker network with n8n. That is why the workflow can call http://flask-scraper:5001/scrape.

Once both are running, open n8n at http://localhost:5678.

3. Import the workflow
In n8n, create a new workflow.
Click the three dots menu, then Import from File.
Choose workflows/price-tracker-workflow.json.

The workflow imports without any logins or personal info, so you need to connect your own accounts next.

4. Connect your own accounts
Append row in sheet: create or select a Google Sheets credential (Google OAuth), then pick your sheet. Replace the placeholder YOUR_GOOGLE_SHEET_ID with your spreadsheet.
Send a message: create or select a Gmail credential (Google OAuth), then replace YOUR_EMAIL_HERE with the address that should get alerts.

n8n's docs walk through creating Google OAuth credentials: Google credentials.

5. Customize and run
Set how often it runs in the Schedule Trigger.
Change the product URL in the HTTP Request node.
Change the price threshold in the If node.
Click Execute workflow to test it, then publish the workflow to run on the schedule.
Project structure

TODO (Aidan): list your real files here. For example:

workflows/    n8n workflow export (credentials and personal info removed)
scraper/      Flask app that scrapes the product page
Notes
The workflow file contains no credentials. Anyone who imports it uses their own Google accounts.
Prices are scraped from the product page HTML, so a site redesign can break the scraper.
