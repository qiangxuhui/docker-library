# This file is generated by the template.

REGISTRY?=lcr.loongnix.cn
ORGANIZATION?=edgexfoundry
REPOSITORY?=device-rest
TAG?=3.0.0
LATEST?=true
BRANCH=v3.0.0

IMAGE=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY):$(TAG)
LATEST_IMAGE=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY):latest

# SOURCE_URL is a url to download source, such as https://github.com/merore/merore.git.
# SOURCE is project sources, its located at src/$(SORUCE).
# PATCH is a patch that supports loong64 to $(SOURCE).
# Be sure to fill in the follows!!!
SOURCE_URL=https://github.com/edgexfoundry/device-rest-go.git
SOURCE=$(shell echo $(SOURCE_URL) | awk -F '/' '{print $$NF}' | awk -F '.' '{print $$1}')
#PATCH=0001-loong64-support-for-device-rest.patch
PATCH=0001-add-loong64-support-abi-2.0.patch

default: image

image: src/$(SOURCE)
# Commands for building.
	$(MAKE) -C src/$(SOURCE) docker_device_rest_go

src/$(SOURCE):
	git clone -b $(BRANCH) --depth=1 $(SOURCE_URL) $@
	cd $@ && \
		git apply ../../$(PATCH)

push:
	docker push $(IMAGE)
	@if [ $(LATEST) == "true" ]; \
		then\
		docker tag $(IMAGE) $(LATEST_IMAGE); \
		docker push $(LATEST_IMAGE); \
	fi

clean:
	rm -rf src
