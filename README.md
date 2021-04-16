# Azure FUnctions: Sample Rest App

## App Endpoints Exposed:
| Method | Endpoint    | Description                   |
|:-------|:------------|:------------------------------|
| GET    | /tasks      | List all tasks                |
| GET    | /tasks/{id} | List details of specific task |
| POST   | /tasks      | Create new task               |

## Schema for Task:
```
{
	"id": "7b2da5e4-0ef9-4339-a8df-ecf2116575bc",
	"task": "Sample todo task"
}
```

## How to create your own functions app project:
1. Create a new directory and enter it. Create a new function app using the following command:
    ```
    func init --worker-runtime custom --docker
    ```

2. Now create a new function from a template using command:
    ```
    func new --name <your-app-name-here> --template "HTTP trigger"
    ```
3. Download or build the binary for your HTTP Trigger app and copy into the directory you created in step 1 and make sure it's executable. If not use the following command:
    ```
    chmod +x <binary-file-name-here>
    ```
4. Add the following script into your project folder with name `start.sh` and also make it executable:
    ```
    #!/usr/bin/env sh
    echo "Starting function..."
    FLOGO_APP_PROPS_OVERRIDE="FUNCTIONS_CUSTOMHANDLER_PORT=${FUNCTIONS_CUSTOMHANDLER_PORT}" ./TasksRestServer-linux_amd64
    ```

    ```
    chmod +x start.sh
    ```

5. Edit your `function.json` to update the default app prefix from `api` to your prefix. For example I'll change it to `hello-world`
    ```
    {
    "bindings": [
        {
        "authLevel": "anonymous",
        "type": "httpTrigger",
        "direction": "in",
        "name": "req",
        "methods": ["get", "post"],
        "route": "hello-world"
        },
        {
        "type": "http",
        "direction": "out",
        "name": "res"
        }
    ]
    }
    ```
6. Edit your `host.json` to look like this:
    ```
    {
    "version": "2.0",
    "logging": {
        "applicationInsights": {
        "samplingSettings": {
            "isEnabled": true,
            "excludedTypes": "Request"
        }
        }
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    },
    "customHandler": {
        "description": {
        "defaultExecutablePath": "start.sh",
        "workingDirectory": "",
        "arguments": []
        },
        "enableForwardingHttpRequest": true
    },
    "extensions": {
        "http": {
        "routePrefix": ""
        }
    }
    }
    ```
7. Finally edit your `Dockerfile` to look like this:
    ```
    # To enable ssh & remote debugging on app service change the base image to the one below
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    COPY . /home/site/wwwroot
    ```
8. You can now test your app locally by running the following command:
    ```
    func start
    ```
    Check if your app works by visiting the URL:
    ```
    http://localhost:7071/tasks
    ```
9. Now follow the instructions here to publish your app to azure: [Publish the project to Azure](https://docs.microsoft.com/en-us/azure/azure-functions/create-first-function-vs-code-other?tabs=go%2Clinux#publish-the-project-to-azure)