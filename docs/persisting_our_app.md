# Persisting Our DB

In case you didn't notice, our todo list is being wiped clean every single time we launch the container. Why is this?
Let's dive into how the container is working.

##The Container's Filesystem¶
When a container runs, it uses the various layers from an image for its filesystem. Each container also gets its own "
scratch space" to create/update/remove files. Any changes won't be seen in another container, even if they are using the
same image.

## Seeing this in Practice

To see this in action, we're going to start two containers and create a file in each. What you'll see is that the files
created in one container aren't available in another.

Start a ubuntu container that will create a file named /data.txt with a random number between 1 and 10000.

```shell
$ docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```

In case you're curious about the command, we're starting a bash shell and invoking two commands (why we have the &&).
The first portion picks a single random number and writes it to /data.txt. The second command is simply watching a file
to keep the container running.

```shell
$ cat /data.txt
```

If you prefer the command line you can use the docker exec command to do the same. You need to get the container's ID (
use docker ps to get it) and get the content with the following command.

```shell
$ docker exec <container-id> cat /data.txt
```

You should see a random number!

Now, let's start another ubuntu container (the same image) and we'll see we don't have the same file.

```shell
$ docker run -it ubuntu ls /
```

And look! There's no data.txt file there! That's because it was written to the scratch space for only the first
container.

Go ahead and remove the first container using the docker rm -f <container-id> command.

```shell
$ docker rm -f <container-id>
````

## Container Volumes

With the previous experiment, we saw that each container starts from the image definition each time it starts. While
containers can create, update, and delete files, those changes are lost when the container is removed and all changes
are isolated to that container. With volumes, we can change all of this.

Volumes provide the ability to connect specific filesystem paths of the container back to the host machine. If a
directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that
same directory across container restarts, we'd see the same files.

There are two main types of volumes. We will eventually use both, but we will start with named volumes.

## Persisting our Todo Data

By default, the todo app stores its data in a SQLite Database at `/etc/todos/todo.db`. If you're not familiar with
SQLite, no worries! It's simply a relational database in which all of the data is stored in a single file. While this
isn't the best for large-scale applications, it works for small demos. We'll talk about switching this to a different
database engine later.

With the database being a single file, if we can persist that file on the host and make it available to the next
container, it should be able to pick up where the last one left off. By creating a volume and attaching (often called "
mounting") it to the directory the data is stored in, we can persist the data. As our container writes to the todo.db
file, it will be persisted to the host in the volume.

As mentioned, we are going to use a named volume. Think of a named volume as simply a bucket of data. Docker maintains
the physical location on the disk and you only need to remember the name of the volume. Every time you use the volume,
Docker will make sure the correct data is provided.

Create a volume by using the docker volume create command.

```shell
$ docker volume create todo-db
```

Stop the todo app container once again in the Dashboard (or with `docker rm -f <container-id>`), as it is still running
without using the persistent volume.

Start the todo app container, but add the `-v` flag to specify a volume mount. We will use the named volume and mount it
to `/etc/todos`, which will capture all files created at the path.

```shell
$ docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```

Once the container starts up, open the app and add a few items to your todo list.

Items added to todo list

Remove the container for the todo app. Use the Dashboard or docker ps to get the ID and then docker rm -f <container-id>
to remove it.

Start a new container using the same command from above.

Open the app. You should see your items still in your list!

Go ahead and remove the container when you're done checking out your list.

Hooray! You've now learned how to persist data!

Pro-tip

While named volumes and bind mounts (which we'll talk about in a minute) are the two main types of volumes supported by
a default Docker engine installation, there are many volume driver plugins available to support NFS, SFTP, NetApp, and
more! This will be especially important once you start running containers on multiple hosts in a clustered environment
with Swarm, Kubernetes, etc.

Diving into our Volume
A lot of people frequently ask "Where is Docker actually storing my data when I use a named volume?" If you want to
know, you can use the docker volume inspect command.

```shell
$ docker volume inspect todo-db
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

The Mountpoint is the actual location on the disk where the data is stored. Note that on most machines, you will need to
have root access to access this directory from the host. But, that's where it is!

---

# Using Bind mounts

In the previous chapter, we talked about and used a **named volume** to persist the data in our database.
Named volumes are great if we simply want to store data, as we don't have to worry about _where_ the data
is stored.

With **bind mounts**, we control the exact mountpoint on the host. We can use this to persist data, but is often
used to provide additional data into containers. When working on an application, we can use a bind mount to
mount our source code into the container to let it see code changes, respond, and let us see the changes right
away.

