progguidedir = $(docdir)/programmers-guide
driverguidedir = $(docdir)/driver-guide

doc_DATA = programmers-guide.pdf  driver-guide.pdf
progguide_DATA = programmers-guide/*
driverguide_DATA = driver-guide/*

programmers_sources = programmers-guide.sgml libdbi-reference.sgml example-program.sgml libdbi-quickstart.sgml libdbi-errorhandling.sgml libdbi-versioning.sgml libdbi-transactions.sgml

driver_sources = driver-guide.sgml 

EXTRA_DIST = $(doc_DATA)  $(progguide_DATA) $(driverguide_DATA) $(programmers_sources) $(driver_sources) include/copying-fdl.sgml include/doc-html.dsl include/doc-print.dsl

all: programmers-guide.pdf programmers-guide/* driver-guide.pdf driver-guide/*

# jadetex spews out tons of useless crap. shut it up:
programmers-guide.pdf: $(programmers_sources)
	@echo "Converting programmers-guide.sgml to PDF...";
	$(myjade) -t tex -V tex-backend -d $(top_srcdir)/doc/include/doc-print.dsl programmers-guide.sgml
# run jadetex three times to get the references right
	$(mypdftex) "&pdfjadetex" programmers-guide.tex
	$(mypdftex) "&pdfjadetex" programmers-guide.tex
	$(mypdftex) "&pdfjadetex" programmers-guide.tex

programmers-guide/*: $(programmers_sources)
	@echo "Converting programmers-guide.sgml to HTML...";
# the sleep command is a workaround for a race condition which may occur
# on some filesystems, causing the target to be rebuilt over and over again
	mkdir -p programmers-guide && cd programmers-guide && $(myjade) -t sgml -d $(top_srcdir)/../doc/include/doc-html.dsl $(top_srcdir)/../doc/programmers-guide.sgml && sleep 1

driver-guide.pdf: $(driver_sources)
	@echo "Converting driver-guide.sgml to PDF...";
	$(myjade) -t tex -d include/doc-print.dsl driver-guide.sgml
# run jadetex three times to get the references right
	$(mypdftex) "&pdfjadetex" driver-guide.tex
	$(mypdftex) "&pdfjadetex" driver-guide.tex
	$(mypdftex) "&pdfjadetex" driver-guide.tex

driver-guide/*: $(driver_sources)
	@echo "Converting driver-guide.sgml to HTML...";
	mkdir -p driver-guide && cd driver-guide && $(myjade) -t sgml -d $(top_srcdir)/../doc/include/doc-html.dsl $(top_srcdir)/../doc/driver-guide.sgml && sleep 1

distclean: clean
	rm -f programmers-guide.pdf
	rm -rf programmers-guide/
	rm -f driver-guide.pdf
	rm -rf driver-guide/

maintainer-clean: distclean
	rm -f driver-guide.log
	rm -f programmers-guide.log
	rm -f driver-guide.tex
	rm -f programmers-guide.tex
	rm -f driver-guide.out
	rm -f programmers-guide.out
	rm -f driver-guide.aux
	rm -f programmers-guide.aux
