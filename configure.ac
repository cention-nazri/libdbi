dnl Process this file with autoconf to produce a configure script.
AC_PREREQ(2.61)
AC_INIT([libdbi], [0.9.0])
AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_SRCDIR([src/dbi_main.c])
AC_USE_SYSTEM_EXTENSIONS

dnl set up libtool library versioning
LIB_CURRENT=3
LIB_REVISION=0
LIB_AGE=0

dnl set up libdbi versioning from the above interface version numbers
dnl the math contains an ugly hack to massage the minor version number
dnl into the scheme used up to 0.8.3. minor should be used without
dnl that correction as soon as major is larger than 0. Also, major is
dnl forced to remain in the 0.9 series for the time being
LIBDBI_MAJOR=`expr $LIB_CURRENT - $LIB_AGE - 1`
LIBDBI_MINOR=`expr $LIB_AGE + 8`
LIBDBI_PATCH=$LIB_REVISION

AM_INIT_AUTOMAKE([foreign dist-zip])
AM_CONFIG_HEADER(config.h)
AC_CONFIG_MACRO_DIR([m4])

dnl promote library versioning to Makefiles
AC_SUBST(LIB_CURRENT)
AC_SUBST(LIB_REVISION)
AC_SUBST(LIB_AGE)
AC_SUBST(LIBDBI_MAJOR)
AC_SUBST(LIBDBI_MINOR)
AC_SUBST(LIBDBI_PATCH)

AC_CANONICAL_HOST

dnl ====================================
dnl Fix CFLAGS to avoid the default -g option
dnl CFLAGS will still be picked up from the environment
dnl ====================================
: ${CFLAGS=""}

dnl ====================================
dnl Check for programs
dnl ====================================

AC_PROG_CC
AC_PROG_CPP
AC_PROG_LD
AC_PROG_INSTALL
LT_INIT([dlopen])
dnl AC_PROG_LIBTOOL
dnl AC_SUBST(LIBTOOL)

dnl ====================================
dnl Set some general compile options
dnl ====================================

if test -z "$GCC"; then
        case $host in
        *-*-irix*)
                if test -z "$CC"; then
                        CC=cc
                fi
                DEBUG="-g -signed"
                CFLAGS="-O2 -w -signed $CFLAGS"
                PROFILE="-p -g3 -O2 -signed" ;;
        sparc-sun-solaris*)
                DEBUG="-v -g"
                CFLAGS="-xO4 -fast -w -fsimple -native -xcg92 $CFLAGS"
                PROFILE="-v -xpg -g -xO4 -fast -native -fsimple -xcg92 -Dsuncc" ;;
	*-*-freebsd*)
                DEBUG="-g"
                CFLAGS="-O -I/usr/local/include $CFLAGS"
                CPPFLAGS="-I/usr/local/include $CPPFLAGS"
		LDFLAGS="-L/usr/local/lib $LDFLAGS"
                PROFILE="-g -p" ;;
        *)
                DEBUG="-g"
                CFLAGS="-O $CFLAGS"
                PROFILE="-g -p" ;;
        esac
else

        case $host in
        *-*-linux*)
                DEBUG="-g -Wall -D_REENTRANT -D__NO_MATH_INLINES -fsigned-char"
                CFLAGS="-O20 -ffast-math -D_REENTRANT -fsigned-char $CFLAGS"
                PROFILE="-pg -g -O20 -ffast-math -D_REENTRANT -fsigned-char";;
        sparc-sun-*)
                DEBUG="-g -Wall -D__NO_MATH_INLINES -fsigned-char -mv8"
                CFLAGS="-O20 -ffast-math -D__NO_MATH_INLINES -fsigned-char -mv8 $CFLAGS"
                PROFILE="-pg -g -O20 -D__NO_MATH_INLINES -fsigned-char -mv8" ;;
	*-*-freebsd*)
                DEBUG="-g -Wall -D__NO_MATH_INLINES -fsigned-char"
                CFLAGS="-O20 -D__NO_MATH_INLINES -fsigned-char -I/usr/local/include $CFLAGS"
                CPPFLAGS="-I/usr/local/include $CPPFLAGS"
		LDFLAGS="-L/usr/local/lib $LDFLAGS"
                PROFILE="-O20 -g -pg -D__NO_MATH_INLINES -fsigned-char" ;;
        *)
                DEBUG="-g -Wall -D__NO_MATH_INLINES -fsigned-char"
                CFLAGS="-O20 -D__NO_MATH_INLINES -fsigned-char $CFLAGS"
                PROFILE="-O20 -g -pg -D__NO_MATH_INLINES -fsigned-char" ;;
        esac
fi

AC_SUBST(DEBUG)
AC_SUBST(PROFILE)


