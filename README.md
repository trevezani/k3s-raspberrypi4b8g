# k3s running in Raspberry Pi 4b 8G

## Building and Running

* building:
```
mvn clean package -f api-zipcode
mvn docker:build -f api-zipcode/api-zipcode-infraestructure
```
* running:
```
docker run -it --rm --name api -p 1401:1401 api-zipcode:0.0.1-SNAPSHOT
```
* testing
```
curl http://<url>:1401/zipcode/37188
```