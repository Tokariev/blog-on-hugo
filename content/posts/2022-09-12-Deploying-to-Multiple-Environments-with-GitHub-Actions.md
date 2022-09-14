---
title: "Deploying to Multiple Environments With GitHub Actions"
date: 2022-09-12T20:20:20+02:00
tags:
    - notes
keywords:
    - git
    - actions
---

Original [articel](https://dylanwooters.wordpress.com/2021/10/08/deploying-to-multiple-environments-with-github-actions/) about deploymen.


# Deploying to Multiple Environments with GitHub Actions

Recently I used GitHub Actions to facilitate automated deployments for a new app. I was impressed overall with GitHub’s new CI offering. It was fairly simple to configure, and so far it hasn’t cost anything, even with a private repo. I also use a cool little Slack integration to notify the team of new deployments.

Initially I configured the GitHub Action in a simple way. When the team pushed to master, it deployed to our dev environment. However, after the app went live, we needed a way to push to both the dev and production environments, using separate branches. To my surprise, there were no clear tutorials on the web for how to set this up. I eventually figured it out by combining information from multiple blog posts and a YouTube video.

In this post, I’ll walk you through configuring GitHub Actions to deploy to multiple self-hosted environments. This way you can push to a dev or QA environment for testing and verification of changes, and then to a production environment once everything is working well.

## Installing the runner

First you will want to create a “runner.” A runner is essentially a service that listens for push events in your repo. GitHub offers two types of runners: Github-hosted runners (which run on Github’s servers), and self-hosted runners. Since we used AWS to host our app, we used self-hosted runners.

You can create a self-hosted runner in GitHub by going to your repo > Settings > Actions > Runners. Click on the New Self-hosted Runner button and it will walk you through the process of downloading and configuring the runner on your target environment.

_Note: If you’d like to share a runner with multiple repos, create the runner on the Organization level instead._

## Configuring the runner as a service

The instructions that GitHub provides show you how to run the service directly in the terminal.   
![Script](/run_sh.png)  
You most likely won’t want to do this, however, since once you exit the terminal, the runner will stop. Instead, you’ll want to configure it as a background service, using these instructions. Note that all the commands in the instructions should be run within the actions-runner folder that you created when downloading the runner.

## Repeat as needed for each target environment

Repeat the steps above for each environment/server you’d like to deploy to (dev, prod, etc.).

## Adding tags to the runners

Once the runners are created for all environments, go to your repo > Settings > Actions > Runners. Click on each runner, and add a label with your environment name (i.e., development, production, etc.). This label will be used to match a push event to a specific environment. 
![Runner_tag](/runner_tag.png)  

## Create the yaml file

Next, you’ll create a yaml file with instructions on when and how to deploy. Go to your repo page and click on the Actions tab at the top, then click on “Set up a workflow yourself.”

![Script](/yaml_file.png)  
You’ll then be dropped into the GitHub editor where you can create your yaml file. Here is the file I used to deploy to separate dev and production environments. Our app was built using Nuxt, so the run commands use Node and pm2. You’ll want to change this of course, based on your specific build steps and requirements.

```sh
name: Deploy to server

on:
  push:
    branches: [ "dev", "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  dev_deploy:
    # The type of runner that the job will run on
    runs-on: [self-hosted, development]
    # look at the ref and only run this job if the branch that triggered the workflow is "dev"
    if: endswith(github.ref, 'dev')
    # Steps represent a sequence of tasks that will be executed as part of the job
    strategy:
      matrix:
        node-version: [14.x]
       
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: cp ~/skimpel-api/production.env ~/skimpel-api/_work/skimpel-api/skimpel-api
    - run: npm run build:prod
    - run: pm2 restart skimpel-api

  prod_deploy:
    runs-on: [self-hosted, production]
    # look at the ref and only run this job if the branch that triggered the workflow is "dev"
    if: endswith(github.ref, 'master')
    # Steps represent a sequence of tasks that will be executed as part of the job
    strategy:
      matrix:
        node-version: [14.x]
       
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: cp ~/skimpel-api/production.env ~/skimpel-api/_work/skimpel-api/skimpel-api
    - run: npm run build:prod
    - run: pm2 restart skimpel-api
```

A few important things to note about the above yaml:

1. The “on” section is watching for push events to two branches: dev and master
2. There are two deploy jobs (dev_deploy and prod_deploy).
3. The “runs-on” section use the runner label created above to target the correct runner and environment.
4. The “if” section uses the endswith function to apply conditional logic for the job based on the branch name from github.ref.
5. The final job, notify, used the Slack integration mentioned above, and is totally optional. Notice the needs section, which ensures that the notification is sent for a production deployment only.

In a nutshell, the runs-on section targets the environment based on the runner label, and the if section ensures that only pushes to a specific branch get deployed to that environment. For example, a push to the dev branch will get deployed to the dev environment, via the runner with the “development” label.

## Finishing up

Once the yaml file is ready, go ahead and commit it to master, and the GitHub Actions deployments will automatically start. You can view the deployments, including details on each run, in the Actions tab.

Also, the specific folder that GitHub uses for deployments on the target server is actions-runner/_work. Use that folder for hosting your app, or to troubleshoot issues in the code.

Let me know if you have any questions or problems using the comments below.