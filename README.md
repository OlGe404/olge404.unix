# olge404.unix
This ansible collection is useful to streamline and ease the automated setup and configuration
of various software packages on unix-like operating systems (e.g. ubuntu, debian, alpine, RHEL and macOS).

# Prerequisites
Run the [python-venv.sh](scripts/python-venv.sh) and [docker-install.sh](scripts/docker-install.sh) scripts to bootstrap
the virtuelenv for python and to install docker (on ubuntu).

Next, run `source .venv/bin/activate` to activate the python virtualenv in your shell and
`ansible-galaxy collection install -r requirements.yaml` to install the necessary collections.

To test that all prerequisites are fullfilled, run `scripts/ansible-test.sh` and `scripts/moluecule-tests.sh apt`.
This will run tests for the ansible collection and for the `apt` role. If those can be performed, you are good to go.

# Add more roles
Read and follow the next sections to understand what a new role should look like, how you can get a headstart and what this fuzz is all about.

## Bootstrapping
Run the following commands to bootstrap the skeleton for a new role:

```bash
export ROLE_NAME="<roleName>"
cd roles
ansible-galaxy role init $ROLE_NAME
cd $ROLE_NAME
molecule init scenario --driver-name docker
molecule test
```

This creates a default layout for a new role (following ansible best practices) and tests if the molecule setup works.


## Scope
Each role should do one thing and one thing only. The goal is to keep each role very simple and testable to ensure
it does what you would expect from it by reading its `README`.

Good examples are roles like `apt`, `dnf` or `apk`. They are so simple that you might think "do I even need a dedicated role for that?" and the
answer is: YES! Using tested, simple building blocks is a key element to build any reliable automation and that is the purpose of this collection.

## Test platforms
Roles in this collection are tested on various unix-like operating systems (test platforms). Testing is done by leveraging molecule as test framework and docker as driver to launch the test platforms locally. This ensures repeatable test results by creating test infrastructure on-demand in a simple way.

Because we are using docker, we need a container image for each platform we want to run our tests on. The Dockerfiles for these container images can be found in the [.molecule/platforms dir](.molecule/platforms/). The [build.sh](.molecule/platforms/build.sh) script should be used to build, tag and push the container images to dockerhub. The container images are referenced in the [molecule.yml file](roles/apt/molecule/default/molecule.yml) for a role.

All test platforms need to be prepared to work with ansible and molecule. This includes:

* Installing python3
* Creating a non-root user to perform tests with
* Enable passwordless sudo for the non-root user

See the [Dockerfile for Ubuntu 22.04](.molecule/platforms/Dockerfile.ubuntu-22.04) as an example.