dnl ====================================
dnl Set some host-specific options
dnl dlsym_handle: if 1, use the handle returned by dlopen() to query
dnl               the symbols contained in a shared library which the
dnl               dlopen()ed object is linked against. if 0, use
dnl               the special handle "RTLD_NEXT" instead
dnl               note to self: is this hack a consequence of
dnl               using RTLD_LAZY in the first place??
dnl dlopen_flag:  flag to use with dlopen(). RTLD_LAZY is preferred
dnl               for performance reasons if it works ok on a
dnl               particular platform
dnl ====================================
case $host in
	*-*-*bsd*)
dnl		dlsym_handle=0
dnl		dlopen_flag="RTLD_LAZY" ;;
		dlsym_handle=0
		dlopen_flag="RTLD_NOW" ;;
	*)
		dlsym_handle=1
		dlopen_flag="RTLD_NOW" ;;
esac

AC_DEFINE_UNQUOTED(DLOPEN_FLAG, $dlopen_flag, [ Specifies the mode of dlopen behavior ])
AC_DEFINE_UNQUOTED(DLSYM_HANDLE, $dlsym_handle, [ use dlopen handle with dlsym ])

dnl ==============================
dnl Check for libraries
dnl ==============================

dnl functions to load dynamic libraries
use_ltdl=no
AC_ARG_WITH(ltdl,
	[  --with-ltdl	          Use libtool's libltdl to load runtime modules [no]],
	[  use_ltdl=$withval ])
	
if test "x$use_ltdl" = xyes; then
AC_CHECK_HEADERS([ltdl.h], [AC_CHECK_LIB([ltdl],[lt_dlopen],[LIBADD_DL=-lltdl])])
else
AC_CHECK_HEADERS([mach-o/dyld.h],[AC_CHECK_FUNCS([NSLinkModule], [
       LIBADD_DL=""
       dlsym_prefix=_
       ])],
[
AC_CHECK_HEADERS([dlfcn.h])
AC_CHECK_FUNCS([dlopen],[LIBADD_DL=],
		[AC_CHECK_LIB([dl],[dlopen],[LIBADD_DL=-ldl],[])])])
fi
AC_SUBST(LIBADD_DL)
dnl ==============================
dnl Check for functions
dnl ==============================

case $host in
	*-*-win32*)
		shlib_ext=.dll ;;
	*-*-cygwin*)
		shlib_ext=.dll ;;
	*-*-mingw32*)
		shlib_ext=.dll ;;
	*-*-darwin*)
		shlib_ext=.so ;;
	*)
		shlib_ext=.so ;;
esac

AC_ARG_WITH(shlib-ext,
	[  --with-shlib-ext	  Specifies the filename extension of loadable modules (usually not necessary).],
	[  shlib_ext=$withval ])
AC_ARG_WITH(dlsym-prefix,
	[  --with-dlsym-prefix	  Specifies a required prefix for symbol names of dynamically loaded modules (usually not necessary).],
	[  dlsym_prefix=$withval ])
AC_ARG_WITH([driverdir], AS_HELP_STRING([--with-driverdir=PATH],
	[Path to look in for libdbi plugins [[LIBDIR/dbd]]]),
	[driverdir="$withval"], [driverdir='${libdir}/dbd'])
AC_SUBST([driverdir])


AC_DEFINE_UNQUOTED(DRIVER_EXT, "$shlib_ext", [ Specifies the filename extension of loadable modules ])
AC_DEFINE_UNQUOTED(DLSYM_PREFIX, "$dlsym_prefix", [ Specifies a required prefix for symbol names of dynamically loaded modules ])


AC_CHECK_DECLS([readdir_r, gmtime_r, vasprintf, asprintf])
AC_CHECK_FUNCS(strtoll readdir_r gmtime_r fpathconf dirfd vasprintf)
AC_REPLACE_FUNCS(atoll timegm asprintf)
AC_CHECK_FUNCS(vasprintf)
AC_REPLACE_FUNCS(asprintf)

dnl ==============================
dnl Checks for header files
dnl ==============================

AC_CHECK_HEADERS(string.h strings.h)

dnl ==============================
dnl See whether to build the docs
dnl ==============================

ac_docs="YES"
AC_ARG_ENABLE(docs,
	[  --disable-docs	  do not build and install the documentation.],
	[  if test "$enable_docs" = "no"; then
	       ac_docs="NO"
	   fi])

if test "$ac_docs" = "YES"; then
      docs_subdirs="doc"
      AC_PATH_PROGS(myjade, openjade jade, none)
      AC_PATH_PROG(mypdftex, pdftex, none)
else
   docs_subdirs=""
fi

if test "$myjade" = "none"; then
   AC_MSG_ERROR(cannot build docs without openjade or jade)
fi
if test "$mypdftex" = "none"; then
   AC_MSG_ERROR(cannot build docs without pdftex)
fi

AC_SUBST(docs_subdirs)

dnl ==============================
dnl Write output files
dnl ==============================

AC_CONFIG_FILES([include/dbi/dbi.h])

AC_OUTPUT([
	libdbi.spec
	Makefile
	src/Makefile
	doc/Makefile
	include/Makefile
	include/dbi/Makefile
	dbi.pc
	])

