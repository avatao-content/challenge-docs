# Content of the challenge repository

`CHALLENGE_REPO   
├── 1. controller  
│   └── [solution checking]  
├── 2. hack contains a bash script for the automated build&run   
├── 3. solvable  
│   ├── 4. Dockerfile   
│   ├── 5. nginx webserver configurations   
│   ├── 6. supervisor process manager (init replacement)   
│   ├── 7. frontend clone of the frontend   
│   └── 8. src example source code   
│       ├── 9. user: take the files you want to show with the user here   
│       ├── 10. event_handler.py: you can manage the deployment process in this file   
│       └── 11. fsm.py: a file to define the actions of the final state machine   
├── 12. metadata   
│    ├── 13. descriptions.md   
│    ├── 14. summary.md   
│    └── 15. writeup.md [optional]   
├── 17. config.yml challenge configuration file  
└── 16. additional files (.drone.yml; CODEOWNERS; README.md): should be there but   
        nothing interesting for us` 

### 1. controller

This folder is for the solution checking. In most cases, you don’t have to modify anything in this directory. There are two main approaches to accept the solution \(however, you can also implement a unique solution checker too\). The solution can be accepted:

* when the user reached the last state defined in the **final state** machine at `solvable/src/app_fsm.py`: `...  class SIDFSM(LinearFSM):   def init(self):      super().init(8)      self.message_sender = MessageSender()      self.uplink = TFWServerConnector()      self.sender_name = 'avataobot'  ...`

Here, there are seven states \(`1` to `7`\) defined and if the user is in the seventh state and clicks on **Check Solution** on the Avatao platform, then it’saccepted. \(See the details of the final state machine description file later.\)

* if the captured flag is right: most commonly used for ‘attack type challenges’. The captured flag should be copied onto the platform’s related textbox \(see the picture below\). To define the flag you have to add these lines to the `config.yml`. `enable_flag_input: true  flag: 'mySecretFlag'` 

![](../.gitbook/assets/kep%20%287%29.png)

* as mentioned above, it’s possible to implement a custom logic as needed.

### 2. hack

Here we have the `tfw.sh`, a magic script to start the challenge. To perform this, use the following command in the challenge’s root directory:

`./hack/tfw.sh start`

There’s another way to start the challenge locally \(see the `build.py` and `start.py` in the `challenge-toolbox` repo\) and I do recommend to use that instead of the `tfw.sh`, because it stands closer to the conditions of the real environment on the Avatao platform. \(i.e. there’s no external internet access on the platform, but there is when you start the challenge with the `tfw.sh`. It might confuse you in some specific case\).

### 3. solvable

The implemented application: the custom final state machine and event handler should be here.

### 4. solvable/Dockerfile

