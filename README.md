# `galaxy-tool-tests`

Proof-of-concept container for running a Galaxy instance for testing with planemo.

The aim is to avoid planemo having to spin Galaxy up with every run.

## Status

- [x] Container spins up a Galaxy instance
- [x] Interface is accessible at http://localhost:8080
- [x] You can create a login with the test account for admin access to the GUI
- [x] Local wrappers can be installed by mounting the wrapper XML and a tool_conf.xml
- [x] Changes to the wrapper are reflected in the running instance
- [x] `planemo test` with the `--galaxy_url` argument triggers tests without spinning up a new instance
- [ ] The Galaxy instance has to be configured to resolve dependencies
- [ ] Configure `galaxy.yml` during the build
- [ ] Work out how to run the instance without `sudo`
- [ ] Add equivalent Docker commands
- [ ] Add data managers

## Instructions

This is for testing tool wrappers locally.
**Don't host this container on the web**.
It runs a server over plain http and the admin password is stored in the Dockerfile.


**TODO**: Add Docker instructions

### Spin up the instance

```bash
sudo singularity instance start \
    --writable-tmpfs \
    docker://ghcr.io/tomharrop/galaxy-tool-tests:v0.0.2 \
    galaxy-tool-test

sudo singularity run \
    --writable-tmpfs \
    instance://galaxy-tool-test
```

Now you can access Galaxy at http://localhost:8080.

To install tools, create an account with the email specified in the Dockerfile.
The details can be overridden by setting the environment variables.

**TODO**: Make this run when the instance starts, like Singularity's `runscript`. Right now this can be done by building the image from `galaxy-tool-test.Singularity`


### Stop the instance

```bash
sudo singularity instance stop \
    galaxy-tool-test
```

### Install local tool wrappers

This just requires some extra arguments to the run command to mount the local files into the container.

I'm demonstrating this with a wrapper at `local_tools/cactus/cactus_cactus.xml`.
You can add other tools to `job_conf.xml`.

Make sure you don't already have the instance up before running the following commands.

```bash
sudo singularity instance start \
    -B ${PWD},${TMPDIR} \
    -H $(mktemp -d) \
    --containall --cleanenv --writable-tmpfs \
    -B "$(readlink -f local_tools)":/local_tools/ \
    -B "$(readlink -f tool_conf.xml)":/galaxy/server/config/tool_conf.xml.sample \
    docker://ghcr.io/tomharrop/galaxy-tool-tests:v0.0.2 \
    galaxy-tool-test

sudo singularity run \
    -B ${PWD},${TMPDIR} \
    -H $(mktemp -d) \
    --containall --cleanenv --writable-tmpfs \
    instance://galaxy-tool-test
```

Now you have Galaxy running with the wrapper in local_tools installed.
Changes to the wrapper and to job_conf.xml are reflected if you refresh the interface in your browser.

### Trigger a test with `planemo`

```bash
sudo singularity exec \
    instance://galaxy-tool-test \
    planemo test \
    --galaxy_url http://localhost:8080 \
    --galaxy_admin_key fakekey \
    --galaxy_root /galaxy/server \
    /local_tools/cactus/cactus_cactus.xml
```

It runs straight away with no spin up time!

```bash
cactus_cactus (Test #1): failed
cactus_cactus (Test #2): failed
cactus_cactus (Test #3): failed
cactus_cactus (Test #4): failed

real    0m1.181s
user    0m0.001s
sys 0m0.004s
```

Right now the tests are failing with `command not found`, because there is no dependency resolution configured.

