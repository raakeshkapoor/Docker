# Docker Commands

1.	View running containers
```yml
docker ps
```

3.	View all the containers, including running and stop containers. 
```yaml
docker ps -a
```

4.	List all the containers except one container whose name is web01

```sh
# grep -v web01 is used for an exception. It can be used with various combinations. 
sudo docker ps -a | grep -v web01
```

4.	Command to create the container. This command will create the container and then stop it.
```yml
docker run ubuntu
```

5.	Command to run the container and then keep it running.
```yml
docker run ubuntu sleep 60
```
6.	Command to detach the container and show the container ID.
```
docker run -d ubuntu sleep 60
```
7.	Let the container run for unlimited time.
```
docker run -d -it ubuntu
```
8.	Stop the container
```
docker stop containerid
```

9.	Start the container
```
docker start containerid
```

10.	Remove the container. This command will not let you delete the running container. 
```
docker rm containerid
```

11.	Force to remove the container. 
```
docker rm cotnainerid -f
```

12.	If you want to create the container and wanted to go into the container then use the /bin/bash in the command. 
```
docker run -it ubuntu /bin/bash
```

13.	Connect to the running Container if you don’t have /bin/bash of the container. 
```
docker exec -it containerid /bin/bash
```

14.	When you are in the container then you can run the command to update the container. 
```
apt-get update
```

15.	Install the Apache on the Ubuntu
```
apt-get install apache2
```

16.	Steps to create a new Ubuntu Container and install Apache on it. 
```
# this command will create the new container and give the console of the docker
docker run -it ubuntu

OR

docker run -it ubuntu /bin/bash

#run the below mentioned commands to update Ubuntu and install Apache

apt-get update
apt-get install apache2

service apache2 start
service apache2 status

# Check the Default Web Page
curl http://172.17.0.2

ELSE Go to the path /var/www/html and create a new index.htm file by using the below mentioned command

echo “Welcome to my Web Site” > index.htm

# To check the processes running on the container. 
docker container ls
docker top containerid

docker container stats containerid
```

17.	Run the Container and Map with the Port of the Server
```
# This command will map the local port of 3600 with the container port of 80. To access the same use the local IP address of the Server and use the port 3600.

docker run -it -p 3600:80 ubuntu
```

18.	How to remotely execute a command in a Container. 
```
docker exec 0b7 ls

docker exec 0b7 touch abc.txt
```

19.	Steps to rename Container Name.
```
docker rename containerid NewContainerName

e.g:
docker rename 346 Web01
```

20.	Copy the file from the local Server to the Container. 
```sh
docker cp NameOfFileToCopy containerID:PathOfContainerToWhichFileIsToCopy

# e.g.:

docker cp index.html ed0:/var/www/html

# In the above-mentioned command, we’ll copy the index.html file from local Server to Container with the Container ID starts with ed0 and the path of the container to which file is to copy is /var/www/html
```

21.	Map a drive from the Local Server to the Docker.
```
sudo docker run -it --rm -v /var/rk/one:/rk -w /rk ubuntu

# Above mentioned command will map the drive
```

# Docker Network

22.	Create a New Network
```
# myNewNetwork is the name of the Network created with the Subnet range of 192.168.1.0/24
sudo docker network create -d bridge --subnet 192.168.1.0/24  myNewNetwork
```

23.	Attach a Network to the Container. 
```
# Web01 Container was created and attached with myNewNetwork. Prerequisite is to create the Network before we run this command. 
sudo docker run -it --name web01 --network myNewNetwork ubuntu
```

24.	Assign a specific IP Address to the new Container. 
```
# vnet01 is the name of an existing Network and --ip is to assign a specific IP to the container.
docker run -itd --name web05 --network vnet01 --ip 192.168.2.100 ubuntu
```

25.	Connect a VNET and assign a specific IP to the existing Container.
```
# vnet02 is the existing Network and c59 is the Container ID, --ip is the IP Address we wanted to assign to the Container. 
docker network connect vnet02 --ip 192.168.3.100 c59
```

26.	How to Check the Stats of a Container
```
# Below mentioned command will show the stats of all the Containers. For a particular container we can give the container id.
sudo docker stats

sudo docker system events
```

27.	How to assign the CPU and Memory to the Container. 
```
# below mentioned command will create a container with 2 CPUs and 2 GB of Memory.
sudo docker run -itd --cpus=2 --memory=”2gb” ubuntu
```

28.	How to create multiple Containers using for loop.
```
# below mentioned for loop will be used to create 5 containers with the name of Web1.. Web5. Similarly we can use the commands to stop or remove the Containers. 

for i in {1..5}; do
  docker run -d --name Web$i ubuntu
done
```

29.	How to Create MySQL Container
```
docker run -itd -e MYSQL_ROOT_PASSWORD=mypassword mysql

docker exec -it 416 /bin/bash

mysql -u root -p
```

30.	Use the running container to create an Image. 
```
# 497 is the container id and myubuntu01 is the name of the image that we want to create. 
docker commit 497  myubuntu01
```

