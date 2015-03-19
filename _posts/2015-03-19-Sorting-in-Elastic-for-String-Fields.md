---
layout: post
title: "Sorting in Elastic for String Fields"
description: "Sotring in elastic elasticsearch for string fields using multifield"
tags: [elastic, elasticsearch, nosql, sorting, elastic]
---

I encountered this issue with elasticsearch while I was trying to implement the sorting feature for one of my projects. The problem is that the sorting is not working as how I expected it for my string fields. 

This is how the sorting problem looks like. Say you have this data.

    [
      {
        "name": "Juan Andres"
      },
      {
        "name": "Jasmin Mae"
      },
      {
        "name": "Jorge"
      }
    ]

When I do a regular sort via the ```name``` field in ```ASC``` order with a sample request like below:

    {
      "sort": [
        {
          "name": "asc"
        }
      ]
    }

I get this result:

    [
      {
        "_source": {
          "name": "Juan Andres"
        },
        "sort": ["Andres"]
      },
      {
        "_source": {
          "name": "Jasmin Mae"
        },
        "sort": ["Jasmin"]
      },
      {
        "_source": {
          "name": "Jorge"
        },
        "sort": ["Jorge"]
      }
    ]

This is not the sorting I want to happen. The reason why this is happening is because my field ```name``` is being ```analyzed```. Elasticsearch uses the tokens to do the sorting. This is actually [not advisable](http://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-sort.html).

I can just change the string index to ```not_analyzed``` but I need it for my full-text search. 

To make this work, I need to make a field both ```analyzed``` and ```not_analyzed```. This can be done by using the ```fields``` property. ```Fields``` is a newer version of ```multi-field```. Your property will basically have two parts - one tokenized and one unanalyzed. Since you have both, you can use the ```not_analyzed``` part for sorting, and the ```analyzed``` part for the full-text search. You can read more about it [here](http://www.elastic.co/guide/en/elasticsearch/reference/current/_multi_fields.html).

I modified my type-mapping for the field to this:

    "name" :   {
      "type": "string",
      "fields": {
          "raw":   { "type": "string", "index": "not_analyzed" }
      }
    }

Once I had ```name``` configured to this, I was able to sort it properly. I needed to indicate the sort field to ```name.raw``` instead of just plain ```name```. My new sort request looks like below:

    {
      "sort": [
        {
          "name.raw": "asc"
        }
      ]
    }

Once I ran the command above, I get the correct sorted list.

    [
      {
        "_source": {
          "name": "Jasmin Mae"
        },
        "sort": ["Jasmin Mae"]
      },
      {
        "_source": {
          "name": "Jorge"
        },
        "sort": ["Jorge"]
      },
      {
        "_source": {
          "name": "Juan Andres"
        },
        "sort": ["Juan Andres"]
      } 
    ]

The sorting criteria becomes the ```not_analyzed``` version of the field.