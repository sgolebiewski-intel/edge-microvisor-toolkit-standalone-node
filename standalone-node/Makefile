# Standalone Node Makefile

# SPDX-FileCopyrightText: (C) 2025 Intel Corporation
# SPDX-License-Identifier: Apache-2.0

.DEFAULT_GOAL := help
.PHONY: build lint license help fuzz

# Optionally include tool version checks, not used in Docker builds
TOOL_VERSION_CHECK ?= 0

##### Variables #####

# Defining the shell, users and groups
SHELL       := bash -e -o pipefail
CURRENT_UID := $(shell id -u)
CURRENT_GID := $(shell id -g)

# Project variables
PROJECT_NAME := standalonenode
BINARY_NAME  := $(PROJECT_NAME)

# Code versions, tags, and so on
VERSION       ?= $(shell cat VERSION)
VERSION_MAJOR ?= $(shell cut -c 1 VERSION)
IMG_NAME      ?= ${PROJECT_NAME}
IMG_VERSION   ?= $(VERSION)
GIT_COMMIT    ?= $(shell git rev-parse HEAD)

# Yamllint variables
YAML_FILES           := $(shell find . -path './venv_$(PROJECT_NAME)' -path './vendor' -prune -o -type f \( -name '*.yaml' -o -name '*.yml' \) -print )
YAML_IGNORE          := vendor, .github/workflows

# Include shared makefile
include ../common.mk

all: 
	@# Help: Runs build, lint, test stages
	build lint test 	

configure:
	echo 'http_proxy=$(http_proxy)' > hook_os/config
	echo 'https_proxy=$(http_proxy)' >> hook_os/config
	echo 'ftp_proxy=$(ftp_proxy)' >> hook_os/config
	echo 'socks_proxy=$(socks_proxy)' >> hook_os/config
	echo 'no_proxy=$(no_proxy)' >> hook_os/config

lint: license yamllint mdlint

build: configure
	@# Help: Runs build stage
	@echo "---MAKEFILE ISO BUILD---"
	echo $@
	cd installation_scripts && ./build-hook-os-iso.sh && cd ..
	@echo "---END MAKEFILE Build---"
	
image:
	@# Help: Runs build stage
	@echo "---MAKEFILE BUILD---"
	echo $@
	cd host_os && ./download_tmv.sh && cd ..
	@echo "---END MAKEFILE Build---"

docker-build: 
	@# Help: Runs docker-build stage
	@echo "---MAKEFILE BUILD---"
	echo $@
	@echo "---END MAKEFILE Build---"

dependency-check:
	@# Help: Runs dependency-check stage
	@echo "---MAKEFILE TEST---"
	echo $@
	@echo "---END MAKEFILE TEST---"

clean:
	@# Clean: Remove build files
	@echo "---MAKEFILE CLEAN---"
	cd host_os && rm -f *.gz *.sha256sum *.der && cd ..
	cd hook_os && make clean && cd ..
	cd installation_scripts && rm -rf out && cd ..
	rm -rf ./out
	rm -rf .hypothesis
	@echo "---END MAKEFILE CLEAN---"

list: 
	@# Help: displays make targets
	help

fuzz: $(VENV_NAME)
	@# Help: Runs fuzz testing for 60 seconds
	@echo "---MAKEFILE FUZZ TEST---"
	mkdir -p ./out
	@. $(VENV_NAME)/bin/activate; $(VENV_NAME)/bin/python ./esc/fuzz_Edge_microvisor.py 60 > ./out/fuzz_repor_4hr.logs 2>&1
	@echo "---END MAKEFILE FUZZ TEST---"
