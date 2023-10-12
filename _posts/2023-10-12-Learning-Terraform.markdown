---
layout: post
title:  "Learning Terraform"
subtitle: "Infrastructure as Code"
date: 2023-10-12 01:15:24 -0800
author: oliver_puffer
categories: Devlogs
tags: Terraform Dendrobium Devlog Django

banner:
  loop: true
  volume: 0.8
  start_at: 8.5
  image: /assets/images/banners/terraform.svg
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
---

## Overview

# Terraform is a proprietary Infrastructure as Code (IaC) tool developed by HashiCorp

Since its inception in 2014, Terraform has been developed and popularized under the Mozilla Public License. As of August 10th 2023, however, future improvements to Terraform ceased to be Open Source [instead falling under the far more restrictive Business Source License][git-commit]. This development has been disheartening to many fans and contributors to the project, but an open source fork of the project exist in the form of the nearly identical [OpenTofu] project.

As things currently stand, this license change primarily impacts companies using Terraform to offer a service that competes with HashiCorp's own Terraform Cloud, and the actual impact to end users has been marginal.

For the purposes of this page, I will be talking about my experience with HashiCorp's Terraform.

## What is Infrastructure as Code (IaC)

Infrastructure as Code is the practice of managing all cloud infrastructure required for an application through a version-controlled file. This file can then be used alongside a tool such as Terraform, to provision all missing cloud resources automatically. This makes it easy to track changes to provisioned infrastructure, and ensure deployed resources match the expectations of all  developers on a team.

I originally worried that this would be a difficult process. But the underlying concept is quite simple. 

For example, Take Deploying a Django App to Heroku, such as Dendrobium.
The steps historically looked something like this:

  1. Download the Heroku CLI
  2. run `heroku create -a dendrobium`
  3. Find out dendrobium is already taken
  4. eventually try to run `heroku create -a dendro`
  5. Try to Remember that the app is named dendro on Heroku
  6. then run `heroku addons:create heroku-postgresql:mini`

But then, lets say your app takes off, and you need more database space for all the new accounts and user data. That $5/mo mini-postgresql database plan isn't going to cut it anymore.

And then when you decide that it is finally time to Scale to a CDN, you repeat the same steps again, but this time with AWS/Azure/digitalocean.

Plus again for every microservice... you get the picture.

Instead, Terraform allows us to describe our infrastructure in .tf files.

## Terraform-ing Dendrobium  
# To get started, I installed Terraform with WSL, and created a `main.tf` file. 

Terraform by itself doesn't know what cloud providers we use, so we need to define it. We can do this in the required_providers section of the Terraform block, defining the Heroku automation tool set, along with the desired version.

```HCL
terraform {
  required_providers {
    heroku = {
      source  = "heroku/heroku" # Heroku's Heroku package
      version = "~> 5.0"  # Version 5.0
    }
  }
}
```

That's it. Terraform now knows how to interface with Heroku. So now we need to define what we want. At this point, we are still in early development, so I only need to provision two Heroku Resources.
  1. A Heroku App configured to use Eco Dynamos
  2. An attached PostgreSQL "mini-tier" database to store all application data.

So we can begin to provision the apps.

First we define a new resource of type "heroku_app" and we name it "Dendrobium-Core". This name is not the Heroku app name, but rather the name we will use within this Terraform document to refer back to this resource. 

We must assign the app name and desired region through a series of key value pairs inside of the "Dendrobium-Core" block. 

For me, that looked something like this:

In `main.tf` :
```HCL
  # Provision the Core Heroku App "Dendrobium-Core"
  resource "heroku_app" "Dendrobium-Core" {
      name = "dendro"
      region = "us"
  }
```

Now would be a good time to check syntax. Luckily Terraform has tf file syntax checker. 

First I run 

`terraform init`

to setup Terraform based on my `main.tf`` file. Now I should be able to execute other Terraform commands including: 

`terraform validate` 

which, if everything was done correctly, should return: 

`Success! The configuration is valid.`

I am not done yet. I still need to attach a database, but if it is anything like provisioning an app, It should be no problem.

In summary, it both is an isn't. First, we can go over what is the same. 

  1. You create a database resource, this is the same as before except instead of an "heroku_app", it is a "heroku_addon" with the name "database"
      
      `resource "heroku_addon" "database" {...}`

  2. I need to Select my database plan, in this case `postgresql:mini`

      ```HCL
        resource "heroku_addon" "database" {
          <...>
          plan   = "heroku-postgresql:mini"
        }
      ```

So far so good. This has been very similar in concept to provisioning a Heroku-App, and ultimately been quite easy. There is one slight twist however. One quirk about Heroku is that databases are provisioned as "add-ons" to an existing app. This means, I need to somehow tell Terraform which Heroku app to attach the database to.

At first I thought maybe there was some way to use the app name, but Terraform was fairly adamant that I should be using an `app_id` value instead. Only one problem. I don't have an app_id. Those are assigned by Heroku after the app is provisioned, and obviously I haven't done that yet as I am using Terraform to provision resources right now.

I considered some naive approaches (ie. What if I create the app, check the portal for the app_id, then hardcode it into Terraform?) but they weren't satisfying, and many would defeat the purpose of Terraform altogether (ie. If I need to redeploy, then I will need to manually update the hardcoded value.)

Thats when I discovered that Terraform lets you dynamically access values (like app_ids) from other resources. You see, I erroneously believed that Terraform wouldn't have access to the app_id, as it is assigned upon provisioning, but that is not entirely true. When you run Terraform, Terraform creates a terraform.tfstate file. This file contains a ton of information about a resource after it is provisioned. I am talking about Heroku assigned hostnames, connected github accounts, organizations, stacks, web urls, even secrets required to deploy successfully, and yes this includes app_ids.

We can access this information with for our Dendrobium-Core Heroku app with the code snippet `heroku_app.Dendrobium-Core.id`

which we can add to our `main.tf` file's heroku_addon configuration block to get:

```HCL
  # Create a database, and configure the app to use it
  resource "heroku_addon" "database" {
    app_id = heroku_app.Dendrobium-Core.id
    plan   = "heroku-postgresql:mini"
  }
```

With both resources defined, we can now run 

`terraform validate` to verify that our syntax is correct

`terraform plan` to double check what Terraform is going to do to provision the resources we defined.

and if everything looks good we can finally run

`terraform apply`

To automatically provision all the infrastructure we need for our app.

Now the Dendrobium Project infrastructure is managed by Terraform!

## TLDR
# Here is the configuration file I finally landed on:
`main.tf`:

```HCL
# Heroku Boilerplate
terraform {
  required_providers {
    heroku = {
      source  = "heroku/heroku"
      version = "~> 5.0"
    }
  }
}

# Provision the Core Heroku App "Dendrobium-Core"
resource "heroku_app" "Dendrobium-Core" {
    name = "dendro" # The name Heroku will use for the App Resource
    region = "us" # The Region I want this App hosted in
}

# Create a database, and configure the app to use it
resource "heroku_addon" "database" {
  app_id = heroku_app.Dendrobium-Core.id
  plan   = "heroku-postgresql:mini"
}

```

# Commands:

```shell
  terraform validate
  terraform plan
  terraform apply
```




[git-commit]: https://github.com/hashicorp/terraform/commit/b145fbcaadf0fa7d0e7040eac641d9aef2a26433
[OpenTofu]: https://opentofu.org/