---
title: Setting Selenium Grid
layout: post
categories:
- devops
---

Selenium Grid enables you to spread your tests across multiple machines and multiple browsers, which allows your to run tests in parallel. Also having Hub as central point of communication handles all the driver configuration and runs them automatically.

## Downloading Selenium

http://docs.seleniumhq.org/download/

### Setting up Hub

Once you have the jarfile downloaded from the Selenium Website,

`java -jar selenium-server-standalone-2.x.x.jar –role hub`

This starts up a jetty server on default port 4444. Grid Console can be viewed at `http://localhost:4444/grid/console`

### Setting up Nodes

Nodes are the actual machine which performs the tests,

`java –jar selenium-server-standalone-2.x.x.jar –role node –hub http://hubIP:4444/grid/register`

---

#### IE, Chrome, Safari & firefox selenium NODE

```bash
java -Dwebdriver.ie.driver=C:/eclipse/IEDriverServer/IEDriverServer.exe -Dwebdriver.chrome.driver=C:/eclipse/chromedriver/chromedriver.exe -jar selenium-server-standalone-2.48.2.jar -port 5555 -role node -hub http://localhost:4444/grid/register -browser "browserName=firefox, maxInstances=10, platform=ANY, seleniumProtocol=WebDriver" -browser "browserName=internet explorer, version=11, platform=WINDOWS, maxInstances=10" -browser "browserName=chrome,version=ANY,maxInstances=10,platform=WINDOWS"
```

#### IE Node Setup

```bash
java -Dwebdriver.ie.driver=C:/eclipse/IEDriverServer/IEDriverServer.exe -jar selenium-server-standalone-2.48.2.jar -port 5555 -role node -hub http://localhost:4444/grid/register -browser "browserName=internet explorer,version=11,platform=WINDOWS,maxInstances=10"
```

#### Chrome Node Setup

```bash
java -Dwebdriver.chrome.driver=C:/eclipse/chromedriver/chromedriver.exe -jar selenium-server-standalone-2.48.2.jar -port 5556 -role node -hub http://localhost:4444/grid/register -browser "browserName=chrome, version=ANY, maxInstances=10, platform=WINDOWS"
```

#### FireFox Node Setup

```bash
java -jar selenium-server-standalone-2.48.2.jar -port 5557 -role node -hub http://localhost:4444/grid/register -browser "browserName=firefox, maxInstances=10, platform=ANY, seleniumProtocol=WebDriver"
```
