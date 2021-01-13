# Communication with the TFW

There are two cases when you might want to communicate with the framework:
 1. From event handlers reacting to subscribed events
 2. From a running application (like the webservice)


## Event handling using an SDK
The TFW is really flexible and it allows us to create arbitrary event handler classes by subscribing to any combination of ZMQ message keys, however you have to be familiar with the inner workings of the Framework to use these features effectively. However, most of the time only a subset of these events are needed. To make easier handling them, we've created language specific SDKs. Currently available: 
 - Python: [PyPI](https://pypi.org/project/tfwsdk), [GitHub](https://github.com/avatao-content/tutorial)
 - Node.js: [NPM](https://www.npmjs.com/package/@avatao/tfwsdk), [GitHub](https://github.com/avatao-content/sdk-tfw-node)
 - C#: [NuGet](https://www.nuget.org/packages/Avatao.TutorialFramework.SDK/), [GitHub](https://github.com/avatao-content/sdk-tfw-csharp)
 - JDK is coming soon, [GitHub](https://github.com/avatao-content/sdk-tfw-jdk)

Using an SDK, you can easily send messages to the TFW and subscribe to various events. Click on their names for more information.

## Sending messages without an SDK
In case you are using a language that has no SDK support you can still send messages to the TFW through named pipes. A simple Python examle looks like this:
```python
with open('/tmp/tfw_send', 'w') as f:
    import json
    f.write(json.dumps({'key': 'fsm.trigger', 'transition': 'step_2'}) + '\n')
```