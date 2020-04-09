
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
