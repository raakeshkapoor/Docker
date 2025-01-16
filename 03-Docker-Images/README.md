
# Docker Images

1.	Pull Images from https://hub.docker.com
```
# This command will pull the Ubuntu Image from the portal. Similarly, we can pull any other image from the portal. 

docker pull ubuntu

# To list all the images
docker images
```

2.	Steps to Push an Image to https://hub.docker.com
```
# Tag the Image by using the below mentioned command. In the below mentioned command raakeshkapoor is the login id of the https://hub.docker.com and ubuntu01 is the name of the Image. 

sudo docker tag ubuntu raakeshkapoor/ubuntu01

# Use the below mentioned command to login to the Docker Hub.
docker login -u <username>

# Push the Image to the https://hub.docker.com 
docker push raakeshkapoor/ubuntu01
```

3.	Create and Map a Volume with the Docker.
```
# Create a Volume using the below-mentioned command
docker volume create myvol01

# List and Inspect the Volume
docker volume inspect myvol01

# Below mentioned command will map the myvol01 with the /var/lib/vol01 in the Container. 
docker run -it -v myvol01:/var/lib/vol01 -w /var/lib/vol01 ubuntu

# Create some files and folders by ensuring you are in /var/lib/vol01. Delete the Container and then create a new Container and map the myVol01. Check if the files created in the old Container are still available in the myVol01 or not. 
```

4.	Create a Docerfile
```
# Either use vim or vi to create a new file with the name of Docerfile and add the below mentioned content in the file. 

FROM ubuntu
RUN apt-get update -y
RUN apt-get install apache2 -y
RUN service apache2 start
RUN apt-get install htop -y
EXPOSE 80
CMD [“apachectl”, “-D”, “FOREGROUND”]
WORKDIR /var/www/html
RUN rm index.html
COPY /myvolume01/* /var/www/html
```

5.	Build an image by using Dockerfile
```
# In the below-mentioned command myimage001 is the name of the new image and “.” is referring to the Docerfile. Please ensure that Dockerfile is at the same path from where we are running this command. 

docker build -t myimage001 .
OR
docker image build -t myimage:v01 .
OR

# in the below mentioned command imagefile is the file created instead of Dockerfile
docker build -t ubuimages:v01 -f imagefile .

# Use the below mentioned command to create the Container using the new image. 
docker run -it myimage001 /bin/bash

# Use the below mentioned command if you want you new Container to map with the Volume already created. Make sure the the Volume “myvol01” is already created before we use the below mentioned command. This command will show all the content which is in myvol01 in the container at path /var/www/html.
sudo docker run -it -v myvol01:/var/www/html myimage001 /bin/bash

# Make sure you check the apache2 service 
service apache2 status

# Run the apache2 Service if it is not started.
service apache2 start
```

6.	Build a Website by using Dockerfile.
```
# Use the latest Ubuntu image
FROM ubuntu:latest

# Update the package list and install required packages
RUN apt-get update && apt-get install -y \
    apache2 \
    unzip \
    wget

WORKDIR /var/www/html

# Download the website content using ADD
RUN  wget -O /tmp/inance.zip https://www.free-css.com/assets/files/free-css-templates/download/page296/inance.zip

# Unzip the website content into /var/www/html and clean up the zip file
RUN unzip /tmp/inance.zip 
RUN cp -rvf inance-html/* /var/www/html/ # && rm /tmp/inance.zip

# Expose port 80 (HTTP)
EXPOSE 80

# Start Apache2 in the foreground
CMD ["apachectl", "-D", "FOREGROUND"]
```

7.	Install and Configure Wordpress 
```
# Create a mysql Container using the below mentioned Command
sudo docker container run -itd --name db01 -e MYSQL_ROOT_PASSWORD=mypassword mysql

# run the below mentioned command to check the container id of mysql
sudo docker ps

# access the console of MySQL  by using the below mentioned command. In the below mentioned command fd1 is the container id.
sudo docker exec -it fd1 /bin/bash

# Once connected to MySQL Container run the following command.
mysql -u root -p
Enter the Password, in this scenario password is mypassword defined earlier.

# Show and Create a Database
show databases;
create database wordpress;

# Create a Wordpress Container using the below mentioned command. 
sudo docker container run --name wp01 -itd -e WORDPRESS_DB_HOST=172.17.0.2:3306 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=mypassword wordpress

# Inspect Wordpress Container to check the IP Address of the Wordpress Container. 
sudo docker ps
sudo docker inspect containerid

# Follow the Steps Given below:
 
# Click on Install Wordpress
# Click on Login
```

8.	Docker Vs Docker-Compose

- Docker	Docker-Compose
  - Used to define the blueprint for building a single Docker image. It specifies the steps and instructions to assemble the image, including setting up the environment, installing dependencies, and copying application code.	
  - Focuses on creating a single container (or image).	
  - Defines how that specific container should be built and behave.
  - Does not manage dependencies between multiple containers. It only handles the setup for a single image.	
	
- Docker-Compose
  - Used to define and run multiple Docker containers as a service. It allows you to define a multi-container setup, specifying how the containers interact, their dependencies, and environment configurations.
  - Manages multiple containers, their interaction, networks, volumes, and lifecycle.
  - Used for defining the infrastructure for multi-service applications (e.g., web app + database).
  - Can define dependencies between services (like one service needing to wait for another to start). This is useful for microservices, databases, etc.


