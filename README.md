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
3. create API that will receive the SQL commands on the front end on a json format, execute the SQL command, then return the result to the front end.


Front end

1. import casca UI package from your react based project
2. connect the casca ui to casca backend using the rest api
3. Render the dashboard based on the backend config
4. send commands from dashboard to backend using rest api.



Here is a simple flow chart:

```mermaid
graph TD;
    CascaBackend-->Setup connections, users, views, dashboards, routes;
    Setup connections, users, views, dashboards, routes-->routes listens to API requests;
    CascaFrontend-->Connect on backend via authenticate();
    Connect on backend via authenticate()-->Render dashboard on iframe;
    Render dashboard on iframe-->User selects fields and filters;
    User selects fields and filters-->Send commands via API;
    Send commands via API-->routes listens to API requests;
    routes listens to API requests-->routes interpret the request, generates SQL query, executes it;
    routes interpret the request, generates SQL query, executes it-->returns result on the front end;
    routes interpret the request, generates SQL query, executes it-->Render dashboard on iframe;
```

# The Basic setup and Terminologies

Views

Views in Casca are the config file to generate the dashboard.
You configure a view by declaring what tables it represents on your database and declaring the fields inside it.

```
{
    "table_name" : "records", 
    "view_name" : "Movie Records",
    "fields" : [
        {
            "name" : "id",
            "visible" : false,
            "type" : "integer",
            "value" : "${TABLE}.id",
        },
        {
            "name" : "name",
            "display_name" : "Record Name",
            "type" : "string",
            "value" : "${TABLE}.name",
        },
        {
            "name" : "isActive",
            "display_name" : "Record Active?",
            "type" : "boolean",
            "value" : "${TABLE}.deleted_at IS NULL",
        }
    ]
}
```

Fields

Fields are the dashboard columns. Like SQL queries you can also make your own fields on the `value` property.

```
{
    "name" : "id",
    "visible" : false,
    "type" : "integer",
    "value" : "${TABLE}.id",
}
```

Dashboard

Dashboard is the UI part that consist of single or multiple views. it has a main view, subviews can be added like how you join tables on SQL queries.

```
{
    "includes" : [
        "views/*.json"
    ],    
    "name" : "sales_report",
    "display_name" : "Sale reports",
    "main_view" : "records",
    "sub_views: [
        {
            "name" : "sales",
            "join_type" : "left_outer",
            "on" : "${records}.id = ${sales}.record_id"
        }
    ]
}
```

Once configs are done, the ideal code on the backend will look like this:

```
import { createInstance} from 'casca' //or require, i dont know, lets see

const casca = createInstance("connection_name") //connect_name setup on the strapi backend
casca.init("dashboard_config.json") //will initiate the dashboard
```

for the front end code, it will look like this:

```
import { authenticate } from 'casca'

const config = {
    user_name: "user",
    password: "pass",
    dashboard: "sales_report",
    extra: {
        
    }
}
const casca = authenticate(config)

if(!casca) {
    return <iframe>
    {
        casca.render(dashboard)
    }
    </iframe>
}
```

These are the basic concepts.
Ideally, developers using Casca only needs to know how to :

1. Setup the strapi base back end, add the users/connections from there.
2. Setup the front end thru iframe
3. create views and dashboard configs using the template above.