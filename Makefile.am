ACLOCAL_AMFLAGS = -I m4

SUBDIRS = src include @docs_subdirs@

EXTRA_DIST = AUTHORS COPYING ChangeLog INSTALL README README.win32 README.osx TODO NEWS UPGRADING libdbi.spec.in config.h.in Makefile.w32 autogen.sh drivers/example/AUTHORS drivers/example/Makefile.am drivers/example/README drivers/example/TODO drivers/example/.cvsignore drivers/example/dbd_example.c drivers/example/dbd_example.h drivers/example/dbd_example.sgml

debug:
	$(MAKE) all CFLAGS="@DEBUG@"

profile:
	$(MAKE) all CFLAGS="@PROFILE@"


# we use exec here to install the .pc file with the library, not with
# the docs when using install-data and install-exec
pkgconfigexecdir = $(libdir)/pkgconfig
pkgconfigexec_DATA = dbi.pc

