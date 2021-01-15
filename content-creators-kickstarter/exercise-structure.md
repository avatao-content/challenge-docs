# Content of an exercise repository

## General structure

### Tutorial
The solution checking is integrated into the TFW, since all we have to do is check if the state machine is in the last state, so there is no separate `controller` folder. However, stepping the state machine based on user actions is the your task.

The general file structure looks like this:
```
├── 1. tfw  
│   └── [the TFW itself, please don't modify it]  
├── 2. solvable  
│   ├── 3. Dockerfile   
│   ├── 4. nginx    
│   │   └── [Nginx config files]
│   ├── 5. supervisor
│   │   └── [Supervisor config files]
│   ├── 6. tutorial  
│   │   ├── 7. tfw.yml: The YAML file that describes the whole exercise, including steps, views and chatbot messages   
│   │   └── 8. A file that implements your event handlers using your SDK of choice (e.g. `eventhandler.py` in the Python tutorials)     
│   └── 9. webservicve
│       └── [The vulnerable application or files that you want to run/showcase using the TFW]    
├── 10. config.yml exercise configuration file  
└── 11. additional files (.circleci/; CODEOWNERS; README.md): should be there but   
        nothing interesting for exercise development
```

### Attack

Example file structure for an attack exercise:

```   
├── 1. controller  
│   └── [solution checking]  
├── 2. solvable  
│   ├── 3. Dockerfile   
│   └── 4. webservicve
│       └── [The vulnerable application or files that you want to run/showcase]   
├── 5. config.yml exercise configuration file  
└── 6. additional files (.circleci/; CODEOWNERS; README.md): should be there but   
        nothing interesting for exercise development 
```

## Detailed explanation

### Controller

This folder is for the solution checking. It can be:
 * A flag, enabled in the `config.yml` file with `enable_flag_input: true`. You can specify a flag in the `config.yml` file like (like this: `flag: 'mySecretFlag'`), however, it is not recommended. Dynamic flags makes it harder to distribute the solution, it is advised to implement dynamic solution checking.
 * A custom solution check if the `enable_flag_input` is set to `false`. The most common use case is to visit a page to check if the attack has been successful. You can use this [XSS injection solution check](https://github.com/avatao-content/challenge-toolbox/tree/v3/templates/xss/controller) as a good example.

### Solvable

The implemented application: the custom final state machine and event handler should be here.

#### Solvable/Dockerfile

Contains the commands to build up the solvable exercise's image. Please check the Dockerfile reference [here](https://docs.docker.com/engine/reference/builder/).

Quick note: in the `VOLUME` array you can define the routes that’ll be excluded from the read-only file system and will be shared with other containers. The basic commands of this file should be understood.

#### Solvable/webservice

The source files you want to be seen by the user in the TFW IDE should be put here.

#### Solvable/tutorial/<event handling implementation> (Only for Tutorial)

For further details, please click [here](https://github.com/avatao-content/tutorial#event-handling).

### Solvable/tutorial/fsm.yaml (Only for Tutorial)

A good state machine is the backbone of a good TFW exercise. For further details please click [here](https://github.com/avatao-content/tutorial#frontend-config-and-app-fsm).

### Solvable/nginx (Only for Tutorial)

In most cases you don’t have to deal with it.

All TFW based exercises expose a single port defined in the `TFW_PUBLIC_PORT` envvar which is set to `8888` by default. This means that in order to listen on more than a single port we must use a reverse proxy. Any `.conf` files in solvable/nginx/ will automatically be included in the nginx configuration. In case you want to serve a website or service, you must proxy it through `TFW_PUBLIC_PORT`. This is really easy: just create a config file in `solvable/nginx/` similar to this one:

```
location /yoururl {   
  proxy_pass http://127.0.0.1:3333;   
}
``` 

After this, you can access the service running on port `3333` at `http://localhost:8888/yoururl`. 

It’s very important to understand that from now on your application must behave well behind a reverse proxy. What this means is all hrefs must point the proxied paths \(e.g. links should refer to `/yoururl/register` instead of `/register`\) on your HTML pages. You can learn about configuring nginx in [this](https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts) handy little tutorial.

### Solvable/supervisor (Only for Tutorial)

In most Docker containers there’s a single process running \(it gets `PID 1`\). When working with TFW you can run as many processes as you want to, by using supervisord. 

Any `.conf` files in the solvable/supervisor/ directory will be included in the supervisor configuration. The programs, you define this way, are easy to manage \(starting/stopping/restarting\) using the `supervisorctl` command line tool or our built-in event handler. You can even configure your processes to start with the container by including `autostart=true` in your configuration file.

To run your own webservice for instance, you need to create a config file in `solvable/supervisor/` similar to this one:

```
[program:yourprogram]  
user=user  
directory=/home/user/example/  
command=python3 server.py  
autostart=true
```

This starts the `/home/user/example/server.py` script using Python3 after your container entered the running state \(because of autostart=true, supervisor doesn’t start programs by default\). You can learn more about configuring supervisor[ here](http://supervisord.org/configuration.html).

If there’s a simple console application, the only file here may be for the event handler, but if there's a webservice too, you also have to define a `webservice.conf`.  \(The webservice’s port should be `11111` in a TFW exercise, and `8888` in an attack type exercise\). In the following example a Spring Boot webservice starts as well with:

```
[program:webservice]  
user=root   
directory=%(ENV_TFW_WEBSERVICE_DIR)s  
environment=BASEURL="/webservice"   
command=mvn -o spring-boot:run -Dserver.port=11111   
autostart=true  
startretries=0   
stdout_logfile=/tmp/webservice_logs   
stderr_logfile=/tmp/webservice_logs
```

### Config.yml

See an example with description:
```
version: 'v2.0.0': should be this version   
name: "Integer overflow" : the name of the exercise you'll find on the Avatao platform  
difficulty: 100: should be between 10 and 500. If you’re not sure what the value should be, the best is nott to panic, we’ll check it for you!   
enable_flag_input: false : you can choose between flag-based and final-state-based solution checking here. If you want to use a flag, type "true" here (enable_flag_input: true) and next line write: "flag: 'string'"  
skills: ['C Sharp','Defensive','Secure Coding']: the tags that will appear at the Avatao platform. Choose them well and please use existing tags!  
recommendations:` [`http://projects.webappsec.org/w/page/13246946/Integer Overflows`](http://projects.webappsec.org/w/page/13246946/Integer%20Overflows)`: 'Integer Overflows attack type': The recommended readings section for each exercise is to provide a better understanding of the related security issue and should contain 1-4 items. Define a URL with : ’title’.  
owners: ['developer@avatao.com']: change to your own e-mail address
```

### 17. Other files

You can see some additional files \(`.drone.yml, CODEOWNERS, README.md`\) automatically created in the Git repository, but you don’t have to deal with them.
