# Jenkins-master Docker container

This project builds a jenkins-master docker image and a persistent storage container to use for continuous integration projects.

# Deploy

Run the following to deploy Jenkins after building both the master and data images.

```
docker run --name=jenkins-data jenkins-data
docker run -p 8080:8080 -p 50000:50000 --name=jenkins-master --volumes-from=jenkins-data -d jenkins-master
```

# Test

[Goss](https://github.com/aelsabbahy/goss]) (Go YAML based serverspec tool) is used to validate the jenkins-master container by launching a test image with `FROM jenkins-master`. Its purpose is to confirm the master image was built w/required packages, plugins and other attributes defined from its Dockerfile. The `jenkins-test` container will run the goss tests and exit with appropriate exit code.

```
docker run jenkins-test
...........................................
Total Duration: 0.744s
Count: 43, Failed: 0, Skipped: 0
```

# Workflow

If you need to iterate with the `jenkins-master` image, follow this process:

* Update the jenkins-master Dockerfile
* Build the jenkins-master image locally `docker build —tag jenkins-master -f Dockerfile .`
* Build the jenkins-test image locally `docker build —tag jenkins-test -f Dockerfile-test .`
* Run the tests `docker run jenkins-test`
* If all is well, commit and push



# Local Development

The following is copied over from the [dgoss](https://github.com/aelsabbahy/goss/blob/master/extras/dgoss/README.md) README.

#### Linux:

Follow the goss [installation instructions](https://github.com/aelsabbahy/goss#installation)

#### Mac OSX

Since goss runs on the target container, dgoss can be used on a Mac OSX system by doing the following:

```
# Install dgoss
curl -L https://raw.githubusercontent.com/aelsabbahy/goss/master/extras/dgoss/dgoss -o /usr/local/bin/dgoss
chmod +rx /usr/local/bin/dgoss

# Download goss to your preferred location
curl -L https://github.com/aelsabbahy/goss/releases/download/v0.3.4/goss-linux-amd64 -o ~/Downloads/goss-linux-amd64

# Set your GOSS_PATH to the above location
export GOSS_PATH=~/Downloads/goss-linux-amd64

# Use dgoss
dgoss edit ...
dgoss run ...
```

## Usage

`dgoss [run|edit] <docker_run_params>`

### Run

Run is used to validate a docker container. It expects a `./goss.yaml` file to exist in the directory it was invoked from. In most cases one can just substitute the docker command for the dgoss command, for example:

**run:**

`docker run -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`

**test:**

`dgoss run -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`

`dgoss run` will do the following:

- Run the container with the flags you specified.
- Stream the containers log output into the container as `/goss/docker_output.log`
  - This allows writing tests or waits against the docker output
- (optional) Run `goss` with `$GOSS_WAIT_OPTS` if `./goss_wait.yaml` file exists in the current dir
- Run `goss` with `$GOSS_OPTS` using `./goss.yaml`

### Edit

Edit will launch a docker container, install goss, and drop the user into an interactive shell. Once the user quits the interactive shell, any `goss.yaml` or `goss_wait.yaml` are copied out into the current directory. This allows the user to leverage the `goss add|autoadd` commands to write tests as they would on a regular machine.

**Example:**

`dgoss edit -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`



# Build status

[![Build Status](https://semaphoreci.com/api/v1/2ffs2nns/jenkinsci/branches/master/badge.svg)](https://semaphoreci.com/2ffs2nns/jenkinsci)
