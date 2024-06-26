# This file is generated by the template.

REGISTRY?=lcr.loongnix.cn
ORGANIZATION?=calico

TAG?=v3.26.1
LATEST?=false


IMG_TAG ?= $(patsubst v%,%,$(TAG))
CALICO_BUILD ?= lcr.loongnix.cn/calico/go-build:0.85
GOPROXY ?= https://goproxy.cn,https://mirrors.aliyun.com/goproxy/,https://goproxy.io,direct

SOURCE_URL=https://github.com/projectcalico/calico.git
SOURCE=$(shell echo $(SOURCE_URL) | awk -F '/' '{print $$NF}' | awk -F '.' '{print $$1}')
PATCH=0001-add-loong64-support-abi-2.0.patch


BUILD_TG ?= \
    pod2daemon \
    calicoctl \
    cni-plugin \
    apiserver \
    kube-controllers \
    app-policy \
    typha \
    node

PUSH_IMG ?= pod2daemon \
    calicoctl \
    cni \
    apiserver \
    kube-controllers \
    app-policy \
    typha \
    node \
    flannel-migration-controller

THIS_DIR := $(dir $(realpath $(lastword $(MAKEFILE_LIST))))

default: image

src/$(SOURCE):
	git clone -q -b $(TAG) --depth=1 $(SOURCE_URL) $@
	cd $@ && \
		git apply ../../$(PATCH)

image: src/$(SOURCE)
	echo "ci test"
	@$(foreach value, $(BUILD_TG), \
		EXTRA_DOCKER_ARGS="-e HTTPS_PROXY=$(https_proxy) -e HTTP_PROXY=$(http_proxy)" \
		GOPROXY="" \
		BUILDARCH=loong64 \
			BIRD_IMAGE=lcr.loongnix.cn/calico/bird:0.3.3 \
			UBI_IMAGE=lcr.loongnix.cn/library/openeuler:22.03-LTS \
			CALICO_BUILD=$(CALICO_BUILD) \
			IMAGE_NAME=$(REGISTRY)/$(ORGANIZATION)/$(value):$(IMG_TAG) \
			FLANNEL_IMAGE_NAME=$(REGISTRY)/$(ORGANIZATION)/flannel-migration-controller:$(IMG_TAG) \
			GOMOD_CACHE=$(THIS_DIR)src/go-mod make -C src/$(SOURCE)/$(value) image IMAGETAG=$(TAG) VALIDARCHES=loong64; \
	)
	docker tag $(REGISTRY)/$(ORGANIZATION)/cni-plugin:$(IMG_TAG) \
		$(REGISTRY)/$(ORGANIZATION)/cni:$(IMG_TAG)
	chmod -R +w src

push:
	@$(foreach value, $(PUSH_IMG), \
		docker push $(REGISTRY)/$(ORGANIZATION)/$(value):$(IMG_TAG); \
		if [ $(LATEST) = "true" ]; then \
			$(eval LATEST_IMAGE=$(REGISTRY)/$(ORGANIZATION)/$(value):latest) \
			docker tag $(REGISTRY)/$(ORGANIZATION)/$(value):$(IMG_TAG) ${LATEST_IMAGE}; \
			docker push $(LATEST_IMAGE); \
		fi; \
	)

clean:
	rm -rf src
