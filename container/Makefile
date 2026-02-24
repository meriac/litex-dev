.PHONY: run rebuild build clean login publish status version multi
CONTAINER_NAME:=litex-dev
CONTAINER_NAME_RISCV:=$(CONTAINER_NAME)-riscv

CONTAINER_OWNER:=meriac
CONTAINER_REGISTRY:=ghcr.io/$(CONTAINER_OWNER)
CONTAINER_PREFIX:=$(CONTAINER_REGISTRY)/$(CONTAINER_NAME)
CONTAINER_PREFIX_RISCV:=$(CONTAINER_REGISTRY)/$(CONTAINER_NAME_RISCV)
CONTAINER_GITHUB_LINK:=https://github.com/$(CONTAINER_OWNER)/$(CONTAINER_NAME)

# determine container architecture
CONTAINER_ARCH:=$(shell uname -m)
ifeq ("$(CONTAINER_ARCH)","x86_64")
	CONTAINER_ARCH:=amd64
endif
CONTAINER_PLATFORM:=linux/$(CONTAINER_ARCH)

# Supported containers
CONTAINER:=$(CONTAINER_PREFIX):$(CONTAINER_ARCH)
CONTAINER_RISCV:=$(CONTAINER_PREFIX_RISCV):$(CONTAINER_ARCH)

run:
	podman run -h $(CONTAINER_NAME) --rm -v $(PWD)/workspaces:/workspaces --platform $(CONTAINER_PLATFORM) -it localhost/$(CONTAINER_NAME)

rebuild: clean build run

build:
	podman build --platform $(CONTAINER_PLATFORM) -t $(CONTAINER_NAME)       -t $(CONTAINER)       -f Containerfile       --build-arg GITHUB_LINK=$(CONTAINER_GITHUB_LINK)
	podman build --platform $(CONTAINER_PLATFORM) -t $(CONTAINER_NAME_RISCV) -t $(CONTAINER_RISCV) -f Containerfile.RISCV --build-arg GITHUB_LINK=$(CONTAINER_GITHUB_LINK)

clean:
	podman rmi -f $(CONTAINER) $(CONTAINER_RISCV)

clean_all: clean
	podman rm -f --all
	podman system prune -af

login:
	@echo $(CR_PAT) | podman login $(CONTAINER_REGISTRY) -u DOESNT_MATTER --password-stdin

publish: status
	podman push --compress --compression-level 9 $(CONTAINER)
	podman push --compress --compression-level 9 $(CONTAINER_RISCV)

status:
	podman image tree $(CONTAINER)
	podman image tree $(CONTAINER_RISCV)

version:
	@echo $(CONTAINER)

manifest:
	podman rmi -f $(CONTAINER_PREFIX):latest
	podman manifest create $(CONTAINER_PREFIX):latest $(CONTAINER_PREFIX):amd64 $(CONTAINER_PREFIX):arm64
	podman push --compress --compression-level 9 $(CONTAINER_PREFIX):latest
	podman rmi -f $(CONTAINER_PREFIX):latest

	podman rmi -f $(CONTAINER_PREFIX_RISCV):latest
	podman manifest create $(CONTAINER_PREFIX_RISCV):latest $(CONTAINER_PREFIX_RISCV):amd64 $(CONTAINER_PREFIX_RISCV):arm64
	podman push --compress --compression-level 9 $(CONTAINER_PREFIX_RISCV):latest
	podman rmi -f $(CONTAINER_PREFIX_RISCV):latest
