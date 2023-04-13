# Updating our application

As a small feature request, we've been asked by the product team to change the "empty text" when we don't have any todo
list items. They would like to transition it to the following:

```
You have no todo items yet! Add one above!
```

Pretty simple, right? Let's make the change.

### Updating our Source Code

In the src/static/js/app.js file, update line 56 to use the new empty text.

```diff
-                <p className="text-center">No items yet! Add one above!</p>
+                <p className="text-center">You have no todo items yet! Add one above!</p>
```

Let's build our updated version of the image, using the same command we used before.

```shell
$ docker build -t getting-started .
```

Let's start a new container using the updated code.

```shell
docker run -dp 3000:3000 getting-started
```

Uh oh! You probably saw an error like this (the IDs will be different):

```shell
docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell
(bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 0.0.0.0:3000 failed: port is already
allocated.
```

So, what happened? We aren't able to start the new container because our old container is still running. The reason this
is a problem is because that container is using the host's port 3000 and only one process on the machine (containers
included) can listen to a specific port. To fix this, we need to remove the old container.

Replacing our Old Container¶
To remove a container, it first needs to be stopped. Once it has stopped, it can be removed. We have two ways that we
can remove the old container. Feel free to choose the path that you're most comfortable with.

### Removing a container using the CLI

Get the ID of the container by using the docker ps command.

```shell
$ docker ps
```

Use the docker stop command to stop the container.

Swap out <the-container-id> with the ID from docker ps

```shell
$ docker stop <the-container-id>
```

Once the container has stopped, you can remove it by using the docker rm command.

```shell
$ docker rm <the-container-id>
```

#### Pro tip

`
You can stop and remove a container in a single command by adding the "force" flag to the docker rm command. For
example: docker rm -f <the-container-id>
`

### Starting our updated app container

Now, start your updated app.

docker run -dp 3000:3000 getting-started
Refresh your browser on http://localhost:3000 and you should see your updated help text!

Updated application with updated empty text

---

# Sharing our application

Now that we've built an image, let's share it! To share Docker images, you have to use a Docker registry. The default
registry is Docker Hub and is where all of the images we've used have come from.

### Create a Repo

To push an image, we first need to create a repo on Docker Hub.

Go to Docker Hub and log in if you need to.

Click the Create Repository button.

For the repo name, use getting-started. Make sure the Visibility is Public.

Click the Create button!

If you look on the right-side of the page, you'll see a section named Docker commands. This gives an example command
that you will need to run to push to this repo.

Docker command with push example

### Pushing our Image

In the command line, try running the push command you see on Docker Hub. Note that your command will be using your
namespace, not "docker".

```shell
$ docker push docker/getting-started

The push refers to repository [docker.io/docker/getting-started]
An image does not exist locally with the tag: docker/getting-started
```

Why did it fail? The push command was looking for an image named docker/getting-started, but didn't find one. If you run
docker image ls, you won't see one either.

To fix this, we need to "tag" our existing image we've built to give it another name.

Login to Docker Hub using the command

```shell
$ docker login -u YOUR-USER-NAME --password-stdin
```

Use the docker tag command to give the getting-started image a new name. Be sure to swap out YOUR-USER-NAME with your
Docker ID.

```shell
$ docker tag getting-started YOUR-USER-NAME/getting-started
```

Now try your push command again. If you're copying the value from Docker Hub, you can drop the tagname portion, as we
didn't add a tag to the image name. If you don't specify a tag, Docker will use a tag called latest.

```shell
$ docker push YOUR-USER-NAME/getting-started
````

Running our Image on a New Instance¶
Now that our image has been built and pushed into a registry, let's try running our app on a brand new instance that has
never seen this container image! To do this, we will use Play with Docker.

Open your browser to Play with Docker.

Log in with your Docker Hub account.

Once you're logged in, click on the "+ ADD NEW INSTANCE" link in the left side bar. (If you don't see it, make your
browser a little wider.) After a few seconds, a terminal window will be opened in your browser.

Play with Docker add new instance

In the terminal, start your freshly pushed app.

```shell
$ docker run -dp 3000:3000 YOUR-USER-NAME/getting-started
```

You should see the image get pulled down and eventually start up!

Click on the 3000 badge when it comes up and you should see the app with your modifications! Hooray! If the 3000 badge
doesn't show up, you can click on the "Open Port" button and type in 3000.

---
[Home](../README.md)
