# Copyright (c) 2023 Red Hat, Inc.

################################################
###### Regional DR Trigger Operator Chart ######
################################################
LOCALBIN = $(shell pwd)/bin
$(LOCALBIN):
	mkdir -p $(LOCALBIN)

OS=$(shell go env GOOS)
ARCH=$(shell go env GOARCH)

BIN_CURL ?= curl
BIN_YQ ?= yq

BIN_HELM ?= $(LOCALBIN)/helm
VERSION_HELM = v3.14.0

OPERATOR_REPO ?= https://github.com/RHEcosystemAppEng/regional-dr-trigger-operator
OPERATOR_BRANCH =? main

CHART_VERSION ?= $(shell $(BIN_YQ) '.version' Chart.yaml)

TEMP_FOLDER = operator_tmp # ignored by git

.PHONY: lint
lint: $(BIN_HELM)
	$(BIN_HELM) lint .

.PHONY: package
package: $(BIN_HELM)
	$(BIN_HELM) package .

.PHONY: test
test: $(BIN_HELM)
	$(BIN_HELM) template . > /dev/null

.PHONY: install
install: $(BIN_HELM)
	$(BIN_HELM) install --generate-name .

.PHONY: generate
generate:
	rm -rf $(TEMP_FOLDER)
	git clone --branch $(OPERATOR_BRANCH) $(OPERATOR_REPO) $(TEMP_FOLDER)
	cd $(TEMP_FOLDER) && $(MAKE) generate/chart CHART_VERSION=$(CHART_VERSION) CHART_TARGET=$(PWD)
	rm -rf $(TEMP_FOLDER)

$(BIN_HELM): $(LOCALBIN)
	$(BIN_CURL) -sSL https://get.helm.sh/helm-$(VERSION_HELM)-$(OS)-$(ARCH).tar.gz | tar xzf - -C $(LOCALBIN) --strip-components=1 --wildcards '*/helm'
