# Docker image for `terragrunt-fmt`

[![Build Status](https://travis-ci.com/cytopia/docker-terragrunt-fmt.svg?branch=master)](https://travis-ci.com/cytopia/docker-terragrunt-fmt)
[![Tag](https://img.shields.io/github/tag/cytopia/docker-terragrunt-fmt.svg)](https://github.com/cytopia/docker-terragrunt-fmt/releases)
[![](https://images.microbadger.com/badges/version/cytopia/terragrunt-fmt:latest.svg?&kill_cache=1)](https://microbadger.com/images/cytopia/terragrunt-fmt:latest "terragrunt-fmt")
[![](https://images.microbadger.com/badges/image/cytopia/terragrunt-fmt:latest.svg?&kill_cache=1)](https://microbadger.com/images/cytopia/terragrunt-fmt:latest "terragrunt-fmt")
[![](https://img.shields.io/badge/github-cytopia%2Fdocker--terragrunt-fmt-red.svg)](https://github.com/cytopia/docker-terragrunt-fmt "github.com/cytopia/docker-terragrunt-fmt")
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
> [goimports](https://github.com/cytopia/docker-php-cs-fixer) **•**
> [golint](https://github.com/cytopia/docker-golint) **•**
> [jsonlint](https://github.com/cytopia/docker-jsonlint) **•**
> [phpcbf](https://github.com/cytopia/docker-phpcbf) **•**
> [phpcs](https://github.com/cytopia/docker-phpcs) **•**
> [php-cs-fixer](https://github.com/cytopia/docker-php-cs-fixer) **•**
> [pycodestyle](https://github.com/cytopia/docker-pycodestyle) **•**
> [pylint](https://github.com/cytopia/docker-pylint) **•**
> [terraform-docs](https://github.com/cytopia/docker-terraform-docs) **•**
> [terragrunt](https://github.com/cytopia/docker-terragrunt) **•**
> [terragrunt-fmt](https://github.com/cytopia/docker-terragrunt-fmt) **•**
> [yamllint](https://github.com/cytopia/docker-yamllint)


> #### All [#awesome-ci](https://github.com/topics/awesome-ci) Makefiles
>
> Visit **[cytopia/makefiles](https://github.com/cytopia/makefiles)** for seamless project integration, minimum required best-practice code linting and CI.

View **[Dockerfile](https://github.com/cytopia/docker-terragrunt-fmt/blob/master/Dockerfile)** on GitHub.

[![Docker hub](http://dockeri.co/image/cytopia/terragrunt-fmt?&kill_cache=1)](https://hub.docker.com/r/cytopia/terragrunt-fmt)

Tiny Alpine-based multistage-build dockerized version of [Terraform](https://github.com/hashicorp/terraform)<sup>[1]</sup> with the ability to do `terraform fmt` on Terragrunt files (`.hcl`).

<sub>[1] Official project: https://github.com/hashicorp/terraform</sub>


## Available Docker image versions

The following Docker image tags are rolling releases and built and updated nightly. This means
they always contain the latest stable version as shown below.

| Docker tag   | Terraform version      |
|--------------|------------------------|
| `latest`     | latest stable          |
| `0.12`       | latest stable `0.12.x` |
| `0.11`       | latest stable `0.11.x` |


## Docker mounts

The working directory inside the Docker container is **`/data/`** and should be mounted to your local filesystem where your Terragrant project resides.
(See [Examples](#examples) for mount location usage.)


## Usage
```
$ docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt --help
```
```
Usage: cytopia/terragrunt-fmt [options] [DIR]
       cytopia/terragrunt-fmt --help
       cytopia/terragrunt-fmt --version

       Rewrites all Terragrunt configuration files to a canonical format. All
       hcl configuration files (.hcl) are updated.

       If DIR is not specified then the current working directory will be used.

Options:

  -list=true     List files whose formatting differs

  -write=false   Don't write to source files
                 (always disabled if using -check)

  -diff          Display diffs of formatting changes

  -check         Check if the input is formatted. Exit status will be 0 if all
                 input is properly formatted and non-zero otherwise.

  -recursive     Also process files in subdirectories. By default, only the
                 given directory (or current directory) is processed.

  -ignore=a,b    Comma separated list of paths to ignore.
                 The wildcard character '*' is supported.
```


## Examples

#### List filenames that need to be fixed
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -list
```

#### Show diff of files that need to be fixed
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -diff
```

#### Fix files
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -write
```

#### Fix files and show diff
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -write -diff
```

#### List filenames that need to be fixed recursively
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -list -recursive
```

#### Show diff of files that need to be fixed recursively
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -diff -recursive
```

#### Fix recursively
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -write -recursive
```

#### Ignore files and directories

Ignore all files named `terragrunt.hcl`.
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -recursive -ignore=*terragrunt.hcl
```

Ignore all directories named `dev/` and everything inside.
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -recursive -ignore=*/dev/
```

Ignore all directories named `dev/` and `testing/` and everything inside.
```bash
docker run --rm -v $(pwd):/data cytopia/terragrunt-fmt -recursive -ignore=*/dev/,*/testing/
```


## Project and CI integration

#### Makefile
You can add the following Makefile to your project for easy linting anf fixing of Terragrunt `.hcl` files.
```make
ifneq (,)
.error This Makefile requires GNU Make.
endif

.PHONY: help lint fix _pull

CURRENT_DIR = $(dir $(abspath $(lastword $(MAKEFILE_LIST))))

# Adjust according to your needs
IGNORE      = */.terragrunt-cache/*/.terraform/
FMT_VERSION = latest

help:
	@echo "help    Show this help"
	@echo "lint    Exit > 0 if any files have wrong formatting"
	@echo "fix     Fix all .hcl files"

lint: _pull
	docker run --rm -v $(CURRENT_DIR):/data cytopia/terragrunt-fmt:$(FMT_VERSION) \
		-check -diff -recursive -ignore='$(IGNORE)'

fix: _pull
	docker run --rm -v $(CURRENT_DIR):/data cytopia/terragrunt-fmt:$(FMT_VERSION) \
		-write -diff -recursive -ignore='$(IGNORE)'

_pull:
	docker pull cytopia/terraform-fmt:$(FMT_VERSION)
```

#### Travis CI integration
With the above Makefile in place, you can easily add a Travis CI rule to ensure the Terragrunt code
uses correct coding style.

```yml
---
sudo: required
language: minimal
services:
  - docker
install: true
script:
  - make lint
```


## Related [#awesome-ci](https://github.com/topics/awesome-ci) projects

### Docker images

Save yourself from installing lot's of dependencies and pick a dockerized version of your favourite
linter below for reproducible local or remote CI tests:

| Docker image | Type | Description |
|--------------|------|-------------|
| [awesome-ci](https://github.com/cytopia/awesome-ci) | Basic | Tools for git, file and static source code analysis |
| [file-lint](https://github.com/cytopia/docker-file-lint) | Basic | Baisc source code analysis |
| [jsonlint](https://github.com/cytopia/docker-jsonlint) | Basic | Lint JSON files **<sup>[1]</sup>** |
| [yamllint](https://github.com/cytopia/docker-yamllint) | Basic | Lint Yaml files |
| [ansible](https://github.com/cytopia/docker-ansible) | Ansible | Multiple versoins of Ansible |
| [ansible-lint](https://github.com/cytopia/docker-ansible-lint) | Ansible | Lint  Ansible |
| [gofmt](https://github.com/cytopia/docker-gofmt) | Go | Format Go source code **<sup>[1]</sup>** |
| [goimports](https://github.com/cytopia/docker-php-cs-fixer) | Go | Format Go source code **<sup>[1]</sup>** |
| [golint](https://github.com/cytopia/docker-golint) | Go | Lint Go code |
| [eslint](https://github.com/cytopia/docker-eslint) | Javascript | Lint Javascript code |
| [checkmake](https://github.com/cytopia/docker-checkmake) | Make | Lint Makefiles |
| [phpcbf](https://github.com/cytopia/docker-phpcbf) | PHP | PHP Code Beautifier and Fixer |
| [phpcs](https://github.com/cytopia/docker-phpcs) | PHP | PHP Code Sniffer |
| [php-cs-fixer](https://github.com/cytopia/docker-php-cs-fixer) | PHP | PHP Coding Standards Fixer |
| [black](https://github.com/cytopia/docker-black) | Python | The uncompromising Python code formatter |
| [pycodestyle](https://github.com/cytopia/docker-pycodestyle) | Python | Python style guide checker |
| [pylint](https://github.com/cytopia/docker-pylint) | Python | Python source code, bug and quality checker |
| [terraform-docs](https://github.com/cytopia/docker-terraform-docs) | Terraform | Terraform doc generator (TF 0.12 ready) **<sup>[1]</sup>** |
| [terragrunt](https://github.com/cytopia/docker-terragrunt) | Terraform | Terragrunt and Terraform |
| [terragrunt-fmt](https://github.com/cytopia/docker-terragrunt-fmt) | Terraform | `terraform fmt` for Terragrunt files **<sup>[1]</sup>** |

> **<sup>[1]</sup>** Uses a shell wrapper to add **enhanced functionality** not available by original project.

### Makefiles

Visit **[cytopia/makefiles](https://github.com/cytopia/makefiles)** for dependency-less, seamless project integration and minimum required best-practice code linting for CI.
The provided Makefiles will only require GNU Make and Docker itself removing the need to install anything else.


## License

**[MIT License](LICENSE)**

Copyright (c) 2019 [cytopia](https://github.com/cytopia)