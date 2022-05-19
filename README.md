# Bazar Store



## Overview

We are required to create and redesign the ***Bazar online store*** we built in Part 1 to manage a heavier workload in this section of the project.
## System Design and Replication
- - -
As indicated in the figure below, we created two replicas of the ***catalog server***, two replicas of the ***order server***, one replica of the ***front-end server***, and one replica of the ***cache server (memcached server)***

![alt text](https://i.ibb.co/m41vpNG/bazar-store.png "Title")

every replica is running in a diffrent docker container and communicate with each other using REST API calls.

#####Note : Docker images is uploaded to Docker Hub and we can access them using the following links : 

[Catalog Primary Server](https://hub.docker.com/repository/docker/95367920/bazar-catalog-primary-server )

[Catalog Normal Server](https://hub.docker.com/repository/docker/95367920/bazar-catalog-normal-server)

[Order Server](https://hub.docker.com/repository/docker/95367920/bazar-order-server)

[Front-end Server](https://hub.docker.com/repository/docker/95367920/bazar-front-end-server)

and we can download ***memcached server*** docker image using the following link :

[Memcached Server ](https://hub.docker.com/_/memcached)


## How to Run
- - -
1. **Catalog Primary Server:**
> sudo docker run --rm --name  catalog-instance1 --network bazar-store-network -d -it 95367920/bazar-catalog-primary-server

2. **Catalog Normal Server:**
> sudo docker run --rm --name  catalog-instance2 --network bazar-store-network -d -it 95367920/bazar-catalog-normal-server

3. **Order server**:
> sudo docker run --rm --name  order-instance1 --network bazar-store-network -d -it 95367920/bazar-order-server

4. **Front-end Server:**
> sudo docker run --publish 4200:4200 --rm --name  fcache --network bazar-store-network -d -it 95367920/bazar-front-end-server

5. **Memcached server:**
> sudo docker run --publish 11211:11211 --network bazar-store-network --name my-memcache memcached


## Consistancy 
- - -
We use an internal protocol called ***(Remote-Write Protocol)*** to ensure consistency across replicas, and we treated catalog-instance1 as a primary server and other replicas like catalog-instance2 as normal servers, so any write requests that come to catalog-instance2 are forwarded to the primary server, and the primary server tells other replicas to update their data, but if the write request comes to catalog-instance1 which is the primary server, it is updated

## Cache Consistancy
---
We use the **Push Approach** to ensure cache consistency: whenever a write request is made, we check if the value we wrote in is cached or not, and if it is, we send an invalidate request to the Memcached server to remove it from the cache.


## Load Balancing

We used the Round Robin approach for load balancing in the front-end server, and because all the servers have the same capabilities, we balanced the requests to 50% for each server.



