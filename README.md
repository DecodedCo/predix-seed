# Predix Experience 2.0 Seed
Dashboard Seed is an application that uses Px Web Components inside a [Polymer](https://www.polymer-project.org) web application.
It runs on the [Express](http://expressjs.com/) web server.

## Getting Started

### Get the source code
Make a directory for your project.  Clone or download and extract the seed in that directory.
```
git clone https://github.com/PredixDev/predix-seed.git
```

### Install tools
If you don't have them already, you'll need node, bower and gulp to be installed globally on your machine.
1. Install [node](https://nodejs.org/en/download/).  This includes npm - the node package manager.
2. Install [bower](https://bower.io/) globally `npm install bower -g`
3. Install [gulp](http://gulpjs.com/) globally `npm install gulp -g`

### Install the dependencies
Change directory into the new project you just cloned, then install dependencies.
```
npm install
bower install
```

## Running the app locally
The default gulp task will start a local web server.  Just run this command:
```
gulp
```
Browse to http://localhost:5000.
Initially, the app will use mock data for the views service, asset service, and time series service.
Later you can connect your app to real instances of these services.

## Running in Predix Cloud
With a few commands you can build a distribution version of the app, and deploy it to the cloud.

### Create a dist version
Use gulp to create a distribution version of your app.
You will need to run this command during development every time before you cf push to make the latest dist.
```
gulp compile:all
```

### Deploy to the cloud
First make sure you're logged in to the Predix Cloud using the `cf login` command.
Then deploy your app using this command:
```
cf push my-seed-app
```
You can give the app any unique name you like.  "my-seed-app" is just an example.


### UAA

# 1. Setup UAA service
1. `cf create-service predix-uaa Tiered xlpuser04-uaa -c '{"adminClientSecret":"clientsecret"}'`
2. Add `xlpuser-04-uaa` to `manifest.yml`
3. `cf push`
4. `cf env xlp-polymer` to extract <uaa-URI>

# 2. Add client for UAA
1. `uaac target <uaa-URI>`
2. `uaac token client get admin`
3. `uaac client add xlpuser04-uaa-client -i`:
  1. scope: openid uaa.none
  2. authorized grant types: password client_credentials authorization_code
  3. authorities: uaa.resource openid uaa.none
4. `echo -n xlpuser04-uaa-client:clientsecret | base64` - save in manifest.yml under `base64ClientCredential`
5. Update `clientId` in manifest.yml

# 3. Add users to app
1. `uaac user add <my-user> --emails <my_user>@domain.com --password <my_password>`
2. `uaac group add uaa.user`
3. `uaac member add uaa.user user`

### Predix Time series

# 1. Create Service

1. `cf env xlp-polymer` - grab issuer id from uaa
2. `cf create-service predix-timeseries Tiered xlpuser04-timeseries -c '{"trustedIssuerIds":["https://xxx.run.aws-usw02-pr.ice.predix.io/oauth/token"]}'`
3. Update manifest.yml with timeseries instance, `cf push`

# 2. Config Service

1. `cf env xlp-polymer` - grab zone id
2. `uaac client update xlpuser04-uaa-client -i` add
  1. authorities:
`timeseries.zones.<zone-id>.user timeseries.zones.<zone-id>.query timeseries.zones.<zone-id>.ingest`
  2. scope:
`timeseries.zones.<zone-id>.user timeseries.zones.<zone-id>.query timeseries.zones.<zone-id>.ingest`

## Questions?
- Ask questions and file tickets on <a href="https://www.predix.io/community" target="_blank">https://www.predix.io/community</a>.

# Copyright
Copyright &copy; 2015 GE Global Research. All rights reserved.

The copyright to the computer software herein is the property of
GE Global Research. The software may be used and/or copied only
with the written permission of GE Global Research or in accordance
with the terms and conditions stipulated in the agreement/contract
under which the software has been supplied.
