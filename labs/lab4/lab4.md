# Lab 4 - Triggers

Any DevOps lifecycle comprises of bunch of process that run at different stages of the lifecycle consuming and exposing data through various channels. Triggers enable customer to orchestrate the DevOps process in an efficient manner by automating the CI/CD process.

Triggers are events on which you can start your pipeline run automatically. You can enable triggers on your pipeline by subscribing to both internal and external events. An event can be completion of a process, availability of a resource, status update from a service or a timed event.

Lets have a look at how we would work with triggers.

# 4. 1 Working with triggers and branches

**Scenario**

***I would like to trigger my pipeline when a commit is made to a certain branch.**

First lets create a couple of branches to work with.

* Goto Repos > Branches
* Click "New branch"
* Give it a suitable name for example rathishr/feature1
* Click "Create"
* Similarly create another branch for example heyko/feature2

![](img/lab4_create_branches.PNG)

Once done your branches will look similar to the below

![](img/lab4_allbranches_view.PNG)

Next lets create a new pipeline, this time from the repos.

* Goto Repos > Files
* Select one of the feature branch you created above for example **rathishr/feature1**

![](img/lab4_create_new_yml_file.png)

* Give the file a suitable name like "working-with-triggers.yml"

![](img/lab4_new_pipeline_name.png)

* Click "Create"
* Copy paste the below content.

```
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
  branches:
    include:
    - rathishr/*
    exclude:
    - master
    - heyko/*

pool:
  vmImage: 'ubuntu-latest'

steps:
- script: echo Hello, world!
  displayName: 'Run a one-line script'

- script: |
    echo Add other tasks to build, test, and deploy your project.
    echo See https://aka.ms/yaml
  displayName: 'Run a multi-line script'
  ```
  * Click "Commit" twice.
  * Click Pipelines>Pipelines
  * Click New Pipeline
  * Select Azure Repos Git (YAML)
  * Select your repository
  * Select "Existing Azure Pipelines YAML file"
  * Select feature branch "rathishr/feature1" and path as "/working-with-triggers.yml"
  
  ![](img/lab4_select_feature_branch.PNG)
  
  * Click "Continue"
  * We will not run the pipeline yet, instead from the drop click "Save"
  
  ![](img/lab4_save_pipeline01.png)
  
Notice carefully that you are creating a trigger based on  changes made to a branch and inlcuding one branch and excluding two others.

* At this point we are only going to save this pipeline and run later. Hence click on the drop down "Save and Run" and Click on "Save"

![](img/lab4_save_pipeline.png)

* Click "Save" again.
* Go to Pipelines > Pipelines
* Click on "All" to view the pipeline we just created.
* Click and rename the pipeline as done in the previous labs.
* Give it a suitable name like for example "Working with triggers"
* Click "Save"

Next lets put this to action. Lets make a change to our code/content in branch **rathishr/feature1**

* Goto Repos> Files
* Change the branch from "master" to "rathishr/feature1"

![](img/lab4_pick_branch.png)

* Click on the three dots and select **New**>**Folder**

![](img/lab4_create_folder.png)

* Provide a name to the folder and also a file name. Click "Create"

![](img/lab4_create_file_folder01.png)

* Type some sample text on the ReadMe.md edit window and Click "Commit"
* Click "Commit" again! Ensure that you have the correct "branch name" selected.
* Click on Pipelines>Pipelines.

![](img/lab4_running_pipelines.PNG)

Notice how only one of the pipelines gets triggered based on our inlcusion and exclusion rules and other pipelines remain as is.

# 4.2 Working with triggers and path

**Scenario**

***I would like to trigger my pipeline only when a new commit goes into the file path “Repository/Web/*”.**

In this exercise we will see how we can make use of path based triggers to include/exclude certain files from triigering the pipeline. For example when you have docs in wiki, you would need to trigger the pipeline for changes you make to the document files.

Lets see this in action

First lets modify the YAML pipeline.

* Click Pipelines>Pipelines
* Select the "working with triggers" pipeline and click "Edit"
* Replace the "trigger" section with the below code

```
trigger:
  branches:
    include:
    - rathishr/*
    exclude:
    - master
    - heyko/*
  paths:
    include:
    - docs/*
    exclude:
    - docs/sample-file.md
```
Your pipeline should like below

![](img/lab42_sample_file.PNG)

* Click "Save" twice to commit to feature branch.

Now lets put this logic into action. We are going to edit the sampe-file.md which should not trigger the pipeline based on our path based exclusion.

* Click on Repos> Files
* Make sure you are in the right branch which in our case is "rathishr/feature1".
* Create a new folder called "docs" and a file called "sample-file.md"
* Type some sample text and click on "Commit" twice.
* Swith to pipleines view and notice that the "Working with triggers" pipeline does not get triggered based on our exclusion.
* Go back to Repos> Files and ensure the right branch (rathishr/feature1) is selected.
* Create a new file under the docs folder, for example "sample-file02.md". Click "Create"
* Type some sample text and click "commit" twice.
* Notice how the "Working with triggers" pipeline gets triggered.

This concludes Lab 4.
