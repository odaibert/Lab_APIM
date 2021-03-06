THIS PART IS UNDER CONSTRUCTION. LAST UPDATE 06/15/2020.


# API Management - Hands-on Lab Script - part 9

- [Part 1 - Create an API Management instance](apimanagement-1.md) 
- [Part 2 - Developer Portal and Product Management](apimanagement-2.md) 
- [Part 3 - Adding API's](apimanagement-3.md) 
- [Part 4 - Caching and Policy Expressions](apimanagement-4.md) 
- [Part 5 - Versioning and Revisions](apimanagement-5.md) 
- [Part 6 - Analytics and Monitoring](apimanagement-6.md) 
- [Part 7 - Security](apimanagement-7.md) 
- Part 8 - DevOps... this document

Please see [aka.ms/apimdevops](http://aka.ms/apimdevops) for (work in progress) guidance and tools around automating deployment across multiple API Management environments.

# Continuos Integration and Continuos Deployment using Azure DevOps - AzDO

The following instructions demonstrate how to deploy the contents of this example repository using Azure DevOps Repos and Azure DevOps Pipelines.

## Pre-reqs

To run this solution you will need:

- Azure DevOps Account
- An Azure DevOps Repo configured (how to configura an Azure DevOps Repo)
- For this solution, we will use 3 API Management deployed (Dev, QA and Production) 
- Some API(s) on your API Management 

## Architecture

We'll follow the same scenario presented on [README.md] file. 

![alt](.//images/architecture.png){:height="50%" width="50%"}

## The proccess

This is one example how to use Azure DevOps Repo and Azure DevOps Pipelines to enable a Continuos Integration and Continuos Deploiment (CI/CD) for API's on a Azure API Management Service. There's other ways to do this, but this is a "Keep it Simple" to help you start. (Planning to have another version using Github and Github Actions soon).

This is our Development API management. In general, developers will create their API's on one instance of APIM to test it. 

![alt](./images/apim-dev-apis.png){:height="50%" width="50%"}

Once a Developer create and test the API on API Management, it's time to extract these API's using this DevOps Resource Kit. 

To runn it:

<a name="extractor1"></a>

## Running the Extractor
Below are the steps to run the Extractor from the source code:
- Clone this repository and navigate to {path_to_folder}/src/APIM_ARMTemplate/apimtemplate 
- Restore its packages using ```dotnet restore```
- Make sure you have signed in using Azure CLI and have switched to the subscription containing the API Management instance from which the configurations will be extracted. 
```
az login
az account set --subscription <subscription_id>
```
#### Extractor Arguments

You have two choices when specifying your settings:
1. By using a json file with key-values where the keys matches the table below. Use the `extractorConfig` argument:
`extract --extractorConfig c:/temp/extractSettings.json`. [See more examples.](#extractorParameterFileExamples)
2. Pass the arguments on the command line. For instance `extract --sourceApimName my-feature-apim --destinationApimName company-stable-apim --resourceGroup my-feature-rg --fileFolder c:\temp\apim-extract --apiName MyFeatureV1Api`.

For this example we will only use the option #2

So, run the aplication with: 
```
dotnet run extract --sourceApimName <DEV-APIM-NAME> --destinationApimName <DESTINATION-APIM-NAME> --resourceGroup <RESOURCE-GROUP-NAME>  --fileFolder c:\temp\apim-extract.
```
Where: 

> **DEV-APIM-NAME:** API Management where you created your API
> **DESTINATION-APIM-NAME:** It's just a convention to set the DESTINATION-API-NAME in front of the generated files.
> **RESOURCE-GROUP-NAME:** Resource group where the DEV-APIM-NAME is hosted.

After execute the command above, you will see something similar to this:

![alt](./images/extractedapis.png){:height="50%" width="50%"}

Then you see the json files extracted: 

![alt](./images/extractedfiles.png){:height="50%" width="50%"}

Now, push your to your Azure DevOps Repo 

```
git push 
```

Now lets create our Build Pipeline: 

Add these tasks to your build: 
- Get Sources
- Copy Publish Artifacts 
  
![alt](./images/buildpipelinetasks.png){:height="50%" width="50%"}

Configure the fields as show bellow:

![alt](./images/copypublishartifact.png){:height="50%" width="50%"}

Now you have a Build Pipeline which will pull your APIM JSON files and publish it to the Artifact folder on your Azure DevOps. 

![alt](./images/releasepipeline.png){:height="50%" width="50%"}



[Home](README.md) | [Prev](apimanagement-7.md) 
