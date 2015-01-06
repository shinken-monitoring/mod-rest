================
mod-rest Module 
================

What is it 
===========

The mod-rest module offer an API RESTFULL to Shinken.


Definitions of routes
======================

Introduction
-------------

All queries sent to backend:

- have specific URL and parameters (GET, POST)
- reply with array in JSON format

The interface of BackEnd works with the principe of CRUD and RESTfull

Route: get list of resources names
-----------------------------------

This route is static.

This route list all resources names available in Shinken.
It can be used to generate menu.

=================       ======
Route	                Method
=================       ======
http://[...]/item	GET
=================       ======

Example
Use http://[...]/item

And return:

::
  [
     {
       "item":"hosts",
       "name":"Hosts monitored"
     },
     {
       "item":"services",
       "name":"Services monitored"
     }
  ]


Route: get fields + types of a resource
---------------------------------------

This route is dynamic.

This route will get all fields of a resource and the type of each field.
By default, it get all fields with attribute visible = True

====================================    ======     ======================================================
Route	                                Method	   Dynamic
====================================    ======     ======================================================
http://[...]/item/{{resources}}	        GET	
http://[...]/item/{{resources}}/all	GET        all permit to get all fields, visible or not (optional)

Example

Use http://[...]/item/hosts

And return:

::
  {
      "data": {
          "host-name": "",
          "address": "127.0.0.1",
          "business_impact": "5" 
      },
      "meta": {
          "host-name": {
              "type": "string",
              "label": "hostname" 
          },
          "address": {
              "type": "string",
              "label": "address" 
          },
          "business_impact": {
              "type": "integer",
              "label": "impact level of business" 
          }
      }
  }

In this case, all values in "data" are default values

Route: get a list of resources
------------------------------

This route is dynamic.

This route will :

- Get a list of resources
  - manage pagination
  - get relationships info
- get all fields of a resource and the type of each field.

============================================================    ======  =======
Route	                                                        Method  Dynamic
============================================================    ======  =======
http://[...]/{{resources}}	                                GET	
http://[...]/{{resources}}?page={{page}}&perpage={{perpage}}	GET	Pagination: {{page}} is the page number (optional) / {{perpage}} is the number of elements per page (optional, default is 10)
============================================================    ======  =======

Use @http://[...]/hosts

And return:

::
  {
      "data": [
          {
              "id": 3,
              "host_name": "srv01",
              "address": "192.168.20.1"
          },
          {
              "id": 5,
              "host_name": "srv02",
              "address": "192.168.20.2"
          }
      ],
      "meta": {
          "total": 2,
          "perpage": 10,
          "totalpage": 1,
          "currentpage": 1,
          "Link": [
              "<http:\/\/127.0.0.1\/hosts?page=1&per_page=10>; rel=\"next\"",
              "<http:\/\/127.0.0.1\/hosts?page=1&per_page=10>; rel=\"last\"",
              "<http:\/\/127.0.0.1\/hosts?page=0&per_page=10>; rel=\"first\"",
              "<http:\/\/127.0.0.1\/hosts?page=2&per_page=10>; rel=\"prev\"" 
          ]
      }
  }


Route: get a resource
---------------------

This route is dynamic.

This route will :
- Get a resource based on id
- Get a resource with search field (it get only the first item)

===================================================================     ======  =======
Route	                                                                Method	Dynamic
===================================================================     ======  =======
http://[...]/{{resources}}/{{id}}	                                GET	{{id}} is the id of the resource
http://[...]/{{resources}}/{{id}}?{{field}}={{value}}	                GET	{{field}} is the name of the field and {{value}} is the value of the field we search. BE CARREFULL, use this will ignore the {{id}}
===================================================================     ======  =======

Example
Use @http://[...]/hosts/1

And return:

::
  {
      "data": {
          "id": 2,
          "host_name": "srv02",
          "address": "192.168.20.2"
      },
      "meta": {
          "host_name": {
              "type": "string",
              "label": "host name" 
          },
          "address": {
              "type": "string",
              "label": "address" 
          }
      }
  }


