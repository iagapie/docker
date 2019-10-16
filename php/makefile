help: #prints list of commands
	@cat ./makefile | grep : | grep -v "grep"

build_cli_debian: #create image v=[version]
	@docker build -t iagapie/php:$(v)-cli-debian -f ./cli/$(v)/Dockerfile ./cli/$(v)

build_cli_alpine: #create image v=[version]
	@docker build -t iagapie/php:$(v)-cli-alpine -f ./cli/$(v)/Dockerfile-alpine ./cli/$(v)

build_fpm_debian: #create image v=[version]
	@docker build -t iagapie/php:$(v)-fpm-debian -f ./fpm/$(v)/Dockerfile ./fpm/$(v)

build_fpm_alpine: #create image v=[version]
	@docker build -t iagapie/php:$(v)-fpm-alpine -f ./fpm/$(v)/Dockerfile-alpine ./fpm/$(v)

push: #push image t=[tag]
	@docker push iagapie/php:$(t)