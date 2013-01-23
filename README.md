MindMup - Zero Friction Mind Map Canvas
=======================================

[![Build Status](https://api.travis-ci.org/mindmup/mindmup.png)](http://travis-ci.org/mindmup/mindmup)

MindMup is a zero-friction mind map canvas. Our aim is to create the most productive mind mapping environment out there, removing
all the distractions and providing powerful editing shortcuts. 

This git project is the server-side portion of MindMup. It provides a web interface that serves maps, allows users to edit and save them,
provides menus and feedback forms and links to analytics. All maps are public, and each time anyone saves a map the changed version
gets a unique URL. You can see an example of this live on http://www.mindmup.com. 

Dependencies
-------------

- This app is designed to run in the Heroku cloud or as a standalone [Sinatra application](https://github.com/sinatra/sinatra/). 
- It depends on the [MapJS](http://github.com/mindmup/mapjs) javascript canvas for client side rendering, and imports a compiled
single-file version of that project as /public/mapjs-compiled.js
- It uses Amazon S3 AWS service to store maps
- It uses Google analytics to track feature votes, usage patterns and report error rates
- It uses JotForm to send e-mails and submit user feedback
- It uses ShareThis to enable easy map sharing and URL shortening
  
Configuration
-------------

The server depends on the following environment variables:

- GOOGLE_ANALYTICS_ACCOUNT _Google analytics ID to use for tracking. Don't use the production IDs in staging. Not used in dev and testing_
- S3_BUCKET_NAME _Only the name of S3 Bucket where files are located (eg mindmup)_
- S3_KEY_ID _AWS Access key ID that has write access for the bucket_
- S3_SECRET_KEY _AWS Secret key corresponding to the ID above_
- S3_UPLOAD_FOLDER _folder within the bucket where user maps are stored. (eg map). Avoid using the same folder for production and testing_
- S3_WEBSITE _website domain name where user files are publicly accessible. Don't specify protocol or slashes (eg mindmup.s3.amazonaws.com)_
- SITE_URL _public URL for the web site instance, used to get S3 to redirect back to us. Include protocol and slashes (eg http://localhost:5000/)_
- JOTFORM_CONTACT _ID of the form that will receive feedback on JotForm_
- SHARETHIS_PUB _ShareThis publisher ID for share links_
- DEFAULT_MAP _name or key of the map to be shown on homepage (eg default)_
- RACK_SESSION_SECRET _hashing key for rack sessions (should be relatively random, alphanumeric)_
- MAX_UPLOAD_SIZE _in KB, maximum size allowed for the users to upload. If not defined, 100 is the default value_
- NETWORK_TIMEOUT_MILLIS _number of milliseconds before we start reporting to users that there is a problem, in case of a timeout. Default is 10000._
- CURRENT_MAP_DATA_VERSION _prefix for files signaling the data format. For now, we use a1. This is to help in the future with format versioning_

Running standalone
------------------

To run the application in standalone mode (without Heroku), create a .env configuration file (_don't commit this to git_) and put your S3 account info
and other stuff mentioned above into that. You don't need ShareThis or JotForm to run the site (but sharing and feedback won't work without it). Then
execute:


    gem install foreman
    bundle install
    foreman start

See [Heroku Configiration Local Setup](https://devcenter.heroku.com/articles/config-vars#local-setup) for more information.

Note: Unless RACK_ENV is production, Google analytics is not used, the analytics information is instead logged to the JavaScript console.


Running offline
---------------

We don't package any of third party javascript dependencies (and there are plenty). In order to deploy faster and provide users with a better
page loading experience, all third party dependencies are retrieved from CDNs where available or the S3 file storage. To run offline, set the environment
variable OFFLINE to true, and run the setup_offline.sh script in the root folder to download all the dependencies to /public/offline. This enables
development and testing without an internet connection.

Executing tests
---------------

The server does very little apart from serving files. There are some server-side tests that you should run before committing, in the test folder. Run them with:
    rspec test/*.rb

The tests don't depend on the .env file intentionally. Supply your own config as part of the test if needed.
