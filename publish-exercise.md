# Exercise creation

## Prerequisites
You'll need access for the [Avatao-Content GitHub organization](https://github.com/avatao-content) and creator privileges on the [Avatao platform](https://next.avatao.com).

## Create an exercise on the platform

## Extensions
Operation on exercises require the usage of various TamperMonkey extensions. You can find them with the usage guide [here](https://github.com/avatao-content/content-scripts-next/tree/master/geri/extensions).

### Creation and development
Install the `CreateChallenge` extension and access it [here](https://next.avatao.com/new).

With this, you can generate an exercise on Avatao with a GitHub repository. Clone the repository and create a branch called `staging`. The `master` branch contains the published exercise while the `staging` branch is for development purposes. Both branches will trigger a build on every push. 

To test your unpublished changes, just visit your exercise on the Avatao platform and before starting it, select the `staging` branch.

We suggest to use one of the prepared starting projects instead of developing your own vulnerable application. These starting projects contains a working exercise using the Tutorial Framework, you just have to modify them to contain the intended vulnerability. More at the [`Generate a starter`](Generate a starter) section.

### Publish an exercise
Create a pull request to merge your changes into the `master` branch. After passing the manual review, your exercise will be published.

### Modify an existing exercise
Install the `EZAPI` extension and access it

## Generate a starter
We've prepared starter projects that you can use to speed up the development. You can find the list of available languages and frameworks [here](TODO).

You can use the [StartR tool](TODO) to generate these. It'll create a ZIP file, just extract it and copy the files into the cloned repository.
