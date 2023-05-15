# You can set these variables from the command line, and also from the environment for the first two.
.DEFAULT_GOAL := help
SPHINXOPTS    ?= -W
SPHINXBUILD   ?= sphinx-build
SOURCEDIR     = docs
BUILDDIR      = build


.PHONY: help
help:	## Show this help
	@echo "Available targets:"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: Makefile
%: Makefile
	@$(SPHINXBUILD) -M $@ "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

.PHONY: clean
clean:
	rm -rf build

.PHONY: test-local-docs
test-local-docs: clean
	docker run -it --rm -v $(PWD):/repo containers.cisco.com/aide/sphinx-tools validate.sh

.PHONY: show-local-docs
show-local-docs: clean
	docker run -it --rm -p 8000:8000 -v $(PWD):/repo containers.cisco.com/aide/sphinx-tools cicd.sh
