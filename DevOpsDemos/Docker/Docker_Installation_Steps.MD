##################Fresh installation Docker##############
sudo yum install yum-utils
sudo yum-config-manager --enable rhui-REGION-rhel-server-extras
sudo yum install docker -y
docker --version
sudo service docker start
systemctl enable docker
sudo groupadd docker
sudo usermod -a -G docker root
sudo usermod -a -G docker ec2-user
docker info
docker run hello-world
docker images

*******issue regarding redhat docker push edit below file in linux machine:***********
vi /etc/sysconfig/docker
ADD_REGISTRY='--add-registry docker.io --add-registry registry.access.redhat.com'

**********************

systemctl restart docker

##################Fresh installation Docker##############

# Installing Docker on Amazon Linux server

1.Update packages
```sh 
yum update -y
```
2.Install docker
```sh 
yum install docker -y
```

# Docker Installation on CentOS server
##### Referance URL : https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository
### PREREQUISITES

Please follow below steps to install docker CE on CentoOS server instance. For RedHat only Docker EE available 

1.Install required packages.

```sh 
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
  ```
  
2.Use the following command to set up the stable repository.
 
 ```sh 
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### INSTALLING DOCKER CE

1.Install the latest version of Docker CE.
```sh 
sudo yum install docker-ce
```

If prompted to accept the GPG key, verify that the fingerprint matches 
060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35, and if so, accept it.

2.Start Docker.
```sh 
sudo systemctl start docker
```

3.Verify that docker is installed correctly by running the hello-world image.
```sh
sudo docker run hello-world
```
