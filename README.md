# dev-swarm

![image](https://user-images.githubusercontent.com/59050392/162619721-118dd81b-efa4-4d6a-b9fc-d122d72b82f0.png)

## Swarm project for Test usage. 
### This project implemente a real world scenario of front & back end application which take an users reactions from the front-end and move them to REDIS key/value database, Then move it to .NET worker which's the convertor from REDIS and store the results on postgreSQL after the data is stored on postgreSQL the results to the user are sent to Node.js for read only by the client. 

# Services (names below should be service names)

* vote

  * bretfisher/examplevotingapp_vote
  * web frontend for users to vote dog/cat
  * ideally published on TCP 80. Container listens on 80
  * on frontend network
  * 2+ replicas of this container

* redis

  * redis:3.2
  * key-value storage for incoming votes  
  * no public ports
  * on frontend network
  * 1 replica 

* worker

  * bretfisher/examplevotingapp_worker
  * backend processor of redis and storing results in postgres
  * no public ports
  * on frontend and backend networks
  * 1 replica

* db

  * postgres:9.4
  * one named volume needed, pointing to /var/lib/postgresql/data
  * on backend network
  * 1 replica
  * remember set env for password-less connections -e POSTGRES_HOST_AUTH_METHOD=trust


* result

  * bretfisher/examplevotingapp_result
  * web app that shows results
  * runs on high port since just for admins (lets imagine)
  * so run on a high port of your choosing (I choose 5001), container listens on 80
  * on backend network
  * 1 replica



# Swarm Commnd for deploying this cluser. 

###  docker networks - create the front & the back end networks for communicate beetwen the containers. 

 $ docker network create -d overlay backend
 $ docker network create -d overlay frontend 

### "vote" service on the cluster.

 $ docker service create --name vote -p 80:80 --network frontend --replica 2 bretfisher/examplevotingapp_vote
 
### "redis" service on the cluster.

 $ docker service create --name redis --network frontend  redis:3.2
 
### "worker" service on the cluster. 
 
 $ docker service create --name worker --network frontend --network backend bretfisher/examplevotingapp_worker

### "db" service on the cluster.
 
 $ docker service create --name db --network backend --mount type=volume,source=db-data,target=/var/lib/postgresql/data postgres:9.4
 
### "result" service on the cluster.
 
 $ docker service create --name result --network backend -p 5001:80 bretfisher/examplevotingapp_result
