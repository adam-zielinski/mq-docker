
.PHONY: build
build:
	@echo "Building images ..."
	@docker build -t cstanciu/nifi-mq .

.PHONY: run
run:
	docker run --env LICENSE=accept --env MQ_QMGR_NAME=QM1 --volume ibmmqvol:/mnt/mqm --volume /Users/cs186081/ibmmq/vol:/shared-data --publish 1414:1414 --publish 9443:9443 --detach cstanciu/nifi-mq

.PHONY: stop
stop:
	@docker stop 



