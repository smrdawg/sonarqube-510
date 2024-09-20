# sonarqube-510
In this Lab you will use SonarQube in a docker container to analyze a vulnerable source code package from GitHub.  We will use Docker Compose to simplify the setup and run SonarQube with a PostgreSQL database.  Then we'll clone a Vunerable code repository and analyze it using SonarQube.

# Step by Step Lab:
## Prerequisites:
* Docker and Docker Compose installed
* Git installed on your system

1. Set up SonarQube with Docker Compose
   First download the docker-compose file from this repository.  Read through it to get a good understanding of how Docker Compose works.  It will set up a SonarQube container, and a PostgreSQL container on your system.  Be sure to put it into a folder for this lab, something like sonarqube-510
2. Start Sonarcube
   Run the following commands in the directory where you've saved the docker-compose.yaml

   
   ```
   docker-compose up -d
   ```

   If you do not have the file named correctly you may need to use the -f flag to specify the file.

   ```
   docker-compose -f <filename> up -d
   ```

   Sonarqube will take a couple of minutes to initialize.  Once it's up, you can access the SonarQube dashboard by visiting http://localhost:9000 in your webbrowser of choice.

   Default login credentials:
   * Username:```admin```
   * Password:```admin```
   You'll be prompted to change the password after logging in for the first time.
3. Clone a Vulnerable Source Code Package
   Now let's clone a vulnerable source code package from GitHub.  We'll use a popular vulnerable project, such as VulnerableApp.
   ```
   git clone https://github.com/SasanLabs/VulnerableApp.git
   cd VulnerableApp
   ```
4. Create a New Project in SonarQube
   1. In the SonarQube dashboard, click on "Create New Project".
   2. Give your project a name, generate a token, and copy the token.
5. Install SonarScanner
   Install the Sonarqube Scanner to analyze the code.  While there is a sonarscanner for windows, lets install it using a docker container.  Go to https://hub.docker.com/r/sonarsource/sonar-scanner-cli and follow the directions for setting up the sonarqube scanner.
   Essentially this is
   ```
   docker pull sonarsource/sonar-scanner-cli
   ```

7. Configure the SonarScanner
   Follow the directions on the link above to configure your project
   Create a sonar-project.properties file in your vulnerableApp directory.  The file should have something like this:

   ```
   sonar.projectKey=vulnerableapp
   sonar.host.url=http://localhost:9000
   sonar.login=your_token_here
   sonar.sources=./src
   sonar.language=java
   sonar.java.binaries=.

   ```
   The sonar.host will probably need to be the ip address of your docker sonar server docker container.  You can get this by running the following command:
   ```
   docker inspect sonarqube
   ```
8.  Run the scanner
   You will want to run the following to perform the scan.
   ```
   docker run \
    --rm \
    -e SONAR_HOST_URL="http://${SONARQUBE_URL}" --network sonarcube_default -e SONAR_SCANNER_OPTS="-Dsonar.projectKey=${YOUR_PROJECT_KEY}" -e SONAR_LOGIN="myAuthenticationToken" \
    -v "${YOUR_REPO}:/usr/src" sonarsource/sonar-scanner-cli
   ```
   ```$(YOUR_REPO)``` will be the folder were you downloaded VulnerableApp, and ```$(YOUR_PROJECT_KEY)``` will be the token  you've created in the gui.

   Any errors you get, look back through your settings to make sure you have everything correctly spelled, and inputed.  When complete you should be able to the completed scan in the gui.

   Research the findings you get.  Document how you would address with your development staff?  How you try to impliment SAST into your developers workflow?

   Go to https://github.com/orgs/vulnerable-apps/repositories to look for other vulnerable apps.  This will help you understand the different type of vulnerabilities out there.  Inspect all of the apps for documentation on what vulnerabilities should be there.  Some of these vulnerabilities can't be found using SAST Tools.  Not all tools will find all vulnerabilites.  Some Vulnerabilities will only be found using DAST as well.
   
