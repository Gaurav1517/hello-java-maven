
# Task 8: Run a Simple Java Maven Build Job in Jenkins

##  Objective

Automate the build process for a simple Java application using Maven and Jenkins (Freestyle Job). Learn Continuous Integration fundamentals.

---
## Tool used
* Linux
* Java
* Jenkins
* Maven
* Git
* Github

<a href="https://www.linux.org/" target="_blank" rel="noreferrer"> 
    <img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/linux/linux-original.svg" alt="linux" width="60" height="60"/> 
</a>
<a href="https://www.oracle.com/java/technologies/downloads/" target="_blank" rel="noreferrer">
    <img src="https://www.svgrepo.com/show/452234/java.svg" alt="Java" width="60" height="60"/> 
</a>
<a href="https://www.jenkins.io" target="_blank" rel="noreferrer">
    <img src="https://www.vectorlogo.zone/logos/jenkins/jenkins-icon.svg" alt="jenkins" width="60" height="60"/> 
</a>
<a href="https://maven.apache.org">
  <img src="https://www.svgrepo.com/show/354051/maven.svg" alt="Maven" width="60" height="60">
</a>
<a href="https://git-scm.com">
  <img src="https://www.svgrepo.com/show/452210/git.svg" alt="Git" width="60" height="60">
</a>
<a href="https://github.com">
  <img src="https://www.svgrepo.com/show/475654/github-color.svg" alt="GitHub" width="60" height="60">
</a>


##  Project Structure

```

hello-java-maven/
├── pom.xml
├── readme.md
└── src
    └── main
        └── java
            └── HelloWorld.java
```

---

##  Source Code

### `HelloWorld.java`

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Jenkins + Maven!");
    }
}

```

### `pom.xml`

```xml
 <project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>hello</artifactId>
    <version>1.0</version>

    <build>
        <plugins>
            <!-- Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>

            <!-- JAR Plugin with Main-Class -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.2.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>HelloWorld</mainClass> <!-- Replace with full class name if package exists -->
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

                                                            
```
### Push to GitHub

```bash
git init
git add .
git commit -m "add source code"
git branch -M main
git remote add origin https://github.com/Gaurav1517/hello-java-maven.git
git push -u origin main
```

---

## 1. Launch AWS EC2 Instance

- **Instance Name**: jenkins  
- **OS**: Ubuntu Linux  
- **Instance Type**: t2.micro  
- **Key Pair**: jenkins.pem  
- **Security Group Ports**: 22, 80, 443, 8080  
- **VPC**: Default  
- **User Data Script**: (used for automatic Jenkins installation)

### EC2 User Data Script

```bash
#!/bin/bash
set -e

# Install Java
apt-get update -y
apt-get install openjdk-17-jdk git -y

# Install Jenkins
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | tee /etc/apt/sources.list.d/jenkins.list > /dev/null
apt-get update -y
apt-get install jenkins -y
systemctl enable jenkins
systemctl start jenkins

# Configure swap memory
if swapon --show | grep -q '/swapfile'; then
    echo "Swap file already exists."
else
    fallocate -l 4G /swapfile
    chmod 600 /swapfile
    mkswap /swapfile
    swapon /swapfile
    echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab
fi

# Output Jenkins initial password
cat /var/lib/jenkins/secrets/initialAdminPassword
````

---

## 2. SSH into EC2 Instance

```bash
ssh -i jenkins.pem ubuntu@<EC2-PUBLIC-IP>
```

---

## 3. Access Jenkins in Browser

Open in browser:

```
http://<EC2-PUBLIC-IP>:8080
```

* Enter the initial admin password from:

  ```bash
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```

* Install **Suggested Plugins**

* Create **admin user** with username, password, name, and email

---

## 4. Install Java & Maven (Manual Setup)

Run this script after SSH into the server:

```bash
#!/bin/bash

# Install Java (OpenJDK 17)
apt update
apt install -y openjdk-17-jdk

# Download and install Maven
wget -q https://dlcdn.apache.org/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz
tar -xzf apache-maven-3.9.11-bin.tar.gz
mv apache-maven-3.9.11 /usr/local/maven
ln -sf /usr/local/maven/bin/mvn /usr/local/bin/mvn

# Verify installations
java -version
mvn -version
```
---

## 5. Create Jenkins Freestyle Project

* **Dashboard → New Item → Freestyle Project**
* **Project name**: hello-java-maven
* **Description**: Build and run Maven project
* **GitHub project**: `https://github.com/Gaurav1517/hello-java-maven`

### Source Code Management

* Choose **Git**
* Repository URL: `https://github.com/Gaurav1517/hello-java-maven.git`
* Branch: `*/main`

### Build Step

* Add **Execute Shell**
* Paste the following script:

```bash
echo "Build with Maven"
mvn clean package

echo "Run the JAR"
java -jar target/hello-1.0.jar
```

### Save and Build

* Click **Save**
* Then click **Build Now**
* Open **Console Output** to verify the result

---

## 6. Deliverable

* Screenshot of the Jenkins console output with `BUILD SUCCESS`
- **build-snap** ![build-success](/snap/build-success.png)
- **tools-verify installtation & jenkins workspace** ![tools-verify-and-jenkins-workspace](/snap/tools-verify-and-jenkins-workspace.png)

---

## 7. References

* Java [java download link](https://www.oracle.com/java/technologies/downloads/)
* Jenkins Install Docs: [jenkins insallation](https://www.jenkins.io/doc/book/installing/linux/)
* Maven: [maven download link](https://maven.apache.org/download.cgi)

---
