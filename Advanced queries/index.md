---
layout: page
title: Advanced queries and filtering
nav_order: 4
has_children: false
permalink: /connection/home/
---

# Advanced queries and filtering

In this section, we will explore more advanced queries, use the pagination and descover the power of filters.

The Data Exchange GraphQL API has great filtering capabilities, and we'll explore that a lot in the next queries. In this section, we'll focus on advanced filtering capabilities, versioning and use the pagination.

## Advanced Filtering Capabilities

In previous section we already used basic filter that helped us to get the Walls objects

```javascript
filter: {
  query: "property.name.category==Walls";
}
```

![before_filtering](../../assets/images/before_pagination.png)

In this case the number of returned elements is relatively small (only seven), but in case we get dozens results we might want to start using pagination to limit the amount of results that we want to receive after filtering:

![after_filtering](../../assets/images/after_pagination.png)

As you can notice, this is done by adding `pagination` along with `filter` and can be used within query body at the object level. For example, the pagination, as well as filtering can be done at element level (to filter for elements satisfying a certain criteria) as well as at property level, which we will explore next.

Even when we get several elements, usually each one might have dozens of properties, from which we need just several of them.
As in bellow example, we might be interested only in `Area` and `Length` of walls, but we have to scroll through all those properties to get the right ones:

![wall_props.png](../../assets/images/wall_props.png)

We can add a filter at properties level, that allows us to specify the name of properties we are interested in:


![wall_props_filtered.png](../../assets/images/wall_props_filtered.png)

At this step we can refine even further our output by specifying whihc criteria these properties should satisfy. As an example, we will remove the pagination limit and specify in the filter at element level that not only we want the elements that are of category `Wall`, but also those that have area less than 90sqm:

![property_value.png](../../assets/images/property_value.png)

We get three results that satisfy the given filtering criteria and I hope that now you understand the power of filtering expressions.
More information on what is supported and how to combine different filtering criteria can be found on [Advanced Filtering Capabilities](https://aps.autodesk.com/en/docs/fdxgraph/v1/developers_guide/API%20Essentials/advanced_filtering/) page.

### Versioning

Being familiar with filtering helps not only with getting the data from an exchnage, but it is also applicable in other situations like exploring the changes between two different exchange versions.

To facilitate understanding of the new call, let us look at a situation where we created an exchange that contains a subset of a Revit model.
The Revit file was changed and consequently the exchange also updated from v2 to v3, to reflect the latest changes, in this case there were added doors and windows:

![v2v3](../../assets/images/v2v3.png)

The difference can be easily seen if we use the diff tool in ACC to compare, between versions, the view of the Revit file that was used as the source of the exchange:

![diff](../../assets/images/diff.png)

As we can see, the wall properties modified by adding windows and doors and in what follows, we will try to capture using the Data Exchange GraphQL these very elements and their properties.

For this purpose we will use a dedicated call [exchangeByVersions](https://aps.autodesk.com/en/docs/fdxgraph/v1/reference/queries/exchangebyversions/) that "Retrieves a list of Elements in an exchange by version".

![graphql_diff](../../assets/images/graphql_diff.png)

We will get back all the elements and notice here two apects:

1. We use the filtering feature to compare two versions by providing the verions between which versions it should be compared and the keywords `latest` and `latest-1` are sort of shortcuts that can be used instead of inserting the long version id. 
2. We will get back all the elements that in the latest version were either inserted, modified or removed and this can be noticed by looking at `Operation` field that will have the corresponding values.

As result of this call we might have a lot of results and to narrow down even more the needed elements, we can use the filter at the element level and specify that we are interested only in walls, by specifying `filter: {query: "property.name.category=='Walls'"}`:

![diff_element_filtering](../../assets/images/diff_element_filtering.png)

As you can see, we can use filters at query level, like in this case for getting the version diff, and also within query body at level of the objects wher ethis is specified. The best way to know where the filtering is possible is obvioulsy the documentation:

![filters](../../assets/images/filters.png)



In the next section, we will see how to use the Data Exchange GraphQL API to create exchanges from Revit files or AEC Data Models.


[Next Step - Create exchanges from design](../../mutation/home/){: .btn}
