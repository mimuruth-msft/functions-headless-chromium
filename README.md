# functions-headless-chromium
# Running headless Chromium in Azure Functions with Puppeteer

There was an update to Azure Functions, it is now possible to run headless Chromium in the Azure Function Consumption plan for Linux plan. This enables some serverless browser automation scenarios using popular frameworks such as Puppeteer.

The necessary dependencies to run headless Chromium were added to the Azure Functions Linux Consumption environment. This means that we can simply npm install Puppeteer in a Node.js function app to start using one of those frameworks to interact with Chromium.

It's pretty straightforward to run Puppeteer as we use npm to install it. Note that, because it is needed at run-time, we should install the package as a production dependency. 
In this examples, we use Puppeteer/Playwright with headless Chromium in an HTTP triggered function to open a web page and return a screenshot.

#Puppeteer
// also installs and uses Chromium by default
npm install puppeteer

For the example, when we run the function app locally, browse to http://localhost:7071/api/screenshot?url=https://bing.com/, we get back a screenshot of the page. After publishing to Azure App Service, you can used https://<sitename>.azurewebsites.net/api/screenshot

#Deploy to Azure
Since we're deploying to a Linux environment, we have to make sure we run npm install in Linux so it downloads a version of Chromium that matches the deployment target. Because Azure Functions supports remote build so that the app is built in the correct Linux environment during deployment.

By default, the Azure Functions VS Code extension will deploy the app using local build, which means it'll run npm install locally and deploy the app package. For remote build, we update the app's .vscode/settings.json to enable scmDoBuildDuringDeployment.
And because you are running npm install remotely, we can add node_modules to .funcignore. This excludes the node_modules folder from the deployment package to make the upload as small as possible.

When publishing the app, if using VS Code, we can use the Azure Functions: Deploy to Function App... command to publish the app and it will recognize the app settings scmDoBuildDuringDeployment configured and use remote build. If using Azure Functions Core Tools, we need to run the command with the --build remote flag:

func azure functionapp publish $appName --build remote

