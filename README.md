# REST Error to BPMN Error Example
This example show how you can trigger a BPMN error in Camunda if you don't get the response you're expecting from a REST API that 

## The Process

The process take an owner and reponame and tries to make a rest call to git hub to find the repo. By default the values are

``repoOwner = octocat``
``repoName = hello-world``

It will make the following rest call

``https://api.github.com/repos/octocat/hello-world``

![model]

If the response is 200 OK the process will continue the `Show Repo Details` task any other request type will trigger a BPMN error that move the process to the `Show Error` task. 

## The Code 

A java delegate will make the rest call
```Java

        Unirest.setTimeouts(0, 0);
        HttpResponse<String> response = Unirest.get("https://api.github.com/repos/" + repoOwner +  "/" + repoName)
                .asString();

```

It will then check the response and throw an error if anything but a 200 is returned 

```Java
  if(response.getStatus() != 200){
            throw new BpmnError("NO_REPO_FOUND", "Error making call - Repose Code: "+response.getStatus());
        }else{

            execution.setVariable("repoResponse", response.getStatusText());
        }

```

[model]: ./src/main/resources/static/img/process.png