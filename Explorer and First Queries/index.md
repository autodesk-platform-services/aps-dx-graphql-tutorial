---
layout: page
title: Explorer and first queries
nav_order: 3
has_children: false
permalink: /explorer/home/
---

# Explorer and First Queries

Now that you're familiar with what GraphQL is, we can start experimenting with the queries and the explorer to get more comfortable with the Data Exchange GraphQL API.

In this section, we'll introduce you to the interface that will help you explore your design data, focusing mainly on the API. As said before, we don't want you to worry about frameworks, coding, and cloud providers. We can keep it simple using the [explorer](https://aps-dx-explorer.autodesk.io/).

The explorer's UI was built to be simple and intuitive. We'll use it mostly to perform our queries by passing the payload and checking the response, just like in the image below:

![Explorer UI](../../assets/images/explorerui.png)

> _The explorer is built on top of the [graphiql](https://github.com/graphql/graphiql) project! If you want additional details on this project, feel free to check its documentation ;)_

It also comes with multiple functionalities to check the history of queries, format queries, configure themes and shortcuts, and a button to display the queries available with the API. This last option is the first one we'll go through, as it provides us access to our API schema. This will be our entry point. We'll start our journey by getting familiar with the Data Exchange GraphQL API schema.

> More information on Data Exchange Model Explorer you can find [here](https://aps.autodesk.com/en/docs/fdxgraph/v1/developers_guide/fdx_data_explorer/).

## Data Exchange GraphQL API schema

As described at [graphql.org](https://graphql.org/learn/schema/):

> _"Every GraphQL service defines a set of types which completely describe the set of possible data you can query on that service. Then, when queries come in, they are validated and executed against that schema."_

The Data Exchange API has a schema suitable to address the common data from different industries with main focus (for now) on AEC industry. The main constructs are:

- **Elements**: An Element is a building block of design data. It represents an individual piece such as a wall, window, or door without enforcing a rigid definition. The absence of a rigid definition allows the Element to be flexible to adapt to the different requirements needed now and in the future. The data contained in an Element gives it context by using Classification, Property, and Property Definition.
- **Reference Property**: A reference property describes the relationship between elements.
- **Property**: A Property is a well-defined granular piece of data that describes the Element. For example: Revit parameters and their values like area, volume, length, etc.
- **Property Definition**: A Property Definition provides detailed information about a Property. It contains metadata that gives context to the Property. For example: Unit, type, etc.


![schema](../../assets/images/schema_element.png)

### Explorer Docs

Now let's use the explorer to view our schema.

Log in with your Autodesk account, then click on the Docs button and scroll down to access the queries available in the Data Exchange GraphQL schema.

![Schema through explorer](../../assets/images/schema.gif)

The first query we used in the previous section returned to us a list of hubs. According to this documentation we could, for instance, use a filter to retrieve only the hubs matching certain conditions. Exploring the schema gives us a better idea about the capabilities of the API. If you scroll down you'll see a list with all the queries available including the parameters that can be passed to compose the responses.

### GraphQL Voyager

> There's also another great tool to explore GraphQL API's schemas:
> The [GraphQL Voyager](https://graphql-kit.com/graphql-voyager/)

To try this tool, you just need to click the `Voyager` button in the explorer and it will open a new tab with the schema compatible with the Data Exchange GraphQL API.

![Voyager](../../assets/images/voyager.gif)

With that you will be able to inspect all the available queries and constructs from Data Exchange GraphQL API.

> _Keep one tab with the schema open for further exploration throughout this tutorial ;)_

Now that we know the schema's importance and know how to view it using the explorer, we can continue with the subsequent queries.

## First Queries

In case you're not familiar with how the data is organized in the context of ACC hubs, here is a quick overview:

At the top level, there are the hubs.
Inside each hub, there are the projects.
Inside a project, there are multiple folders.
Inside a folder, there can be other folders or items.
Lastly, an item can have multiple versions.

![ACC hierarchy](../../assets/images/hierarchy.png)

Let's traverse this structure through our queries in 4 steps:

### Step 1 - Listing the hubs

The query to retrieve the hubs is quite simple and it is available in the first pane of the explorer. To list the hubs available you just need to click in the first panel of the explorer and then run the query, like the gif below:

![GET hubs](../../assets/images/gethubs.gif)

Now make sure you can see in the response the needed hub. If the needed hub doesn't appear here, it means that the client id used for this application was not provisioned on needed hub and you'll need to [provision access in your ACC hub](https://get-started.aps.autodesk.com/#provision-access-in-other-products) to the client id `HKVjhUXySDGLGJimolxAgDdpoCuZLlql`.

This tutorial will move to the next steps using the hub named `DevTech ME+MFG`.
In the next query, you'll need to use your hub id as input.

> _This id is different from the one used by other APS APIs (ACC and Data Management) to point to hubs._

### Step 2 - Listing the projects

Following the hierarchy, we're going to list all of the projects available inside one hub. For that, we'll need to provide the hub id as input for the get projects query.

Go ahead and copy the id of the hub you're using, move to the `GetProjects` pane, and paste the id in the proper field, just like in the gif below:

![GET projects](../../assets/images/getprojects.gif)

Now you'll need to find the project that hosts your Revit 2024+ designs for this tutorial.

This tutorial uses the project `ACC-Learning-project`, which is already visible on the first page of the response.

In case your hub has many projects, the one you need to use might be missing from the first page (or even hard to find).

There's a way to filter the response.

For that you can filter the projects by name, passing the name of your project like the gif below:

![GET projects](../../assets/images/getprojectsfilter.gif)

For simplicity, you can just copy and paste the query below if needed (replacing it with your project name and hub id) ;)

```js
query GetProjects($hubId: ID!) {
    projects(hubId: $hubId, filter:{name:"Your Project Name Here!"}) {
      pagination {
        cursor
      }
      results {
        id
        name
        alternativeIdentifiers{
          dataManagementAPIProjectId
        }
      }
    }
}
```

```js
{
  "hubId": "YOUR HUB ID HERE!"
}
```

The next query requires the project id from the response, and we will list the project top folders

### Step 3 - Listing the project folders

Go ahead and copy the id of the project you're using, move to the `GetProjectFolders` pane, and paste the id in the proper field, just like in the gif below:

![GET project folder](../../assets/images/getprojectfolders.gif)

In your case you might have multiple top folders, but in our context we are interested only in "Project Files" folder


We are not going to use the alternative representation for the projects in this tutorial but is always good to know how to retrieve it. You'll need it if you want to connect with ACC APIs or Data Management APIs, for instance.


### Step 4 - Listing the folder content

Since in ACC Docs, within "Project Files" we can have many folders and subfolders, this step is "recursive", used to navigate through folders and can be used multiple times till we get into needed folder.

Copy the id of the project "Project Files" folder, move to the `GetFolderContent` pane and paste the id in the proper field. Once you get the results, get the id of the needed folder and replace the `folderId` variable and run the query again. Repeat this step till you get to the needed folder like in the gif below:

![GET project folder](../../assets/images/getfoldercontent.gif)

In this query, we query the folder content and ask to provide data on contained `folders`, `exchanges` and `items`. 

In this context an `item` is a design file like Revit, IFC etc. and we will need their id later when we will create exchanges from them using the Data Exchange GraphQL API.

For the next step we are interested in `exchanges` and we need an id to be able to inspect it first.


### Step 5 - Exchange info


Go ahead and copy the id of the exchange you're interested in, move to the `getExchangeInfo` pane, and paste the id in the proper field, just like in the gif below:

![GET project folder](../../assets/images/exchangeinfo.gif)

In above example we look at exchange that have two versions. In fact the versions in case means that after the exchange was initialy created, the source file (in this case a Revit file) was updated and the exchange reflects that by pointing to different version. Under the hood, it is a single graph with two snapshots: first is the snapshot to the initial graph and the second is to the new state of the graph. We will go into it deeper later when we will compare the versions using the Data Exchange GraphQL API.

Note the `alternativeIdentifiers` fields: Any time an exchange is created, a viewable is also created so it can be visualized within ACC. We can use the `fileUrn` to visualize the viewable as shown in gif below:

![GET project folder](../../assets/images/viewable.gif)


### Step 6 - Listing Elements

Now we can explore the components from our exchange and look for the needed properties.

To facilitate understanding how data is structured, let us look in parallel at the viewable of the exchange:

![](../../assets/images/model.png)

Using the DataExchange GraphQL API, we will look at all elements that exist in the exchange and then, to narrow down the results, we can use the filtering feature to show only the elements of category "Walls":

```js
filter: {
  query: "property.name.category==Walls";
}
```

![Get Elements](../../assets/images/getelements.gif)

In the next section, we will see more advanced querries and more complex filters, like filtering for walls that have `Area` between certain values, to further narrow down the results.


[Next Step - Advanced queries and filtering](../../connection/home/){: .btn}
