# Building a CI/CD Pipeline Project: Deploy Flask Application using GitHub Actions and Azure CI/CD Pipelines

### Status Badge

[![Python Flask Application with Github Actions](https://github.com/Syedahmad75/Udacity-Bosch-CICD/actions/workflows/pythonapp.yml/badge.svg)](https://github.com/Syedahmad75/Udacity-Bosch-CICD/actions/workflows/pythonapp.yml)

### Overview
In this project, we build a Github repository from scratch and create a scaffolding that will assist us in performing both Continuous Integration and Continuous Delivery. we use Github Actions along with a Makefile, requirements.txt and application code to perform an initial lint, test, and install cycle. Next, we integrate this project with Azure Pipelines to enable Continuous Delivery to Azure App Service.
This project consists of flask application that is developed to predict housing prices in Boston. This project could be extended to any pre-trained machine learning model, such as those for image recognition and data labeling.

### Project Plan
we created a Trello board for task tracking as well as a spreadsheet with the estimated project plan. 
1. A [Trello](https://trello.com/invite/b/AReh0Xp0/296d484fc3920032b2dc0d1597cb9cf2/ci-cd-pipeline) board has been created to keep track of the tasks.
2. A [Project Planning Spreadsheet](https://diricoshare.blob.core.windows.net/sharex/Project%20Planning.xlsx)spreadsheet has been created to manage the project schedule.
   
### Getting Started
1. Clone this repository 

2. Install the dependencies below

3. Create your own CI-CD Pipeline, follow the instructions below


### Note: The execution is done in our own private subscription and Azure Cloud Shell.


### Dependencies
1. Create an [Azure Account](https://portal.azure.com) 
2. Use [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)
3. Create [Azure Devops Account](https://azure.microsoft.com/en-us/services/devops/?nav=min)
4. Create [Self Hosted Agent in your PC](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser)



### Architectural Diagram
You can find Architectural Diagram below to get the Idea about this project. \
![Architectural Diagram](https://i.imgur.com/kfqlpBH.png)



### Instructions
We assume that, the code is cloned in the Azure Cloud Shell inside Udacity-Bosch-CICD named folder
![GitClone](https://i.imgur.com/CALPMXP.png)
1. Switch to New Directory
2. `cd Udacity-Bosch-CICD`
3. Create a Virtual Environment
4. `python -m venv ~/.Udacity-Bosch-CICD` 
5. Activate the Virtual Environment
6. `source ~/.Udacity-Bosch-CICD/bin/activate`
7. Install dependencies in the virtual environment and run tests using `make all` command:
8. Screenshots showing the passing tests that are displayed after running the `make all` command from the <Makefile>
 ![makeallresult1](https://i.imgur.com/fpPPQ8S.png)
 ![makeallresult2](https://i.imgur.com/ucLLPkm.png)
 ![makeallresult3](https://i.imgur.com/CvQ4htz.png)
9. Uncomment line no 16 and 17 in test_hello.py file to see failed testcases
 ![failed-test-cases](https://i.imgur.com/JENmeae.png)
10. CI: Configure GitHUB Actions
    Enable Github Actions
    Go to your Github Account and enable Github Actions.
11. Replace yml code with scaffolding if it's not already there. 
    ```
    name: Python application test with Github Actions

    on: [push]

    jobs:
     build:

        runs-on: ubuntu-latest

        steps:
        - uses: actions/checkout@v2
        - name: Set up Python 3.7
          uses: actions/setup-python@v1
          with:
            python-version: 3.7
        - name: Install dependencies
        run: |
        make install
        - name: Lint with pylint
        run: |
          make lint
        - name: Test with pytest
          run: |
            make test
    ```
12. GitHub Actions Build Passed
    ![Github Actions](https://i.imgur.com/OZdhAoF.png)
13. Deploy the app to an Azure App Service
    - `az webapp up -n flaskapp-udacity -g FlaskApp-RG`
    ![App Service](https://i.imgur.com/bbnXxkT.png)

14. Now we need to create a Pipeline on Azure Devops. Steps are as follows. 
    1.  Go to https://dev.azure.com and sign in.
    2.  Once you sign in, the browser displays your Azure DevOps dashboard. If more than one organization is listed, select the one you want to use for this tutorial. By default, Azure DevOps creates a new organization using the email alias you used to sign in.
    3.  If your organization doesn't have any projects, enter the project name Flask Pipelines under Create a project to get started, and then select Create project.
    ![Create Project](https://i.imgur.com/5NeyPXS.png)
    4. From your project page left navigation, select Pipelines.\
    ![Create Pipeline](https://i.imgur.com/NlW3WTZ.png)
    5. Select Create Pipeline:\
    ![Create First Pipeline](https://i.imgur.com/DqiIo9l.png)
    6. On the Where is your code screen, select GitHub. You may be prompted to sign into GitHub.\
    ![Connect GitHub](https://i.imgur.com/th4f06p.png)
    7. On the Select a repository screen, select the repository that contains your app, such as your fork of the example app.\
    ![Select Repository](https://i.imgur.com/4e58YgN.png)
    8. You may be prompted to enter your GitHub password again as a confirmation, and then GitHub prompts you to install the Azure Pipelines extension:\
    ![Select Repository](https://i.imgur.com/TTAzJPW.png)\
    On this screen, scroll down to the Repository access section, choose whether to install the extension on all repositories or only selected ones, and then select Approve and install:\
    ![Select Repository](https://i.imgur.com/TTAzJPW.png)
    9. On the Configure your pipeline screen, select Python to Linux Web App on Azure.
    Your new pipeline appears. When prompted, select the Azure subscription in which you created your Web App.

       - Select the Web App
       - Select Validate and configure
    10.  Azure Pipelines creates an azure-pipelines.yml file that defines your CI/CD pipeline as a series of stages, Jobs, and steps, where each step contains the details for different tasks and scripts. Take a look at the pipeline to see what it does. Make sure all the default inputs are appropriate for your code.

15. After Setup the CI-CD Workflow, push the code to check pipeline. Every Push Event will trigger a new CI-CD pipeline and your webapp will update accordingly. 
    ![CICD Workflow](https://i.imgur.com/DkHrwF3.png)

16. Check the Deployment by hitting the URL.In my case https://flaskapp-udacity.azurewebsites.net/. \
    ![AppURL](https://i.imgur.com/yVe7OYe.png)

17. Verify Prdiction with Starter code file `make_predict_azure_app.sh`. (modify with your app name)
    1.  To make a prediction, you have to open a separate tab or terminal window. In this new window, navigate to the main project directory (some computers will do this automatically) and call `./make_prediction.sh` if testing locally, or modify `make_predict_azure_app.sh`.
    2.  This shell script is responsible for sending some input data to your application via the appropriate port. Each numerical value in here represents some feature that is important for determining the price of a house in Boston. The source code is responsible for passing that data through a trained, machine learning model, and giving back a predicted value for the house price.
    3.  In the prediction window, you should see the value of the prediction, and in your main window, where it indicates that your application is running, you should see some log statements print out. You’ll see that it prints out the input payload at multiple steps: when it is JSON and when it’s been converted to a DataFrame and about to be scaled.\
    ![local Prdiction](https://i.imgur.com/LdGDpxC.jpeg)
    1. you can also check on Postman, just add the following URL "https://flaskapp-udacity.azurewebsites.net/predict" and send the POST request to check the results. Make sure you added the model data in the body section. 
    ![Server Prdiction](https://i.imgur.com/jIechB0.png)



### Enhancements
1. Create a UI with a button to predict the outcome. 
2. Custom Autoscale plan can be created for automated scaling based on load. 
![Scaleup](https://i.imgur.com/7RjfeMX.png)
![Scaleup-Custom-autoscale](https://i.imgur.com/PP9am65.png)



### Youtube Video Link
Test Comment


### References

1. [Create a UI with a button to predict the outcome.](https://docs.microsoft.com/azure/app-service/containers/quickstart-python?tabs=bash&WT.mc_id=udacity_learn-wwl) 
2. [Use CI/CD to deploy a Python web app to Azure App Service on Linux](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/python-webapp?view=azure-devops&WT.mc_id=udacity_learn-wwl)
3. [Create a CI/CD pipeline for Python with Azure DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-python?WT.mc_id=udacity_learn-wwl)
4. [Continuous deployment to Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment#option-1-use-app-service-kudu-build-server?WT.mc_id=udacity_learn-wwl)
5. [Flask on Azure App Services](https://docs.microsoft.com/azure/app-service/containers/quickstart-python?tabs=bash&WT.mc_id=udacity_learn-wwl)
6. [Azure Pipelines for Python](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/python?view=azure-devops&WT.mc_id=udacity_learn-wwl)
