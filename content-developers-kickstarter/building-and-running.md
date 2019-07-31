# Building and running

It’s possible to build and run a challenge two ways:

1. **Automated**: with the `./hack/tfw.sh` start command. This a bit faster because this way the frontend will run locally. The challenge will be at `localhost:4200`.

2**.** With **challenge-toolbox**: you can also use our toolbox to build and run TFW based challenges, just like the regular ones. Note that this will always create a production built with the frontend included. The challenge will be at `localhost:8888`.

* `./build.py <challenge path like ../jakabakos-integer-overflow-csharp>`
* `./start.py <challenge path like ../jakabakos-integer-overflow-csharp>`

There’sa very useful script in the `challenge-toolbox` repo to purge your environment from the useless files of Docker, so if your disk starts to run out of space, run the `docker-cleanup.sh`.

