# `galaxy-tool-tests`

Proof-of-concept container for running a Galaxy instance for testing with planemo.

The aim is to avoid planemo having to spin Galaxy up with every run.

## Status

-[x] Container spins up a Galaxy instance
-[x] Interface is accessible at http://localhost:8080
-[x] Local wrappers can be installed by mounting the wrapper XML and a tool_conf.xml
-[x] Changes to the wrapper are reflected in the running instance
-[x] `planemo test` with the `--galaxy_url` argument triggers tests without spinning up a new instance
-[ ] The 

## Instructions

This is for testing tool wrappers locally.
**Don't host this container on the web**.
It runs a server over plain http and the admin password is stored in the Dockerfile.


