# slack-poker-planner

Poker planning app for Slack. You can directly add it to your slack team [from here](https://deniz.co/pp/). This version includes Plotly site-specific customizations.

![Demonstration](demo.gif)

### Plotly-specific customizations

We run this in the `planning-poker` namespace of the pre-emptible `misc-tools-prod` Google Kubernetes Engine cluster in the `plotly-misc-tools` Google Cloud Platform project. Most Kubernetes resources used are in the `k8s_resources` subdirectory. These two exceptions are omitted as security-sensitive:

* The `slack` Secret object, used to feed Slack credentials to the app. To recreate this, provide the following keys from the Slack app administration interface: `SLACK_CLIENT_ID`, `SLACK_CLIENT_SECRET`, `SLACK_VERIFICATION_TOKEN`
* The `db` Secret object, which persists the app's SQLite state file (primarily OAuth tokens) without needing to arrange persistent storage volumes. To recreate this:
  1. Re-authorize the app via the Slack app administration interface.
  1. Fetch `/db/db.sqlite` from the running container, such as with `kubectl cp`.
  1. Create the `db` Secret with `db.sqlite` as the only key and the contents of that file as the value.

Additionally, the load balancer IP of the `planning-poker` Service is intentionally configured to match the `planning-poker` IP address which we have reserved in the same GCP project and zone as the GKE cluster in which we are running, and to which the `planning-poker.plotly.systems` Google Cloud DNS hostname points. Please keep these three items synchronized.

The instructions below this point are preserved unchanged from the upstream Git repository.

## Setup

For any reason, if you want to host your own app, follow this steps.

### Creating slack app

- Create a new Slack app [from here](https://api.slack.com/apps).
- Create a new Slash Command `/pp` (or any command you want) and set request url as `http://my.awesome.project.url/slack/pp-command`
    - Make sure that "Escape channels, users, and links sent to your app" option is turned on
- Activate Interactive Messages with request url `http://my.awesome.project.url/slack/action-endpoint`, options load url is not used, you can leave it blank.
- Add a new OAuth Redirect URL: `http://my.awesome.project.url/oauth`
- Required permission scopes: `commands`, `channels:read`, `chat:write:bot`, `groups:read`, `users:read`


### Running

- Clone this repo
- Install dependencies with `npm i` or `yarn`
- Start the app with `npm start`

**Environment variables:**
- **`PORT`**: Port number for webserver. Default: `3000`
- **`BASE_PATH`**: If you're not serving from root, set this variable. Default: `/`
- **`SLACK_CLIENT_ID`**: Slack client id, default null
- **`SLACK_CLIENT_SECRET`**: Slack client secret, default null
- **`SLACK_VERIFICATION_TOKEN`**: Slack verification token, default null