For Node-based applications, [nodemon](https://npmjs.com/package/nodemon) is a great tool to watch for file
changes and then restart the application. There are equivalent tools in most other languages and frameworks.

## Quick Volume Type Comparisons

Bind mounts and named volumes are the two main types of volumes that come with the Docker engine. However, additional
volume drivers are available to support other use
cases ([SFTP](https://github.com/vieux/docker-volume-sshfs), [Ceph](https://ceph.com/geen-categorie/getting-started-with-the-docker-rbd-volume-plugin/), [NetApp](https://netappdvp.readthedocs.io/en/stable/), [S3](https://github.com/elementar/docker-s3-volume),
and more).

|                                              | Named Volumes             | Bind Mounts                   |
|----------------------------------------------|---------------------------|-------------------------------|
| Host Location                                | Docker chooses            | You control                   |
| Mount Example (using `-v`)                   | my-volume:/usr/local/data | /path/to/data:/usr/local/data |
| Populates new volume with container contents | Yes                       | No                            |
| Supports Volume Drivers                      | Yes                       | No                            |

## Starting a Dev-Mode Container

To run our container to support a development workflow, we will do the following:

- Mount our source code into the container
- Install all dependencies, including the "dev" dependencies
- Start nodemon to watch for filesystem changes

So, let's do it!

1. Make sure you don't have any of your own `getting-started` containers running (only the tutorial itself should be
   running).

1. Also make sure you are in app source code directory, i.e. `/path/to/getting-started/app`. If you aren't, you can `cd`
   into it, .e.g:

    ```bash
    $ cd /path/to/getting-started/app
    ```

1. Now that you are in the `getting-started/app` directory, run the following command. We'll explain what's going on
   afterwards:

    ```bash
    $ docker run -dp 3000:3000 \
        -w /app -v "$(pwd):/app" \
        node:18-alpine \
        sh -c "yarn install && yarn run dev"
    ```

   If you are using PowerShell then use this command.

    ```powershell
    $ docker run -dp 3000:3000 `
        -w /app -v "$(pwd):/app" `
        node:18-alpine `
        sh -c "yarn install && yarn run dev"
    ```

    - `-dp 3000:3000` - same as before. Run in detached (background) mode and create a port mapping
    - `-w /app` - sets the container's present working directory where the command will run from
    - `-v "$(pwd):/app"` - bind mount (link) the host's present `getting-started/app` directory to the
      container's `/app` directory. Note: Docker requires absolute paths for binding mounts, so in this example we
      use `pwd` for printing the absolute path of the working directory, i.e. the `app` directory, instead of typing it
      manually
    - `node:18-alpine` - the image to use. Note that this is the base image for our app from the Dockerfile
    - `sh -c "yarn install && yarn run dev"` - the command. We're starting a shell using `sh` (alpine doesn't
      have `bash`) and
      running `yarn install` to install _all_ dependencies and then running `yarn run dev`. If we look in
      the `package.json`,
      we'll see that the `dev` script is starting `nodemon`.

1. You can watch the logs using `docker logs -f <container-id>`. You'll know you're ready to go when you see this...

    ```bash
    $ docker logs -f <container-id>
    $ nodemon src/index.js
    [nodemon] 2.0.20
    [nodemon] to restart at any time, enter `rs`
    [nodemon] watching path(s): *.*
    [nodemon] watching extensions: js,mjs,json
    [nodemon] starting `node src/index.js`
    Using sqlite database at /etc/todos/todo.db
    Listening on port 3000
    ```

   When you're done watching the logs, exit out by hitting `Ctrl`+`C`.

1. Now, let's make a change to the app. In the `src/static/js/app.js` file, let's change the "Add Item" button to simply
   say
   "Add". This change will be on line 109 - remember to save the file.

    ```diff
    -                         {submitting ? 'Adding...' : 'Add Item'}
    +                         {submitting ? 'Adding...' : 'Add'}
    ```

1. Simply refresh the page (or open it) and you should see the change reflected in the browser almost immediately. It
   might
   take a few seconds for the Node server to restart, so if you get an error, just try refreshing after a few seconds.

   ![Screenshot of updated label for Add button](./img/updated-add-button.png)


7. Feel free to make any other changes you'd like to make. When you're done, stop the container and build your new image
   using `docker build -t getting-started .`.

Using bind mounts is _very_ common for local development setups. The advantage is that the dev machine doesn't need to
have
all the build tools and environments installed. With a single `docker run` command, the dev environment is pulled and
ready
to go. We'll talk about Docker Compose in a future step, as this will help simplify our commands (we're already getting
a lot
of flags).
---
[Home](../README.md)
