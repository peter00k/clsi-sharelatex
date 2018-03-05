#  Update existing texlive images to include qpdf

all: 2016 2015 2014

2016:
	$(MAKE) TAG=2016.1 build

2015:
	$(MAKE) TAG=2015.1 build

2014:
	$(MAKE) TAG=2014.2 build

build: image.$(TAG)

image.$(TAG): Dockerfile.$(TAG)
	docker build --force-rm=true -t quay.io/sharelatex/texlive-full:$(TAG)-qpdf -f Dockerfile.$(TAG) .

Dockerfile.$(TAG): Dockerfile.patch.in
	m4 -D TAG=$(TAG) Dockerfile.patch.in > Dockerfile.$(TAG)

clean:
	rm -f Dockerfile.201[456].*
