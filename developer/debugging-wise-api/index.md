---
title: Debugging WISE API
layout: page
date: 2025-05-30 10:30 -0700
---

# Introduction

When running WISE-Docker-Dev, the services (WISE-API, WISE-Client, etc) run inside their own container, so debugging requires special configurations.

Debugging WISE-API is done with [Java Debug Wire Protocol (JDWP)](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/introclientissues005.html) at port 5005. When you start WISE-Docker-Dev, the wise-api container will be listening to that port for connections.

# Debugging WISE-API with VSCode

In this section, we describe how to connect to the wise-api container at port 5005 using Debugger for VSCode.

1. Open the WISE-API project in VSCode.
2. Create a .vscode/launch.json if you don't have one already, and add a configuration.

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        ...
        {
            "type": "java",
            "name": "Attach to Remote WISE-API",
            "request": "attach",
            "hostName": "localhost",
            "projectName": "wise",
            "port": "5005"
        }
        ...
    ]
}
```

3. Start the debugger. You can do this by pressing F5, or go to the "Run and Debug" view in VSCode and running the "Attach to Remote WISE-API" target. This should connect to the WISE-API JDWP port.

From here, you can set breakpoints and start using the VSCode debugging tools to inspect and step through code.
