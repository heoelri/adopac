# Lab 3 - Working with (YAML) Templates

In this lab we're going to take a deeper look into one of the powerful features of YAML-based pipelines in Azure DevOps. We're going to work with YAML Templates.So first of all let's start with a question.

**Why do i need templates and why should i use them?**

The short answer is YAML-based or pipelines in general can quickly get very complex. There are often times situations where you're doing things multiple times. Think of stages for example. And to avoid duplicating code over and over again we can use templates to make parts of our pipeline re-usable.

> Azure Pipelines supports these four kinds of templates: **Stage**, **Job**, **Step** and **Variable**  
> Templates themselves can include other templates. Azure Pipelines supports a maximum of 50 unique template files in a single pipeline. Go to [docs.microsoft.com](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#template-references) to learn more.

## 3.1 Load Steps from Templates

Let's start with a basic example. We want to extend a new pipeline, like the one we created in [Lab 1](/labs/lab1/lab1.md) and [Lab 2](/labs/lab2/lab2.md), with **steps** from a **template**.

> **Important!** Make sure that you've, as part of [Lab 1](/labs/lab1/lab1.md#11-create-a-yaml-pipeline-via-gui) imported our GitHub repository into your Azure DevOps project.

Let's start..

* Goto Pipelines > Pipelines
* Click "New pipeline"
* Select "Azure Repos Git (YAML)"
* Select our repository
* Click "Starter pipeline"

This will now create new new "Starter pipeline" in our repository.

![New Starter Pipeline](img/lab3_new_starter_pipeline.png)

Let's give it a name that's a bit better that the default `azure-pipelines-1-yml`.

* Click on the filename

![Rename Starter Pipeline](img/lab3_rename_starter_pipeline.png)

Let's use `starter-pipeline-with-template.yml`.

![Renamed Starter Pipeline](img/lab3_renamed_starter_pipeline.png)

Now that we've created a new pipeline and renamed it successfully, let's now reference to a template that contains our additional steps/tasks.

```YAML

```

## 3.2 Implement Stages using Templates

## 3.3 Use Parameters to parameterize templates

