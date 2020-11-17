
Slides:

https://docs.google.com/presentation/d/1ckFkQyszXuHJixhn8RP_PI8Lzoj7FK1lyKtsB7pPPYI/edit#slide=id.g74c57ff882_0_0




Instructions
============

Iteration 1
===========
- Using the knowledge acquired in previous stage, start creating a new listening lambda by doing

```
serverless create --template aws-nodejs --path listening_lambda --name listening_lambda

```
####Step 1
- Listening Lambda
 - Modify your lambda descriptor **serverless.yml** to be able to react to a published event (Define the event yourself).
 - Modify your `handler.js` function and make sure to simply **log** something. We'll need this to check if the event was received.
 - Deploy your newly created lambda.
 
 How are you going to test it works (manually)?
 
 
####Step 2
- Publish Lambda
 - Create another lambda called `publish_lambda` and prepare it for modification.
 - Modify your lambda descriptior *serverless.yml* to be able to react to a `get` http event.
 - Don't forget to include iamRoleStatements to allow this function to put events
 - Modify your `handler.js` function to be able to publish an event of the type described in the **Listening Lambda**.
 - Deploy your modified lambda.


In order to check if both sides are properly working, you need to either trigger a local invocation of your **Publish Lambda** or remotely invoke it via `http get` invokation.

Expected result:
Go to your each of your function definitions in AWS console, **Monitoring** Option and take a look at the logs, you should see the logs created on each `handler.js` file

---


Iteration 2
===========

Now, we need you to make your **Publish Lambda** to react to a `POST` event instead of `GET`. This will act as a **post** (as an entity) creation.

In order to achieve this, you have to:

- Modify your `serverless.yml` file and configure post http event
- Modify your `handler.js` to be able to handle data sent to your function and forward it as part of the event
- You can test locally your function, to check if it reacts to the post event, if you can read the data posted and if the data is forwarded as part of the event
- Deploy your lambda and test it again. As this is a `POST` event, you will need to pass data. We recommend using a API testing tool like POSTMAN

After having done that, modify your **Listening Lambda** to be able to read data from the event
- Modify your `handler.js` to be able to extract the data from the event and log it

Verify, as in Iteration 1, via Cloudwatch

---

Iteration 3
===========

In this stage we need you to modify the event to have all the required data that a **post** entity should have in order to persist a **post**

- Modify the data you're passing in order to have the required data `dateTime`, `postId`, `text` and `userId`
- Enable your `handler.js` function to access the database created in **Get From Database** exercise
- Modify your function so it can create a new **post** from the data received (HINT: Use DynamoDB DocumentClient and update expression)
- Verify that persistence is happening by checking logs and DynamoDB table

Example of a **post**

```

{
   "dateTime": 1549312452000,
   "postId": 102030,
   "text": "Fake post",
   "userId": 1234
}

```

---
# HRLW

Iteration 1 
===========

In this stage, we'll start the development of a second function that will take care of materializing the "timeline" of a user, whenever a post creation happens. In order to simplify the exercise let's assume that a user's posts list will be under `posts` table, whereas timeline will be a collection of `posts` from multiple users, which corresponds with the users a user follows. The list of followers will be in another table. 
The suggested logic includes: 
- User **A** creates a post
- Query the list of users following user **A**
- As user **A** updated his posts list, the users following user **A** should have their timeline updated
- Timeline update will happen simply as adding the same post to each of the followers of **A**


In order to achieve this, let's start with:
- Rename current function into something more meaningful
- Refactor code if necessary
- Create new function that will react to event
- Create database tables for handling followers and timeline
- Enable your newly created function to access these new tables
- Create the logic for getting the list of followers a user has
- Add logic to add post to each user's timeline
- Refactor code if necessary



# Do send events manually

## DoD:
 I can throw an event and listen to it

## Tips:

- Hello world command:
    sls create --template aws-nodejs --path myService



- you need a template for the json
    aws events put-events --generate-cli-skeleton          

- you need to send 
    aws events put-events --entries file://firstEvent.json 


# Lambda does receive events

Mind the Source 
Wire it in the serverless.yaml

# Lambda sends events 

you can call the lambda locally 

    serverless invoke local -f publish

# You can pass data to your local invokation

    serverless invoke local -f publish -d "testData.json"
Where `testData.json` can have the following shape

``` 
{
  "body": {
    "dateTime": 1549312452000,
    "postId": 102030,
    "text": "Fake post done via CLI while backend is under construction"
  }
}

```
# Event data comes inside ´detail´

# Use DynamoDB.DocumentClient

```
var params = {
    TableName: 'posts',
    Key: {
      userId: "1234"
    },
    UpdateExpression: "SET #posts  = list_append(#posts, :post)",
    ExpressionAttributeNames: { "#posts" : "posts"},
    ExpressionAttributeValues: {":post": [event.detail]},
    ReturnValues : "UPDATED_NEW"

  }
...
...
docClient.update(params, function(err, data) { ... } );

```


