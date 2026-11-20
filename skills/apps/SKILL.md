---
name: apps
description: Hosts Streamlit apps behind a team's login, and runs notebooks and Python scripts on Fastero's machines, now or on a schedule such as every morning at 7, so they do not depend on anyone's own computer; the organisation's Secrets reach them as environment variables. Use when someone wants to deploy, host or share a Streamlit app with their team; run a Python script, a report generator or a notebook every morning, every hour or on a cron timetable; keep an app or a job running without their laptop; or email a notebook's results on a schedule. Works through the Fastero connector.
---

# Streamlit apps, notebooks and scheduled Python in Fastero

All of this runs on Fastero's machines and uses compute while it runs. `fastero_list_projects` gives the
projectId each tool takes.

Pick by what people do with it: a Streamlit app for something they use in the browser, a notebook for analysis
they read in place, and a Python project for a repeatable job with its own log.

## Host a Streamlit app

1. `fastero_create_streamlit_app` with all the app's files. The top-level `app.py` is what runs, and
   `requirements.txt` lists extra packages. It is created stopped.
2. Passwords and API keys belong in Secrets, which the app reads as environment variables, not in its files.
   `fastero_create_secret` sets one for a project or for the whole organisation; only organisation Owners and
   Admins can.
3. `fastero_run_streamlit_app` starts it. `fastero_get_streamlit_app` shows `running` once it is up (about 12
   seconds, a few minutes when packages install), or `errorMessage` saying why it did not start.
   `fastero_get_streamlit_app_logs` has the start-up output, pip's errors included.
4. Give the person `viewerUrl`, where signed-in members of the project open it. Apps have no public link: to add
   people, `fastero_invite_to_project`, which waits until a signed-in Owner or Admin confirms it at the
   `confirmUrl` it returns.
5. To change it, `fastero_write_streamlit_file` changes one file and `fastero_replace_streamlit_files` replaces
   them all, deleting any file it leaves out. A running app picks up code in about 5 seconds; a changed
   `requirements.txt` applies on the next start.

An idle app sleeps, after 10 minutes by default, and starts again in about 15 seconds when someone opens it.
`fastero_set_streamlit_always_on` keeps it running so nobody waits. Always-on is a paid-plan feature: without a
paid plan the tool says so and names the billing page.

## Notebooks

- `fastero_create_notebook`, then `fastero_write_notebook` with the cells: Python, markdown, and SQL cells whose
  rows become a pandas DataFrame for the cells after them (metadata `custom_type: 'sql'`, `connId` and
  `variableName`).
- `fastero_run_notebook` runs it as last saved and answers at once with a runId. `fastero_get_notebook_run`
  reports how the run went, and `fastero_read_notebook` has the outputs once it has finished.
- `fastero_schedule_notebook` runs it on a timetable, at most once an hour, and emails a good run's last output to
  the members of the organisation it lists.
- `fastero_pin_notebook_cell` makes a dashboard widget from a cell's result, which follows the notebook's latest
  run.

## Python scripts

- `fastero_create_python_project` starts a project with `main.py` and an entrypoint `main`;
  `fastero_write_python_file` writes its files.
- `fastero_run_python_project` runs an entrypoint now. `fastero_list_python_runs` follows the run, and
  `fastero_get_python_run_log` reads what it printed once it has finished.
- `fastero_set_python_schedule` runs an entrypoint on a cron timetable, in UTC.
