---
title: Ubuntu Packages
layout: post
categories:
- devops
---

### List Packages Installed

`dpkg -l`

### Create Backup of What Packages Installed

`dpkg --get-selection > list.txt`

### Restore

```
dpkg --clear-selections
sudo dpkg --set-selections < list.txt
sudo apt-get autoremove
sudo apt-get dselect-upgrade
```
