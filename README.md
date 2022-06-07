# DN6 Simple Web with Auth and Insights

This is a simple template with Auth and Insights for DotNet 6 MVC.  I've also added app configuration and code to integrate Azure App Config and Azure Key Vault.  If you try to use those, make sure to read all the notes in the Program.cs file.  It would also help if you've seen the video from the Opsgility Azure A-Z conference.

## Use at your own risk

We are not responsible for anything you do with this code.

## Set Up the solution

For DevOps, you need an S1 App service with three additional slots to simulate the environment.

You then need a backing database that you can leverage from all of the environments (it's not part of the demo, but needed for the app and additional discussion around migrations, etc).  

You will create deployment yaml files for

- Staging       [merge to main]
- QA            [pr to main]
- Test          [merge to dev]
- Developer     [pr to dev - volatile deployment]

Before running the live demo, I set up everything except the Staging deployment.  I demonstrate getting that from Azure and then updating it to match the others.

## Set up your secrets

For DevOps, you need a lot of secrets

- Publish profiles for each environment
- Azure Credentials generated 
- GitHub PAT with workflow permissions
- Azure Subscription Id
- Azure Resource Group Name
- SonarCloud Token (static code scanning)

## YAML

Use the sample YAML at your discretion.

You will want to update your user secret to map correctly for your publish profile and you will want to update the name of your azure web app and slot for each environment deployment.

## Add additional security scanning and tools

Use additional YAML files to add scanning and tools for your repository
