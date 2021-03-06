define([scm_r], esyscmd([sh -c "git rev-parse --short=7 HEAD"])) dnl
define([ver_major], 1)
define([ver_minor], 2)
define([revcount], esyscmd([git rev-list HEAD | wc -l | sed -e 's/ *//g' | xargs -n1 printf])) dnl

AC_INIT([OSHMEM verifier], [ver_major.ver_minor.revcount])
AM_INIT_AUTOMAKE([1.10 foreign tar-ustar silent-rules subdir-objects])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_PROG_CC([oshcc])
AM_PROG_CC_C_O
AC_PROG_LIBTOOL
AM_MAINTAINER_MODE
AC_CONFIG_MACRO_DIR([m4])

if [ $CC --version | grep icc ]; then
    CFLAGS="$CFLAGS -wd188,981,1419,810"
else
    CFLAGS="$CFLAGS -Wundef"
fi

AC_CHECK_LIB(open-pal, opal_progress, [
                 CFLAGS="$CFLAGS -DHAVE_OPAL_PROGRESS"
                 LDFLAGS="-lopen-pal $LDFLAGS"
                 ],
			     [AC_MSG_WARN([opal_progress() not found. Some OpenMPI/SHMEM versions require it in data suite.])])

dnl Check oshmem version
AC_CHECK_LIB([oshmem], [shmem_getmem_nbi],
    [AM_CONDITIONAL([HAVE_NBI],[true])
     CFLAGS="$CFLAGS -DHAVE_NBI"
    ],
    [AM_CONDITIONAL([HAVE_NBI],[false])])

dnl Check oshmem alltoall
AC_CHECK_DECLS([shmem_alltoall32, shmem_alltoall64],
    [AM_CONDITIONAL([HAVE_ALLTOALL],[true])
     CFLAGS="$CFLAGS -DHAVE_ALLTOALL"
    ],
    [AM_CONDITIONAL([HAVE_ALLTOALL],[false])], [#include "shmem.h"])

AC_ARG_ENABLE([active-sets],
    [AC_HELP_STRING([--enable-active-sets],
    [Enable checking whether PEs are within the active set before calling reductions (default: disabled)])])
AS_IF([test "$enable_active_sets" = "yes"], [AC_DEFINE([CHECK_ACTIVE_SET], [1], [Enable active set check])], [])
AM_CONDITIONAL([CHECK_ACTIVE_SET], [test "$enable_active_sets" = "yes"])

AC_ARG_ENABLE([mpi],
    [AC_HELP_STRING([--disable-mpi],
    [Disable OPENSHMEM+MPI mixed tests (default: enabled)])])
AS_IF([test "$enable_mpi" != "no"], [AC_DEFINE([ENABLE_MPI], [1], [Enable MPI mixed tests])])
AM_CONDITIONAL([ENABLE_MPI], [test "$enable_mpi" != "no"])

AC_ARG_ENABLE([quick-tests],
    [AC_HELP_STRING([--enable-quick-tests],
    [Enable faster tests with fewer iterations/operations (default: disabled)])])
AS_IF([test "$enable_quick_tests" = "yes"], [AC_DEFINE([QUICK_TEST], [1], [Enable quick tests])], [])
AM_CONDITIONAL([QUICK_TEST], [test "$enable_quick_tests" = "yes"])

AC_ARG_ENABLE([error],
    [AC_HELP_STRING([--enable-error],
    [Pass the -Werror flag to treat all warnings as errors (default: enabled)])])
AS_IF([test "$enable_error" != "no"], [AC_DEFINE([ENABLE_ERROR], [1], [Pass -Werror])], [])
AM_CONDITIONAL([ENABLE_ERROR], [test "$enable_error" != "no"])

AC_CHECK_DECLS([shmem_uint_atomic_fetch_and, shmem_ulong_atomic_fetch_and, shmem_ulonglong_atomic_fetch_and,
                shmem_uint_atomic_fetch_or, shmem_ulong_atomic_fetch_or, shmem_ulonglong_atomic_fetch_or,
                shmem_uint_atomic_fetch_xor, shmem_ulong_atomic_fetch_xor, shmem_ulonglong_atomic_fetch_xor],
    [], [], [#include "shmem.h"])

AC_CHECK_DECLS([shmem_uint_atomic_and, shmem_ulong_atomic_and, shmem_ulonglong_atomic_and,
                shmem_uint_atomic_or, shmem_ulong_atomic_or, shmem_ulonglong_atomic_or,
                shmem_uint_atomic_xor, shmem_ulong_atomic_xor, shmem_ulonglong_atomic_xor],
    [], [], [#include "shmem.h"])

AC_CONFIG_FILES([Makefile])
AC_OUTPUT
