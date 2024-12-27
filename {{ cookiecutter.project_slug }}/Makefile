PROJECT_NAME = {{ cookiecutter.project_name }}
ORGANIZATION = {{ cookiecutter.author }}
DESCRIPTION  = {{ cookiecutter.project_description }}
GOCMD ?= go
GOBUILD=$(GOCMD) build
GOCLEAN=$(GOCMD) clean
GOTEST=$(GOCMD) test
GOTOOL=$(GOCMD) tool
GOGET=$(GOCMD) get
GOMOD=$(GOCMD) mod
GOINST=$(GOCMD) install
GOPATH ?= $(shell $(GOCMD) env GOPATH)
GOBIN ?= $(GOPATH)/bin

# Tools & Linters
GOLANGCI_LINT ?= $(GOBIN)/golangci-lint
GOFUMPT ?= $(GOBIN)/gofumpt
GOVULNCHECK ?= $(GOBIN)/govulncheck
GOREMAN?= $(GOBIN)/goreman
AIR ?= $(GOBIN)/air


BINARY_NAME = {{ cookiecutter.project_slug }}
MAIN_SRC = ./cmd/{{ cookiecutter.project_slug }}

DEPS = wget grep
K := $(foreach exec,$(DEPS),\
        $(if $(shell which $(exec)),some string,$(error "🥶 `$(exec)` not found in PATH please install it")))

all: help

tidy:
	@$(GOMOD) tidy

## Build
dev: tools ## 🚀 run in watch mode
	$(GOREMAN) start

test:
	$(GOTEST) ./... -v

build: tidy ## 📦 build the project into a binary
	@$(GOBUILD) -o $(BINARY_NAME) $(MAIN_SRC)
	@echo "📦 built"

clean: ## 🧹 Clean files and caches
	@$(GOCLEAN)
	@rm -f $(BINARY_NAME)
	@echo "🧹 cleaned"

tools:
	@if [ ! -f "$(GOLANGCI_LINT)" ]; then \
		$(GOINST) github.com/golangci/golangci-lint/cmd/golangci-lint@latest; \
	fi
	@if [ ! -f "$(GOFUMPT)" ]; then \
		$(GOINST) mvdan.cc/gofumpt@latest; \
	fi
	@if [ ! -f "$(GOVULNCHECK)" ]; then \
		$(GOINST) golang.org/x/vuln/cmd/govulncheck@latest; \
	fi
	@if [ ! -f "$(GOREMAN)" ]; then \
		$(GOINST) github.com/mattn/goreman@latest; \
	fi
	@if [ ! -f "$(AIR)" ]; then \
		$(GOINST) github.com/air-verse/air@latest; \
	fi

GREEN  := $(shell tput -Txterm setaf 2)
YELLOW := $(shell tput -Txterm setaf 3)
WHITE  := $(shell tput -Txterm setaf 7)
CYAN   := $(shell tput -Txterm setaf 6)
RESET  := $(shell tput -Txterm sgr0)

VERSION_STRATEGY = semver # git, semver, date
VERSION := $(shell cat VERSION 2>/dev/null || echo "0.1.0")
GIT_COMMIT ?= $(shell git rev-parse --short HEAD 2>/dev/null || echo "unknown")
GIT_BRANCH ?= $(shell git rev-parse --abbrev-ref HEAD 2>/dev/null || echo "unknown")
BUILD_TIME ?= $(shell date -u '+%Y-%m-%d_%H:%M:%S')
BUILD_BY ?= $(shell whoami)

version: ## Display version information
	@echo "$(CYAN)Version:$(RESET)    $(VERSION)"
	@echo "$(CYAN)Commit:$(RESET)     $(GIT_COMMIT)"
	@echo "$(CYAN)Branch:$(RESET)     $(GIT_BRANCH)"
	@echo "$(CYAN)Built:$(RESET)      $(BUILD_TIME)"
	@echo "$(CYAN)Built by:$(RESET)   $(BUILD_BY)"
	@echo "$(CYAN)Go version:$(RESET) $(shell $(GOCMD) version)"

## Help:
help: ## Show this help.
	@echo ''
	@echo 'Usage:'
	@echo '  ${YELLOW}make${RESET} ${GREEN}<target>${RESET}'
	@echo ''
	@echo 'Targets:'
	@awk 'BEGIN {FS = ":.*?## "} { \
		if (/^[a-zA-Z_-]+:.*?##.*$$/) {printf "    ${YELLOW}%-20s${GREEN}%s${RESET}\n", $$1, $$2} \
		else if (/^## .*$$/) {printf "  ${CYAN}%s${RESET}\n", substr($$1,4)} \
		}' $(MAKEFILE_LIST)