9.	Install Docker-Compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```

10.	Reference of docker-compose.yml file.
```
version: "3.9"  # Compose file version

services:
  # First service: web
  web:
    build: .  # Build the image using the Dockerfile in the current directory
    ports:
      - "3000:3000"  # Map host port 3000 to container port 3000
    environment:
      - NODE_ENV=development  # Set environment variables
    depends_on:
      - db  # Wait for the 'db' service to be ready before starting
    volumes:
      - .:/app  # Mount the current directory to /app in the container
    networks:
      - app-network  # Connect this service to the defined network

  # Second service: db
  db:
    image: postgres:13  # Use the official Postgres image version 13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydatabase
    volumes:
      - db-data:/var/lib/postgresql/data  # Mount a volume for persistent data storage
    networks:
      - app-network  # Connect this service to the same network as 'web'

volumes:
  db-data:  # Define a named volume for the Postgres database

networks:
  app-network:  # Define a custom network for communication between 'web' and 'db'

```

11.	Create a Docker Compose File to run the Ubuntu
```
# create a docker-compose.yml file and paste the below mentioned content in that:
service:
  web01:
    image: ubuntu
    expose: 
      - 80
# If we don’t use the below mentioned command then the Ubuntu container will be created and then shutdown automatically.
    stdin_open: true
    tty: true
```

12.	Steps to Scale a Docker Compose
```
# below mentioned command will 5 copies of Web01. Web01 is the name of the Service mentioned in the Docker-Compose file. 
# NOTE: PORT mapping is not enabled in the Docker-Compose file, else the containers will be created by will not start. 

sudo docker compose up -d --scale web01=5
```

13.	Create a HTML Website Container using both Dockerfile and docker-compose.yml.
```
Dockerfile
# Use the latest Ubuntu image
FROM ubuntu:latest

# Update the package list and install required packages
RUN apt-get update && apt-get install -y \
    apache2 \
    unzip \
    wget

WORKDIR /var/www/html

# Download the website content using ADD
RUN  wget -O /tmp/inance.zip https://www.free-css.com/assets/files/free-css-templates/download/page296/inance.zip

# Unzip the website content into /var/www/html and clean up the zip file
RUN unzip /tmp/inance.zip 
RUN cp -rvf inance-html/* /var/www/html/ # && rm /tmp/inance.zip

# Expose port 80 (HTTP)
EXPOSE 80

# Start Apache2 in the foreground
CMD ["apachectl", "-D", "FOREGROUND"]

docker-compose.yml File

version: "3.9"
services:
  web:
    build: .
    container_name: apache_container
    ports:
      - "8080:80"
    stdin_open: true
    tty: true
    restart: always

Docker-Compose Commands
docker-compose up -d

# The above mentioned command will create a new image and a Container. 
docker images
docker ps -a
```

14.	Install Wordpress using Docker Compose File
```
# Create a folder with the name of wp-config
# https://gist.github.com/bradtraversy/faa8de544c62eef3f31de406982f1d42
mkdir wp-config
cd wp-config
vi docker-compose.yml

#press i for insert and then press Ctrl+ Shift+ V to copy the content of file to docker-compose.yml file. 

version: “3.9”
services:
  # Database
  db:
    image: mysql:latest
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
      - wpsite
  # phpmyadmin
  phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin/phpmyadmin
    restart: always
    ports:
      - '8080:80'
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: password 
    networks:
      - wpsite
  # Wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - '8000:80'
    restart: always
# a new volume with the name of wp_data can be created and mapped with the volume if required. 
    volumes: ['./:/var/www/html']
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
    networks:
      - wpsite
networks:
  wpsite:
volumes:
  db_data:
```


15.	Create Wordpress Containers
```
sudo docker compose up -d

# This command will create the dockers, Network, Volume. 
```

16.	To shutdown the Containers.
```
sudo docker compose down
```

17.	Combine Dockerfile and Docker-Compose File to build an Ubuntu Container and install the Apache.
```
# Docker-Compose.yml file
version: “3.9”
services:
  web:
    build: .
    container_name: apache_container
    ports:
      - "8080:80"
    stdin_open: true
    tty: true
    restart: always

# Dockerfile content

# Use the official Ubuntu image
FROM ubuntu:latest

# Prevent interactive installation issues
ENV DEBIAN_FRONTEND=noninteractive

# Update package list and install apache2
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean

# Ensure apache2 runs in the foreground
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

```

18.	Build the image and start the container. 
```
docker-compose up --build -d
```

19.	Create a Network in Docker-Compose file
```
services:
  # Define the web service
  web:
    #   build: .  # This uses the Dockerfile in the current directory
    image: nginx  
    networks:
      app-network:
        ipv4_address: 192.168.10.10
networks:
  app-network:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.10.0/24
```

20.	Steps to assign Image Name to an image Created using docker-compose and Dockerfile
```
version: "3.9"  # Specify the version of docker-compose

services:
  # Define the web service
  web:
    build: .  # This uses the Dockerfile in the current directory
    image: mynginx:v01
    networks:
      app-network:
        ipv4_address: 192.168.10.10
networks:
  app-network:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.10.0/24
```

