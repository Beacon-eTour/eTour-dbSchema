# eTour-dbSchema

eTour schema template. Use this as an example and create your own schema. Template includes three tour templates, which has multiple beaconinfos per tour. Template schema comntains also localication examples.

## Overview

eTour schema can be created in Google Firestore NoSQL database in native mode. Localizations are separated from tour, so these are separate documents in schema.
Mobile application creates a dynamic webpage. Webpage uses a template and tour information is shown on the created webpage. Tour related pictures are stored in to a public Google Cloud Platform (GCP) bucket and URL of picture(s) is stored in the database. Also URLs of YouTube-videos are stored in the DB. If cache is expired, then data is fetched from database to the webpage. Each beaconinfo in schema contains text and media for the tour stop.

## Firestore
To interact with the database locally, see Google’s documentation on how to setup authentication as a service account, in order to access the Firestore DB in GCP. Note that you have to configure this to develop the application.

Link to Google service account creation guide: https://cloud.google.com/docs/authentication/production#automatically. 

##	Firestore-export-import tool
This tool imports/exports the whole database as a one JSON file. Exporting backups and importing new content to Firestore can be executed with firestore-export-import tool. Install firestore-export-import, configure the tool and connect to Firestore via terminal. Schema should be in JSON file format when importing it to Firestore.

### Installation and configuration procedure
 
 Install node package manager ```$ npm install -g npx```. Then install firestore tool ```$ npm install firestore-export-import```.
 
* Modify: ```firestore-export-import/dist/index.js```. Set databaseURL (and serviceAccount).
* Create ```credentials.json```. This file is the private key, e.g., serviceAccount.json, which can be generated in Firestore.

### Export

Export the whole database ass json ```$ npx -p node-firestore-import-export firestore-export - a credentials.json -b backup.json```.
 
### Import

Import database as json file ```$ npx -p node-firestore-import-export firestore-import - a credentials.json -b backup.json```.
 
### Example file

Short example JSON TEMPLATE for import:
```json
{
"__collections__": {
	"testcollection": { 
		"testdocument": {
			"test_field": "Testing", 
				"__collections__": {}
			} 
		}
	}
}
```


Importing new collection does not replace old collections. But importing collection with the same name will erase the existing collection, all the documents and fields!

Sublime Text editor is a good tool for handling JSON. JSON formatting or prettyprinting can be done with Sublime plugin “Pretty JSON”, so it’s easier to edit the schema as a JSON format.

##	Custom admin interface tool – for potentially easier modification

There is also an alternative for the Firebase-import-export-tool, maybe more sophisticated way for administering the DB. This allows you to look at each document in a separate json file allowing a potentially easier modification. 

This tailored tool uses the same Firestore admin APIs, and can be found here along with the documentation & guide: 
https://github.com/imatra/iTour-dbAdmin

## DB and Cache keys
Currently, when making changes to the DB, you must also reset cache keys to ensure that the applications get fresh data (OR wait for the cache keys to expire), instead of the stale data that is not yet updated in the cache. ForceUpdate – Cloud Function can be accessed from here: https://europe-west3-itour-308011.cloudfunctions.net/forceUpdate (by accessing the URL, the function runs and resets all cache keys). 

The TTL-value is (as now) set to 8 hours (a typical workday) and can be modified in the NextJS repository under /lib/constants.ts. The lower you set the value, more DB reads there will be done against the DB, and higher the value, the more often you will need to use the Cloud Function endpoint to reset all the keys. In future, if/when an admin UI would exist, you should update the cache keys when updating/deleting the data from DB.

