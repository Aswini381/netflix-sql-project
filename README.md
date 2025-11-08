# Netflix-sql-project and TV shows data analysis using sql


![Netflix Logo](https://github.com/Aswini381/netflix-sql-project/blob/main/logo.png)

## Overview

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.


## Objectives
- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Schema


```sql
drop table if exists netflix;
create table netflix(
show_id varchar(6),
type varchar(10),
title varchar(150),
director varchar(210),
cast varchar(1000),
country varchar(150),
date_added varchar(50),
release_year  int,
rating varchar(10),
duration varchar(50),
listed_in varchar(100),
description varchar(250)
);
```

## Business Problems and Solutions

1. Count the number od movies vs TV shows
