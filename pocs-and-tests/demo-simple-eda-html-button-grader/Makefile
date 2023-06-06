CONTAINER_ENGINE=docker # podman  # docker 
COMPOSE_FILE=docker-compose.yml

# SECRETS_FILE=$(SECRETS_DIR)/secret-base-rhel-infra-aws-sandbox0$(SANDBOX).yaml
EXTRA_ARGS=
# Adjust to taste

: ## TIP! make supports tab completion with *modern* shells e.g. zsh etc
: ## e.g. make depl<TAB> == make deploy 
: ## 

.SILENT: setup my-env ssh-target

help: ## Show this help - technically unnecessary as `make` alone will do
help: ## 
	@egrep -h '\s##\s' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-20s\033[0m %s\n", ($$2=="" ? "" : $$1 ),  $$2}' | less

# Thanks to victoria.dev for the above syntax
# https://victoria.dev/blog/how-to-create-a-self-documenting-makefile/


prune: ## Simple container-engine prune, be careful this will remove all stopped containers
prune: ## 
	$(CONTAINER_ENGINE) container prune #-f

up: ## 
up: ## 
	$(CONTAINER_ENGINE) compose -f $(COMPOSE_FILE) up -d --remove-orphans

# TODO: Simple down with remove orphans
# TODO: Simple status
# TODO: Simple exec onto container


down: ## 
down: ## 
	$(CONTAINER_ENGINE) compose -f $(COMPOSE_FILE) down --remove-orphans


deploy: ## Deploy ent-demo-automate-the-enterprise
deploy: ## See README.adoc for recommended contents of ~/secrets/secret-aws-ate-no-sandbox.yml 
deploy: ## 
	docker compose up -d --remove-orphans
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/main.yml \
		-e @$(SECRETS_FILE) \
		-e env_type=$(ENV_TYPE) \
		$(EXTRA_ARGS)

nav-deploy: ## Deploy ent-demo-automate-the-enterprise
nav-deploy: ## See README.adoc for recommended contents of ~/secrets/secret-aws-ate-no-sandbox.yml 
nav-deploy: ## 
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-navigator run \
		--container-engine  $(CONTAINER_ENGINE) \
		--eei $(EE):$(EE_TAG) \
		$(PLAYBOOK_DIR)/$(PLAYBOOK) \
		-e @$(SECRETS_FILE) \
		-e env_type=$(ENV_TYPE) \
		$(EXTRA_ARGS)


deploy-fast: ## Deploy ent-demo-automate-the-enterprise without AAP2 Controller, package updates etc
deploy-fast: ## See README.adoc for recommended contents of ~/secrets/secret-aws-ate-no-sandbox.yml 
deploy-fast: ## 
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/main.yml \
		-e @$(SECRETS_FILE) \
		-e env_type=$(ENV_TYPE) \
		-e agd_deploy_automationplatform=false \
		-e agd_automationcontroller_cert=false \
		-e agd_automationhub_cert=false \
		-e update_packages=false \
		$(EXTRA_ARGS)

destroy: ## Destroy ent-demo-automate-the-enterprise
destroy: ## See README.adoc for recommended contents of ~/secrets/secret-aws-ate-no-sandbox.yml 
destroy: ## 
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/destroy.yml \
		-e @$(SECRETS_FILE) \
		-e env_type=$(ENV_TYPE) \

ssh-target: ## ssh to your bastion by default or use `make ssh-target target=hostname` 
ssh-target: ## Assumes an existing output_dir
ssh-target: ## 
	echo "\n\nYou may want to 'sudo su - devops' - the primary user if you are on bastion\n\n"
	ssh -F $(OUTPUT_DIR)/sbox0$(SANDBOX)/$(ENV_TYPE)_sbox0$(SANDBOX)_ssh_conf $(TARGET)

last-status: ## Output last status file
last-status: ## Update status file via make status
last-status: ## 
	ls -l $(OUTPUT_DIR)/sbox0$(SANDBOX)/status.txt
	cat $(OUTPUT_DIR)/sbox0$(SANDBOX)/status.txt

# | awk '{ print \$6 " " $7 " " $8 " " $9 " " $10 }'
update-status: ## Generate status
update-status: ## Update status file
update-status: ## 
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/lifecycle_entry_point.yml \
		-e ACTION=status \
		-e @$(SECRETS_FILE) \
		-e env_type=$(ENV_TYPE) \

#	ansible-playbook ../../../ansible/lifecycle_entry_point.yml \
stop: ## Suspend, stop, instances
stop: ## Restart with make start
stop: ## 
stop:
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/lifecycle_entry_point.yml \
			-e ACTION=stop \
			-e @$(SECRETS_FILE) \
			-e env_type=$(ENV_TYPE) \

start: ## Suspend, start, instances
start: ## Restart with make start
start: ## 
	cd ../../.. ; \
	export ANSIBLE_LOG_PATH=$(OUTPUT_DIR)/sbox0$(SANDBOX)/base-app2-infra-sbox0$(SANDBOX).log; \
	ansible-playbook $(PLAYBOOK_DIR)/lifecycle_entry_point.yml \
			-e ACTION=start \
			-e @$(SECRETS_FILE) \
			-e env_type=$(ENV_TYPE) \

bounce: ## Bounce the deploy 
bounce: ## IE stop then start
bounce: ## 
bounce: stop start

exit-make-help: ## You're in less - type q to exit
