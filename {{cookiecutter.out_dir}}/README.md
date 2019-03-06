# Initialise your development environment

All following commands must be run only once at project installation.


## First clone

```sh
git clone --recursive {{cookiecutter.git_project_url}}
{%-if cookiecutter.use_submodule_for_deploy_code%}git submodule init --recursive  # only the fist time
git submodule upate{%endif%}
```

## Install docker and docker compose

if you are under debian/ubuntu/mint/centos you can do the following:

```sh
.ansible/scripts/download_corpusops.sh
.ansible/scripts/setup_corpusops.sh
local/*/bin/cops_apply_role --become \
    local/*/*/corpusops.roles/services_virt_docker/role.yml
```

... or follow official procedures for
  [docker](https://docs.docker.com/install/#releases) and
  [docker-compose](https://docs.docker.com/compose/install/).


## Update corpusops
You may have to update corpusops time to time with
￼
```
./control.sh up_corpusops
```
￼
## Configuration

Use the wrapper to init configuration files from their ``.dist`` counterpart
and adapt them to your needs.

```bash
./control.sh init
```

**Hint**: You may have to add `0.0.0.0` to `ALLOWED_HOSTS` in `local.php`.

## Login to the app docker registry

You need to login to our docker registry to be able to use it:


```bash
docker login {{cookiecutter.docker_registry}}  # use your gitlab user
```

{%- if cookiecutter.registry_is_gitlab_registry %}
**⚠️ See also ⚠️** the
    [project docker registry]({{cookiecutter.git_project_url.replace('ssh://', 'https://').replace('git@', '')}}/container_registry)
{%- else %}
**⚠️ See also ⚠️** the makinacorpus doc in the docs/tools/dockerregistry section.
{%- endif%}

# Use your development environment

## Update submodules

Never forget to grab and update regulary the project submodules:

```sh
git pull
{%-if cookiecutter.use_submodule_for_deploy_code%}git submodule init --recursive  # only the fist time
git submodule upate{%endif%}
```

## Control.sh helper

You may use the stack entry point helper which has some neat helpers but feel
free to use docker command if you know what your are doing.

```bash
./control.sh usage # Show all available commands
```

## Start the stack

After a last verification of the files, to run with docker, just type:

```bash
# First time you download the app, or sometime to refresh the image
./control.sh pull # Call the docker compose pull command
./control.sh up # Should be launched once each time you want to start the stack
```

## Launch app as foreground

```bash
./control.sh fg
```

**⚠️ Remember ⚠️** to use `./control.sh up` to start the stack before.

## Start a shell inside the {{cookiecutter.app_type}} container

- for user shell

    ```sh
    ./control.sh usershell
    ```
- for root shell

    ```sh
    ./control.sh shell
    ```

**⚠️ Remember ⚠️** to use `./control.sh up` to start the stack before.

## Rebuild/Refresh local docker image in dev

```sh
control.sh buildimages
```

## Run tests

```sh
./control.sh tests
# also consider: linting|coverage
```

**⚠️ Remember ⚠️** to use `./control.sh up` to start the stack before.

## Docker volumes

Your application extensivly use docker volumes. From times to times you may
need to erase them (eg: burn the db to start from fresh)

```sh
docker volume ls  # hint: |grep \$app
docker volume rm $id
```

## Doc for deployment on environments
- [See here](./.docs/README.md)

## Wordpress settings managment
- We embrace many concepts to manage wordpress settings
    - 12Factors: we try to make system environment the primary sources of settings
    - For hosted environments, we use ByEnv pythonic settings that extends prod and
      leverage complexity of combining settings by allowing to write logic to factorize the needed glue
- The layout and variable precedence is to load local.php which loads itself values from env
    - If the value is exposed on the environment, whenever you add/edit it, you need to add it
        - to ``docker.env`` & ``docker.env.dist`` in dev
        - To **ansible setup**, [Read this section of the ansible readme](./.docs/README.md#wordpress-settings-setup).

