## IBM Cloud Garage Tekton Pipelines

This repository provides Tekton pipelines and tasks for all nodejs and java application templates.

### Get the code

Clone this repo.

```bash
git clone https://github.com/ibm-garage-cloud/ibm-garage-tekton-tasks.git
cd ibm-garage-tekton-tasks
```

## Service account to run Pipeline

If you install Tekton using the OpenShift Pipeline Operator on OCP4, a service account `pipeline` is already created and you can skip the following commands.

Create a service account like `pipeline`
```
oc create serviceaccount pipeline
oc adm policy add-scc-to-user privileged -z pipeline
oc adm policy add-role-to-user edit -z pipeline
```

### Create Pipeline Tasks

IMPORTANT: If Tekton version is lower than `0.7.0` then use the tasks from the `pre-0.7.0` directory.

- Create pipelines tasks for each environment for example the `dev` namespace:

    ```bash
    kubectl create -f ibm-garage-tekton-tasks/pre-0.7.0/tasks/ -n dev
    ```

- If using Tekton version `0.7.0` or greater use this command instead:

    ```bash
    kubectl create -f ibm-garage-tekton-tasks/tasks/ -n dev
    ```

This step will create the following tasks:
- igc-nodejs-tests
- igc-java-gradle-tests
- igc-build-push.yaml
- igc-build-tag-push.yaml
- igc-build-tag-push-ibm.yaml
- igc-deploy
- igc-health-check
- igc-helm-package
- igc-gitops

### Create Pipelines

- Create pipelines for each environment for example the `dev` namespace.

    ```bash
    kubectl create -f ibm-garage-tekton-tasks/pipelines/ -n dev
    ```

This step will create following Pipelines:

- igc-java-gradle
- igc-nodejs

### Manually run a Pipeline

The internal container registry service hostname is different for ocp3 and ocp4 
- ocp3: `docker-registry.default.svc:5000`
- ocp4: `image-registry.openshift-image-registry.svc:5000`

To install the input pipeline resources for the respective application template run the following commands:

For ocp4:
```bash
OCP=ocp4 kubectl apply -f test/resources/$OCP/
```

For ocp3:
```bash
OCP=ocp3 kubectl apply -f test/resources/$OCP/
```

- Run a pipeline for one of the application templates using the Tekton CLI `tkn` and the helper script
    ```bash
    Usage: ./test/scripts/run.sh [nodesjs-typescript | nodejs-react | nodejs-angular | java-spring]
    ```
    For example to run the pipeline for the application template `nodejs-typescript`
    ```bash
    ./test/scripts/run.sh nodesjs-typescript
    ```
    The script will output the name of the pipelinerun, and a command to follow the logs
    ```
    Pipelinerun started: igc-nodejs-run-fqgr7

    In order to track the pipelinerun progress run:
    tkn pipelinerun logs igc-nodejs-run-fqgr7 -f -n dev
    ```

### Create Git Webhook

- Create a Git Webhook on the `dev` namespace using the tekton dashboard.

Now, your pipeline runs whenever the changes are pushed to the repository.
