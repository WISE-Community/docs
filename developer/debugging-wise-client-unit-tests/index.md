---
title: Debugging WISE Client Unit Tests
layout: page
date: 2025-05-30 09:45 -0700
---

# Introduction

When running WISE-Docker-Dev, the services (WISE-API, WISE-Client, etc) run inside their own container, so debugging requires special configurations.

When you start the 'wise-client-test' container, it will start the karma process to run the test, watch for file changes, and be listening to port 9876 for connections. We'll connect to this port from VSCode.

# Starting the 'wise-client-test' container

Start the container by running
`docker compose --profile wise-client-test up`

This will start the karma process to run the test, watch for any file changes, and be listening to port 9876 for connections.

# Debugging WISE-Client test with VSCode

In this section, we describe how to connect to the wise-client-test container using Debugger for VSCode.

1. Open the WISE-Client project in VSCode.
2. Create a .vscode/launch.json if you don't have one already, and add a configuration.

```
{
    "version": "0.2.0",
    "configurations": [
        ...
        {
            "type": "chrome",
            "request": "launch",
            "name": "Attach to Remote WISE-Client-Test",
            "url": "http://localhost:9876/debug.html",
        }
        ...
    ]
}
```

3. Add a breakpoint in your code. You can do this by clicking on the left sidebar of the line where you want to break. It should add a marker there, indicating a breakpoint.
4. Start the debugger. You can do this by pressing F5, or go to the "Run and Debug" view in VSCode and running the "Attach to Remote WISE-Client-Test" target.

This should launch a chrome browser and connect to the wise-client-test container at port 9876. This should also break at the breakpoint you set above, assuming that the tests invoke that line of code.

From here, you can set start using the VSCode debugging tools to inspect and step through the code.
