# sunbird-user-creation
creation of user and assign roles after installing sunbird

# Steps After completion:-
1> Copy user creation script to cassandra and change vars under variable section
2> Validate the vars and run the user creation script

########################################################
## Steps to Validate
### Validation Before Running the script:

 1> Make curl calls with es Host
 curl -s ES_HOST:9200/_cat/indices?v

 2> Check Analytic API health
 curl -s AnalyticsAPI:9000/health | jq '.'

 3> check the api key
 curl -X POST \
 DOMAIN_NAME/api/channel/v1/list \
 -H 'Authorization: Bearer API_KEY' \
 -H 'Content-Type: application/json' \
 -H 'Postman-Token: 9e12519e-be8c-43f5-9eb8-29560a83c2b4' \
 -H 'cache-control: no-cache' \
 -d '{
 "request": {}
 }'
 
 4> Run the user creation script

##################################################

3> After Running the script we will get channelID and userID as mentioned Below

Channel ID is "0126967773240442880"
UserID: "230297f4-0753-4cb9-8584-caa5b75e57ff"

### IF You are getting null value in 'UserID' then the user creation failed because of invalid email or phone number so we can run only user creation command by changing 'DOMAIN', 'API_KEY' and 'CHANNEL_NAME' with 'DOMAIN' and change the user details in json data

curl -X POST https://DOMAIN/api/user/v1/create                  -H 'Cache-Control: no-cache'                  -H 'Content-Type: application/json'                  -H 'accept: application/json'                  -H 'authorization: Bearer API_KEY'                  -d '{
                         "request": {
                             "firstName":"admins",
                             "lastName":"admins",
                             "userName":"admins",
                             "password":"admins",
                             "email":"test@mydomain.com",
                             "phone":"8090809080",
                             "channel": "CHANNEL_NAME",
                             "emailVerified": true,
                             "phoneVerified": true
                         }
                     }'
Preserve the UserID which you obtained from the json

### Generate a temporary X-AUTH-TOKEN which need to be used while Assigning Roles

curl -s -X POST  https://DOMAIN_NAME/auth/realms/sunbird/protocol/openid-connect/token   -H 'cache-control: no-cache'   -H 'content-type: application/x-www-form-urlencoded'   -H 'postman-token: 15d3852b-de08-ea78-3342-bfaf5d4d4f09'  --data  "client_id=admin-cli&username=admin&password=admin&grant_type=password" | jq -r '.access_token'


# Assign the Permissions for the user which you created, change DOMAIN_NAME,API_KEY,X-AUTH-TOKEN(which was obtained by above command), USER_ID,ORG_ID

curl -X POST \
  https://DOMAIN_NAME/api/user/v1/role/assign \
  -H 'Authorization: Bearer API_KEY' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 9c0f9079-7d1c-4080-936b-e27f18c70576' \
  -H 'X-Consumer-ID: X-Consumer-ID' \
  -H 'X-msgid: 8e27cbf5-e299-43b0-bca7-8347f7e5abcf' \
  -H 'cache-control: no-cache' \
  -H "x-authenticated-user-token: X-AUTH-TOKEN" \
  -d '{
    "request": {
        "userId": "USER_ID",
        "organisationId": "ORG_ID",
        "roles": [
            "CONTENT_CREATOR",
            "CONTENT_REVIEWER",
            "CONTENT_CURATION",
            "FLAG_REVIEWER",
            "BOOK_CREATOR"
        ]
    }
}'

#################
# Create another user for Reviewer Purpose, change DOMAIN,CHANNEL,API_KEY, change the user details in json data

curl -X POST https://DOMAIN/api/user/v1/create                  -H 'Cache-Control: no-cache'                  -H 'Content-Type: application/json'                  -H 'accept: application/json'                  -H 'authorization: Bearer API_KEY' \
                 -d '{
                         "request": {
                             "firstName":"reviewer",
                             "lastName":"reviewer",
                             "userName":"reviewer",
                             "password":"reviewer",
                             "email":"reviewer@master.com",
                             "phone":"8095477777",
                             "channel": "CHANNEL_NAME",
                             "emailVerified": true,
                             "phoneVerified": true
                         }
                     }'


### Generate a temporary X-AUTH-TOKEN which need to be used while Assigning Roles

curl -s -X POST  https://DOMAIN_NAME/auth/realms/sunbird/protocol/openid-connect/token   -H 'cache-control: no-cache'   -H 'content-type: application/x-www-form-urlencoded'   -H 'postman-token: 15d3852b-de08-ea78-3342-bfaf5d4d4f09'  --data  "client_id=admin-cli&username=admin&password=admin&grant_type=password" | jq -r '.access_token'


# Assign the Permissions for the user which you created, change DOMAIN_NAME,API_KEY,X-AUTH-TOKEN(which was obtained by above command), USER_ID,ORG_ID

curl -X POST \
  https://DOMAIN_NAME/api/user/v1/role/assign \
  -H 'Authorization: Bearer API_KEY' \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: 9c0f9079-7d1c-4080-936b-e27f18c70576' \
  -H 'X-Consumer-ID: X-Consumer-ID' \
  -H 'X-msgid: 8e27cbf5-e299-43b0-bca7-8347f7e5abcf' \
  -H 'cache-control: no-cache' \
  -H "x-authenticated-user-token: X-AUTH-TOKEN" \
  -d '{
    "request": {
        "userId": "USER_ID",
        "organisationId": "ORG_ID",
        "roles": [
            "CONTENT_CREATOR",
            "CONTENT_REVIEWER",
            "CONTENT_CURATION",
            "FLAG_REVIEWER",
            "BOOK_CREATOR"
        ]
    }
}'


