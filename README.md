# Installing Omada Controller on Ubuntu Server

## Description
This guide provides step-by-step instructions to install and configure the Omada Controller on an Ubuntu server. The Omada Controller is a centralized management platform for TP-Link devices, offering advanced features for managing networks.

## Prerequisites
Before starting, ensure the following:
- A running Ubuntu Server (version 22.04 or 24.04).
- Sudo privileges for installing and configuring software.
- Basic knowledge of Linux command-line operations.
- A stable internet connection.

## Why These Dependencies?
### Java (OpenJDK 8)
The Omada Controller is a Java-based application, requiring Java Runtime Environment (JRE) to run.

### MongoDB
MongoDB is a NoSQL database used by the Omada Controller to store configuration data and network statistics.

> **Note**: Prior to version 5.14.20, the Omada Controller supported MongoDB versions 3 and 4. Starting from version 5.14.20, it supports MongoDB up to version 7. This guide uses MongoDB v4.4. Choose the version that matches your system's needs.

### jsvc
The `jsvc` utility is needed to allow the Omada Controller to run as a background service.

### curl
`curl` is used for downloading files and managing repository keys during the installation process.


## Step 1: Updating System Packages

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install openjdk-8-jre-headless jsvc curl -y
```

## Step 2: Installing MongoDB

### Adding MongoDB Repository
```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
sudo apt-key list
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
sudo apt update
```

### Installing MongoDB
```bash
sudo apt install mongodb-org -y
sudo systemctl start mongod.service
sudo systemctl status mongod
sudo systemctl enable mongod
```

### Resolving Dependency Issues
If you encounter the following error while installing MongoDB, refer to the fix mentioned below:

```
The following packages have unmet dependencies:
mongodb-org-mongos : Depends: libssl1.1 (>= 1.1.1) but it is not installable
mongodb-org-server : Depends: libssl1.1 (>= 1.1.1) but it is not installable
mongodb-org-shell : Depends: libssl1.1 (>= 1.1.1) but it is not installable
```

**Fix** - Ubuntu 22.04 has upgraded `libssl` to version 3, which does not support `libssl1.1`. To resolve this, add the Ubuntu 20.04 source and install `libssl1.1`:

```bash
echo "deb http://security.ubuntu.com/ubuntu focal-security main" | sudo tee /etc/apt/sources.list.d/focal-security.list
sudo apt-get update
sudo apt-get install libssl1.1 -y
```

Re-run the MongoDB installation command:

```bash
sudo apt install mongodb-org -y
```

## Step 3: Downloading and Installing Omada Controller

### Download the Omada Controller
Official download link: [Omada Software Controller](https://support.omadanetworks.com/us/product/omada-software-controller/?resourceType=download)

Use the following command to download the specific version:

```bash
sudo wget https://static.tp-link.com/upload/software/2025/202501/20250109/Omada_SDN_Controller_v5.15.8.2_linux_x64.tar.gz
```

### Extract and Install
```bash
tar zxvf Omada_SDN_Controller_v5.15.8.2_linux_x64.tar.gz
cd Omada_SDN_Controller_v5.15.8.2_linux_x64/
sudo bash ./install.sh
```

## Step 4: Accessing the Omada Controller Web Interface

To access the Omada Controller, open a browser and navigate to:

```
http://<server_ip_address>:8088
```

## Step 5: Enabling SSL Certificate

### Generate a Self-Signed Certificate
```bash
keytool -genkey -keyalg RSA -alias tomcat -keystore selfsigned.jks -validity 365 -keysize 2048
keytool -list -v -keystore selfsigned.jks
```

### Upload the Certificate to Omada Controller
1. Navigate to **Global View** → **Settings** → **System Settings**.
2. Upload the `selfsigned.jks` file.
3. Enter the keystore password.

---

You have successfully installed the Omada Controller on your Ubuntu server. For additional configuration, refer to the official TP-Link documentation.
