
# Task 8: Run a Simple Java Maven Build Job in Jenkins

## 🧠 Objective

Automate the build process for a simple Java application using Maven and Jenkins (Freestyle Job). Learn Continuous Integration fundamentals.

---

## 📦 Project Structure

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

## 🔧 Source Code

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

---

## 🚀 Jenkins Setup on RHEL/CentOS

### 1. ✅ Install Jenkins

```bash
sudo yum install -y fontconfig java-21-openjdk wget
java --version

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
sudo yum install -y jenkins

sudo systemctl daemon-reload
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

### 2. 🔒 Grant Docker Access (Optional)

```bash
sudo usermod -aG docker jenkins
newgrp docker
```

---

## ⚙️ Maven & Java Configuration in Jenkins

### 🔧 Maven Setup in Jenkins UI:

1. Go to: `Manage Jenkins` → `Global Tool Configuration`
2. Under **Maven**:

   * Add Maven
   * Name: `maven`
   * Version: `3.9.9` *(can also use stable 3.8.6 if needed)*

### ☕ Java Version:

Java 21 is already installed:

```bash
java --version
openjdk 21.0.7 2025-04-15 LTS
```

---

## 💻 Copy Source Code from Windows to Jenkins Server

### 1. From Windows (CMD):

```cmd
scp -r hello-java-maven root@192.168.70.135:/tmp/
```

### 2. On Jenkins Server:

```bash
mkdir -p /var/lib/jenkins/workspace/task-8
cp -r /tmp/hello-java-maven/* /var/lib/jenkins/workspace/task-8/
sudo chown -R jenkins:jenkins /var/lib/jenkins/workspace/task-8
```

---

## 🛠 Create Jenkins Job (Freestyle)

1. Go to Jenkins UI → New Item → Name: `task-8` → Type: **Freestyle project**
2. In **Build** section:

   * Add build step: `Invoke top-level Maven targets`
   * Maven Version: `maven`
   * Goals: `clean package`

---

## ✅ Run & Verify

Click **Build Now**
Then go to **Console Output** and look for:

```
[INFO] BUILD SUCCESS
```

---

## ✅ Running the JAR Artifact

After successful build, run:
mvn clean package
java -jar target/hello-1.0.jar

Expected output:
```
Hello, Jenkins + Maven!
```

---

## 📸 Deliverable

* Screenshot of the Jenkins console output with `BUILD SUCCESS`
![build-success](/snap/build-success.png)
![jenkins-dir](/snap/jenkins-dir.png)
---

## 🎯 What You Learned

* Jenkins + Maven setup on RHEL
* Creating and compiling a Java app with Maven
* Running Jenkins builds manually
* Troubleshooting Maven and permission issues

---

## 🔗 References

* Jenkins Install Docs: [https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)

---


=======================================
Absolutely! Here's how you can configure a **Jenkins Freestyle Job** to build your **Java Maven "Hello World" project**.

---

## ✅ Goal:

Set up a **Jenkins Freestyle project** to:

1. Pull or use your Maven project (with your current `pom.xml` and source files).
2. Run `mvn clean package` to build it.
3. Optionally archive the `.jar` output.

---

## 🧰 Prerequisites:

Before you start:

* Jenkins is installed and running on your system.
* Jenkins has:

  * ✅ **Maven** installed (Manage Jenkins → Global Tool Configuration)
  * ✅ **JDK** installed (same page)
  * ✅ Your project available either:

    * Locally on the Jenkins machine
    * Or in a Git repository (e.g., GitHub)

---

## 🔧 Step-by-Step: Configure Jenkins Freestyle Job

### ✅ Step 1: Create a New Job

1. Open Jenkins.
2. Click **"New Item"**.
3. Enter a name, e.g., `hello-java-maven`.
4. Select **Freestyle project**.
5. Click **OK**.

---

### 🔧 Step 2: Configure Source Code (if using Git)

If your project is in GitHub or any Git repo:

1. In the job config, go to **Source Code Management**.
2. Select **Git**.
3. Enter the Repository URL (e.g., `https://github.com/your-username/hello-java-maven.git`).
4. Add credentials if needed.

---

### ⚙️ Step 3: Configure Build Environment

Scroll to **Build Environment**, and optionally check:

* `Delete workspace before build starts` — useful for clean builds.

---

### 🛠️ Step 4: Configure Build Step

1. Scroll to **Build** section.
2. Click **Add build step** → **Invoke top-level Maven targets**.
3. Configure:

   * **Goals**: `clean package`
   * **POM**: Leave default (`pom.xml`)
   * **Maven Version**: Select your configured Maven (from Global Tools)

---

### 📦 Step 5: (Optional) Archive the JAR

To save the `.jar` as a build artifact:

1. Scroll to **Post-build Actions**.
2. Click **Add post-build action** → **Archive the artifacts**.
3. In **Files to archive**, put:

```sh
target/*.jar
```

---

### ✅ Step 6: Save and Run

1. Click **Save**.

2. On the job page, click **"Build Now"**.

3. Watch the console output for success:

   ```
   [INFO] BUILD SUCCESS
   ```

4. If you enabled archiving, you can download the `.jar` from the **"Last Successful Artifacts"** section.

---

## ✅ Result

Your Jenkins Freestyle job will:

* Pull or use your Maven project
* Build it with `mvn clean package`
* Create a `.jar` file
* (Optionally) Archive it in Jenkins UI

---

### 🧪 Want to Add Automated Testing?

Let me know if you want to add:

* Unit tests
* Email notifications
* GitHub integration
* Docker container build
* Deployment steps

I'm happy to help configure those as well.
=============
Great question!

You're asking how to run **both Maven build (`mvn clean package`)** and then immediately **run the built `.jar` file (`java -jar target\hello-1.0.jar`)** in a **Jenkins Freestyle job**.

Let me guide you step-by-step.

---

## ✅ Jenkins Freestyle: Run `mvn clean package` + `java -jar target\hello-1.0.jar`

There are **two main ways** to do this in a Freestyle job:

---

### ✅ **Option 1: Use “Execute Windows Batch Command”**

1. In Jenkins, go to your Freestyle job config.
2. Scroll to **Build** section.
3. Click **“Add build step” → “Execute Windows batch command”**
4. Paste this script:

```bat
mvn clean package
java -jar target\hello-1.0.jar
```

> ✅ This works on **Windows agents**, since you're using Windows-style paths like `target\hello-1.0.jar`.

5. Click **Save**.
6. Click **“Build Now”**.

---

### ✅ **Option 2: Use Maven Build Step + Batch Step**

If you want more control or need to separate steps:

#### 🔹 Step 1: Add **"Invoke top-level Maven targets"** build step

* Goals: `clean package`

#### 🔹 Step 2: Add **"Execute Windows batch command"** step:

```bat
java -jar target\hello-1.0.jar
```

> This assumes the build step above has successfully created the `.jar`.

This way:

* Jenkins runs `mvn clean package` first
* Then executes the built `.jar` with `java -jar`

---

### 📦 Optional: Archive the `.jar` file

1. Go to **Post-build Actions** → **Archive the artifacts**
2. Use:

   ```
   target\*.jar
   ```

So you can download the JAR after the job runs.

---

## ✅ Final Result

Your Freestyle job will:

1. Run `mvn clean package` to build the JAR
2. Immediately run the `.jar` file with `java -jar ...`
3. (Optional) Archive the JAR

---

Let me know if:

* You're using **Linux agents** (commands change slightly)
* You want to see the console output in Jenkins from your Java program
* You want to automate input/output or logs

I'm happy to help tailor this further.
