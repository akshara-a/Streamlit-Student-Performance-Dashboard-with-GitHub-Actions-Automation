# Student Performance Dashboard

A simple Streamlit dashboard for exploring dummy student performance data.
The app loads a CSV file, applies sidebar filters, calculates key metrics, and
shows charts plus a sortable student table.

## Project Overview

This project is useful as a small Streamlit starter app. It demonstrates how to:

- Load tabular data with pandas.
- Cache data loading with `st.cache_data`.
- Build interactive filters in the Streamlit sidebar.
- Display KPI metrics with `st.metric`.
- Create quick charts with Streamlit's built-in chart helpers.
- Show filtered records in an interactive dataframe.
- Keep a deployed Streamlit Community Cloud app warm with GitHub Actions and Playwright.

## Features

- Course filter using a sidebar multiselect.
- City filter using a sidebar multiselect.
- Minimum final score filter using a sidebar slider.
- Total student count for the selected data.
- Average final score.
- Average attendance.
- Top final score.
- Average score by course bar chart.
- Grade distribution bar chart.
- Student records table sorted by final score.
- Simple insight showing the best-performing course for the selected filters.

## Project Structure

```text
simple-streamlit-student-dashboard/
|-- app.py
|-- requirements.txt
|-- README.md
|-- data/
|   `-- students.csv
`-- .github/
    `-- workflows/
        `-- keep-alive.yml
```

## File Guide

### `app.py`

This is the main Streamlit application.

Important parts:

- `st.set_page_config(...)` sets the page title, icon, and wide layout.
- `load_data()` reads `data/students.csv` using pandas.
- `@st.cache_data` prevents Streamlit from re-reading the CSV on every rerun unless the data changes.
- Sidebar widgets collect the selected courses, cities, and minimum score.
- `filtered_df` applies all selected filters to the full dataset.
- KPI values are calculated from the filtered data.
- `st.columns(4)` places the four metrics in one row.
- `st.bar_chart(...)` renders the course and grade charts.
- `st.dataframe(...)` displays the filtered student records.
- The final insight uses `groupby("course")["final_score"].mean().idxmax()` to identify the course with the highest average final score.

### `data/students.csv`

This CSV contains the dummy student records used by the dashboard.

Columns:

| Column             | Description                |
| ------------------ | -------------------------- |
| `student_id`       | Unique student identifier. |
| `name`             | Student name.              |
| `course`           | Course name.               |
| `city`             | Student city.              |
| `attendance`       | Attendance percentage.     |
| `assignment_score` | Assignment score.          |
| `test_score`       | Test score.                |
| `final_score`      | Final calculated score.    |
| `grade`            | Final grade label.         |

The current dataset includes 40 records across these courses:

- Python
- Data Analytics
- Machine Learning
- Web Development

And these cities:

- Bengaluru
- Chennai
- Hyderabad
- Pune

### `requirements.txt`

The Python dependencies are:

```text
streamlit
pandas
```

### `.github/workflows/keep-alive.yml`

This GitHub Actions workflow runs every 6 hours and can also be triggered
manually from the GitHub Actions tab.

Its job is to open the deployed Streamlit app in a headless Chromium browser.
This helps prevent the app from going idle on Streamlit Community Cloud.

## Setup

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd simple-streamlit-student-dashboard
```

### 2. Create a virtual environment

On Windows PowerShell:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

On macOS or Linux:

```bash
python -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the app

```bash
streamlit run app.py
```

Streamlit will start a local server and print a local URL, usually:

```text
http://localhost:8501
```

Open that URL in your browser to use the dashboard.

## How To Use The Dashboard

1. Start the app with `streamlit run app.py`.
2. Use the sidebar to select one or more courses.
3. Use the sidebar to select one or more cities.
4. Move the minimum final score slider to filter lower-scoring records.
5. Review the four KPI cards at the top.
6. Compare average scores by course in the left chart.
7. Review grade distribution in the right chart.
8. Check the detailed student records table.
9. Read the simple insight section to see the best-performing course in the selected data.

