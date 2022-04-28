*********************Assiggment1 - Replica Set *******************


Use a DaemonSet instead of a ReplicaSet for Pods that provide a machine-level function, such as machine monitoring or machine logging. 
These Pods have a lifetime that is tied to a machine lifetime: the Pod needs to be running on the machine before other Pods start, and are safe to terminate when the machine is otherwise ready to be rebooted/shutdow

ReplicasSet will ensure that the number of pods (defined in our config file) is always running in our cluster. 
Does not matter in which worker node they are running. The scheduler will schedule the pods on any node depending upon the free resources. 
If one of our nodes goes down then all pods running on the node will be randomly scheduled on different nodes as per the resource availability. 
In this way, ReplicaSet ensures that the number of pods of an application is running on the correct scale as specified in the conf file.


Whereas in the case of DaemonSet it will ensure that one copy of pod defined in our configuration will always be available on every worker node.

The total # of pods running in DaemonSet = Number of worker nodes in our cluster

If a new node is added in our cluster then DaemonSet will automatically allocate pod on that node. Similarly, if a node is deleted then the pod running on the node is also destroyed, it will not reschedule the pod on different nodes as in case of ReplicaSet.





****************** Assignment2 Deployment ***************************************

1. Set the image : kubectl set image deployment nginx-deployment



apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 8000
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
********************************** Assignment 3 Service **********************



1. Cross Check if connections from Nodeport to service IP (clusterIP) to port to target port are all correct.

2. Just verify all the YAML files including syntax difference and containerPort


example 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  selector:
    matchLabels:
      run: load-balancer-example
  replicas: 2
  template:
    metadata:
      labels:
        run: load-balancer-example
    spec:
      containers:
        - name: hello-world
          image: gcr.io/google-samples/node-hello:1.0
          ports:
            - containerPort: 8000
              protocol: TCP


*********************************Assignment 4 VOTING APP DEMP PRACTICAL********************************

Last login: Thu Apr 28 11:18:53 UTC 2022 on pts/0
[root@ip-172-31-26-40 ~]# pwd
/root
[root@ip-172-31-26-40 ~]# ls
example-voting-app  get_helm.sh  kubernetes-training
[root@ip-172-31-26-40 ~]# cd example-voting-app/
[root@ip-172-31-26-40 example-voting-app]# git clone https://github.com/omrKalyan/example-voting-app
Cloning into 'example-voting-app'...
remote: Enumerating objects: 494, done.
remote: Total 494 (delta 0), reused 0 (delta 0), pack-reused 494
Receiving objects: 100% (494/494), 236.47 KiB | 7.88 MiB/s, done.
Resolving deltas: 100% (178/178), done.
[root@ip-172-31-26-40 example-voting-app]# ls
architecture.png  docker-compose-javaworker.yml  docker-compose.yml  example-voting-app  LICENSE      README.md  vote
dockercloud.yml   docker-compose-simple.yml      docker-stack.yml    k8s-specifications  MAINTAINERS  result     worker
[root@ip-172-31-26-40 example-voting-app]# cd k8s-specifications/
[root@ip-172-31-26-40 k8s-specifications]# kubectl apply -f .
deployment.apps/db created
service/db created
deployment.apps/redis created
service/redis created
deployment.apps/result created
service/result created
deployment.apps/vote created
service/vote created
deployment.apps/worker created
[root@ip-172-31-26-40 k8s-specifications]# kubectl get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/db-b54cd94f4-wrz7k           1/1     Running   0          7m24s
pod/hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          4h30m
pod/redis-868d64d78-sfkxf        1/1     Running   0          7m24s
pod/result-5d57b59f4b-x7bjf      1/1     Running   0          7m24s
pod/vote-94849dc97-z5llj         1/1     Running   0          7m23s
pod/worker-dd46d7584-p2mtm       1/1     Running   0          7m23s

NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/db          ClusterIP   10.100.197.167   <none>        5432/TCP         7m24s
service/hai-mysql   ClusterIP   10.101.61.215    <none>        3306/TCP         4h30m
service/redis       ClusterIP   10.97.130.211    <none>        6379/TCP         7m24s
service/result      NodePort    10.108.98.78     <none>        5001:31001/TCP   7m23s
service/vote        NodePort    10.103.245.149   <none>        5000:31000/TCP   7m23s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db          1/1     1            1           7m24s
deployment.apps/hai-mysql   0/1     1            0           4h30m
deployment.apps/redis       1/1     1            1           7m24s
deployment.apps/result      1/1     1            1           7m24s
deployment.apps/vote        1/1     1            1           7m23s
deployment.apps/worker      1/1     1            1           7m23s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/db-b54cd94f4           1         1         1       7m24s
replicaset.apps/hai-mysql-6476bf46c5   1         1         0       4h30m
replicaset.apps/redis-868d64d78        1         1         1       7m24s
replicaset.apps/result-5d57b59f4b      1         1         1       7m24s
replicaset.apps/vote-94849dc97         1         1         1       7m23s
replicaset.apps/worker-dd46d7584       1         1         1       7m23s


open the webbrowser :

http://18.140.53.252:31000/ 

http://18.140.53.252:31001/ 

[root@ip-172-31-26-40 example-voting-app]# cd vote
[root@ip-172-31-26-40 vote]# ls
app.py  Dockerfile  requirements.txt  static  templates

[root@ip-172-31-26-40 ~]# kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
db-b54cd94f4-wrz7k           1/1     Running   0          45m
hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          5h8m
redis-868d64d78-sfkxf        1/1     Running   0          45m
result-5d57b59f4b-x7bjf      1/1     Running   0          45m
vote-94849dc97-z5llj         1/1     Running   0          45m
worker-dd46d7584-p2mtm       1/1     Running   0          45m

[root@ip-172-31-26-40 ~]# kubectl delete pods vote-94849dc97-z5llj 
pod "vote-94849dc97-z5llj" deleted
[root@ip-172-31-26-40 ~]# kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
db-b54cd94f4-wrz7k           1/1     Running   0          47m
hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          5h10m
redis-868d64d78-sfkxf        1/1     Running   0          47m
result-5d57b59f4b-x7bjf      1/1     Running   0          47m
vote-94849dc97-ntrpr         1/1     Running   0          80s
worker-dd46d7584-p2mtm       1/1     Running   0          47m
[root@ip-172-31-26-40 ~]# ^C
[root@ip-172-31-26-40 ~]# 



*********************Assiggment1 - Replica Set *******************


Use a DaemonSet instead of a ReplicaSet for Pods that provide a machine-level function, such as machine monitoring or machine logging. 
These Pods have a lifetime that is tied to a machine lifetime: the Pod needs to be running on the machine before other Pods start, and are safe to terminate when the machine is otherwise ready to be rebooted/shutdow

ReplicasSet will ensure that the number of pods (defined in our config file) is always running in our cluster. 
Does not matter in which worker node they are running. The scheduler will schedule the pods on any node depending upon the free resources. 
If one of our nodes goes down then all pods running on the node will be randomly scheduled on different nodes as per the resource availability. 
In this way, ReplicaSet ensures that the number of pods of an application is running on the correct scale as specified in the conf file.


Whereas in the case of DaemonSet it will ensure that one copy of pod defined in our configuration will always be available on every worker node.

The total # of pods running in DaemonSet = Number of worker nodes in our cluster

If a new node is added in our cluster then DaemonSet will automatically allocate pod on that node. Similarly, if a node is deleted then the pod running on the node is also destroyed, it will not reschedule the pod on different nodes as in case of ReplicaSet.





****************** Assignment2 Deployment ***************************************

1. Set the image : kubectl set image deployment nginx-deployment



apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 8000
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
********************************** Assignment 3 Service **********************



1. Cross Check if connections from Nodeport to service IP (clusterIP) to port to target port are all correct.

2. Just verify all the YAML files including syntax difference and containerPort


example 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  selector:
    matchLabels:
      run: load-balancer-example
  replicas: 2
  template:
    metadata:
      labels:
        run: load-balancer-example
    spec:
      containers:
        - name: hello-world
          image: gcr.io/google-samples/node-hello:1.0
          ports:
            - containerPort: 8000
              protocol: TCP


*********************************Assignment 4 VOTING APP DEMP PRACTICAL********************************

