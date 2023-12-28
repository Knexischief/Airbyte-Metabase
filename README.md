<<<<<<< HEAD
# Sync data with airbyte to metabase Documentation
### This documentation is about how to create airbyte container on docker to sync data from Google sheet to Postgres and link it to Metabase locally
=======
# Introduction
## Why I started this project.

Ever felt the frustration of continually uploading CSV data to your PostgreSQL database for analysis in personal projects? I certainly did. That repetitive process sparked a quest for an effortless solution—a quest that led me to delve into the world of data engineering.
Driven by the desire for real-time data updates without the hassle of manual uploads, I sought guidance from a formal colleague, Cliff who is a data engineer. I posed the question: "Is it possible to achieve live data updates in my database without paying a dollar?" And to my delight, Cliff introduced me to Airbyte, an open-source platform.
Airbyte promise a seamless synchronisation of data, ensuring that any changes in the source data would  reflect in my Postgres database.
That is how my maiden data engineering project was born, fuelled by the aspiration to break free from manual data uploads and embrace a dynamic, always-updated database landscape.
This documentation serves as a testament to that journey: a story of discovery, experimentation, and triumph in the realm of data engineering with Airbyte as the catalyst.
Join me as we unravel the power and simplicity of Airbyte a tool that empowers live data integration without breaking the bank.

# Deploy Airbyte on your local machine
## Setup and launch Airbyte on Mac
1. Install Docker Engine and the Docker Compose plugin on your workstation.You can check on youtube on how to Install docker on your local machine.
2. After Docker is installed, you can immediately get started locally by running:
```
### clone Airbyte from GitHub
git clone --depth=1 https://github.com/airbytehq/airbyte.git

### switch into Airbyte directory
cd airbyte

### start Airbyte
./run-ab-platform.sh
```
In your browser, visit http://localhost:8000

You will be asked for a username and password. By default, that's username airbyte and password password. Once you deploy Airbyte to your servers, be sure to change these in your .env file:
``` 
# Proxy Configuration
# Set to empty values, e.g. "" to disable basic auth
BASIC_AUTH_USERNAME=your_new_username_here
BASIC_AUTH_PASSWORD=your_new_password_here
```
## Setup and launch Airbyte on Windows
After installing the WSL 2 backend and Docker you should be able to run containers using Windows PowerShell. Additionally, as we note frequently, you will need docker-compose to build Airbyte from source. The suggested guide already installs docker-compose on Windows.
Install [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/) from here.

Make sure to select the options:

1. Enable Hyper-V Windows Features
2. Install required Windows components for WSL 2 when prompted. After installation, it will require to reboot your computer.
3. You're done!

```
git clone --depth=1 https://github.com/airbytehq/airbyte.git
cd airbyte
bash run-ab-platform.sh
```

In your browser, just visit http://localhost:8000
You will be asked for a username and password. By default, that's username airbyte and password password. Once you deploy airbyte to your servers, be sure to change these.
Start moving some data!

# Let's setup your first data source on Airbyte(Google sheet)
Before you create a new source on airbyte, you would have to create your google sheet API before.
### How to create google sheet API.
First, you need to setup your google platform account [here](https://console.cloud.google.com/) if you don't have one already.
Second, enable your google sheet API on your google cloud platform. How?
In the search bar, search for "Google sheet API" then click on enable.
Third, you can also enable your Google drive API using the same steps.
### Create your service account.
NB; The service account will generate the credentials that will use to access google sheet.
* On the cloud console, click on;
1. Credentials
2. Create credentials
3. Service account

#### Under service account details;
1. Service account name : Airbyte
2. Service account ID : Airbyte
* After that click on create and continue 
3. Select role and choose owner
Click continue and click done and you have your service account setup. Also don't forget service account email because you will need it again.

### Create your service account key
After setting up your service account, click on;
1. The email to create your key
2. Add key
3. Create new key
4. Choose JSON is the key type and click on create.
* Automatically your key will be downloaded so make sure you check your downloads because you will use that key for authentication on airbyte to source your data from goofle sheet.

### Share your google sheet data with your service account email
1. Copy your service email
2. Go to google sheet where your source data is located
3. On the top right corner, you will see share 
4. Click on share and paste your service account email. Make sure you give it an editor access.

### Now that you have your authentication key, lets go back to Airbyte to connect your *Google sheet*;
* On the Airbyte platform, click on connections 
* At the top right corner, click on new connection
* Set up new source
* Search for google sheet 
* For Source name, enter a name to help you identify this source; In this case, "Google sheet"
* Under authentication, click on the dropdown to select "Service account key authentication"
* Go to your downloads and open the key that you downloaded whilst creating your service account key, copy it and paste it under **service account information**
* For **Spreadsheet Link**, enter the link to the Google spreadsheet. To get the link, go to the Google spreadsheet you want to sync, click *Share* in the top right corner, and click **Copy Link**.
* (Optional) You may enable the option to Convert Column Names to SQL-Compliant Format. Enabling this option will allow the connector to convert column names to a standardized, SQL-friendly format. For example, a column name of Café Earnings 2022 will be converted to cafe_earnings_2022. We recommend enabling this option if your target destination is SQL-based (ie Postgres, MySQL). Set to false by default
* Click Set up source and wait for the tests to complete.

## Create a Postgres instance on docker 
You need to create a Postgres instance on docker like we did for Airbyte as your destination to store data sync from Airbyte.
Run this command on docker to create your airbyte-Postgres container on docker;
```
docker run --rm --name airbyte-postgres -e POSTGRES_PASSWORD=password -p 3000:5432 -d postgres
```
Your airbyte-postgres should be up and running on docker.
Now go back to Airbyte to connect your destination to the source with the following details.

* In the left navigation bar, click Destinations. In the top-right corner, click new destination.
* On the Set up the destination page, enter the name for the Postgres connector and select Postgres from the Destination type dropdown.
* Enter a name for your source.
##### For the Host, Port, and DB Name, enter the hostname, port number, and name for your Postgres database.
* List the Default Schemas.
##### The schema names are case sensitive. The 'public' schema is set by default. Multiple schemas may be used at one time. No schemas set explicitly - will sync all of existing.
```
DATABASE_USER=postgres
DATABASE_PASSWORD=password
DATABASE_HOST=host.docker.internal # refers to localhost of host
DATABASE_PORT=3000
DATABASE_DB=postgres
```



