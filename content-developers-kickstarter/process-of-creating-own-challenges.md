# Process of creating own challenges

### 1. Create a challenge on the Avatao platform

After logging in, click to **Dashboard** \(1.\) on the Avatao platform, then there will be a **Create a challenge** \(2.\) menu on the left \(if you can’t see this, please contact us\). Here, all you have to do is give the repository name \(3.\). If you hada challenge with the name „Integer overflow” in C\#, the repo name could be integer-overflow-csharp. Please use only lowercase characters and hyphens instead of spaces. The repo name of course should correspond to name of the challenge. Your registered username and the date will automatically be concatenated on the repo’s name. 

![](../.gitbook/assets/kep%20%284%29.png)

### Creating a new branch

After that, you’ll be redirected to a page where you can find the URL of the created GitHub repository. Your next task is to create a new branch with name `staging` \(see the image on the right\).This is necessary because you can’t write on the `master` branch due to security reasons. You have to clone this repo to your local:

```
$ git clone -b staging <repo-url.git>
```

![](../.gitbook/assets/kep%20%283%29.png)

### Choose an existing challenge

As for your next step, it’s recommended to choose an existing challenge, similar to your ideas \(for example the _Deploy mechanism in the event handler_ is the one that matches the most your ideas\), rewrite the parts you need, and change the related files.

### Implement the application itself

You should place this application in the `solvable/src/` or `solvable/src/webservice/` directory.

### Visualize the scenario

Think about the scenario of the challenge: what and how to show the related \(security\) issues. This takes some getting used to , so if you’rea newbie you can ask a more experienced colleague. Please solve as much challenges on the Avatao platform as any you can, to get some XP!

### Modifying the necessary files

You’ll also have to rewrite the following files \(see details later\):

* `metadata/description.md`: the description of the challenge that’llbe seen on the platform. As you can see, it’s supported by Markdown markup language
* `metadata/summary.md`: one or two sentences to summarize what the challenge is about
* `metadata/writeup.md`: you’ll need a writeup only in the attack challenges
* `config.yml`: contains the configuration of the challenge
* `solvable/Dockerfile`: contains the instructions to build the solvable challenge’s image
* `solvable/frontend/src/app/config.ts`: you can find the Angular frontend’s configs here
* `solvable/supervisor`: directory for the process manager. Each process will be run with .conf extension here
* `solvable/src`: directory for the webservice and other application-related source files
* `solvable/src/tfw_server.py`: the server app
* `solvable/supervisor/tfw_server.conf`: to set up the server to run
* `solvable/src/event_handler_main.py`: the event handler
* `solvable/src/test_fsm.py`: a final state machine that describes the actions triggered at several states

### Upload your files

You can push your files to the remote repository during the development and - of course - after completing the challenge, too. To do soyou can use the following commands:

`git add .   
git commit –m”<message>”   
git push origin staging`

If you want to see the challenge on the platform, you also have to ask for a merge `staging` branch to the `master` branch, by clicking on „New pull request” on the GitHub repo. After , the building of the project will automatically start. 

If the challenge was built successfully and the merge is done, you’ll ****find it on the Avatao platform by doing a search. At this stage, this can be seen by the Avatao staff only. Before uploading your challenge to the platform, it’s recommended to run the `check-format.py` in the `challenge-toolbox` repo. As a parameter, you should add the local route of your repo.

![](../.gitbook/assets/kep%20%286%29.png)

