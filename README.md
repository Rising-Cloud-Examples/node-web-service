# node-web-service
A simple Hello World example using Node.js as a Web App on Rising Cloud. You can find all the instructions to build this example at: https://risingcloud.com/docs/node-web-service

For this example, we will modify the Node.js how to dockerize example program provided by Node.js [here](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/), and run as a Rising Cloud Web Service.

# 1. Install the Rising Cloud Command Line Interface (CLI)
In order to run the Rising Cloud commands in this guide, you will need to install the Rising Cloud Command Line Interface. This program provides you with the utilities to setup your Rising Cloud Task or Rising Cloud Web Service, upload your application to Rising Cloud, setup authentication, and more. If you haven’t already, [click here](/docs/install) to for instructions to install Rising Cloud CLI.

# 2. Login to Rising Cloud Using the CLI
Using a command line console (called terminal on Mac OS X and command prompt on Windows) run the Rising Cloud login command. The interface will request your Rising Cloud email address and password.

```risingcloud login```

# 3. Initialize Your Rising Cloud Web Service

Create a new directory to place your project files in, then open this directory with your command line.

Using the command line in your project directory, run the following command replacing $TASK with your unique task name.

Your unique task name must be at least 12 characters long and consist of only alphanumeric characters and hyphens (-). This task name is unique to all tasks on Rising Cloud. A unique URL will be provided to you for sending jobs to your task.

If a task name is not available, the CLI will return with an error so you can try again.

```risingcloud init -w $TASK_URL```

This creates a **risingcloud.yaml** file in your project directory. This file can be used to configure the build script.

# 4. Configure your Rising Cloud Web Service

Configure your risingcloud.yaml

In the previous step, a risingcloud.yaml file should have generated in your project directory. Open that file and change the port to:

```
port: 8080
```

**Create Your Program**

For this example, we will modify the Node.js dockerization example program provided by Node.js.

Create a new file in your project directory named **package.json**.  Paste the following into your new file and save.

```
{
  "name": "docker_web_app",
  "version": "1.0.0",
  "description": "Node.js on Docker",
  "author": "First Last <first.last@example.com>",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.16.1"
  }
}
```

Within your project directory, run **npm install** to create **package-lock.json**

Create a new file in your project directory named **server.js**.  Paste the following into your new file and save.  Note the change of HOST from 0.0.0.0 in the Node.js example to :: which is required 

```
'use strict';

const express = require('express');

// Constants
const PORT = 8080;
const HOST = '::';

// App
const app = express();
app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

# 6. Create your Dockerfile
Within your project directory, create a new file **Dockerfile** to tell Rising Cloud how to build and run your application.  Within the file paste the following:

```
FROM node:slim
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
CMD [ "node", "server.js" ]
```

Create a new file **.dockerignore** and within it, paste the following:
```
node_modules
npm-debug.log
```

# 6. Build and Deploy Your Rising Cloud Task
Use the push command to push your updated risingcloud.yaml for your Web Service on Rising Cloud.

```risingcloud push```

Use the build command to zip, upload, and build your app on Rising Cloud.

```risingcloud build```

Use the deploy command to deploy your app as soon as the build is complete.  Change $TASK to your unique task name.

```risingcloud deploy $TASK```

Alternatively, you could also use a combination to push, build and deploy all at once.

```risingcloud build -r -d```

Rising Cloud will now build out the infrastructure necessary to run and scale your application including networking, load balancing and DNS.  Allow DNS a few minutes to propogate and then your app will be ready and available to use!

# 6. Use Your Web App

Open your Web Browser and navigate **https://$TASK.risingcloud.app** Change $TASK to your unique task name.

Rising Cloud will take a few moments to spin-up your app, and proces your request.  In the future it will learn from the apps usage patterns to anticipate usage, making instances available in advance of need, while also spinning down instances when not needed.  

You should see within your Web Browser

```Hello world```

Congratulations, you’ve successfully used Node.js as a Web Service on Rising Cloud!
