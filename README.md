
Slides:

https://docs.google.com/presentation/d/1ckFkQyszXuHJixhn8RP_PI8Lzoj7FK1lyKtsB7pPPYI/edit#slide=id.g74c57ff882_0_0


### Goals structure:
- 10 minutes describing the goal and giving some tips
- 30 minutes of trainees attempting to solve it
- 10 minutes of sharing the solution
- 10 minutes rest

#### Goal 1: trigger a lambda to log something by posting an event via aws-cli. 
- Listening Lambda
 - Modify your lambda descriptor **serverless.yml** to be able to react to a published event (Define the event yourself).
 - Modify your `handler.js` function and make sure to simply **log** something. We'll need this to check if the event was received.
 - Deploy your newly created lambda.
 
 How are you going to test it works (manually)?
 
 
#### Goal 2: trigger a lambda via GET message that sends an envent that triggers goal 1 lambda.
- Publish Lambda
 - Create another lambda called `publish_lambda` and prepare it for modification.
 - Modify your lambda descriptior *serverless.yml* to be able to react to a `get` http event.
 - Don't forget to include iamRoleStatements to allow this function to put events
 - Modify your `handler.js` function to be able to publish an event of the type described in the **Listening Lambda**.
 - Deploy your modified lambda.


In order to check if both sides are properly working, you need to either trigger a local invocation of your **Publish Lambda** or remotely invoke it via `http post` invokation.

Expected result:
Go to your each of your function definitions in AWS console, **Monitoring** Option and take a look at the logs, you should see the logs created on each `handler.js` file


#### Goal 3: change the goal 2 lambda to receive post messages and pipe the post payload throught the event bus so it gets logged the listening lambda
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

#### Goal 4: have the listening lambda to store the post info in the database

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


# Help:

- Events get published but not received:
    check the amazon region where they are published

- Hello world command:
    sls create --template aws-nodejs --path myService



- you need a template for the json
    aws events put-events --generate-cli-skeleton          

- you need to send 
    aws events put-events --entries file://firstEvent.json 

- you can call the lambda locally 

    serverless invoke local -f publish

- You can pass data to your local invokation

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
- Event data comes inside ´detail´

- Use DynamoDB.DocumentClient

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