Last login: Thu Apr 28 11:18:53 UTC 2022 on pts/0
[root@ip-172-31-26-40 ~]# pwd
/root
[root@ip-172-31-26-40 ~]# ls
example-voting-app  get_helm.sh  kubernetes-training
[root@ip-172-31-26-40 ~]# cd example-voting-app/
[root@ip-172-31-26-40 example-voting-app]# git clone https://github.com/omrKalyan/example-voting-app
Cloning into 'example-voting-app'...
remote: Enumerating objects: 494, done.
remote: Total 494 (delta 0), reused 0 (delta 0), pack-reused 494
Receiving objects: 100% (494/494), 236.47 KiB | 7.88 MiB/s, done.
Resolving deltas: 100% (178/178), done.
[root@ip-172-31-26-40 example-voting-app]# ls
architecture.png  docker-compose-javaworker.yml  docker-compose.yml  example-voting-app  LICENSE      README.md  vote
dockercloud.yml   docker-compose-simple.yml      docker-stack.yml    k8s-specifications  MAINTAINERS  result     worker
[root@ip-172-31-26-40 example-voting-app]# cd k8s-specifications/
[root@ip-172-31-26-40 k8s-specifications]# kubectl apply -f .
deployment.apps/db created
service/db created
deployment.apps/redis created
service/redis created
deployment.apps/result created
service/result created
deployment.apps/vote created
service/vote created
deployment.apps/worker created
[root@ip-172-31-26-40 k8s-specifications]# kubectl get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/db-b54cd94f4-wrz7k           1/1     Running   0          7m24s
pod/hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          4h30m
pod/redis-868d64d78-sfkxf        1/1     Running   0          7m24s
pod/result-5d57b59f4b-x7bjf      1/1     Running   0          7m24s
pod/vote-94849dc97-z5llj         1/1     Running   0          7m23s
pod/worker-dd46d7584-p2mtm       1/1     Running   0          7m23s

NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/db          ClusterIP   10.100.197.167   <none>        5432/TCP         7m24s
service/hai-mysql   ClusterIP   10.101.61.215    <none>        3306/TCP         4h30m
service/redis       ClusterIP   10.97.130.211    <none>        6379/TCP         7m24s
service/result      NodePort    10.108.98.78     <none>        5001:31001/TCP   7m23s
service/vote        NodePort    10.103.245.149   <none>        5000:31000/TCP   7m23s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db          1/1     1            1           7m24s
deployment.apps/hai-mysql   0/1     1            0           4h30m
deployment.apps/redis       1/1     1            1           7m24s
deployment.apps/result      1/1     1            1           7m24s
deployment.apps/vote        1/1     1            1           7m23s
deployment.apps/worker      1/1     1            1           7m23s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/db-b54cd94f4           1         1         1       7m24s
replicaset.apps/hai-mysql-6476bf46c5   1         1         0       4h30m
replicaset.apps/redis-868d64d78        1         1         1       7m24s
replicaset.apps/result-5d57b59f4b      1         1         1       7m24s
replicaset.apps/vote-94849dc97         1         1         1       7m23s
replicaset.apps/worker-dd46d7584       1         1         1       7m23s


open the webbrowser :

http://18.140.53.252:31000/ 

http://18.140.53.252:31001/ 

[root@ip-172-31-26-40 example-voting-app]# cd vote
[root@ip-172-31-26-40 vote]# ls
app.py  Dockerfile  requirements.txt  static  templates

[root@ip-172-31-26-40 ~]# kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
db-b54cd94f4-wrz7k           1/1     Running   0          45m
hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          5h8m
redis-868d64d78-sfkxf        1/1     Running   0          45m
result-5d57b59f4b-x7bjf      1/1     Running   0          45m
vote-94849dc97-z5llj         1/1     Running   0          45m
worker-dd46d7584-p2mtm       1/1     Running   0          45m

[root@ip-172-31-26-40 ~]# kubectl delete pods vote-94849dc97-z5llj 
pod "vote-94849dc97-z5llj" deleted
[root@ip-172-31-26-40 ~]# kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
db-b54cd94f4-wrz7k           1/1     Running   0          47m
hai-mysql-6476bf46c5-xz6mm   0/1     Pending   0          5h10m
redis-868d64d78-sfkxf        1/1     Running   0          47m
result-5d57b59f4b-x7bjf      1/1     Running   0          47m
vote-94849dc97-ntrpr         1/1     Running   0          80s
worker-dd46d7584-p2mtm       1/1     Running   0          47m
[root@ip-172-31-26-40 ~]# ^C
[root@ip-172-31-26-40 ~]# 




		
		
		
		
		
		
		



		
		
		
		
		
		
		


