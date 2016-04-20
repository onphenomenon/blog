---
layout: post
title:  "Reverse Engineer mySQL Visual Diagram"
date:   2015-07-06 18:05:42
tags: ["front"]
---

Visual diagrams for database schema are key to communicating table structure and relationships. Especially on a team, database structure must be transparent, so visual diagraming tools are extremely useful.

Here I will give an example of visual diagraming with MySQL Workbench.
For a recent project I used mySQL and the ORM Bookshelf to create a relational database to store information from GitHub API for users on my application with computed attributes and  associations. I deployed on AWS Elastic Beanstalk with an EC2 instance for my server files and an RDS instance for the mySQL database.

In order to skip to the visual diagramming tool, I'll assume you have a server running your MySQL database and that you have MySQl Workbench up and running.

First you would connect to your remote or local database with MySQL connections, either Standard TCP/IP or SSH. Once that connection is established and opened, the editor instance should show your schemas in the left hand side-bar.

To start a new diagram, first click on New Model in the file dropdown.
Then to build the visual diagram from your current schema, under the Database dropdown, click on Reverse Engineer. In the menu that pops up you can choose your stored connection in the first dropdown in 'Set Parameters for Connection to a DBMS'. You'll be able to choose the schema you want to create a diagram for as you continue through the options. Upon completion, the EER Diagram screen will appear with the tables and fields for the schema you selected.

If you click on a table, a lower option menu will appear, where you can edit columns.

If you didn't explicitly set foreign keys and associations in your schema with the correct mySQL typing, you can manually draw relationships between tables.

The left bar editor shows a variety of one-to-one or one-to-many relationships.

Visual Schema for CodeUs, an app that facilitates connection and collaboration among coders based on expertise and location:

<img src="/foundation/img/diagram.png" alt="Diagram" height="800" width="800">

The lines that are dashed signify a non-identifying relationship between tables. A solid line, identifying, represents dependency in relationship. The entries in the dependent table belong to or depend on a row in another table.

The three main mappings of relationships are:

**1:1**, one to one, Primary key is foreign key in other table. teacher:address.

**1:n**, one to many, Primary key is the foreign key in the many relations on the other table. Ex: teachers:classes.

**n:m**, many to many, A join table is created where the primary keys from the two original tables identify the relationship. Ex: teachers:subjects.




