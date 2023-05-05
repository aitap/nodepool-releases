all:

.PHONY: all clean

src:
	git clone https://github.com/aitap/nodepool $@

ifeq ($(wildcard src/.),)

# Cannot set $(PACKAGE) without having cloned the repo
# (thankfully, this is needed only once)
all: src
	$(MAKE)

else

.PHONY: check repo commit

all: commit

PACKAGE := $(shell (cd src && git pull) >/dev/null 2>&1 && Rscript -e "\
 cat(read.dcf('src/DESCRIPTION')[,c('Package','Version')], sep = '_'); \
 cat('.tar.gz') \
")

$(PACKAGE): src/*
	R CMD build src

check: $(PACKAGE)
	R CMD check $(PACKAGE)

repo: PACKAGES.rds
PACKAGES.rds: PACKAGES.gz
PACKAGES.gz: PACKAGES
PACKAGES: $(PACKAGE) check
	Rscript -e 'tools::write_PACKAGES(verbose = TRUE)'

commit: repo
	git checkout -B repo makefile
	git add PACKAGES* *.tar.gz
	git commit -m "Publish $(PACKAGE)"
	git push -f
	git checkout makefile

endif

clean:
	-rm -rf src PACKAGES* *.tar.gz *.Rcheck
