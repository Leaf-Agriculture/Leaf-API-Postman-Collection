# How to query & access operations and satellite images via Leaf
## This is Leaf's onboarding Postman Collection


1. Import Postman Collection
1. Import Environment Variables
1. Register with Leaf 

The included Postman Collection contains the following folders:  
- Leaf Auth
- Climate Field View Auth
- John Deere Auth
- Leaf User
- Query Operations by Field
- Get Operation Files
- Merge Operation Files
- Satellite

This document is divided into topics with the exact same structure as the folders in the collection.

---

## Leaf Auth
### Get Leaf Token
After registering with Leaf, you can get your Leaf token, which must be included in all requests to Leaf’s API as an authorization header.

How to get your Leaf Yoken:

- Update current value of "leaf_email" variable to your Leaf account email
- Update current value of "leaf_pass" variable to your Leaf account password

Hit "Send"

An "id_token" should be returned. It will be automatically transferred to the current value of "leaf_token" to be used in all calls to Leaf's API

---

## Climate Field View Auth

To get a better sense of the authentication process with Climate Field View and the results, check our Medium Post 

### 1. Get CFV auth URL
We will be generating a url to redirect your user to authenticate with Climate.

- Update environment variables "cfv_client_id" and "cfv_client_secret" to your CFV developer account credentials
- Update environment variable "cfv_redirect_url" to your application's backend

Redirect your user to the script's output url.  
They will authenticate and be redirected to the redirect_uri.  
A code will be sent to that cfv_redirect_url. We will need this code Step 2.  
Copy the code value.  

Note: this code expires after 1 minute.

Paste code value to environment variable "cfv_code"

### 2. Get CFV tokens
Hit "Send"

A lot of information will be returned. The important ones are "access_token" and "refresh_token. These will be automatically transferred to "cfv_access_token" and "cfv_refresh_token" environment variables and will be used in the next step.
	
### 3. Add CFV credentials
Now we can create a Developer-User pair credentials ID that will allow you to access your user’s Climate data. We first add the Climate credentials to Leaf API, to do that:
Hit "Send"

An "id" for the credentials you just created will be returned. This "id" will be automatically transferred to the value of "cfv_credentials_id" to be used in the next step.

---

## John Deere Auth. 
To get a better sense of the authentication process with John Deere and the results, check our Medium Post 

### 1. Get John Deere auth URL. 
In Step 1 we will be generating tokens from John Deere. The goal In step 2 is we will get our john deere token_id and token_secret

Token Verifier  

In step 1 we will get a temporary "token verifier" from John Deere that confirms a user’s authentication of your application to access their John Deere data and generate credentials. We get that verifier by going through their authentication flow (oauth2).
Before generating the authentication URL, please:  

- Update current value of "jd_client_key" to your app's client key on John Deere
- Update current value of "jd_client_secret" to your app's client secret on John Deere
- Update current value of "jd_callback_url" to your app's callback_url on John Deere

Then, to generate the authentication URL your application will send to your user so they can authorize access to their account files you can use the included step 2 in the Postman Collection. Change "client_key" and "client_secret" variables to yours received from John Deere when you created an app on your developer account with them and redirect_uri to a uri the "token verifier" will be sent after the user authorizes your application.
Hit "Send".

Redirect your user to the url included in the response.

They will authenticate and be redirected to the redirect_url

Copy the entire url you were redirected to. It looks like:

`https://leafagriculture.com.br/?oauth_token=TOKEN&oauth_verifier=CODE`

Paste it in the environment variable "jd_response_url"

### 2. Get John Deere Tokens

After updating the “jd_response_url” in Postman you can submit your credentials and receive your tokens:

Hit "Send"

"token_id" and "token_secret_key" will be automatically transferred to "jd_token_id" and "jd_token_secret" environment variables and will be used in the next step.

### 3. Add John Deere ID

Now we can create a Developer-User pair credentials ID that will allow you to access your user’s John Deere data. We first add the John Deere credentials to Leaf API, just:
Hit "Send"

All the info needed has already been filled automatically in Step 2.

An "id" for the credentials you just created will be returned. This "id" will be automatically transferred to the value of "jd_credentials_id" to be used in the next step.
	
---

## Leaf User 	

### Create Leaf User

