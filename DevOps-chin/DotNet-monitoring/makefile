# Docker image settings
IMAGE_REG ?= docker.io
IMAGE_REPO ?= chunmay123/dotnet-monitoring
IMAGE_TAG ?= latest

# Used by test-api
TEST_HOST ?= localhost:5000

SRC_DIR := src
TEST_DIR := tests

.PHONY: help lint lint-fix image push run test test-report test-api clean .EXPORT_ALL_VARIABLES
.DEFAULT_GOAL := help

help: ## Show available commands
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", $$1, $$2}'

lint: ## Check and format .NET code
	@dotnet format --help > /dev/null 2> /dev/null || dotnet tool install --global dotnet-format
	dotnet format --verbosity diag ./src

image: ## Build Docker image
	docker build . --file build/Dockerfile \
	--tag $(IMAGE_REG)/$(IMAGE_REPO):$(IMAGE_TAG)

push: ## Push Docker image to Docker Hub
	docker push $(IMAGE_REG)/$(IMAGE_REPO):$(IMAGE_TAG)

run: ## Run the application locally
	dotnet watch --project $(SRC_DIR)/dotnet-demoapp.csproj

test: ## Run unit tests
	dotnet test tests/tests.csproj

test-report: ## Run unit tests and create reports
	rm -rf $(TEST_DIR)/TestResults
	dotnet test $(TEST_DIR)/tests.csproj --test-adapter-path:. --logger:junit --logger:html

test-api: .EXPORT_ALL_VARIABLES ## Run API tests
	cd tests \
	&& npm install newman \
	&& ./node_modules/.bin/newman run ./postman_collection.json --env-var apphost=$(TEST_HOST)

clean: ## Clean project build files
	rm -rf $(TEST_DIR)/node_modules
	rm -rf $(TEST_DIR)/package*
	rm -rf $(TEST_DIR)/TestResults
	rm -rf $(TEST_DIR)/bin
	rm -rf $(TEST_DIR)/obj
	rm -rf $(SRC_DIR)/bin
	rm -rf $(SRC_DIR)/obj
