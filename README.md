# Casca

A Looker inspired project (<https://cloud.google.com/looker>).

The goal is to simplify the whole thing thru JSON configs instead of coding via liquid.

The project will consist of the ff packages:

1. Node based SQL generator  (will focus on MySQL for now, can add more in the future)
2. React based Dashboard UI
3. Send commands/fetch data via REST API

Simplified Workflow

Backend

1. set up a MySQL database connection on Casca via json config
2. create a dashboard config by declaring views, subviews, fields and filters on a json config


Front end

1. import casca UI package from your react based project
2. connect the casca ui to casca backend using the rest api
3. Render the dashboard based on the backend config
4. send commands from dashboard to backend using rest api.