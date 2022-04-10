# dev-swarm

![image](https://user-images.githubusercontent.com/59050392/162619721-118dd81b-efa4-4d6a-b9fc-d122d72b82f0.png)

## Swarm project for Test usage. 
### This project implemente a real world scenario of front & back end application which take an users reactions from the front-end and move them to REDIS key/value database, Then move it to .NET worker which's the convertor from REDIS and store the results on postgreSQL

#Services (names below should be service names)
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
  * 1 replica NOTE VIDEO SAYS TWO BUT ONLY ONE NEEDED

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
