# `galaxy-tool-tests`

Proof-of-concept container for running a Galaxy instance for testing with planemo.

The aim is to avoid planemo having to spin Galaxy up with every run.

## Status

- [x] Container spins up a Galaxy instance
- [x] Interface is accessible at http://localhost:8080
- [x] Local wrappers can be installed by mounting the wrapper XML and a tool_conf.xml
- [x] Changes to the wrapper are reflected in the running instance
- [x] `planemo test` with the `--galaxy_url` argument triggers tests without spinning up a new instance
- [ ] The Galaxy instance has to be configured to resolve dependencies
- [ ] Work out how to run the instance without `sudo`
- [ ] Add equivalent Docker commands

## Instructions

This is for testing tool wrappers locally.
**Don't host this container on the web**.
It runs a server over plain http and the admin password is stored in the Dockerfile.


**TODO**:
:   Add Docker instructions

### Spin up the instance

```bash
sudo singularity instance start \
    docker://ghcr.io/tomharrop/galaxy-tool-tests:v0.0.2 \
    galaxy-tool-test

sudo singularity run \
    instance://galaxy-tool-test
```

Now you can access Galaxy at http://localhost:8080.

To install tools, create an account with the email specified in the Dockerfile.
The details can be overridden by setting the environment variables.

**TODO**:
:   Make this run when the instance starts, like Singularity's `runscript`


### Stop the instance

```bash
sudo singularity instance stop \
    galaxy-tool-test
```

### Install local tool wrappers




```bash
sudo singularity instance start \
    -B ${PWD},${TMPDIR} \
    -H $(mktemp -d) \
    --containall --cleanenv --writable-tmpfs \
    docker://ghcr.io/tomharrop/galaxy-tool-tests:v0.0.2 \
    galaxy-tool-test

sudo singularity run \
    -B ${PWD},${TMPDIR} \
    -H $(mktemp -d) \
    --containall --cleanenv --writable-tmpfs \
    instance://galaxy-tool-test
