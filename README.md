Hello World! (WAR-style)
===============

This is the simplest possible Java webapp for testing servlet container deployments.  It should work on any container and requires no other dependencies or configuration.

# to install jfrog using docker 

```shell
#!/bin/bash
sudo apt-get update && sudo apt install docker.io -y
sudo usermod -aG docker $USER
sudo docker pull docker.bintray.io/jfrog/artifactory-oss:latest
sudo mkdir -p /jfrog/artifactory
sudo chown -R 1030 /jfrog/
sudo docker run --name artifactory -d -p 8081:8081 -p 8082:8082 \
-v /jfrog/artifactory:/var/opt/jfrog/artifactory \
docker.bintray.io/jfrog/artifactory-oss:latest
sudo tee /etc/systemd/system/artifactory.service > /dev/null <<EOL
[Unit]
Description=Setup Systemd script for Artifactory Container
After=network.target

[Service]
Restart=always
ExecStartPre=-/usr/bin/docker kill artifactory
ExecStartPre=-/usr/bin/docker rm artifactory
ExecStart=/usr/bin/docker run --name artifactory -p 8081:8081 -p 8082:8082 \
  -v /jfrog/artifactory:/var/opt/jfrog/artifactory \
  docker.bintray.io/jfrog/artifactory-oss:latest
ExecStop=-/usr/bin/docker kill artifactory
ExecStop=-/usr/bin/docker rm artifactory

[Install]
WantedBy=multi-user.target
EOL

sudo systemctl daemon-reload
sudo systemctl start artifactory
sudo systemctl enable artifactory

sudo systemctl status artifactory

echo "Artifactory setup completed. Access the web interface at http://server_url:8081/"
```
