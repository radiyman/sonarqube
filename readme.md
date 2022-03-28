# Sonarqube
## System preparation
1. Selenium server uses Elasticsearch. It is required additional system settings. Description can be found at:  
https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#_macos_with_docker_for_mac  
For Linux You can use the prepare_system.sh script.  
```
sudo ./prepare_system.sh
```
2. Make .env file  
```
cp .env.example .env
```
3. Add the full path to the repository, you wanted to scan to YOUR_REPO variable in .env file  

## Run Sonarqube
```
docker-compose up -d
```
server url:  
http://localhost:9000/  
After first launch login/password is admin admin .
## Add new project
Perform actions on the Sonarqube server:  
1. "Add project" / "manually"
2. add "Project key" and "Display name" press "set up". Remember the generated "Project key".
3. add "token name" press generate. Remember the generated token.
Connect to the sonarqube-cli container and run scaning:
4. Connect to container:
```
docker exec -it sonarqube-cli bash
```
5. Replace PROJECT_KEY with your "Project key" value from step 2.  
   Replace TOKEN with your "token" value from step 3.  
   run next command:
```
sonar-scanner \
  -Dsonar.projectKey=PROJECT_KEY \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=TOKEN
```

## Sonar-scanner cli docker image:
https://hub.docker.com/r/sonarsource/sonar-scanner-cli  

## Permanently set server settings for Ubuntu  
Instead running ./prepare_system.sh every time, you can change your settings forever.  
Perform actions on the Sonarqube server:  
1) create /etc/sysctl.d/99-sonarqube.conf file  
add code to it:  
```
# These parameters are needed for elasticsearch to work, which is needed for sonarqube to work.
vm.max_map_count=262144
fs.file-max=131072
```
2) restart sysctl with new params:  
```
sudo sysctl -p
```
3) update /etc/security/limits.conf file, add lines to it:  
```
* soft nproc           131072
* hard nproc           131072
* soft nofile          131072
* hard nofile          131072
root soft nproc           131072
root hard nproc           131072
root soft nofile          131072
root hard nofile          131072
```
4) restart system.
You can check that all is ok by command:  
```
ulimit -n
```
if the response is 131072 then everything is done correctly.  