# docker-gcc

docker-gcc allows you to easily create a simple gcc environment inside of a specified container to compile.

Currently only supports debian based distribution docker containers

## Example usage:

#### Create an environment
```bash
docker-gcc create debian:10-slim
```

#### Compile the file test.c in the same directory

```bash
docker-gcc test.c -o test
```

#### Destroy the environment

```bash
docker-gcc destroy
```

## Installation
docker-gcc depends on docker and python3. Follow your local distributions installation instructions.

Then git clone the repo:

```
git clone https://github.com/Mymaqn/docker-gcc.git
```

Once cloned you can either run it from the local directory or add it to your PATH

```bash
./docker-gcc create debian:10-slim
```

## Contribution
There are only two requirements for making changes
1) Everything should be contained in the single file for easy installation. Basically you should be able to curl the raw docker-gcc file with python3 and docker installed, whereafter running it just works
2) All changes are only allowed to depend on Python 3.9 standard libraries and up. I'm not installing any seperate libraries to make this work.

If your code fullfills these two requirements you are welcome to submit a PR with your changes

## Features for the future
1) Having an option in an env to do `docker-gcc clean` to remove the environment file in case of errors
2) Having the option to copy out libc and ld easily, then patching the binary to use them locally
3) Having the option to add extra installation commands to the setup process to install any extra needed libraries
4) Support for non-debian based docker images
5) A way to make sure the user that compiles the file has full access to it after it comes out of the docker container. It's annoying if you want to delete it, it asks you if you're sure as it's "write-protected"
6) Finding a way to port the current argument reading into argparse without the tool becoming a nuisance to use (there should never be a command before your gcc arguments)

## How does it work?
In the file there is a simple python wrapper around the needed docker functionality using subprocess, which handles the interaction with docker

### Create
When creating a container the following flow is followed
1) Performs a docker run command in detached mode with the specified image, which installs needed dependences for gcc. It then spawns bash to keep the container running
2) When done, the container outputs a file called .docker-gcc-done into the local directory
3) When the .docker-gcc-done file is detected, it commits the running docker container to a new image
4) It outputs the name of the image into a .docker-gcc-env file.

### Compiling
When compiling the .docker-gcc-env file is read to get the image which is used for compiling and a docker run command is supplied to handle the compilation

### Destroying
Destroying the enviornment removes the .docker-gcc-env file and deletes the image

## Q&A
Q: I deleted the image manually, and now I can't create a new container as it can't find the image, what do I do?
A: Delete the .docker-gcc-env file in the local directory. You should always use docker-gcc destroy to remove your environment afterwards if you'd like to avoid errors

Q: Will you implement x?
A: Submit a PR or issue with the request and I'll take a look at it

Q: Why does it have to be in one file?
A: I want this to be an extremely simple tool to use and install. The whole idea of this project comes from the fact that I don't like supplying very long docker run commands and build compilation images whenever I need to compile. In my opinion the whole idea of the project breaks if you make it a whole long process to even install it. It should just work and it should be easy to make work

Q: What coding standard should I use in my PR?
A: I don't care. As long as I can reasonably easily read your code, it doesn't matter to me.

Q: I have a very specific issue X, what do I do?
A: Submit an issue in here, and I'll take a look. Please provide specific instructions on how to replicate the issue

Q: How do I clean up ALL of the images?
A: Ideally you should run docker-gcc destroy when you're done compiling to avoid images piling up. However I also know it's not that simple to do that every time. Therefore all docker-gcc generated images always follow the following naming convention:
`docker-gcc/name-of-original-image-tag:original_container_id`
You should therefore be able to just prune all images with the docker-gcc prefix. Make sure that if you want to compile again in a folder where you've compiled before, that you remove the .docker-gcc-env file before creating a new environment.


