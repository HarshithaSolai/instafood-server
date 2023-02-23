# Resolving CORS Restriction using Node.js: A Guide for Working with Third-Party APIs

As front-end developers, we may have encountered the CORS (Cross-Origin Resource Sharing) issue when trying to access an API from a different domain. This issue arises when the browser blocks requests made from a different domain, preventing our application from accessing the data we need. However, there is a lot of fixes/solutions to this problem, and we will look into one of the easiest yet efficient solution. It involves using Node.js to create a backend proxy server, which can be used to bypass CORS restrictions by forwarding the client's request to a server that is not subject to the same origin policy. 


In this document, we will see how to use Node.js to create a server that can make API requests without being blocked by CORS restrictions. 

## Problem Statement

When making an API request from a `Instafood` - a hobby react application for online food delivery, to a `Swiggy API` endpoint, the browser is throwing a `CORS` error, which is preventing the application from accessing the data it needs. This CORS error is occurring because the Swiggy API server is **not configured** to allow `cross-origin` requests from the domain where the React application is hosted. As a result, the React application is unable to retrieve the data it needs from the Swiggy API.

Usually when the server is also accessible by us, the problem could be fixed by setting appropriate headers to allows the domain in which our application is running. But, in our case, the Swiggy API is a third-party API, which means that we do not have control over its server configuration. Therefore, we need to find a way to work around the CORS error and retrieve the data from the Swiggy API so that the React application can function properly.

## Possible Solutions 

Following are some of the possible solutions to relax this CORS restriction : 

### 1. Browser Extensions : 

Browser extensions can be used as a `quick` fix for CORS issues in situations where you do not have access to the server-side code. CORS-related browser extensions work by adding the appropriate headers to outgoing requests, effectively bypassing the browser's same-origin policy.

**Example** : Allow CORS: A Chrome extension that adds the necessary CORS headers to requests. 

This solution will only work for the particular browser where the extension is installed, and will not affect requests made from other browsers or applications. It is important to note that using a browser extension to fix CORS issues is not a recommended long-term solution. It is always best to address CORS issues at the server level by properly configuring the CORS headers and allowing requests from trusted domains.

### 2. CORS proxy services : 

There are several CORS proxy services available that act as middleman between the client and the server, adding the necessary CORS headers to the request.

** Example ** : `CORS Anywhere` is a Node.js proxy server that can be used to bypass CORS restrictions by adding the necessary headers to the proxied request. When a request is made to CORS Anywhere, it adds the appropriate CORS headers and forwards the request to the destination API. Once the response is received from the API, the proxy server removes the CORS headers and sends the response back to the requesting client.

Note that using a third-party CORS proxy like CORS Anywhere is not recommended for production use, as it can potentially expose our API key and other sensitive information to the proxy server. It is better to set up our own server and handle CORS headers ourself.

### 3. Server-Side/Backend Proxy :

This the solution that we will be using to fix the CORS restriction. It is simliar to CORS proxy service mentioned above, we will be created our own server/service instead of using the third-party service. 


## Step 1: Set up our environment

Before we get started, make sure you have Node.js installed on our system. If you don't have it installed, head over to the official Node.js website and download the latest stable version.

## Step 2: Create a new project
To get started, create an empty git repository (since we will be deploying the server from github)and clone it in our local machine. Then, run the following command to initialize a new Node.js project:

```bash
npm init
```

This command will create a package.json file that will hold information about our project and its dependencies.

## Step 3: Install the required packages

Next, we need to install the packages required to create our server. Run the following command to install Express, cors, and cross-fetch:

```bash
npm install express cors cross-fetch
```

-  **Express** is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.
- **cors** is a package that allows you to enable CORS with various options.
- **cross-fetch** is a lightweight package that allows us to make HTTP requests It provides a polyfill for the Fetch API in Node.js. 

## Step 4: Create our server

Create a new file in our project directory called `server.js`. This file will contain the code for our server.

### Import the required packages mentioned above and use it 

```javascript
const express = require('express');
const cors = require('cors');
const fetch = require('cross-fetch');

const app = express();
const port = process.env.PORT || 3000;

app.listen(port, () => {
  console.log(`Server is listening on port ${port}`);
});

```

In NodeJS, require() is a `built-in function` to include external modules that exist in separate files. require() statement basically reads a JavaScript file, executes it, and then proceeds to return the export object. One of the major differences between `require()` and `import()` is that require() can be called from anywhere inside the program whereas import() cannot be called conditionally, it always runs at the beginning of the file. Create an express server `app` and create port number variable which takes value from .env file if exists or set value as 3000. Now, the server can be started by using `npm start` and `Server is listening on port 3000` will be printed in console.

