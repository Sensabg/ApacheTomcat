# `Apache-Tomcat---v10.1.24`

## Tomcat is widely used as it acts as a bridge between web servers and Java-based applications.

<img src="/doc/Apache_Tomcat_logo.svg.png" alt="Alt text" title="Optional title" style="display: inline-block; margin: auto; width: 350px; height: auto;">

#

<!---### Regrettably, configuring Tomcat from scratch has been more challenging and time-consuming than expected. However, despite the obstacles, I successfully completed the setup--> 

<hr>

> [!NOTE]
> I performed the integration via Terminal -> SSH on a Linux ( CentOS ) VPS.

<hr>

## Step 1:

Access your server via SSH.

```sh
ssh -p port_number username@hostname
```

> [!IMPORTANT]
> Replace
* "port_number" with the actual port for SSH.
* "username" with the actual username.
* "hostname" with the actual hostname.

## Step 2:

Ensure your system is up to date.

```sh
sudo yum update -y
```

## Step 3:

Install Java.

```sh
sudo yum install java-11-openjdk-devel -y
```

> [!IMPORTANT]
> You can install any desired version.

Verify the Java version.

```sh
java -version
```

## Step 4:

Download and Install Apache Tomcat.

```sh
cd /tmp
```

```sh
curl -O https://downloads.apache.org/tomcat/tomcat-10/v10.1.0/bin/apache-tomcat-10.1.24.tar.gz
```

```sh
sudo mkdir /opt/tomcat
```

```sh
sudo tar xf apache-tomcat-10.1.24.tar.gz -C /opt/tomcat --strip-components=1
```
## Step 5:

Ensure that the correct permissions are set.

```sh
sudo chmod -R 755 /opt/tomcat
```

## Step 6:

Create a Tomcat User.

```sh
sudo groupadd user
```
> [!IMPORTANT]
> Replace 'user' with the desired username.

```sh
sudo useradd -M -s /bin/nologin -g user -d /opt/tomcat user
```
> [!IMPORTANT]
> Replace 'user' with the chosen username.

## Step 7:

Create a Systemd Service File.

```sh
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/jre"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

## Step 8:

Reload Systemd Manager Configuration.

```sh
sudo systemctl daemon-reload
```

Start Tomcat.

```sh
sudo systemctl start tomcat
```

Enable Tomcat Service to Start on Boot.

```sh
sudo systemctl enable tomcat
```

## Step 9:

Open a specific Port (per your preference) in the Firewall settings.

```sh
sudo firewall-cmd --zone=public --permanent --add-port=8080/tcp
```

> [!IMPORTANT]
> Replace '8080' with the desired port.

Reload settings.

```sh
sudo firewall-cmd --reload
```

## Step 10 

Edit the server.xml file located in the conf directory.

```sh
sudo nano /opt/tomcat/conf/server.xml
```

Ensure the following is present:

```sh
<?xml version="1.0" encoding="UTF-8"?>
<Server port={"8005"} shutdown="SHUTDOWN">
    <Service name="Catalina">
        <Connector port={"yourPort"} protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort={"8443"} />
        <Engine name="Catalina" defaultHost={"yourHostnameOrIP"}>
            <Host name={"yourHostnameOrIP"} appBase="webapps"
                  unpackWARs="true" autoDeploy="true">
                <!-- You can add additional configurations here if needed -->
            </Host>
        </Engine>
    </Service>
</Server>
```
> [!NOTE]
> Replace the values in "{}" with the desired data.

## Step 11:

Edit Tomcat Users Configuration File.

```sh
sudo nano /opt/tomcat/conf/tomcat-users.xml
```

Add the following lines inside.

```sh
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="admin" password="password" roles="manager-gui,admin-gui"/>
```

> [!IMPORTANT]
> Replace the username and password if you don't want to get rekt.


Restart.

```sh
sudo systemctl restart tomcat
```

<hr>

# `Configure Apache HTTP to act as a reverse proxy for Tomcat.`

<img src="/doc/Untitled-2024-05-24-1337.png" alt="Alt text" title="Optional title" style="display: inline-block; margin: auto; width: 650px; height: auto;"> 

### Edit the Apache HTTP Server configuration file.

```sh
sudo nano /etc/httpd/conf/httpd.conf
```

### Add the following lines:

```sh
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com

    ProxyPass / https://yourHostnameOrIp:port/
    ProxyPassReverse / https://yourHostnameOrIp:port/

</VirtualHost>
```

### Check your custom domain.

<img src="/doc/tomcat11.png" alt="Alt text" title="Optional title" style="display: inline-block; margin: auto; width: 650px; height: auto;"> 

### Deploy an app.

<img src="/doc/helloapp.png" alt="Alt text" title="Optional title" style="display: inline-block; margin: auto; width: 650px; height: auto;">  

<hr>

# `That's it for now! I hope you find this documentation useful. `
