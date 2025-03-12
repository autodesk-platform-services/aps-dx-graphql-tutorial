---
layout: page
title: Exchange creation from designs
nav_order: 5
has_children: false
permalink: /mutation/home/
---

# Create exchanges from designs

The creation of the exchanges using Data Exchange GraphQL API can be made through a mutation call that is generic enough for all formats but takes into account some particularities. A very good example is creation of an exchange from Revit file compared to creating it from AEC Data Model. A Revit model will contain views and when creating an exchange you have to specify the View from which you would like to create the exchange. In case of AEC Data Model, you can specify the Categories, Family and Type of components that should be included into the exchange. 

## Creating exchanges from Revit file


Using the new mutation API is very simple and in case of Revit model, it requires just:

***id of the Revit file*** - which can be found when query the folder content and looking at the needed id within items list
***id of the folder*** - this is the way specifying the place where the exchange will be created and it can be the same or it can be any other folder within the same project.
***ViewName*** - so that the system knows which View to use as a reference.

Having all this, the call will look like this:

![Mutation with Revit file](../../assets/images/revit_mutation.png)


Here worth mentioning several things:

- it is a mutation instead of usual query 

- we specify in the body that we are interested in id and name of the exchange we create

- getting back the id and the name of the exchange doesn't mean that the exchange was created and ready to be used, it just means that the name and id was allocated to this exchange and the process was started and depending on the job complexity it can be already ready or it can take some time. 
If you immediately take the exchange id that was given to you and try to run the exchange query (usually presented as GetExchangeInfo), you will get a response full of **"[404 Not Found] The specified resource was not found."** errors like bellow:

![Exchange Error](../../assets/images/exchange_error.png)

Thus, give it some time to process the exchange and use the exchange query to see if the results are ready or check the status of it using the new dedicated call:

![Exchange Status](../../assets/images/exchange_status.png)

The status can be:

- **IN_PROGRESS** - The exchange is being created.
- **COMPLETED** - The exchange has been completed processing.
- **FAILED** - The exchange creation failed.


### [Optional] Getting the View names from a Revit model

At least for now, if you don't know the name of the view you want to use for exchange creation, it is not possible to use the Data Exchange GraphQL API to get the views available in the Revit file and we will have to rely on other APS APIs to get that using the following steps:

- get the **id** of the Revit file from the Data Exchange GraphQL API query results, for example ***aXRlbX5iLjc2O_something_more_here_1dl9VSlNPV1pneERiMHZ2WER3***. This **id** is *base64* encoded of an information that looks like this ***item~b.768cae14-76b3-4531-9030-25212dab4e48~b.e3be8c87-1df5-470f-9214-1b6cc85452fa~urn:adsk.wipprod:fs.folder:co.M6jbnY7rQM2q0sEXvliUvA~urn:adsk.wipprod:dm.lineage:m0C5v_UJSOWZgxDb0vvXDw*** and contains the following data:
  - *b.768cae14-76b3-4531-9030-25212dab4e48* - is the hub id
  - *b.e3be8c87-1df5-470f-9214-1b6cc85452fa* - is the project id
  - *urn:adsk.wipprod:fs.folder:co.M6jbnY7rQM2q0sEXvliUvA* - is the id of the folder where the item resides
  - *urn:adsk.wipprod:dm.lineage:m0C5v_UJSOWZgxDb0vvXDw* - is the id of the item in question

- having the id of the item in question, we can use the ***Data Management API*** to get the tip of the item (what version is the latest) as well as the derivative that will be needed to get the available views in the tip version. 

> TIP: The exchanges will be created always from the latest version of the Revit file and we have to check what views are available in the latest version of the file.

- we call **GET https://developer.api.autodesk.com/data/v1/projects/{{PROJECT_ID}}/items/{{ITEM_ID}}** where the ***PROJECT_ID*** is the second id we get from base64 decoded id and the ***ITEM_ID*** is the last id we get from base64 decoded id.

- we will get back a huge body response, but we are actually interested in just one item there, the ***derivative id***:

![Derivative](../../assets/images/06.png)


- having the derivatve id we use the ***Model Derivative API*** to get the list of available views by calling **GET https://developer.api.autodesk.com/modelderivative/v2/designdata/{{DERIVATIVE_URN}}/metadata**, where ***DERIVATIVE_URN*** is obviously our derivateive id and we get back the list of available views:

![Views](../../assets/images/07.png)

which can be used further in ***Data Exchange GraphQL API*** to create the exchange.


## Creating exchanges from AEC Data Model

In case of an AEC Data Model, the workflow is similar to Revit workflow, except that in this case we have to specify the ***"filter"***, so that the system know which component subset to use as a reference.

![AECDM source](../../assets/images/aecdm.png)

Currently, within the filter function, only the levels of **Category**, **Family**, and **Type** are supported. 

For instance, you can use a filter such as "(category=='Windows')". It is important to note that even complex filters with [and]/[or] criteria are supported, and we are in the process of developing additional filter types. We welcome your feedback to help us prioritize the features that are most essential for your workflow.


## Conclusion

Before, if your pipeline required creation of exchanges from certain (e.g. Revit) models, you had to manually do that by going to ACC, selecting the file, selecting the view and triggering creation of the exchange. With addition to Data Exchange GraphQL API of this new functionality, it is now possible not only to automate all this, but also it opens the way to more complex workflows, like:

- automatic creation of an exchange when Revit file was added to ACC (some Webhook API involved)
- automatic creation of an exchange for all model existent in a folder
- automatic creation of the exchanges for all 3D views a Revit file contains (some Model Derivative API involved)
and many others.

Now you've covered many possible scenarios enabled by Data Exchange GraphQL API.
From now you have more than enough to start experimenting with your custom workflows on your designs.
We also have a few samples with live demos and source code available for you to leverage.

[APS Data Exchange Explorer (.NET Core)](https://github.com/autodesk-platform-services/aps-dx-explorer.git)

[Data Exchange GraphQL Sample (Node.js)](https://github.com/autodesk-platform-services/aps-dx-samples-nodejs.git)

[Data Exchange CLI Tool (Python)](https://github.com/autodesk-platform-services/aps-dx-tools-python.git)
