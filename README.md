# k3s running in Raspberry Pi 4b 8G

## Preparing the OS

Get the Pi OS 64 bits on this [url](https://downloads.raspberrypi.org/raspios_lite_arm64/images/)

After create the Micro SD card, mount the card and add the text **cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory** at the end of the first line in the file cmdline.txt *(make sure its only single line in the file)*

## Installing the Docker

```
sudo apt-get update && sudo apt-get upgrade
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker pi

docker version
docker info

docker run hello-world
```

## Installing the k3s

```
curl -sfL https://get.k3s.io | INSTALL_K3S_CHANNEL=latest sh -
sudo chmod 644 /etc/rancher/k3s/k3s.yaml
echo "K3S_KUBECONFIG_MODE=\"644\"" >> /etc/systemd/system/k3s.service.env
```

## Installing and configuring the java environment
* jdk
```
sudo apt update
sudo apt install default-jdk
java –version
```

* maven
```
sudo mkdir /opt/tools
sudo chown pi:pi /opt/tools
wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar -xzvf apache-maven-3.6.3-bin.tar.gz
mv apache-maven-3.6.3 /opt/tools
```

Edit the profile

```
vi ~/.profile
```

and in the end of file add the lines below
```
M2_HOME=/opt/tools/apache-maven-3.6.3
PATH=$PATH:$M2_HOME/bin
```

## Building and Running

* building:
```
mvn clean package -f api-zipcode
mvn docker:build -f api-zipcode/api-zipcode-infraestructure
```
* running:
```
docker run -it --rm --name api -p 1401:1401 api-zipcode:1.0.0
```
* testing
```
curl http://<raspberry server>:1401/zipcode/37188
```
* pushing the image to the registry
```
mvn -Ddocker.registry=localhost:5000 -Ddocker.username=admin -Ddocker.password=admin \
    docker:push -f api-zipcode/api-zipcode-infraestructure

mvn docker:remove -f api-zipcode/api-zipcode-infraestructure    
```    
* listing the tags inside the registry
```
curl -u admin:admin http://<raspberry server>:5000/v2/api-zipcode/tags/list
```