# Docker image for `ansible-lint`

[![Build Status](https://travis-ci.com/cytopia/docker-ansible-lint.svg?branch=master)](https://travis-ci.com/cytopia/docker-ansible-lint)
[![Tag](https://img.shields.io/github/tag/cytopia/docker-ansible-lint.svg)](https://github.com/cytopia/docker-ansible-lint/releases)
[![](https://images.microbadger.com/badges/version/cytopia/ansible-lint:latest.svg?kill_cache=1)](https://microbadger.com/images/cytopia/ansible-lint:latest "ansible-lint")
[![](https://images.microbadger.com/badges/image/cytopia/ansible-lint:latest.svg?kill_cache=1)](https://microbadger.com/images/cytopia/ansible-lint:latest "ansible-lint")
[![](https://img.shields.io/badge/github-cytopia%2Fdocker--ansible--lint-red.svg)](https://github.com/cytopia/docker-ansible-lint "github.com/cytopia/docker-ansible-lint")
[![License](https://img.shields.io/badge/license-MIT-%233DA639.svg)](https://opensource.org/licenses/MIT)

> #### All [#awesome-ci](https://github.com/topics/awesome-ci) Docker images
>
> [ansible](https://github.com/cytopia/docker-ansible) **•**
> [ansible-lint](https://github.com/cytopia/docker-ansible-lint) **•**
> [awesome-ci](https://github.com/cytopia/awesome-ci) **•**
> [black](https://github.com/cytopia/docker-black) **•**
> [checkmake](https://github.com/cytopia/docker-checkmake) **•**
> [eslint](https://github.com/cytopia/docker-eslint) **•**
> [file-lint](https://github.com/cytopia/docker-file-lint) **•**
> [gofmt](https://github.com/cytopia/docker-gofmt) **•**
> [golint](https://github.com/cytopia/docker-golint) **•**
> [jsonlint](https://github.com/cytopia/docker-jsonlint) **•**
> [phpcs](https://github.com/cytopia/docker-phpcs) **•**
> [pycodestyle](https://github.com/cytopia/docker-pycodestyle) **•**
> [pylint](https://github.com/cytopia/docker-pylint) **•**
> [terraform-docs](https://github.com/cytopia/docker-terraform-docs) **•**
> [terragrunt](https://github.com/cytopia/docker-terragrunt) **•**
> [yamllint](https://github.com/cytopia/docker-yamllint)


> #### All [#awesome-ci](https://github.com/topics/awesome-ci) Makefiles
>
> Visit **[cytopia/makefiles](https://github.com/cytopia/makefiles)** for seamless project integration, minimum required best-practice code linting and CI.


View **[Dockerfile](https://github.com/cytopia/docker-ansible-lint/blob/master/Dockerfile)** on GitHub.

[![Docker hub](http://dockeri.co/image/cytopia/ansible-lint?kill_cache=1)](https://hub.docker.com/r/cytopia/ansible-lint)

Tiny Alpine-based multistage-build dockerized version of [ansible-lint](https://github.com/ansible/ansible-lint)<sup>[1]</sup>.
The image is built nightly against the latest stable version of `ansible-lint` and pushed to Dockerhub.

<sup>[1] Official project: https://github.com/ansible/ansible-lint</sup>


## Available Docker image versions

| Docker tag | Build from |
|------------|------------|
| `latest`   | Current stable ansible-lint version |


## Docker mounts

The working directory inside the Docker container is **`/data/`** and should be mounted locally to
the root of your project where your `.ansible-lint` config file is located.


## Usage

```bash
# Single playbook
docker run --rm -v $(pwd):/data cytopia/ansible-lint playbook.yml

# All playbooks via wildcard
docker run --rm -v $(pwd):/data cytopia/ansible-lint *.yml
```


## Related [#awesome-ci](https://github.com/topics/awesome-ci) projects

### Docker images

Save yourself from installing lot's of dependencies and pick a dockerized version of your favourite
linter below for reproducible local or remote CI tests:

| Docker image | Type | Description |
|--------------|------|-------------|
| [awesome-ci](https://github.com/cytopia/awesome-ci) | Basic | Tools for git, file and static source code analysis |
| [file-lint](https://github.com/cytopia/docker-file-lint) | Basic | Baisc source code analysis |
| [jsonlint](https://github.com/cytopia/docker-jsonlint) | Basic | Lint JSON files |
| [yamllint](https://github.com/cytopia/docker-yamllint) | Basic | Lint Yaml files |
| [ansible](https://github.com/cytopia/docker-ansible) | Ansible | Multiple versoins of Ansible |
| [ansible-lint](https://github.com/cytopia/docker-ansible-lint) | Ansible | Lint  Ansible |
| [gofmt](https://github.com/cytopia/docker-gofmt) | Go | Format Go source code |
| [golint](https://github.com/cytopia/docker-golint) | Go | Lint Go code |
| [eslint](https://github.com/cytopia/docker-eslint) | Javascript | Lint Javascript code |
| [checkmake](https://github.com/cytopia/docker-checkmake) | Make | Lint Makefiles |
| [phpcs](https://github.com/cytopia/docker-phpcs) | PHP | PHPCodeSniffer and Code Beautifier and Fixer |
| [black](https://github.com/cytopia/docker-black) | Python | The uncompromising Python code formatter |
| [pycodestyle](https://github.com/cytopia/docker-pycodestyle) | Python | Python style guide checker |
| [pylint](https://github.com/cytopia/docker-pylint) | Python | Python source code, bug and quality checker |
| [terraform-docs](https://github.com/cytopia/docker-terraform-docs) | Terraform | Terraform doc generator (TF 0.12 ready) |
| [terragrunt](https://github.com/cytopia/docker-terragrunt) | Terraform | Terragrunt and Terraform |


### Makefiles

Visit **[cytopia/makefiles](https://github.com/cytopia/makefiles)** for dependency-less, seamless project integration and minimum required best-practice code linting for CI.
The provided Makefiles will only require GNU Make and Docker itself removing the need to install anything else.


## License

**[MIT License](LICENSE)**

Copyright (c) 2019 [cytopia](https://github.com/cytopia)
