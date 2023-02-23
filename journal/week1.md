# Week 1 — App Containerization
## Benefits of App Containerization
    - Makes your application more portable: other developers can pick up your code and work with them on the go as opposed to configuring the setup
    - Allows you to be closer to the environment you are deploying
    - In Dev/Test environments, containers allow you to keep the environment maintained and free from errors. You may have different variants of OS, modules and configs

## Step 1: Download the Docker extention on VS Code
- Create a Dockerfile
- Template can be found in the repo [repo](https://github.com/omenking/aws-bootcamp-cruddur-2023/blob/week-1/journal/week1.md)
- Try to run the python command, if you havent set the env vars, you should get a 404 error
- Set env vars by doing the following:
    - ```export FRONTEND_URL="*" ```
    - ```export BACKEND_URL="*" ```
- After setting the env vars, run the python command & add ```sh /api/activities/home``` to the end of the URL
    - Terminal Output -> ![Terminal Output](../backend-flask/img/output.png)
    - Browser Output -> ![Browser Output](../backend-flask/img/Browser%20Output.png)
    Ctrl + C to kill server