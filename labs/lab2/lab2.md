# Lab 2 - Tasks, Jobs, Stages and Dependencies

In [Lab 1](/labs/lab1/lab1.md) we have learned how to build a simple pipeline using Azure DevOps. In this lab we are going a bit deeper working with more advanced functionality.

In case you have not finished [Lab 1](/labs/lab1/lab1.md), please go back and finish it first.

## 2.1 Separating Tasks into different Jobs

At the end of [Lab 1](/labs/lab1/lab1.md), our pipeline had three tasks. All of them were executed in a single job. But what is a job?

> **What is a job?**  
> You can organize your pipeline into jobs. Every pipeline has at least one job. A job is a series of steps that run sequentially as a unit. In other words, a job is the smallest unit of work that can be scheduled to run.  
> See [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) for more information.

In our previous lab we have created a pipeline using the wizard. The wizard creates a pipeline with a single job. Like this:

```YAML
pool:
  vmImage: 'ubuntu-16.04'
steps:
- bash: echo "Hello world"
```

This example shows a single job, using Ubuntu 16.04 (Microsoft-hosted agent), containing a single task "bash". We now want to split our previously created pipeline into two jobs.

* Goto "Pipelines" > "Pipelines"
* Select our "MyDevOpsProject" pipeline
* Click "Edit"

We now want to separate the last task into a separate job. To achieve this we're now replacing the existing pipeline with the following one:

```YAML
trigger: none

jobs:
- job: part1
  pool:
    vmImage: 'ubuntu-16.04'
  steps:
  - script: echo Hello, world!
    displayName: 'Run a one-line script'

  - script: |
      echo Add other tasks to build, test, and deploy your project.
      echo See https://aka.ms/yaml
    displayName: 'Run a multi-line script'

- job: part2
  pool:
    vmImage: 'ubuntu-16.04'
  steps:
  - task: Bash@3
    inputs:
      targetType: 'inline'
      script: |
        # Write your commands here
        echo 'Greetings from Seattle!'
        echo 'Variable: $(variable1)'
```

* Click on "Save"
* Select "Create a new branch for this commit"
* Call it "addedjobs"

![Added jobs](img/lab2_pipeline_added_jobs.png)

* Select "Start a pull request"
* Click "Save"

This will now save our changes in a separate branch and create a pull request. That allows us to review our changes.

