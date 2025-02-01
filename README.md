# 🚀 Artifact Repository Manager with Nexus

This repository contains data and information for setting up and managing an **Artifact Repository** using **Nexus** on a cloud server.

---

## 🛠 Technologies Used
- **Nexus**
- **DigitalOcean**
- **Linux**
- **Java**
- **Gradle**
- **Maven**

---

## ✅ Pre-requisites
Before proceeding, ensure you have the following:

1. **DigitalOcean Account** – With available credits to create and run a Linux droplet.
2. **Java SDK (Preferably OpenJDK 8)** – Installed on your local computer.
3. **Gradle & Maven** – Installed locally to build Java applications.
4. **SSH Setup** – You must be able to log into the remote server via SSH.
5. **Java SDK 17** – Installed on the remote server.

---

## 📌 Installing & Configuring Nexus on a Cloud Server

1. **Log in as a `root` user** and navigate to the `/opt` directory.
2. **Download Nexus TAR file** using:
   `wget <HTTP URL>`
(Find the latest version [here](https://help.sonatype.com/en/download.html)).
3. Extract the downloaded file:
   `tar -zxvf [file name]`
4. **Create a new user** and set a password (we will use this user to run Nexus):
      `adduser [username]`
5. **Grant ownership of the Nexus directory** to the newly created user:
   `chown -R [username]:[group] [directory path]`
   This ensures that both the user and group have the necessary permissions to manage Nexus.
6. **Start Nexus** service using the below command:
   `/opt/nexus-[version]/bin/nexus start`
7. We can run the following command to ensure the service is running and find the port number:
   `netstat -lntp`
   You will require **net-tools** installed in order to run this command.

---

## 👤 Creating a New User on Nexus with Relevant Permissions

To ensure proper access control, follow these steps to create a new user and assign the necessary permissions:

### 🌐 Accessing Nexus Repository

1. **Find the Nexus repository port** using:
   `netstat -tulnp | grep 8081`
(Port 8081 is the default for Nexus.)
2. Access Nexus in your browser by navigating to:
`http://[server-public-IP]:8081 `
3. You should see the Sonatype Nexus Repository Manager welcome page.

### 🔑 Logging in as Admin
1. Retrieve the default admin password from the sonatype-work directory:
`cat /opt/sonatype/sonatype-work/nexus3/admin.password`
📝 Note: This is a temporary password; you will be required to set a new one upon first login.
2. Log in as the admin user and navigate to the Users section.

### 🆕 Creating a New User & Role
1. In the Users section, create a new user.
2. Assign a new role with the following permissions:
`nx-repository-view-maven2-maven-snapshots-*`
(This is necessary since we will be working with Maven and Gradle repositories.)
3. Sign out from the admin user.
4. Log in with the newly created user credentials.

---

## ☕ Java Gradle - Building & Uploading a JAR File to Nexus Repository

We will use the **login credentials** of the newly created Nexus user to configure **Gradle** and **Maven** for pushing artifacts to the repository.

### 📥 Downloading the Demo Java Application

1. Clone the demo Java application repository:

   `git clone https://gitlab.com/twn-devops-bootcamp/latest/06-nexus/java-app.git`

2. Navigate into the project directory:
   `cd java-app`
   
### 🏗️ Building the Application
1. Build the application using Gradle:
   `gradle build`

### 🚀 Configuring Nexus Credentials & Uploading the JAR
1. **Set up your Nexus username and password in the** `settings.gradle` file. 
   - In this project, we store the credentials using `repoUser` and `repoPassword` variables for security.
2. Update the repository URL in the `build.gradle` file to ensure Gradle publishes the application to the correct Nexus repository.
3. Using the following command you can push the artifact to the Nexus Maven repository:
   `gradle publish`
         
🎉 You have successfully published your Java application to Nexus! 🚀

---

## ☕ Java Maven - Building & Uploading a JAR File to Nexus Repository

We will use the **login credentials** of the newly created Nexus user to configure **Maven** for pushing artifacts to the repository.

### 📥 Downloading the Demo Java Application

1. Clone the demo Java Maven application repository:

   ```git clone git@gitlab.com:twn-devops-bootcamp/latest/06-nexus/java-maven-app.git```

2. Navigate into the project directory:
   `cd java-maven-app`

### 🏗️ Building the Application

1. Build the application using Maven locally:
   `mvn package`
   
### 🔧 Configuring Nexus Credentials

1. Set up your Nexus username and password in the settings.xml file (typically located in `~/.m2/` on Linux/macOS). 
2. Add the following <server> block inside <servers> in `settings.xml`:

   `<servers>
   <server>
   <id>nexus</id>
   <username>[username]</username>
   <password>[password]</password>
   </server>
   </servers>`

### 🚀 Updating pom.xml for Deployment

1. Open the `pom.xml` file and add the distribution management section:

   `<distributionManagement>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <url>http://[server-public-ip]/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>`

2. Ensure to update the URL to match your remote server IP.

### 📤 Deploying the JAR to Nexus
   
1. Run the following Maven command to publish the artifact:
   `mvn deploy`
  
🎉 Your Java application is now successfully built and published to Nexus using Maven! 🚀

---