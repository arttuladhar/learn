---
title: Setting up Jekyll Website
layout: post
categories:
- devops
---

### Pre-Requires

* [Ruby](https://jekyllrb.com/docs/installation/macos/)

```bash
# Verify Ruby is Installed
ruby --version
```

### Installation

```bash
gem install jekyll bundler
```

### Create New Site

```bash
jekyll new myblog
```

### Run Blog Locally

```
bundle exec jekyll serve
```

## Reference

* https://github.com/arttuladhar/my-jekyll-blog