## Deploy To Streamlit Community Cloud

1. Push this project to GitHub.
2. Go to Streamlit Community Cloud.
3. Create a new app from your GitHub repository.
4. Select `app.py` as the main file.
5. Deploy the app.

Because `requirements.txt` includes `streamlit` and `pandas`, Streamlit
Community Cloud can install the required dependencies automatically.

## Playwright Keep-Alive Workflow

Playwright is a browser automation library. It can control real browsers such
as Chromium, Firefox, and WebKit from code. In this project, Playwright is used
inside GitHub Actions to open the deployed Streamlit app automatically.

The workflow is located at:

```text
.github/workflows/keep-alive.yml
```

### What the workflow does

1. Runs on GitHub's `ubuntu-latest` runner.
2. Sets up Node.js 24.
3. Installs Playwright.
4. Installs the Chromium browser used by Playwright.
5. Reads the deployed app URL from the `STREAMLIT_APP_URL` repository secret.
6. Opens the Streamlit app in a headless Chromium browser.
7. Waits for the page to finish loading.
8. Keeps the page open briefly.
9. Prints the page title.
10. Closes the browser.

### Why Playwright is used here

Streamlit Community Cloud apps may go idle when they are not visited for a
while. All apps without traffic for 12 hours go to sleep. Community Cloud 
hibernates apps to conserve resources and allow the best communal use of the 
platform. To keep your app awake, simply visit your app. A simple HTTP request 
can sometimes be enough to wake a site, but a browser visit is closer to a real 
user opening the app. Playwright loads the page in Chromium, waits for browser 
activity to settle, and confirms that the page can be opened.

### Is this a test suite?

Not yet. The current Playwright workflow is a keep-alive check. It verifies
that the deployed page can open, but it does not assert dashboard behavior such
as whether filters work, charts render correctly, or the expected metrics are
visible.

To turn this into a real end-to-end test suite, you could add Playwright tests
that check:

- The dashboard title is visible.
- The sidebar filters are visible.
- Metric labels such as `Total Students` and `Average Score` are visible.
- Changing filters updates the table.
- The app does not show a Streamlit error message.

### Required GitHub secret

After deployment, add this repository secret in GitHub:

```text
STREAMLIT_APP_URL=https://your-app-name.streamlit.app
```

GitHub path:

```text
Repository -> Settings -> Secrets and variables -> Actions -> New repository secret
```

Use the exact deployed Streamlit app URL as the secret value.

### Workflow schedule

The workflow uses this cron schedule:

```yaml
- cron: "17 */6 * * *"
```

That means it runs every 6 hours at minute 17.

It also supports manual runs through:

```yaml
workflow_dispatch:
```

So you can open GitHub Actions and run it manually whenever needed.

## Troubleshooting

### `streamlit` command not found

Make sure the virtual environment is activated and dependencies are installed:

```bash
pip install -r requirements.txt
```

Then run:

```bash
streamlit run app.py
```

### App cannot find `data/students.csv`

Run the app from the project root directory, not from inside the `data` folder:

```bash
streamlit run app.py
```

### GitHub Actions says `STREAMLIT_APP_URL secret is missing`

Add the repository secret named exactly:

```text
STREAMLIT_APP_URL
```

The value should be your deployed Streamlit app URL.

### Playwright install is slow

The workflow installs Playwright and Chromium each time it runs. This is normal
for a simple workflow. For a larger project, you can improve speed by using
dependency caching or by keeping a committed Node.js test setup.

## Possible Improvements

- Add more realistic student data.
- Add date fields and time-based performance trends.
- Add downloadable filtered CSV reports.
- Add charts for attendance vs final score.
- Add a proper Playwright end-to-end test suite.
- Pin dependency versions in `requirements.txt` for more predictable deployments.
- Fix text encoding in `app.py` if the emoji characters appear incorrectly in the browser.