Contains the commands to build up the solvable challenge’s image. Please check the Dockerfile reference[ here](https://docs.docker.com/engine/reference/builder/).

Quick note: in the `VOLUME` array you can define the routes that’ll be excluded from the read-only file system. The basic commands of this file should be understood.

### 5. solvable/nginx

In most cases you don’t have to deal with it.

All TFW based challenges expose a single port defined in the `TFW_PUBLIC_PORT` envvar which is set to `8888` by default. This means that in order to listen on more than a single port we must use a reverse proxy. Any `.conf` files in solvable/nginx/ will automatically be included in the nginx configuration. In case you want to serve a website or service, you must proxy it through `TFW_PUBLIC_PORT`. This is really easy: just create a config file in `solvable/nginx/` similar to this one:

`location /yoururl {   
     proxy_pass http://127.0.0.1:3333;   
}`  


After this, you can access the service running on port `3333` at `http://localhost:8888/yoururl`. 

It’s very important to understand that from now on your application must behave well behind a reverse proxy. What this means is all hrefs must point the proxied paths \(e.g. links should refer to `/yoururl/register` instead of `/register`\) on your HTML pages. You can learn about configuring nginx in[ this](https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts) handy little tutorial.

### 6. solvable/supervisor

In most Docker containers there’s a single process running \(it gets `PID 1`\). When working with TFW you can run as many processes as you want to, by using supervisord. 

Any `.conf` files in the solvable/supervisor/ directory will be included in the supervisor configuration. The programs, you define this way, are easy to manage \(starting/stopping/restarting\) using the `supervisorctl` command line tool or our built-in event handler. You can even configure your processes to start with the container by including `autostart=true` in your configuration file.

To run your own webservice for instance, you need to create a config file in `solvable/supervisor/` similar to this one:

`[program:yourprogram]  
user=user  
directory=/home/user/example/  
command=python3 server.py  
autostart=true`

This starts the `/home/user/example/server.py` script using Python3 after your container entered the running state \(because of autostart=true, supervisor doesn’t start programs by default\). You can learn more about configuring supervisor[ here](http://supervisord.org/configuration.html).

If there’s a simple console application, the only file here may be for the event handler, but if there's a webservice too, you also have to define a `webservice.conf`.  \(The webservice’s port should be `11111` in a TFW challenge, and `8888` in an attack type challenge\). In the following example a Spring Boot webservice starts as well with:

`[program:webservice]  
user=root   
directory=%(ENV_TFW_WEBSERVICE_DIR)s  
environment=BASEURL="/webservice"   
command=mvn -o spring-boot:run -Dserver.port=11111   
autostart=true  
startretries=0   
stdout_logfile=/tmp/webservice_logs   
stderr_logfile=/tmp/webservice_logs`

### 7. solvable/frontend

This is a clone of the `frontend-tutorial-framework` repository with dependencies installed in `solvable/frontend/node_module`s.

The `node_modules` directory can’t be uploaded to the remote Git repo because it's too heavyweight stuff. It’s important that the frontend should be the latest version. The following command will install the necessary packages defined in the node\_modules folder:

`yarn install`

You can modify it to fit your needs, but this requires some Angular knowledge \(not that much\). You might sometimes need to rewrite some configuration data in the `frontend/src/app/config.ts`:

* **`terminalOrConsole: 'terminal':`** you can define the default tool \(terminal/console\) seen in the dashboard at the start of the challenge. 
* **`enabledLayouts: [’ide-only’, etc.]`**: you can define the layouts you want to be available in the dashboard \(you can select these in the right side of the dashboard\):  
  _`terminal-ide-web`_

  _`terminal-ide-vertical`_

  _`terminal-ide-horizontal`_

  _`terminal-only`_

  _`terminal-web`_

  _`ide-web-vertical`_

  _`ide-only`_

  _`web-only`_

* **`showDeployButton`**: you can enable/disable the _Deploy_ button that is on the upper right corner of the IDE.
* **`reloadIframeOnDeploy`**: if set to true, the webservice is updated after each deploy \(the page is refreshing\). The title of the **Deploy** button can also be overwritten \(sometimes **Compile** can be reasonable\): `DeployButtonText: {  'TODEPLOY': 'Compile',   'DEPLOYED': 'Compiled',   'DEPLOYING': 'Compiling...',   'FAILED': 'Compile failed. Retry'  }, ...`

### 8. solvable/src

This folder contains the source code of the implemented application \(a server running TFW\) and another server running our **event handlers**. Note that this isn’t a part of the framework by any means, these are just simple examples.

`solvable/src   
├── event_handler_main.py event handlers implemented in Python   
├── test_fsm.py example FSM in python   
└── test_fsm.yml example FSM in yaml`

### 9. solvable/src/user

The source files you want to be seen by the user in the TFW IDE should be put here.

### 10. solvable/src/event\_handler.py

The `event_handler_main.py` contains the pre-defined event handlers written in Python3. As you can see they run in a separate process \(set up in `solvable/supervisor/event_handler_main.conf`\). These event handlers could be implemented in any language that has ZMQ bindings.

Note that you don't have to use all our event handlers. Should you want to avoid using a feature, you can just delete the appropriate event handler from `event_handler_main.py`.

Event handler also contains the process of deployment mechanism. Minimal Python3 knowledge is needed to define custom logic. Please check different event handlers in several challenges! You can also make some TFW-related adjustment in this file, for example you can _exclude_ files to be shown in the IDE’s folder with a regex:

`if name == 'main':   
  ide = IdeEventHandler( # Web IDE backend   
      ...   
      exclude=['.csproj','Utilities','.sln','bin','obj','*.exe']   
      ...   
  )   
...`

### 11. solvable/src/fsm.py

A good state machine is the backbone of a good TFW challenge. There are **two ways** to define a state machine:

* Using a **YAML** configuration file 
* Implementing it in **Python** by hand

Please check the `test_fsm.yml` and test\_fsm.py in the `test-tutorial-framework` repo! They’rethe implementations of the same FSM in YAML and Python to provide you examples of how to create your own machine. Generally, it’s a good idea to separate these files from the rest of the things in `solvable`, so it’s a good practice to create an `src` directory.

The **first** option allows you to handle FSM callbacks and custom logic in any programming language \(not just Python\) and is generally really easy to work with \(you can execute arbitrary shell commands on events\). You should choose this method unless you have a good reason not to. This involves creating your YAML file \(see `test_fsm.yml` as an example\) and parsing it using our `YamlFSM` class \(see `event_handler_main.py` as an example\). 

The **second** option allows you to implement your FSM in Python, using the transitions library. To do this, just subclass our `FSMBase` class or use our LinearFSM class for simple machines \(see `test_fsm.py` as an example\).

It’s also possible to add preconditions to transitions. This is done by adding a `predicates` key with a list of shell commands to run. If you do this, the transition will only succeed if the return code of all predicates was `0` \(as per unix convention for success\). 

These files are for the custom final state machine’s details where you can define the commands \(TFW-messages\) that should be executed when entering or leaving a state, as a result in your FSM you can define callbacks for states and transitions. State callbacks: `- on_enter - on_exit`. Transition callbacks: `- before – after`. In your YAML file, you can use these in the state and transition objects as keys, then add a shell command to run as a value \(again, see `test_fsm.yml` for examples\). The commands in the `on_enter_3()` will be executed when the user entered in the third state, the `on_exit_3()` methods when the user leaves that state.

Here is an example of sending a hint that can be seen on the left side of the TFW’s dashboard:  


`messages = []  
messages.append('''Dear Secure Coder! In this cool tutorial...''') MSG_SENDER.queue_messages(’avataobot’, messages)`

You can use **Markdown** and some **HTML** tags in these messages.

### 12. metadata

You always have to modify the content of these Markdown files.

### 13. metadata/description.md

**Required**. Contains the unique description of the challenge \(what is the security issue, what the user should do, etc\). Avoid long descriptions because no-one will read that.

### 14. metadata/summary.md

**Required**. A short \(one or two sentences\) summary about the challenge, i.e.:. _„Learn about what is a server-side session, what is a http\_only flag. Steal session id from cookie without http\_only. Then turn on http\_only and learn how to use it properly”._

### 15. metadata/writeup.md

**Optional** \(only needed in the case of attack type challenge\). The user can ask for hints to solve the challenge. In the case of TFW, these hints are sent as a message. You can rate the hint’s value by specifying a percentage that’s related with the points acquired after the challenge is successfully solved. The sum of the hints’s value should be 90% and always define a Complete solution section at the end. The hint can be a code snippet, too. See the example here:[ Alert Me 1](https://platform.avatao.com/challenges/0d6ca1aa-793d-4fe6-8fca-ca9907bf3076).

![](../.gitbook/assets/kep%20%281%29.png)

### 16. config.yml

`See an example with description:  
version: 'v2.0.0': should be this version   
name: "Integer overflow" : the name of the challenge you'll find on the Avatao platform  
difficulty: 100: should be between 10 and 500. If you’re not sure what the value should be, the best is nott to panic, we’ll check it for you!   
enable_flag_input: false : you can choose between flag-based and final-state-based solution checking here. If you want to use a flag, type "true" here (enable_flag_input: true) and next line write: "flag: 'string'"  
skills: ['C Sharp','Defensive','Secure Coding']: the tags that will appear at the Avatao platform. Choose them well and please use existing tags!  
recommendations:` [`http://projects.webappsec.org/w/page/13246946/Integer Overflows`](http://projects.webappsec.org/w/page/13246946/Integer%20Overflows)`: 'Integer Overflows attack type': The recommended readings section for each challenge is to provide a better understanding of the related security issue and should contain 1-4 items. Define a URL with : ’title’.  
owners: ['developer@avatao.com']: change to your own e-mail address`

### 17. Other files

You can see some additional files \(`.drone.yml, CODEOWNERS, README.md`\) automatically created in the Git repository, but you don’t have to deal with them.