* Goto "Repos"
* Click "Pull requests" (make sure that you're in the correct repo)
* Select the pull request in "Created by me"
* Click on "Files"

This will now show us the difference between the previous and the modified pipeline:

![Show diff in PR](img/lab2_show_pr_diff.png)

To complete the PR and merge our changes into master you can now click "Complete". And click "Complete merge".

The default setting is to delete your newly created branch after merging. Leave it as it is.

![Merge PR](img/lab2_merge_pr.png)

Merging our PR will **not** automatically start a new pipeline run as we set our trigger to _none_.

Let us start the pipeline manually and take a look:

* Click "Pipelines" > "Pipelines"
* Select our pipeline
* Select the last run

And here you'll already see that our pipeline is now splitted into two different jobs:

![Splitted pipeline run](img/lab2_splitted_pipeline_details.png)

As we haven't specified any dependencies our two jobs can run in parallel and without a specific order. In aboves screenshot part2 is already finished while part1 wasn't even started.

When you click on one of the jobs, you can see more details about the steps and tasks within each of your jobs:

![Job Details](img/lab2_splitted_pipeline_details2.png)

Jobs are a great feature to run tasks in parallel, in a specific order or on different platforms (Windows, Linux, ..).

Before we now proceed with the next task, let's add a dependency to make sure that part1 is executed before part2 in our pipeline.

## 2.2 Adding dependencies between Jobs

Let's go back to our pipeline and add a dependency:

* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Click "Edit"
* Add a new line after line 25 (in my case, see the following screenshot, below  `- job: part2`)

```YAML
- job: part2
  dependsOn: part1
```

> Make sure that dependsOn is aligned with job.

![Dependency between 1 and 2](img/lab2_part1_dependency.png)

* Click "Save"
* Select "Commit directly to the master branch"
* Click "Save"

This will trigger a new run of our pipeline.

* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Select the last run

Here you'll see now, that part1 will be executed first and part2 will start after part1 was successfully finished.

![Part1 first](img/lab2_part1_part2_dependency.png)

## 2.3 Splitting our pipeline into Stages

Now that we had a quick intro into jobs we'll now continiue with stages.

> **What are stages?**  
> You can organize the jobs in your pipeline into stages. Stages are the major divisions in a pipeline: "build this app", "run these tests", and "deploy to pre-production" are good examples of stages. They are a logical boundary in your pipeline at which you can pause the pipeline and perform various checks.  
> Every pipeline has at least one stage even if you do not explicitly define it. Stages may be arranged into a dependency graph: "run this stage before that one".  
> Goto [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/process/stages) to learn more.

A stage can contain one or more jobs. Here's an example how this could look like:

```YAML
stages:
- stage: A
  jobs:
  - job: A1
  - job: A2

- stage: B
  jobs:
  - job: B1
  - job: B2
```

Let's now implement stages in our pipeline.

* Goto "Pipelines" > "Pipelines"
* Select our "MyDevOpsProject" pipeline
* Click "Edit"

Now we're replacing the whole pipeline with the following:

```YAML
trigger:
- master

stages:
- stage: stage1
  jobs:
  - job: stage1part1
    pool:
      vmImage: 'ubuntu-16.04'
    steps:
    - script: echo Hello, world!
      displayName: 'Run a one-line script'
  - job: stage1part2
    pool:
      vmImage: 'ubuntu-16.04'
      steps:
      - script: |
          echo Add other tasks to build, test, and deploy your project.
          echo See https://aka.ms/yaml
        displayName: 'Run a multi-line script'

- stage: stage2
  jobs:
  - job: stage2part1
    pool:
      vmImage: 'ubuntu-16.04'
    steps:
    - task: Bash@3
      inputs:
        targetType: 'inline'
        script: |
          # Write your commands here
          echo 'Greetings from Seattle!'
          echo 'Variable: $(variable1)'
```

* Click "Save"
* Select "Commit directly to the master branch"
* Click "Save"

This will now trigger our pipeline.

* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Select the last run

You'll directly see the first difference. Our pipeline has now two stages:

![Pipeline run with stages](img/lab2_pipeline_run_with_stages.png)

The result will look like this:

![Schema Stages and Jobs](img/lab2_schema_stages_and_jobs.png)

We now have a pipeline that contains two stages:

* Stage 1
* Stage 2

And both stages contain one or more jobs. But there are no dependencies between our jobs and stages. Our jobs can run in parallel and without a specific order and the stages will be executed based on the order they're defined in the YAML file.

Goto [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/process/stages) to learn more about stages.

In our next task we're going to add dependencies to our stages.

## 2.4 Adding Dependencies between Stages

In our previous task we've mentioned that stages will be, by default, executed in the order they're defined in the YAML file.

But there are more ways to control their behavior. In this task we're going to add dependencies to our stages.

Let's go back to our pipeline:

* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Click "Edit"
* Goto **Line 28** (May differ in your case)
* Add a `dependsOn`

> Make sure that dependsOn is aligned with job.

```YAML
- stage: stage2
  dependsOn: stage1
  jobs:
```

This will define what's already defined due to the order of our stages. Stage2 will now depend on Stage1 and will not be executed before Stage1 was successfully finished.

To make it a bit more interesting, let's now add a third stage that depends on Stage2.

* Goto the end of our pipeline
* Add the following code

```YAML
- stage: stage3
  dependsOn: stage2
  jobs:
  - job: stage3job1
```

And let's also add a forth stage that depends on stage1:

```YAML
- stage: stage4
  dependsOn: stage1
  jobs:
  - job: stage4job1
```

* Click "Save"
* Select "Commit directly to the master branch"
* Click "Save"
* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Select the last run

Our pipeline looks slightly different now:

![Four Stage Pipeline](img/lab2_four_stage_pipeline.png)

As you can see in our example, our pipelines can become as complex as you want. In case simple dependencies are not enough there are also other options to run stages in parallel or to depend on multiple stages.

If you want to run a stage in parallel you can specify this directly:

```YAML
- stage: TestStage
  dependsOn: []    # this removes the implicit dependency on previous stage and causes this to run in parallel
  jobs:
  - job:
    ...
```

If you want your pipeline to depend on multiple stages, you can specify them as a list:

```YAML
- stage: DeployEurope
  dependsOn:         # this stage runs after DeployUS1 and DeployUS2
  - DeployUS1
  - DeployUS2
```

Let's do a last change in our pipline to close the loop and to bring the stages back together as part of an additional, last stage:

* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Click "Edit"
* Goto the end of our pipeline
* Add the following code

```YAML
- stage: stage5
  dependsOn:
  - stage4
  - stage3
  jobs:
  - job: stage5job1
```

* Click "Save"
* Select "Commit directly to the master branch"
* Click "Save"
* Goto "Pipelines" > "Pipelines"
* Select our pipeline
* Select the last run

![Five Stages Closed Loop](img/lab2_five_stages_closed_loop.png)

Our last stage **stage5** will be executed after **stage3** and **stage4** were executed successfully.

You can also see the jobs to stage correlation including time take for each job to complete by clicking on **Jobs** as show below.

![job status overview](img/lab2_job_to_stage_duration.PNG)

## 2.5 Approvals

There are several scenarios where an additional approval is needed before a deployment into a specific stage or environment can be started. In this task we'll exted our pipeline to require a manual approval before stage 5 will be executed.

* Goto Pipelines > Pipelines
* Select our pipeline
* Click on "Edit"

To define approvals we've to introduce environments first. Environments are additional properties that are only available in deployment jobs.

> **Environments**  
> The environment keyword specifies the environment or its resource that is targeted by a deployment job of the pipeline. An environment also holds information about the deployment strategy for running the steps defined inside the job. Goto [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#environment) for more information.

Currently our Stage 5 looks like this:

![stage 5 current](img/lab2_pipeline_stage5.png)

Let's now modify Stage 5 to look like this:

![stage 5 new](img/lab2_pipeline_stage5_new.png)

Here's the code:

```YAML
- stage: stage5
  dependsOn:
  - stage4
  - stage3
  jobs:
  - deployment: stage5job1
    environment: production
```

* Click "Save" (twice)
* Click "Run" (twice)

A few minutes later, all our stages are completed.

![all stages finished](img/lab2_finished_all_stages.png)

But where's our approval? What happened in the background is that Azure DevOps has now created the environment "production". As defined in our deployment job **stage5job1**.

* Goto Pipelines > Environments

You'll see here our new environment **production**.

![new environment](img/lab2_new_environment_production.png)

* Click on environment "production"
* Click on the three dots (top right)
* Click on "Approvals and checks"

![approvals and checks](img/lab2_approvals_and_checks.png)

* Select "Approvals"

![add your first approval](img/lab2_define_an_approval.png)

This will take you to the "Approvals" dialog.

![approvals dialog](img/lab2_approvals_dialog.png)

* Select yourself als "Approver" (in real world deployments you'll probably use a group)
* (optional) Insert some instructions
* Click on "Create"

Let's now go back to our pipeline to check if it now requires an additional approval.

* Goto Pipelines > Pipelines
* Select our pipeline
* Click "Run pipeline"
* Click "Run"

After finishing all previous stages, you should see that stage5 is now in "Waiting" state, waiting for an approval.

![waiting for approval](img/lab2_stage5_waiting_for_approval.png)

* Click "Review"
* Click "Reject" or "Approve"

Let's now go back to the [Overview](/README.md) or continue with [Lab 3](/labs/lab3/lab3.md).
