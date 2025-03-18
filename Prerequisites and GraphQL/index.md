---
layout: page
title: Prerequisites and GraphQL
nav_order: 2
has_children: false
permalink: /prerequisites/home/
---

# Prerequisites

Before starting, ensure you meet a few key prerequisites to follow this tutorial. The steps should be completed in sequence for effective understanding (proceed to the next step only after completing the previous one):

- You will need an ACC account from the **AMER or EMEA region**. The Data Exchange API works with Revit 2024+ designs hosted on ACC hubs in the **AMER and EMEA regions**, so it is **required**. If you don't have access to an **AMER or EMEA ACC hub**, you can find options for getting a test account on [this blog](https://fieldofviewblog.wordpress.com/2017/08/31/bim-360-acc-account-for-development/).

**Make sure you now have access to an ACC hub in the AMER or EMEA region and only then proceed to the next prerequisite.**

- You'll need to [provision access in your ACC hub](https://get-started.aps.autodesk.com/#provision-access-in-other-products) to the client id `HKVjhUXySDGLGJimolxAgDdpoCuZLlql`. This is the client id of the APS app used by the [explorer](https://aps-dx-explorer.autodesk.io/) that we'll be using in this tutorial.

- If you want to follow along with the same design, you can download [this archive containing a Revit design](https://autodesk-adn-transfer.s3.us-west-2.amazonaws.com/ADN+Extranet/AEC/public_downloads/OfficeBuilding.zip) in three different versions, which is very helpful exploring the Data Exchange features.

Now let's cover a quick introduction on GraphQL.

# GraphQL

GraphQL provides a complete and understandable description of the data in an API, and gives you the power to ask for exactly what you need and nothing more.

GraphQL is much easier to use than REST, but it requires a better understanding of data structures​.

With a single endpoint, data fetching is easier because you don't need to make requests to multiple endpoints to get a set of data.

The request specifies precisely the data that’s going to be returned.

Let's compare a case where we need to get the hub list using the Data Exchange GraphQL API and the Data Management REST API.

With the Data Exchange GraphQL endpoint, the query looks like this:

```js
query GetHubs {
  hubs {
    results {
      name
      id
    }
  }
}
```

And the response for this query will look like this

```js
{
  "data": {
    "hubs": {
      "results": [
        {
          "name": "AEC DM Developer Advocacy Support",
          "id": "b.238cb7ac-5f66-4da7-9c8d-b99f6f87ecdf"
        },
        {
          "name": "Developer Advocacy Support",
          "id": "b.489c5e7a-c6c0-4212-81f3-3529a621210b"
        },
        {
          "name": "DevTech ME+MFG",
          "id": "b.5b879731-d709-4e15-abde-bb0431a3c7a0"
        },
        {
          "name": "Autodesk Consulting",
          "id": "b.63ec7ea2-6b33-440d-9e8a-8aff5e121594"
        },
        {
          "name": "Construction Records Testing",
          "id": "b.768cae14-76b3-4531-9030-25212dab4e48"
        }
      ]
    }
  }
}
```

Using the Data Management REST call to `https://developer.api.autodesk.com/project/v1/hubs` endpoint, we will get a very long result, where for each hub we will get the following :

```js
{
    "jsonapi": {
        "version": "1.0"
    },
    "links": {
        "self": {
            "href": "https://developer.api.autodesk.com/project/v1/hubs"
        }
    },
    "data": [
        {
            "type": "hubs",
            "id": "b.5b879731-d709-4e15-abde-bb0431a3c7a0",
            "attributes": {
                "name": "DevTech ME+MFG",
                "extension": {
                    "type": "hubs:autodesk.bim360:Account",
                    "version": "1.0",
                    "schema": {
                        "href": "https://developer.api.autodesk.com/schema/v1/versions/hubs:autodesk.bim360:Account-1.0"
                    },
                    "data": {}
                },
                "region": "US"
            },
            "links": {
                "self": {
                    "href": "https://developer.api.autodesk.com/project/v1/hubs/b.5b879731-d709-4e15-abde-bb0431a3c7a0"
                }
            },
            "relationships": {
                "projects": {
                    "links": {
                        "related": {
                            "href": "https://developer.api.autodesk.com/project/v1/hubs/b.5b879731-d709-4e15-abde-bb0431a3c7a0/projects"
                        }
                    }
                }
            }
        },
        ...
    ],
    "meta": {
        "warnings": [
            ...
        ]
    }
}
```

Note that in case of a REST call we get all the fields that it can get, while in case of GrapQL call we can specify which fields we are interested in.

Moreover, with REST API we would need additional requests to get other data like `projects`, `folders` and so on, while in case of GrapQL call, we can do that in one call providing the info on needed fields.

To summarize, with GraphQL we have the benefits below:

- Single REST API endpoint – You only need to send your query to this endpoint when coding it in your applications
- No fixed Structure for the exchange of data – as compared to the Data Management REST API, where you will get a large JSON dataset, that you need to understand and be able to find the data you are looking for.
- No over-fetching – as compared to the Data Management REST API, where you may need to call various APIs several times to get the data you are looking for.
- Efficiently using resources – Because the GraphQL implementation is on the Autodesk server side, it handles the requests to get the data you are asking for. This minimizes the traffic and allows us to optimize without disruption to the GraphQL aspects.

Before moving to the next step, let's run our very first query.

For that, you just need to go to the explorer app at `https://aps-dx-explorer.autodesk.io/`, log in, and run the query from the very first panel (GetHubs), just like in the image below:

![First Query](../../assets/images/getHubs.gif)

If you don't see your ACC hub listed, please double check step 2.
Once you see your hub, you can move on to the next step.


> _We'll get back to GraphQL syntax throughout the queries in the next section. As it's not like the usual REST requests, we'll cover the differences and possibilities in parallel, while also learning the supported Data Exchange API queries._

[Next Step - Explorer and First Queries](../../explorer/home/){: .btn}
