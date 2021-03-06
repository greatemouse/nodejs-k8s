# DevOps Exercise

## The Users Service

This github repository is a `node.js` app of a very small "users" service.
Below are the instructions of how to build, run and use this service.

### Prerequisites
nodejs 8, 10, 12

#### Mongo DB
The users service works with a mongo DB to store it's users.<BR>
**Database name**: devops-exercise<BR>
**Collection name**: users

### Build

   `npm install`

#### Environment Variables

* MONGO_URI - uri of the mongo DB

### Run
   
   `npm run start`

### Use

* http://localhost:3000/users
    * GET - lists the list of existing users
        * Response:
        ```javascript
        [
            {
                "_id": "5c20ca1d2cdc846b4de1f6ab",
                "name": "u1",
                "date": 1545652765281
            },
            {
                "_id": "5c20ca81c23ea46b5089884b",
                "name": "u2",
                "date": 1545652865843
            }
        ]
        ```
    * POST - add a new user
        * Body: new user name
        ```javascript
        {
            "username":"<username>"
        }
        ```
        * Response: created user, including ID, and created date:
        ```javascript
        {
            "name": "<name>",
            "date": 1545657494671,
            "_id": "5c20dc96e4f6066bc12ab11e"
        }
        ```
* http://localhost:3000/health - endpoint to report the service health
    * GET - report on health of the service
        * Response:
            * In case all OK:
            **Status**:200
            **Headers**: System-Health:true
            * If error occurs:
            **Status**:200
            **Headers**: System-Health:false
            **Body**: Information about the error in json:
            ```javascript
            {
                "status": "DB Down"
            }
            ```

## The Exercise

Your goal is simple - build a full build, deploy and monitor pipeline.

We can look at the pipline as consisting of three stages:

* Continuous Integration
* Continuous Deployment
* Continuous Monitoring(optional)

### CI

Any change in the repository, that is pushed, is automatically built as a docker container and published to a docker registry.

### CD

Latest docker image deployed to a container platform and available to use.

### CM

Add continuous monitoring to the service, that shows the current status, and sends alerts when the service is not functioning.
Use also the `/health` endpoint, in addition to other metrics, and logs.

## What Should You Do?
The exercise is focused on the `CI`. Please create a full CI process as defined above.

**Notice:** The outcome of the exercise must include:
* Configuration \ script files (as part of the repository) - MUST

and should include some of the following:
* CI tool that we can access and see the pipeline
* Access (or snapshot) to the docker registry that is updated per each commit
* Permissions to the repository, where we can commit changes, and see that the pipeline was triggered and new docker was uploaded to the registry
* url\ip of the deployed service - so we can check it over http


## Remarks

### Technologies

* Use whichever CI/CD tool you want.
* Use GCR (Google Container Registry) as the docker registry and GKE (Google Kubernetes Engine) as the docker platform.
* Work on local github\gitlab repository.
* * **Note:** Make sure the solution is not opened to the public, but only to you and us.
* Consider using managed services. For mongo you may use mongo atlas - https://www.mongodb.com/cloud/atlas.

### Notices

* For the sake of the exercise, `/health` endpoint randomly returns that the health is false.
* For simplicity, the service logs all the requests to the console.

**GOOD LUCK!**

****NOTES****
$postParams = @{username='user01'}
Invoke-WebRequest -Uri http://40.122.205.190:3000/users -Method POST -Body $postParams

curl -i -X POST -H 'Content-Type: application/json' -d '{"username":"user02"}' http://40.122.205.190:3000/users

docker build -t exampleacr01/devops-exercise-image .
docker tag exampleacr01/devops-exercise-image exampleacr01.azurecr.io/samples/nodejs
docker push exampleacr01.azurecr.io/samples/nodejs
docker pull exampleacr01.azurecr.io/samples/nodejs
docker run --name devops-exercise -p 3000:3000 -d exampleacr01.azurecr.io/samples/nodejs

kubectl apply -f ./devops-exercise.yaml
kubectl expose deployment devops-exercise --type=LoadBalancer --port=3000
____________________________________________
#!/bin/bash

echo "------> Create Docker Image <------"
docker build -t exampleacr01/devops-exercise-image . \ 

echo "------> Tag and Push image to GCR <------" \ 
&& docker tag exampleacr01/devops-exercise-image gcr.io/stastest/devops-exercise-image:tag01 \ 
&& docker push gcr.io/stastest/devops-exercise-image:tag01 \ 

echo "------> Deploy image to GKE <------" \ 
&& kubectl delete deployment devops-exercise-gke && kubectl delete svc devops-exercise-svc \ 
&& kubectl apply -f ./devops-exercise.yaml
_____________________________________________