Now we have to attach credentials to a Leaf User. To do so, we can create a Leaf user and attach our John Deere and/or Climate Field View credentials via the credentials id. We can also attach credentials from other companies to this same user to query all available data by Grower/Farm/Field regardless of brand. 

Update (optional) fields "address", "email", "name" and "phone" with your user's information.

We have automatically included our “jd_credentials_id” to this call and attached it to this user.

Hit "Send" 

Along with other information returned, there is an "id". This "id" is the leaf_user_id that will be used in the next (final) Step to query and access files.

### Update Leaf User

To update a Leaf User, let's say to add another provider credentials or change the user's address, we can use this PUT request. Since this method overwrites, remember to send all the user's information along with the information you want to add or update. For example, if you want to add John Deere credentials to a user that already has ClimateFieldView credentials, remember to specify both credentials ids.

### Get specific Leaf User

With this endpoint you can query all information on a specific Leaf User, such as their address, email, credentials, etc. To do so, update the value of the environment variable "leaf_user_id" to the Leaf User id you want to query.

### Get all Leaf Users

With this endpoint you can query all information on all your Leaf Users, such as their address, email, credentials, etc. Just hit 'send'.

---

## Query Operations by Field

To query all operations that happened in a specific field (step 2), we first need to create that field (step 1). Then, Leaf will automatically detect operations of that field based on the operations' and on the field's coordinates. This process usually takes about 30 minutes.

### 1. Create Field

Here we need to specify a leafUserId (that will be the Leaf User owner of that field), a externalId (that will be the name we give to the field) and the geojson geometry of the field (location). After creating the field we can query it (step 2) 

### 2. Get operations' ids by Field

To query all operations that happened in a specific field, just update the environment variable "field_external_id" to the field id you want to query files

---

## Get Operation Files

### 1. Query & access all files

With this endpoint you can query all files of all your users (or you can filter by user as well, or by provider, by operation_date and much more). Select and update which params (filters) you want to apply before querying files and just hit "send"

### 2. Query & access specific file

To query for a specific operation file, you just have to update the value of the environment variable "file_id" and hit "send"

---

## Merge Operation Files

### 1. Merge files

Merging files with Leaf is a very simple process. You just have to list, in the request json body, the ids of the files you want to merge. Make sure the operations are of the same type ("APPLIED" or "HARVESTED" or "PLANTED"), so the results are consistent.

After that, just hit "send" and an id for that merged file will be returned. You can query that file as any other. It will be listed when you query for all files and can also be queried specifically by its id

### 2. Query & access specific file
You can query a merged file as any other. It will be listed when you query for all files and can also be queried specifically by its id. So this request is the same as seen on "Get Operation Files". You just have to update the environment variable "file_id" to the id of the merged file.

Keep in mind that merging files is processing-heavy and will take about 20 minutes to finish.

---

## Satellite

### Create satellite monitoring

Creates a new field entry in the database. Whenever a field is added it starts being monitored. It will be continuously monitored for as long as it is in our database. 

note: If you want to stop this process, you can remove the field from the database entirely by using a DELETE HTTP request. Keep in mind that all the images will be lost. 

Payload: The payload of this object should have the following entries: 

- externalId: external ID to be used when searching for this field 
- geometry: a MultiPolygon GeoJSON object with the geometry of the field 

### Get specific satellite monitoring

Fetches a field entry based on its external id. It returns a single JSON object with the following entries:
- "externalId": ID used in registration
- "geometry": MultiPolygon GeoJSON object

### Get all satellite monitorings

This endpoint is used to fetch paged results of all the registered fields by the current logged in user. It returns a list of JSON objects with the following information on each item:
- externalId: external ID used in the field's registration
- geometry: a MultiPolygon GeoJSON object with the geometry of the field

### Get specific sat monitoring process

Fetches just the processes of a monitored field based on its external id. It returns a single JSON object. More details [here](https://dev.agrigate.io/#get-fields-external_id-processes)

### Edit a sat monitored geometry

Endpoint used to update the geometry of the field. You cannot update the external id. The payload is a single JSON object with an entry geometry which contains a MultiPolygon GeoJSON object. The new geometry must respect the area and perimeter limits of POST /fields.

### Delete satellite monitoring

Deletes the field from our database.

Be careful when using this. It will delete all the processed images from the database as well.
