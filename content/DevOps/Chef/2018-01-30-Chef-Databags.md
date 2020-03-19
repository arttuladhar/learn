---
title: Chef Databags
layout: post
categories:
- devops
---

Databags are global variables that is stored as JSON Data and is accessible from Chef Server. A data bag is indexed for searching and can be loaded by recipe or accessed during a search.

## Creating Data Bag (Using Knife)

```shell
$ knife data bag create DATA_BAG_NAME (DATA_BAG_ITEM)
knife data bag create TEST_BAG
```

## Adding File to Data Bag

```shell
knife data bag from file TEST_BAG test.json
```

## Data Bag Items

A data bag is container of related data bag items, where each individual data bag item is a JSON file.

### Using Data Bags

```
data_bag_item('keystore_file', 'dev')
```