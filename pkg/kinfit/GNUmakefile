PKGNAME := $(shell sed -n "s/Package: *\([^ ]*\)/\1/p" DESCRIPTION)
PKGVERS := $(shell sed -n "s/Version: *\([^ ]*\)/\1/p" DESCRIPTION)
PKGSRC  := $(shell basename $(PWD))
TGZ     := ../$(PKGSRC)_$(PKGVERS).tar.gz
TGZVNR  := ../$(PKGSRC)_$(PKGVERS)-vignettes-not-rebuilt.tar.gz

# Specify the directory holding R binaries. To use an alternate R build (say a
# pre-prelease version) use `make RBIN=/path/to/other/R/` or `export RBIN=...`
# If no alternate bin folder is specified, the default is to use the folder
# containing the first instance of R on the PATH.
RBIN ?= $(shell dirname "`which R`")
#
# Specify static documentation directories for subversion on r-forge
RFSVN ?= $(HOME)/svn/kinfit.r-forge
RFDIR ?= $(RFSVN)/pkg/kinfit
SDDIR ?= $(RFSVN)/www/kinfit_static

.PHONY: help

pkgfiles = data/* \
	DESCRIPTION \
	man/* \
	NAMESPACE \
	R/* \
	tests \
	vignettes/header.tex \
	vignettes/examples.Rnw \
	vignettes/kinfit.Rnw \
	vignettes/KinGUI/* \
	vignettes/references.bib

all: check clean

$(TGZ): $(pkgfiles)
	cd ..;\
		"$(RBIN)/R" CMD build $(PKGSRC)

$(TGZVNR): $(pkgfiles)
	cd ..;\
		"$(RBIN)/R" CMD build $(PKGSRC) --no-build-vignettes;\
		cd $(PKGSRC);\
	mv $(TGZ) $(TGZVNR)
                
build: $(TGZ)

build-no-vignettes: $(TGZVNR)

install: build
	"$(RBIN)/R" CMD INSTALL $(TGZ)

install-no-vignettes: build-no-vignettes
	"$(RBIN)/R" CMD INSTALL $(TGZVNR)

check: build
	# Vignettes have been rebuilt by the build target
	"$(RBIN)/R" CMD check --as-cran --no-tests --no-build-vignettes $(TGZ)

check-no-vignettes: build-no-vignettes
	mv $(TGZVNR) $(TGZ)
	"$(RBIN)/R" CMD check --as-cran --no-tests $(TGZ)
	mv $(TGZ) $(TGZVNR)

vignettes/kinfit.pdf: vignettes/kinfit.Rnw
	"$(RBIN)/Rscript" -e "tools::buildVignette(file = 'vignettes/kinfit.Rnw', dir = 'vignettes')"

vignettes/examples.pdf: vignettes/examples.Rnw
	"$(RBIN)/Rscript" -e "tools::buildVignette(file = 'vignettes/examples.Rnw', dir = 'vignettes')"

vignettes: vignettes/examples.pdf vignettes/kinfit.pdf

sd:
	"$(RBIN)/Rscript" -e "library(staticdocs); build_site(site_path = '$(SDDIR)')"

clean: 
	$(RM) -r $(PKGNAME).Rcheck/
	$(RM) vignettes/*.R
