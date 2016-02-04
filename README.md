snap and riemann and riemann dashboard, oh my!
-----------------------------

# Images used
1. **travix/riemann-dash:latest**

        The riemann dashboard ( linked to riemann server via the name riemann ) 
        
1. **travix/riemann-server:latest**

        The riemann server 
        
1. **edyesed/intelsdi-snap:v0.11.0-beta**

        This runs [snap](https://github.com/intelsdi-x/snap)
        
1. **__localbuild__/snap-configurator**

        An image running alpine linux with curl, so that you can configure snap

# How to use it
## Get the docker machines up and going
1. `docker-compose up`
1. `docker ps`, you should see something like:

        CONTAINER ID        IMAGE                                COMMAND                  CREATED             STATUS              PORTS                                                        NAMES
        4e48f283e045        snapriemann_snap-configurator        "/bin/sh -c 'while tr"   2 minutes ago       Up 2 minutes                                                                     snapriemann_snap-configurator_1
        fb0d1860def8        edyesed/intelsdi-snap:v0.11.0-beta   "/opt/snap/snap/snap-"   2 minutes ago       Up 2 minutes        0.0.0.0:8181->8181/tcp                                       snapriemann_snap_1
        ffe42a0b84fa        travix/riemann-dash:latest           "/bin/sh -c 'riemann-"   55 minutes ago      Up 2 minutes        0.0.0.0:4567->4567/tcp                                       snapriemann_riemann-dash_1
        2859cde1fd61        travix/riemann-server:latest         "/bin/sh -c '/usr/bin"   56 minutes ago      Up 2 minutes        0.0.0.0:5555-5556->5555-5556/tcp, 0.0.0.0:16384->16384/tcp   snapriemann_riemann-server_1

## Configure a task in snap      
1. `docker exec -it /bin/sh`  on your snap-configurator. This will make a task, but not start it.

        docker exec -it 4e48f283e045 /bin/sh
        / # cd /opt/
        /opt # curl -XPOST http://snap-server:8181/v1/tasks -d@riemann-and-file.json
        {
            "meta": {
              "code": 201,
              "message": "Scheduled task created (977ff072-6670-4a74-acf3-5168514df28e)",
              "type": "scheduled_task_created",
              "version": 1
            },
        ...
        }/opt #


## Start up that task
1. `docker exec -it /bin/bash` on your snap docker image. Yes, you could have done this from the snap-configurator, but you want to learn. Notice how this task goes from **Stopped** to **Running**

        docker exec -it fb0d1860def8 /bin/bash
        root@fb0d1860def8:/# cd /opt/snap/snap/snap-v0.11.0-beta/bin/
        root@fb0d1860def8:/opt/snap/snap/snap-v0.11.0-beta/bin# ./snapctl task list
        ID 					 NAME 						 STATE 		 HIT 	 MISS 	 FAIL 	 CREATED 		 LAST FAILURE
        977ff072-6670-4a74-acf3-5168514df28e 	 Task-977ff072-6670-4a74-acf3-5168514df28e 	 Stopped 	 0 	 0 	 0 	 5:52AM 2-04-2016
        root@fb0d1860def8:/opt/snap/snap/snap-v0.11.0-beta/bin# ./snapctl task start 977ff072-6670-4a74-acf3-5168514df28e
        Task started:
        ID: 977ff072-6670-4a74-acf3-5168514df28e
        root@fb0d1860def8:/opt/snap/snap/snap-v0.11.0-beta/bin# ./snapctl task list
        ID 					 NAME 						 STATE 		 HIT 	 MISS 	 FAIL 	 CREATED 		 LAST FAILURE
        977ff072-6670-4a74-acf3-5168514df28e 	 Task-977ff072-6670-4a74-acf3-5168514df28e 	 Running 	 388 	 0 	 0 	 5:52AM 2-04-2016

1. snap logs things to  /tmp/  ¯\_(ツ)_/¯.  Look there if you want to find logs.


# Check out your events in the riemann dashboard
1. [http://192.168.99.100:4567](http://192.168.99.100:4567) ( or whatever the IP of your docker-machine is )
2. Change the *127.0.0.1:5556* in the top right corner to *192.168.99.100:5556* ( or whatever the ip of your docker-machine is )
3. **Command + Click** on the big `Riemann` in the middle ( it should turn grey )
4. **e** 
5. **Grid**
6. In the query area, put **true**


TODO
----------------
1. provide the config to riemann dash
2. autoconfigure dash?


