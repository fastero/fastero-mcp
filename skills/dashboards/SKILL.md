---
name: dashboards
description: Builds dashboards and charts in Fastero that a team or a client opens without Claude, emails them to people on a schedule such as every Monday morning, and emails an alert when a number crosses a line, from the organisation's own databases, an uploaded CSV or Excel file, or Fastero's sample data. Use when someone wants a dashboard or report their team, manager or client can open; their numbers or a report emailed to people every week or every morning; a chart that should still be there after the conversation; an alert on a number; or an answer from their company's own database. Works through the Fastero connector.
---

# Dashboards in Fastero

What you build in Fastero stays in the organisation's workspace after the conversation ends. Members open a
dashboard with their own login, whether or not they use Claude.

## Find where to work

1. `fastero_list_projects` names the projects and which organisation and account this connection is. Other tools
   take a projectId from it; a new workspace has one project.
2. `fastero_list_connections` lists the databases and files the organisation has connected. The one marked
   `isSample: true`, Sample sales data, has one table, `sales`, of invented orders. Its numbers are never the
   organisation's own, so say they are invented whenever you show them.
3. When the person has no connection of their own yet:
   - a CSV or Excel file they have: `fastero_upload_file`, then `fastero_create_file_connection` makes it
     queryable;
   - a database: connecting it in the Fastero app keeps the password out of the conversation.
     `fastero_create_connection` also works; its description says what passing a password through the
     conversation means;
   - or try things on the sample.

## Get the numbers right

- For a business number such as revenue, churn or active users, `fastero_get_definitions` with the connectionId
  returns the organisation's own expression for it, so the answer agrees with the team's reports. An
  organisation that has defined nothing gets nothing back.
- `fastero_list_connection_schemas` lists tables and columns. `fastero_run_sql` runs one read-only statement in
  the connection's own SQL dialect and returns at most 500 rows, so aggregate or use a LIMIT.
- Once the person has settled what a number means, `fastero_save_definition` records it, so later conversations
  and Fastero's own assistant work it out the same way.

## Build the dashboard

- `fastero_show_chart` draws a chart in the conversation and saves nothing. Its Save button, or
  `fastero_save_chart` with the same arguments, keeps it.
- `fastero_save_chart` saves the query, makes the chart and adds it to a dashboard in one call. `dashboard.name`
  alone uses the project's dashboard of that name, or creates it. Put the charts that follow on the same
  dashboard.
- Give the person the `url` the answer returns: the page where a signed-in member opens the dashboard.
- `fastero_set_dashboard_refresh` re-runs its charts on a timer, or when the data changes (Postgres and BigQuery).

## Share it and put it on a timetable

- Members of the organisation open the dashboard with their own login. `fastero_invite_to_project` asks to
  invite someone who should sign in. For someone outside with no login, such as a client,
  `fastero_share_dashboard` asks for a public link.
- Neither happens from the conversation alone. An invitation, and a dashboard's first public link, wait until a
  signed-in person confirms them at the `confirmUrl` the answer returns: an Owner or Admin for an invitation, and
  an Owner, Admin or Editor of the project for a link. Give the person that link and say so.
- `fastero_schedule_dashboard_email` emails the dashboard, with a PDF of it attached, to members of the
  organisation on a timetable, such as every Monday at 8.
- To watch one number, `fastero_create_query` saves the SQL, then `fastero_create_alert` emails when the number
  crosses a line.