### Enabling CORS

```javascript
app.use(cors());
```

`app.use(cors())` - This enables CORS (cross-origin resource sharing), In order for our server to be accessible by other origins (domains).

Calling `use(cors())` will enable the express server to respond to preflight requests.

A `preflight request` is basically an `OPTION` request sent by the browser to the server before the actual request is sent, in order to ask which origin and which request options the server accepts.

So `CORS` are basically a set of headers sent by the server to the browser. Calling cors() with no additional information will set the `default` : our server will be accessible to `any domain` that requests a resource from our server via a browser.

### Create a simple test API Endpoint

```javascript
app.get('/', (req, res) => { 
  res.json({"test":"hello instafood lovers !!! "});
}

```
The above code will create an API endpoint `/`, for which the request can be sent. For eg: To hit the above API, request from browser looks something like `http://localhost:3000/`. This the basic step for creating an API in node.js
It takes two params `req` which contains the request object from the browser and `res` object which is used to send response to the browser.

To send response in json format , `res.json()` is used. So, when `http://localhost:3000/` is requested by browser, `{"test":"hello instafood lovers !!! "}` is sent as response.

### Create API Endpoints

```javascript
app.get('/api/restaurants', (req, res) => {
  const { lat, lng } = req.query;
  console.log(req.query);
  const url = `https://www.swiggy.com/dapi/restaurants/list/v5?lat=${lat}&lng=${lng}&page_type=DESKTOP_WEB_LISTING`;

  fetch(url, {
    headers: {
      'Content-Type': 'application/json',
      'Access-Control-Allow-Origin': '*',
      'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36'
    }
  })
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok');
      }
      return response.json();
    })
    .then(data => {
      res.json(data);
    })
    .catch(error => {
      console.error(error);
      res.status(500).send('An error occurred');
    });
});
```

The above code will create an API endpoint `/api/restaurants`, for which the request can be sent. For eg: To hit the above API, request from browser looks something like `http://localhost:3000/api/restaurants`. 

query - it is the query parameter sent in the URL request. 

In the above code, the lat & lng value are taken for the query params and used while sending request to Swiggy API. 

Now, API call is made to fetch data from swiggy API with API url, headers. When swiggy sends response, it is first checked if the response status is ok, if ok .json() is read the json data from the incoming stream. This json data is then sent it to browser using res.json() as mentioned above. If there is any internal server error, those will be catched in catch block.


Similarly, another API endpoint is created for `/api/menu` 

### Testing API Endpoints 

  Now that we have our Node.js server up and running, we can test it out by making a request to the `/api/restaurants` endpoint with some latitude and longitude values as query parameters. In a web browser, go to `http://localhost:3000/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING`, where lat and lng are the latitude and longitude values of the location you want to search for restaurants. You should see a JSON response with a list of restaurants in the specified location.

### Requesting API from React app

  Now that we have our server up and running, we can expose its API to a React app. In our React app, we can use the fetch API to make a GET request to   `http://localhost:3000/api/restaurants`  or `http://localhost:3000/api/menu` . This will retrieve the data from the Swiggy API via our Node.js server and allow you to use it in our React app.

Here's an example of how you can use the fetch API to make a request to our Node.js server from a React app:

- Using fetch : 
```javascript
fetch('http://localhost:3000/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

- Using async/await : 
```javascript
const response =  await fetch(`http://localhost:3000/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING`)

const data = await response.json();
```

## Step 5: Deploy the server

Once you have tested our server locally, you can deploy it to a production environment.

To deploy our Node.js app on render.com, you can follow these steps:

1. Create an account on https://render.com/ if you haven't already.
2. Click on the `New +` button and select `Web Service` from the dropdown menu.
3. `Connect` to the github repository (node server) which you want to deploy
4. In the `Settings` tab, scroll down to the `Environment Variables` section and add `PORT` environment variables to `3000`.
5. Wait for the deployment to finish. Once it's done, you should see a success message and a link to our server url.
6. Click on the link url to test our server.

Note : Now that our server is deployed you can change the API url in react app to the domain in which the server is deployed. For example : if your server is hosted in `http://instafood.onrender.com`, in react app while sending request to API, use this domain name : 

```javascript
const response =  await fetch(`http://instafood.onrender.com/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING`)

const data = await response.json();
```

## Conclusion : 

In this document, we have discussed the CORS issue that can arise when making requests from a client-side application to a server that is hosted on a different domain. Feel free to reach out to me, if you still have any doubts or need clarifications. 

