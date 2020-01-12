# Lab 3 - Working with (YAML) Templates

In this lab we're going to take a deeper look into one of the powerful features of YAML-based pipelines in Azure DevOps. We're going to work with YAML Templates. So first of all let's start with a question:

**Why do i need templates and why should i use them?**

The short answer is YAML-based or pipelines in general can quickly get very long and complex. There are often times situations where you're doing things multiple times, think of stages for example, to avoid duplicating code over and over again we can use templates to make parts of our pipeline re-usable.

> Azure Pipelines supports these four kinds of templates: **Stage**, **Job**, **Step** and **Variable**. Templates themselves can include other templates. Azure Pipelines supports a maximum of 50 unique template files in a single pipeline.  
> Go to [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#template-references) to learn more.

## 3.1 Load steps from templates

Let's start with a basic example. We want to extend a new pipeline, like the one we created in [Lab 1](/labs/lab1/lab1.md) and [Lab 2](/labs/lab2/lab2.md), with **steps** stored in a **template**.

> **Important!** Make sure that you have, as part of [Lab 1](/labs/lab1/lab1.md#11-create-a-yaml-pipeline-via-gui), imported our GitHub repository into your Azure DevOps project.

Let us start..

* Goto Pipelines > Pipelines
* Click "New pipeline"
* Select "Azure Repos Git (YAML)"
* Select our repository
* Click "Starter pipeline"

This will now create new new "Starter pipeline" in our repository.

![New Starter Pipeline](img/lab3_new_starter_pipeline.png)

Let us give our new pipeline a name that is a bit better than the default `azure-pipelines-1-yml`.

* Click on the filename

![Rename Starter Pipeline](img/lab3_rename_starter_pipeline.png)

Let us use `starter-pipeline-with-template.yml`.

![Renamed Starter Pipeline](img/lab3_renamed_starter_pipeline.png)

Now that we have created a new pipeline and renamed it successfully, let us now reference to a template that contains our additional steps/tasks.

To achieve this we can now add the following line to the end of our pipeline:

```YAML
- template: labs/lab3/examples/lab3-steps.template.yaml
```

After adding this line to the end of our new pipeline:

* Click "Save and run"
* Select "Commit directly to the master branch"
* Click "Save and run"

Our new pipeline looks pretty simmilar to what we did in the previous labs, with the difference that we are now referencing to another file that contains additional parts used by our pipeline:

![Starter Pipeline with Template](img/lab3_new_starter_pipeline_with_template.png)

In aboves screenshot you can now see three blocks, the first two steps (13-14 and 16-19) from the starter pipeline example and the last one (line 21) that is loading our template.

Our template contains three additional steps we will find in our pipeline logs. Let's now go there and check the logs:

* Goto Pipelines > Pipelines
* Select our new "MyDevOpsProject (1)" pipeline

Before we continue, let's rename the pipeline first:

* Click on the button with the three dots

![More Options](img/lab3_more_options_button.png)

* Select "Rename/move"
* Call it "Basic Pipeline with Template"

![rename pipeline](img/lab3_rename_pipeline.png)

* Click "Save"

Our pipeline was now renamed and is now easier to find.

* Select the last run
* Select the job "Job"

You will now see the additional steps that are coming from our template:

* Docker pull image
* Docker run image with cowthink
* Docker run image with animal

Please have a deeper look into cowthink and animal. You'll find some funny ASCII art in there.

![cowthink](img/lab3_pipeline_output_cowthink.png)

![vader koala](img/lab3_pipeline_output_vaderkoala.png)

Besides the output itself you'll see here that there is no difference between pipeline coded loaded in the main YAML and the code that's coming from templates.

![pipeline output](img/lab3_pipeline_output_numbered.png)

1) Is the part in our main pipeline file.
2) Are the steps coming from the template.

## 3.2 Reusing stages with templates

In our next task we are now going to build a slightly more advanced pipeline with multiple stages using the same template.

* Goto Pipelines > Pipelines
* Click "New pipeline" (top right)
* Select "Azure Repos Git (YAML)"
* Select our "MyDevOpsProject"
* Select "Existing Azure Pipelines YAML file"
* Select the *master* branch

In the *Path* drop down select (or type):

`/labs/lab3/examples/lab3-multistage.pipeline.yaml`

* Click "Continue"

In the **Review your pipeline YAML** dialog you will now see a pipeline that contains two stages:

![Review multi-stage pipeline](img/lab3_review_multistage_pipeline.png)

In its current state the pipeline contains only structure and no useful tasks. To change that we are now going to add code to load a template in each of our stages.

Extend the 'linux' stage with the following code:

```YAML
  jobs:
  - template: lab3-multistage-jobs.template.yaml
    parameters:
      name: 'Linux'
      pool:
        vmImage: 'ubuntu-16.04'
```

And the 'windows' stage with this:

```YAML
  jobs:
  - template: lab3-multistage-jobs.template.yaml
    parameters:
      name: 'Windows'
      pool:
        vmImage: 'vs2017-win2016'
```

Your pipeline should now look like this:

![multistage pipeline with stages](img/lab3_multistage_pipeline_with_templates.png)

* Click "Save and run"
* Select "Commit directly to the master branch"
* Click "Save and run"

The pipeline should now start to run and you will see two stages (Build Stage Linux and Build Stage Windows) that use exactly the same template with different parameters:

![multistage output](img/lab3_multistage_pipeline_output.png)

## 3.3 Parameterize templates
