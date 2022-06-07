# Azure CLI

There are two main ways you can write imperative commands against Azure.  The first is by using the Azure CLI.  The Azure CLI can be run in either a BASH terminal, or in PowerShell.

For the purposes of this document, consider using BASH as the execution environment of choice.

## Working in the Azure Cloud Shell

The Azure cloud shell is available on any browser from the portal.  When you click on the shell button at the top center of the portal next to the search, you will get a 1/2 page shell terminal.  You can choose BASH or PowerShell, and you can switch between them.

If you would prefer, typing `https://shell.azure.com` will open a cloud shell in an entire browser.

By default, the Azure CLI should be available in the cloud shell.  For this demo, if using the cloud shell, ensure you select `Bash`.

## Getting the Azure CLI

In order to work with the Azure CLI on your local machine in a terminal or in PowerShell, you will need to first get it installed.  To get the Azure CLI, [follow these instructions](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?WT.mc_id=AZ-MVP-5004334).

With the Azure CLI working locally, you can easily run the commands from within your local terminal of choice.

## Getting Connected

If you don't use the Azure Cloud Shell and you want to run locally, you will need to get connected locally.  To do this with the Azure CLI, use the following command:

```bash
az login
```

You will be directed to a new screen to log in and then you will be connected in your local BASH terminal.

>**Reminder**: You will **not** need to log in to use the Azure CLI from the cloud shell.  This only applies if working locally from your machine.

## Using the Azure CLI to set your subscription

If you have one subscription, this part will not be useful.  However, if you have multiple subscriptions, it is incredibly important to ensure you are in the correct subscription.

First, run the command:

```bash
az account show
```

This command will ensure you see the active subscription.  If you are in the correct subscription, you are done.

If you are in the wrong subscription, you can list all of your subscriptions with the command:

```bash
az account list
```

When you see this list, find your subscription and then either copy the subscription id or the name of the subscription.

Set your active subscription with the following command:

```bash
az account set --subscription <subscription-id-or-name-here>
```

Run the first command one last time:

```bash
az account show
```

The correct subscription should be listed.

## Create a resource group

In order to get started with the commands, you'll use imperative commands to create a resource group.  In the next section, you'll use the commands to deploy a free-tier azure app service.  If you want, you can look into deploying a VM or a VNet or any other azure resource of your choice instead.

To create a resource group, you need two variables.  The first is the name of the group, and the second is the location.  Set the variables with the following commands:

```bash
rg=DevUp_RuggedDevOps
loc=centralus
```

Feel free to choose a different region, such as `eastus`, `westus`, `uksouth`, `eastasia`, etc.  You can see all regions with this command: 

```bash
az account list-locations -o table
```

Once you have the location and group name set, run the following command to create the resource group

```bash
az group create --resource-group $rg --location $loc
```

Alternatively, a short-hand version of this is available:

```bash
az group create -n $rg -l $loc
```

Either way, you should get a notification that your group provisioning has succeeded.  You can then review the portal to find the group.

Get the group with the cli:

```bash
az group show -g $rg
```

## Deploy the azure app service to your resource group

With the group provisioned, you can create a free-tier azure app service, but you'll need to run a couple of commands.  First, you need to set some variables (replace yyyymmdd with a date and xyz with your initials).  

### Set the variables 

Use the following variables in these commands: 

```bash
rg=DevUp_RuggedDevOps
loc=centralus
planName=asp-devupRuggedDevOps
sku=S1
siteName=simple-web-20220607-blg
```

### Create the app service plan

```bash
az appservice plan create -g $rg -n $planName --sku $sku
```

After a few seconds, your plan will be provisioned.  You can review the portal to see the app service plan in your resource group.  Alternatively, you can find the plan with an az cli command:

```bash
az appservice plan list --resource-group $rg
```

If you don't see your plan, something went wrong, try the first create step again.

### Create the app service

To complete this activity, create an app service with the following command:

```bash
az webapp create --name $siteName --plan $planName --resource-group $rg --https-only true
```

This will take a few minutes to complete.  Once completed, You can browse the portal and hit the overview tab or you can get the public-facing url of your site with the following command:

```bash
az webapp config hostname list --resource-group $rg --webapp-name $siteName --query [].name -o tsv
```

With the public-facing url, you can browse to the site, and it will be up but there will just be a default page showing it's working.

### Create Test, Staging, and QA slots

Create the environment slots for deploying your app through the pipeline

```bash
az webapp deployment slot create --name $siteName --resource-group $rg --slot 'Staging'
az webapp deployment slot create --name $siteName --resource-group $rg --slot 'QA'
az webapp deployment slot create --name $siteName --resource-group $rg --slot 'Test'
```  

Create the database server and simplest db possible
Add the connection information to each of the slots to share the simple backing db.
