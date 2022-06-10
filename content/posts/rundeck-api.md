+++
title = "Rundeck and Terraform"
date = "2020-09-12"
author = "Mark"
cover = "/img/timothy-eberly-yuiJO6bvHi4-unsplash.jpg"
tags = ["Rundeck", "API", "Terraform"]
keywords = ["Rundeck", "API", "Terraform"]
description = "Interact with Rundeck via it's API"
showFullContent = false
+++

# Intro

Rundeck is an opensource project; it allows you to turn scripts and playbooks into self service jobs. It has a [full featured API](https://docs.rundeck.com/docs/api/rundeck-api.html) but there isn't much online about using it. This post will provide a few examples of using it to interact with Rundeck projects, their settings and jobs.

We'll also briefly go over using [Terraform](https://www.terraform.io/) to manage project life cycles and project settings.

All code is a available in a [github repository](https://github.com/markopolo123/rundeck-terraform-api).

# Prerequisites
* Rundeck, with an user and valid auth token.
* curl - for poking rundeck
* jq - for manipulating JSON
* Terraform - for creating projects

# Create a Rundeck project with Terraform

In Rundeck a project is the container object for a set of jobs and the configuration for which servers those jobs can be run on.

The [Terraform Rundeck Provider](https://www.terraform.io/docs/providers/rundeck/index.html) supports managing the following objects:

* rundeck_acl_policy
* rundeck_job
* rundeck_project
* rundeck_private_key
* rundeck_public_key

## Specifying Ansible as a resource model
Each Rundeck project can have it's own inventory of hosts. You may also use Ansible inventories and `ansible_facts` as a source of truth for your Rundeck project inventories.

In this example we are adding two resource models to a project:

* local
* Ansible

```bash expand="Show"
resource "rundeck_project" "anvils" {
  name        = "anvils"
  description = "Application for managing Anvils"

  ssh_key_storage_path = "path-here"
  resource_model_source {
    type = "local"
    config = {

    }
  }

  resource_model_source {
    type = "com.batix.rundeck.plugins.AnsibleResourceModelSourceFactory"

    config = {
      format                   = "resourcexml"
      ansible-gather-facts     = "true"
      ansible-ignore-errors    = "true"
      ansible-config-file-path = "/ansible/config/path"
      ansible-inventory        = "/ansible/inventory/path"
    }
  }
}
```

## Adding config not explictly supported by the provider

Adding extra config not specifically supported by the provider is pretty easy, thanks to the `extra_config` option.

From the docs:

> `extra_config` - (Optional) Behind the scenes a Rundeck project is really an arbitrary set of key/value pairs. This map argument allows setting any configuration properties that aren't explicitly supported by the other arguments described above, but due to limitations in Terraform the key names must be written with slashes in place of dots. Do not use this argument to set properties that the above arguments set, or undefined behavior will result.

Here's an example, setting the default group expansion setting:

```bash
resource "rundeck_project" "anvils" {
  name        = "anvils"
  description = "Application for managing Anvils"

  #ssh_key_storage_path = rundeck_private_key.anvils.path
  resource_model_source {
    type = "local"
    config = {

    }
  }
  extra_config = {
    "project/jobs/gui/groupExpandLevel" = "1"
  }
}

```

# Rundeck API Examples
In these examples I am passing my Rundeck authentication token and URL through to curl as environment variables.

```bash
export RUNDECK_TOKEN=token-here
export RUNDECK_URL=https://rundeck.url.here
```

We'll start with reading (GET) a few things from Rundeck then move on to POSTing changes to Rundeck. Note the API allows you to work with `XML` or `JSON`. We'll be using `JSON`.

## Listing Projects

```bash
curl -s -X GET "${RUNDECK_URL}/api/30/projects?authtoken=${RUNDECK_TOKEN}" -H "Accept: application/json"
```
This will return you a list of projects, along with some information about each one:

```bash
[
  {
    "url": "${RUNDECK_URL}/api/34/project/anvils",
    "name": "anvils",
    "description": "this is the description",
    "label": "your label"
  }
]

```
Now let's pipe this output to `jq` and filter the result to just show project names:

```bash
curl -s -X GET "${RUNDECK_URL}/api/34/projects?authtoken=${RUNDECK_TOKEN}" -H "Accept: application/json" | jq '.[]| .name'
"anvils"
"anvils2"
```

## Importing jobs from a git repository


### Adding import settings

Note we are using key based auth in the example below:

```bash
 curl -X POST "${RUNDECK_URL}/api/30/project/anvils/scm/import/plugin/git-import/setup?authtoken=${RUNDECK_TOKEN}" -H "Content-Type: application/json" --data '{"config": {
    "url": "git@gitlab.com:rundeck-job-template.git",
    "fetchAutomatically": "true",
    "pullAutomatically": "true",
    "dir": "/var/rundeck/projects/anvils/scm",
    "filePattern": ".*.yaml",
    "importUuidBehavior": "remove",
    "useFilePattern": "true",
    "strictHostKeyChecking": "yes",
    "sshPrivateKeyPath": "keys/rundeck-private-key",
    "format": "yaml",
    "branch": "master",
    "gitPasswordPath": "",
    "pathTemplate": "${job.group}${job.name}-${job.id}.${config.format}"
  },
  "enabled": true,
  "integration": "import",
  "project": "anvils",
  "type": "git-import"
}'

```
### Importing jobs

Once you've added git import settings to a project you'll likely want to import jobs. You could do this manually, or you could run a script similar to the below:

```bash
# Function to build post data
# We are picking up any jobs which have the status  `IMPORT_NEEDED`

generate_post_data() {
# Get list of jobs to import
  ITEMS=$(curl -s -X GET "${RUNDECK_URL}/api/30/project/anvils/scm/import/action/import-all/input?authtoken=${RUNDECK_TOKEN}" -H "Accept: application/json" | jq '[.importItems[] | select(.status == "IMPORT_NEEDED") | .itemId]')
  cat <<EOF
{"input":{
"message":"\$commitMessage"
},
"jobs":[],
"items":${ITEMS},
"deleted":[]
}
}
EOF
}

# Lets import our jobs
curl -X POST "${RUNDECK_URL}/api/30/project/anvils/scm/import/action/import-all?authtoken=${RUNDECK_TOKEN}" -H "Content-Type: application/json" --data "$(generate_post_data)"

```

In the example above we have a `BASH` function which uses `curl` and `jq` to extract a list of jobs which have the status `IMPORT_NEEDED`; the output of which is then feed into the last `curl` command.

### Getting a list of jobs which need importing for a project

```bash
curl -s -X GET "${RUNDECK_URL}/api/30/project/anvils/scm/import/action/import-all/input?authtoken=${RUNDECK_TOKEN}" -H "Accept: application/json" | jq '[.importItems[] | select(.status == "IMPORT_NEEDED") | .itemId]'
"12345-789-abc"
"12345-789-abc"
"12345-789-abc"
"12345-789-abc"
"12345-789-abc"
"12345-789-abc"
"12345-789-abc"
```