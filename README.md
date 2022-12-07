Automating Application Security Testing in GitHub Actions
This  is designed to help you get started with application security testing in GitHub Actions. 
Main repo for this project is found in the link below
https://github.com/kaakaww/vuln_node_express

Step 1  Setup the server resource for the project
Go to the Code section of your newly forked repository in GitHub. Create a new file using the Add file --> Create new file button. Name the file .github/workflows/build-and-test.yml, and add the following contents:

# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Node.js 14.x
        uses: actions/setup-node@v3
        with:
          node-version: 14
          cache: npm
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
Commit the change.

Go to the Actions section of your repository, and you should see the new workflow running.


You choose the kind of project to practice for Software Scans go for Project A & B and Dynamic Application Scan go for Project C


Project A:  Dependency Scanning with Dependabot
Go to the Settings section of your repo, and find the Code security & analysis section in the left pane.

Enable the Dependency graph, Dependabot alerts, and Dependabot security updates features in this section.

Dependabot is now configured.

Go to the Security section of your GitHub repo, and click into the Dependabot alerts on the left pane. Examine some of the dependency alerts, and see if you can resolve them.


Project B: Static Code Analysis with CodeQL
Step 1: Go to the Security section of your repo. Click on Set up code scanning. Click the big green button to Configure CodeQL alerts.
Step 2: Examine the GitHub Actions workflow, .github/workflows/codeql-analysis.yml, and commit it to the repo.
Step 3: Now go to the Actions section of your repo, and watch your new CodeQL workflow run.
Step 4: When CodeQL has finished, examine the results in the Security section under Code scanning alerts in the left pane.




Project C: Dynamic App Scanning with StackHawk 
Step 1: Sign up for a StackHawk Developer account. When prompted, select Scan My Application. Follow the Get Started flow to create your StackHawk API key and first application.
Step 2: Create a StackHawk API Key
When you first log on to the StackHawk web app, it will prompt you to create and save an API key so that the scanner can send results back to the platform.
Step 3: Stash your new StackHawk API key in GitHub Secrets. In your repo, navigate to the Settings section, and find Secrets → Actions in the left pane.
Add a secret named HAWK_API_KEY, and add your StackHawk API key as the value.
Step 4: Create your First Application in StackHawk
After creating your StackHawk API key, the StackHawk web app will prompt you to create your fist app. Enter the details about your new application using the name, vuln_node_express, an environment of: Development, and a host url of: http://localhost:3000.
For the Application Type, select Dynamic Web Application. And for the API Type, select Other.
Commit the stackhawk.yml Configuration File
Step 5: Copy the contents of the stackhawk.yml file that you created in the Get Started flow in the StackHawk platform. And then paste the contents into a new file at the base of your git repo named stackhawk.yml. Commit the file.

Add a StackHawk Scan to your Build and Test Workflow
Update your Build and Test workflow. Add a step to start the vuln_node_express, and a step to run HawkScan using the StackHawk Action at the end:

# .github/workflows/build-and-test.yml
name: Build and Test
on:
  push: 
    branches:
      - main
  pull_request:
jobs:
  build-and-test:
    name: Build and test
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install Node.js 14.x
        uses: actions/setup-node@v3
        with:
          node-version: 14
          cache: npm
      - name: Install dependencies
        run: npm install
      - name: Run unit tests
        run: npm test
        
        ### NEW STEPS BELOW! ###
        
      - name: Daemonize our Node API service
        run: npm run start &
      - name: Run HawkScan
        uses: stackhawk/hawkscan-action@v2.0.0
        with:
          apiKey: ${{ secrets.HAWK_API_KEY }}
Commit this change.

Check your Scan Results
Go to the Actions section of your repo, and watch your updated Build and Test workflow run. Examine the Run HawkScan step console logs.

Check your scan results on the StackHawk platform.

Set a HawkScan Failure Threshold
To make the HawkScan break your build for high severity alerts, add the following section to the end of your stackhawk.yml configuration file at the root of your repository.

hawk:
  failureThreshold: high
Rerun your GitHub Actions workflow, and check to make sure the scan succeeds and the Action run fails due to your new failure threshold being exceeded. Triage all high severity alerts in the StackHawk platform. After triaging all high severity alerts, you should be able to rerun the Actions workflow, and the workflow should succeed.

Link StackHawk to CodeQL (DAST + SAST)
To pull your GitHub CodeQL SAST findings directly into StackHawk, install the GitHub integration!

From the StackHawk integrations page, click the GitHub CodeQL tile > Enable GitHub, then follow the prompts to install the app to the same GitHub account where you forked the example repo.

Next, connect your GitHub Repository to the StackHawk Application you've been scanning through StackHawk's integration management page.

Rerun your GitHub Actions workflow and notice corresponding vulnerabilities getting linked directly in StackHawk's scan details!

With guide lines from https://github.com/kaakaww/workshop-github-actions#workshop-guidebook-automating-application-security-testing-in-github-actions





























# Vulnerable Node Express

This is a vulnerable Node Express service meant to be used as a target for security testing tools.

## Build and Run

### Install NPM Dependencies
```shell script
npm install
```

### Initialize SQLite DB
```shell
node bootstrapdb.js
```

### Run
```shell script
DEBUG=myapp:* npm start
```

## Build and Run with Docker

### Build Docker Image
```shell script
docker build --tag stackhawk/nodeexpressvulny .
```

### Run Docker Container
```shell script
docker run --rm --publish 3000:3000 --name nodeexpressvulny stackhawk/nodeexpressvulny
```

### Build and Run in Docker Compose
```shell script
docker-compose up --build --detach
```

## Known Vulnerabilities
* SQL Injection via search box. - `item%' union all select * from user; -- ` 
* Cross Site Scripting via search box. - `<script>alert("hey guy");</script>`
