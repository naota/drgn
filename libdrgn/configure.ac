AC_INIT([drgn], m4_esyscmd_s([build-aux/version.sh]),
	[https://github.com/osandov/drgn/issues],,
	[https://github.com/osandov/drgn])

AC_CONFIG_AUX_DIR([build-aux])
AC_CONFIG_MACRO_DIR([m4])
AC_PROG_CC

AM_INIT_AUTOMAKE([-Wall foreign subdir-objects])
AM_PROG_AR

LT_INIT

AC_ARG_WITH([python],
	    [AS_HELP_STRING([--with-python@<:@=ARG@:>@],
			    [build Python bindings. ARG may be yes, no, or the
			     path to the Python interpreter to use. This is
			     normally only used by setup.py
			     @<:@default=no@:>@])],
	    [], [with_python=no])

AS_IF([test "x$with_python" != xyes && test "x$with_python" != xno],
      [PYTHON=$with_python])
AM_CONDITIONAL([WITH_PYTHON], [test "x$with_python" != xno])
AM_COND_IF([WITH_PYTHON],
	   [AM_PATH_PYTHON([3.6])
	    AS_IF([test -z "$PYTHON_CPPFLAGS"],
		  [prog="import sysconfig
include = sysconfig.get_path('include')
platinclude = sysconfig.get_path('platinclude')
include_paths = [[include]]
if platinclude != include:
    include_paths.append(plat_include)
print(' '.join('-I' + path for path in include_paths))"
		   PYTHON_CPPFLAGS=`"$PYTHON" -c "$prog"`])
	    AC_SUBST(PYTHON_CPPFLAGS)
	    AC_MSG_CHECKING([for $PYTHON development headers])
	    save_CPPFLAGS="$CPPFLAGS"
	    CPPFLAGS="$CPPFLAGS $PYTHON_CPPFLAGS"
	    AC_COMPILE_IFELSE([AC_LANG_SOURCE([[#include <Python.h>]])],
			      [AC_MSG_RESULT([yes])],
			      [AC_MSG_RESULT([no])
			       AC_MSG_ERROR(
[Could not compile test program with Python headers.

You may need to install your distribution's Python development package (e.g.,
python3-devel or python3-dev) or specify the location of the Python development
headers by setting the PYTHON_CPPFLAGS environment variable.])])
	    CPPFLAGS="$save_CPPFLAGS"])

PKG_PROG_PKG_CONFIG
PKG_CHECK_MODULES(libelf, [libelf])
AC_SUBST([libelf_CFLAGS])
AC_SUBST([libelf_LIBS])
dnl We need dwarf_cu_getdwarf() which was added in elfutils 0.160.
PKG_CHECK_MODULES(libdw, [libdw >= 0.160])
AC_SUBST([libdw_CFLAGS])
AC_SUBST([libdw_LIBS])

AC_CONFIG_FILES([Makefile])
AC_OUTPUT
