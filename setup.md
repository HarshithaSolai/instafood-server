# `Learn React With Harshi 👩🏻‍💻 Series`
   Documenting my learning journey of [Namaste React Live Course](https://learn.namastedev.com/) conducted by Akshay Saini
   
## Steps to create & deploy a Node server :

If you wish to create your own web server using node.js and deploy it in vercel, you can follow the steps below : 

### Creating Node Server 
1. Create GitHub repository 
2. Git Clone with local machine 
3. Install node.js (if not installed already)
4. Initialize a new Node.js project: Run the `npm init` command to initialize a new Node.js project. This will create a package.json file that will hold information about your project and its dependencies.
5. Create .gitignore file with following :
```
  node_modules/
  dist/
  .DS_Store
  .env
```
6. Install the express module: Run the `npm install express` command to install the express module, which will be used to create the server.
7. Install cors : `npm install cors `
8. Install node-fetch (commonJS) or cross-fetch(ESmodules) : `npm install cross-fetch`
9. Create a new file for the server: Create a new file in your project directory called `server.js`.
10. Copy and paste the code in this repo's `server.js` into your server.js file to create the server with fetch API.
11. Start the server: Run the `node server.js` command to start the server. You should see a message in the terminal that says "Server is listening on port 3001" (or whichever port you chose).

(Don't forget to commit & push the code to git)

### Testing Node Server 

12. Test the server: Open a web browser and navigate to `http://localhost:3001/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING`. This should retrieve data from the Swiggy API and display it as a JSON response in the browser.
13. Expose the API to a React app: 

In your React app, you can use the fetch API to make a GET request to `http://localhost:3001/api/restaurants?lat=12.9351929&lng=77.62448069999999&page_type=DESKTOP_WEB_LISTING`. 

For fetching menu items for restaurant, you can use the fetch API in React to make a GET request to `http://localhost:3001/api/menu?lat=12.9351929&lng=77.62448069999999&menuId=`

This will retrieve the data from the Swiggy API via your Node.js server and allow you to use it in your React app.

### Deploying Node Server 

14. Create a Vercel account at https://vercel.com/signup
15. Log in to your Vercel account and click on the "Import Project" button.
16. Select "Import Git Repository".
17. Choose the GitHub repository that you want to deploy.
18. Select the branch you want to deploy.
19. Click "Deploy".
20. 