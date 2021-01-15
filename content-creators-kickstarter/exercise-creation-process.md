# Exercise creation process

## Create an exercise on the Avatao platform

You can create an exercise with the [CreateChallenge](https://github.com/avatao-content/content-scripts-next/blob/master/geri/extensions/CreateChallenge.js) Tampermonkey extension. Click [here] (https://github.com/avatao-content/content-scripts-next/tree/master/geri/extensions) for the installation guide.

After installing it, you can access the creator form [here](https://next.avatao.com/new).

Please make sure to fill the form with valid information, otherwise you will not get access to the created repository.

{% hint style="info" %}
How to pick a good exercise name? Make sure it's unique, related to the exercise, but it's not too general (to avoid name collision and to keep the platform fun).

Good:
 * Externalized secrets *(it's an XXE exercise)*
 * Hello Stored XSS *(it's about the basics of stored XSS)*
 
Bad *(these are basically skill tags)*:
 * Direct object reference Java tutorial
 * Command injection tutorial in ASP.NET
{% endhint %}

## Preparing a local version

Clone the repository to your local machine. The `master` branch is protected so you'll need to create a development branch that you can use for testing. We suggest to name it `staging` since every branch named `staging` will be built and will be available on the platform for creators. This way you can test your exercise on the platform without publishing it.

```
git clone <repo-url.git>
git checkout -b staging
git push -u origin staging
```

## Exercise development

The development process is fairly straightforward, create an exercise on the platform, develop it on the `staging` branch and then submit a pull-request to the `master` branch. We'll review your exercise and provide feedback if needed. When an exercise meets every requirement, we'll accept the pull-request and it'll be available on the Avatao platform. You can always submit new pull-request if you want to change your exercise.

### Tutorial

For tutorial exercises, please use the [Avatao StartR](https://github.com/avatao-content/test-tfw-startr) application to generate a starting project for yourself. You can choose from multiple languages and frameworks. In case your desired language or framework is not available, just download one of the `Hello-world` applications and modify it.

These exercises use the Tutorial Framework, please see the [Wiki](https://github.com/avatao-content/baseimage-tutorial-framework/wiki) for more information.

### Attack

For CTF like exercises we suggest to download one of the starting templates from the [Challenge Toolbox](https://github.com/avatao-content/challenge-toolbox/tree/v3/templates). These contains simple projects that can help you with the development. The two main parts are the `controller` and the `solvable` folders that will be run as Docker containers. The `controller` is responsible for the solution checking while the `solvable` contains the vulnerabile application and accessed by the user.

Usually, the goal is to retrieve and submit a flag that proves the user successfully exploited the vulnerability, but you are free to set the goal to your liking. You can implement different type of solution check in the `controller` container, such as checking a specific endpoint of the vulnerable application with a headless browser. You can find more details about them in the [Exercise structure](exercise-structure.md) section.

## Maintenance

To change parameters of an existing exercise, such as name or description, please use the [EZApi extension](https://github.com/avatao-content/content-scripts-next/blob/master/geri/extensions/EZAPI.js).

To modify the application or downloadable files, please create a pull-request on GitHub.
