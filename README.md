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

## Features for the future
1) Having an option in an env to do `docker-gcc clean` to remove the environment file in case of errors
2) Having the option to copy out libc and ld easily, then patching the binary to use them locally
3) Having the option to add extra installation commands to the setup process to install any extra needed libraries
4) Support for non-debian based docker images
5) A way to make sure the user that compiles the file has full access to it after it comes out of the docker container. It's annoying if you want to delete it, it asks you if you're sure as it's "write-protected"
6) Finding a way to port the current argument reading into argparse without the tool becoming a nuisance to use (there should never be a command before your gcc arguments)
