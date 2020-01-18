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

**Build Stage Linux** is downloading and executing a Docker container image on a Linux-based Build Agent, **Build Stage Windows** is doing exactly the same on a Windows-based one. This is a simple example but can be used for example to test specific things on different platforms.

> **Note!** Our pipeline is expected to fail as the container image we are using will not work on our Windows-based Build Agent. We'll address that in the next task of our lab.

## 3.3 Conditions

What we saw in the previous task is, that it might be sometimes required to not run all steps within a pipeline or its templates in every stage. Sometimes we need more logic and flexbility.

But how can we control that? This is where conditions come to play.

Conditions can be applied to stages, jobs and individual tasks. Let's now add a condition that our build task is only executed when the build agent runs on linux.

* Goto Pipelines > Pipelines
* Select the new MyDevOpsProject (1) pipeline

> This should be the pipeline we have created in previous task.

Before we proceed let's give it a better name.

* Click on the following button:

![More Options](img/lab3_more_options_button.png)

* Select "Rename/move"
* Call it "Multi-stage pipeline with conditions"

![rename pipeline](img/lab3_rename_to_multistage_with_conditions.png)

* Click "Save"

> Now that our pipeline was renamed let's proceed with editing it.

* Click "Edit"

In our editor we can see that we can only modify the pipeline itself but not it's templates. To edit them we've to go to our repository and modify the files directly.

But before we proceed to our template, let's take a deeper look into the pipeline editor and what we can see here:

![pipeline editor](img/lab3_pipeline_with_templates_recognize_paths.png)

1) Shows us the branch we're working in here it's **master**
2) Is our repository **MyDevOpsProject**
3) Is the path to our pipeline file in our repository
4) Is the name of our template file we want to modify next

> The template uses a relative path from the position of our pipeline file. In our case it's stored in the same folder in our repository.

Let's start..

* Goto Repos > Files
* Make sure that you're in the right repository and branch
* Goto `labs > lab3 > examples`
* Click on `lab3-multistage-jobs.template.yaml`
* Click on Edit

We now want to modify the following task in our template:

```YAML
  - script: docker run vanessa/cowsay run cowthink
```

This is the command that failed in our first run. We now want this task only to run in the linux stage. Not in the windows stage.

To achieve this we're going to add a condition (and a displayname for the task to make it easier to identify it).

```YAML
  - script: |
      docker run vanessa/cowsay run cowthink
    displayName: 'Docker run command'
    condition: eq(variables['System.StageName'], 'linux')
```

> If you're looking for a specific builtin or predefined variable, please have a look on the list of [predefined variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#pipeline-variables) on docs.microsoft.com.

* Click on "Commit"
* Save our changes to the "master" branch
* Click on "Commit" again

* Go back to Pipelines > Pipelines
* Select our "Multi-stage pipeline with conditions"
* Select the last job (it's perhaps still running)
* Click on one of the stages

In the job details you'll now see that our stage that previously failed is now not executed as part of the windows stage anymore. Our condition works.

![skipped task in pipeline](img/lab3_skipped_task_in_pipeline.png)

Let's now go back to the [Overview](/README.md) or continue with [Lab 4](/labs/lab4/lab4.md).