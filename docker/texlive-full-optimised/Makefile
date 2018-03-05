YEAR=2015
TAG=2015.1
VERSION=20150521
BUILD_UBUNTU=14.04
REPO=ftp://tug.org/historic/systems/texlive/$(YEAR)/tlnet-final
export WGETRC=wgetrc
export ROOT=quay.io/sharelatex/texlive-full:$(TAG)-base
export DOCKER_BUILD_ARGS

all: image.$(TAG)

2017:
	$(MAKE) YEAR=2017 TAG=2017.1 VERSION=20170524 BUILD_UBUNTU=16.04 REPO=http://ctan.sharelatex.com/tex-archive/systems/texlive/tlnet

2016:
	$(MAKE) YEAR=2016 TAG=2016.1 VERSION=20160523 BUILD_UBUNTU=16.04 ROOT=$(ROOT)

2015: Makefile
	$(MAKE) YEAR=2015 TAG=2015.1 VERSION=20150521 BUILD_UBUNTU=14.04 ROOT=$(ROOT)

2014: Makefile
	$(MAKE) YEAR=2014 TAG=2014.2 VERSION=20140525 BUILD_UBUNTU=14.04 ROOT=$(ROOT)

download: download-2014 download-2015 download-2016 download-github

download-github:
	wget https://github.com/cloudflare/zlib/archive/gcc.amd64.zip -O gcc.amd64.zip
	wget https://github.com/sharelatex/chktex/archive/master.tar.gz -O chktex-master.tar.gz

download-2014:
	wget -c ftp://tug.org/historic/systems/texlive/2014/texlive-20140525-source.tar.xz
	wget -c ftp://tug.org/historic/systems/texlive/2014/tlnet-final/install-tl-unx.tar.gz -O install-tl-unx-2014.tar.gz

download-2015:
	wget -c ftp://tug.org/historic/systems/texlive/2015/texlive-20150521-source.tar.xz
	wget -c ftp://tug.org/historic/systems/texlive/2015/tlnet-final/install-tl-unx.tar.gz -O install-tl-unx-2015.tar.gz

download-2016:
	wget -c ftp://tug.org/historic/systems/texlive/2016/texlive-20160523b-source.tar.xz -O texlive-20160523-source.tar.xz
	wget -c ftp://tug.org/historic/systems/texlive/2016/install-tl-unx.tar.gz -O install-tl-unx-2016.tar.gz

download-2017:
	wget -c ftp://tug.org/historic/systems/texlive/2017/texlive-20170524-source.tar.xz
	wget -c ftp://tug.org/historic/systems/texlive/2017/install-tl-unx.tar.gz -O install-tl-unx-2017.tar.gz

Dockerfile.builder.$(YEAR): Dockerfile.builder.in Makefile
	m4 -D BUILD_UBUNTU=$(BUILD_UBUNTU) -D YEAR=$(YEAR) -D VERSION=$(VERSION) -D REPO=$(REPO) Dockerfile.builder.in > Dockerfile.builder.$(YEAR)

Dockerfile.$(YEAR): Dockerfile.in Makefile
	m4 -D YEAR=$(YEAR) -D VERSION=$(VERSION) -D TAG=$(TAG) -D ROOT=$(ROOT) Dockerfile.in > Dockerfile.$(YEAR)


# Set up extra args for docker build to use proxy
export EXTRA_ARGS=

ifneq ($(http_proxy),)
EXTRA_ARGS += --build-arg http_proxy=$(http_proxy)
endif

ifneq ($(https_proxy),)
EXTRA_ARGS += --build-arg https_proxy=$(https_proxy)
endif

ifneq ($(ftp_proxy),)
EXTRA_ARGS += --build-arg ftp_proxy=$(ftp_proxy)
endif

build.$(YEAR): Dockerfile.builder.$(YEAR)
	docker build --force-rm=true $(EXTRA_ARGS) $(DOCKER_BUILD_ARGS) -t texlive-build -f Dockerfile.builder.$(YEAR) .

image.$(TAG): Dockerfile.$(YEAR) build.$(YEAR)
	rm -rvf usr
	docker run --rm texlive-build tar c /usr/local/texlive/$(YEAR)/bin/x86_64-linux-opt/ /usr/local/bin/chktex /usr/local/etc/chktexrc | tar xv
	docker build --force-rm=true -t $(ROOT):$(TAG)-opt -f Dockerfile.$(YEAR) .
	rm -rvf usr

clean:
	rm -rvf usr
