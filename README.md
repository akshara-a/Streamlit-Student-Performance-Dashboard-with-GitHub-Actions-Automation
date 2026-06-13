# Student Performance Dashboard

This is a basic Streamlit application with random dummy student data.

## What it shows

- Total students
- Average final score
- Average attendance
- Top score
- Average score by course
- Grade distribution
- Filterable student table

## Project structure

```text
simple-streamlit-student-dashboard/
├── app.py
├── requirements.txt
├── data/
│   └── students.csv
└── .github/
    └── workflows/
        └── keep-alive.yml
```

## Run locally

```bash
pip install -r requirements.txt
streamlit run app.py
```

## Deploy

Push this project to GitHub and deploy it on Streamlit Community Cloud.

## Optional keep-alive

After deployment, add this GitHub repository secret:

```text
STREAMLIT_APP_URL=https://your-app-name.streamlit.app
```

The included GitHub Actions workflow will ping the app every 6 hours.