##---------------------------------------------------------------------
# GNUmakefile for python
# Call Makefile to do the work, but for the install case, unpack the
# tarball to create the project source directory
##---------------------------------------------------------------------
PROJECT = python
VERSION = 2.6.9

PWD := $(shell pwd)

ifeq ($(MAKECMDGOALS),)
MAKECMDGOALS = no_target
endif
ifneq ($(filter no_target install,$(MAKECMDGOALS)),)
ifndef DSTROOT
ifdef DESTDIR
export DSTROOT := $(shell mkdir -p '$(DESTDIR)' && echo '$(DESTDIR)')
else
export DSTROOT := /
endif
endif
ifndef OBJROOT
export OBJROOT := $(shell mkdir -p '$(PWD)/OBJROOT' && echo '$(PWD)/OBJROOT')
endif
ifndef SYMROOT
export SYMROOT := $(shell mkdir -p '$(PWD)/SYMROOT' && echo '$(PWD)/SYMROOT')
endif
endif

ifndef SRCROOT
export SRCROOT := $(PWD)
endif
ifndef RC_ARCHS
export RC_ARCHS := $(shell arch)
export RC_$(RC_ARCHS) := YES
endif
ifndef RC_CFLAGS
export RC_CFLAGS := $(foreach A,$(RC_ARCHS),-arch $(A)) $(RC_NONARCH_CFLAGS)
endif
ifndef RC_NONARCH_CFLAGS
export RC_NONARCH_CFLAGS = -pipe
endif
ifndef RC_ProjectName
export RC_ProjectName = $(PROJECT)
endif

NAME = Python
export PYTHON_CURRENT_VERSION = $(shell echo $(VERSION) | sed 's/[a-z].*$$//')
NAMEVERS = $(NAME)-$(VERSION)
TARBALL = $(NAMEVERS).tar.xz
FIX = '$(OBJROOT)/fix'

VERS := $(shell echo $(VERSION) | sed 's/\(^[0-9]*\.[0-9]*\).*/\1/')
FRAMEWORKS = /System/Library/Frameworks
PYFRAMEWORK = $(FRAMEWORKS)/Python.framework
VERSIONSVERS = $(PYFRAMEWORK)/Versions/$(VERS)
INCLUDEPYTHONVERS = $(VERSIONSVERS)/include/$(PROJECT)$(VERS)
LIBPYTHONVERS = $(VERSIONSVERS)/lib/$(PROJECT)$(VERS)

DYLIB = lib$(PROJECT)$(VERS).dylib
USRINCLUDE = /usr/include
USRLIB = /usr/lib
EXTRAS = $(VERSIONSVERS)/Extras
LIBRARYPYTHON = /Library/Python/$(VERS)
SITEPACKAGES = $(LIBRARYPYTHON)/site-packages

EXTRASPYTHON = '$(DSTROOT)$(EXTRAS)/lib/python'
PYTHON = '$(OBJROOT)/$(PROJECT)/$(PROJECT).exe'
PYTHONENV = DYLD_FRAMEWORK_PATH='$(DSTROOT)$(FRAMEWORKS)' DYLD_NEW_LOCAL_SHARED_REGIONS=1 PYTHONPATH='$(DSTROOT)$(LIBPYTHONVERS)':$(EXTRASPYTHON)
PYDTRACE_H = $(OBJROOT)/$(PROJECT)/Python/pydtrace.h

no_target: python

python: $(OBJROOT)/$(PROJECT)
	DSTROOT='$(DSTROOT)' OBJROOT='$(OBJROOT)/$(PROJECT)' \
	    SRCROOT='$(OBJROOT)' SYMROOT='$(SYMROOT)' RC_ARCHS='$(RC_ARCHS)' \
	    DYLD_NEW_LOCAL_SHARED_REGIONS=1 \
	    $(MAKE) -C '$(OBJROOT)' -f Makefile \
	    DSTROOT='$(DSTROOT)' OBJROOT='$(OBJROOT)/$(PROJECT)' \
	    SRCROOT='$(OBJROOT)' SYMROOT='$(SYMROOT)' RC_ARCHS='$(RC_ARCHS)' \
	    VERS=$(VERS)

##---------------------------------------------------------------------
# We patch configure to remove the "-arch_only ppc" option, since we
# build fat.  We also set DYLD_NEW_LOCAL_SHARED_REGIONS or else python.exe
# will crash.  And patch unixccompiler for wxWidgets (submit back to python).
#
# autoconf is now (normally) unavailable.  So we save the output of autoconf
# in fix/autoconf and use it to overwrite the original files.  If we need
# to update the version of python, we will need to install autoconf, and then
# we run:
#
#    % make `pwd`/python OBJROOT=`pwd` PYTHON_AUTOCONF=NO
#    % mv python python.bak
#    % make `pwd`/python OBJROOT=`pwd` PYTHON_AUTOCONF=YES
#    % diff -r --brief python.bak python
#
# Make a tarball out of the differences and put in $(FIX)/autoconf.tar.bz2
##---------------------------------------------------------------------
defarchflags = $(foreach u,$(RC_ARCHS),-arch $(u))

$(OBJROOT)/$(PROJECT):
	@set -x && \
	cd '$(OBJROOT)' && \
	tar -xof $(TARBALL) && \
	rm -rf $(PROJECT) && \
	mv $(NAMEVERS) $(PROJECT) && \
	ed - $(PROJECT)/Include/py_curses.h < $(FIX)/py_curses.h.ed && \
	ed - $(PROJECT)/Include/pymactoolbox.h < $(FIX)/pymactoolbox.h.ed && \
	ed - $(PROJECT)/Lib/ctypes/__init__.py < $(FIX)/Lib_ctypes___init__.py.ed && \
	ed - $(PROJECT)/Lib/xml/__init__.py < $(FIX)/Lib_xml___init__.py.ed && \
	ed - $(PROJECT)/Lib/distutils/command/install.py < $(FIX)/install.py.ed && \
	ed - $(PROJECT)/Lib/distutils/config.py < $(FIX)/Lib_distutils_config.py.ed && \
	sed 's/@DEFARCHFLAGS@/$(defarchflags)/' $(FIX)/sysconfig.py.ed | ed - $(PROJECT)/Lib/distutils/sysconfig.py && \
	ed - $(PROJECT)/Lib/distutils/unixccompiler.py < $(FIX)/unixccompiler.py.ed && \
	ed - $(PROJECT)/Lib/distutils/util.py < $(FIX)/Lib_distutils_util.py.ed && \
	ed - $(PROJECT)/Lib/locale.py < $(FIX)/locale.py.ed && \
	ed - $(PROJECT)/Lib/logging/handlers.py < $(FIX)/Lib_logging_handlers.py.ed && \
	ed - $(PROJECT)/Lib/os.py < $(FIX)/Lib_os.py.ed && \
	ed - $(PROJECT)/Lib/plat-mac/bundlebuilder.py < $(FIX)/bundlebuilder.py.ed && \
	ed - $(PROJECT)/Lib/plat-mac/Carbon/AppleEvents.py < $(FIX)/AppleEvents.py.ed && \
	ed - $(PROJECT)/Lib/platform.py < $(FIX)/Lib_platform.py.ed && \
	ed - $(PROJECT)/Lib/pydoc.py < $(FIX)/pydoc.py.ed && \
	ed - $(PROJECT)/Lib/rlcompleter.py < $(FIX)/rlcompleter.py.ed && \
	ed - $(PROJECT)/Lib/site.py < $(FIX)/site.py.ed && \
	ed - $(PROJECT)/Lib/test/test__locale.py < $(FIX)/Lib_test_test__locale.py.ed && \
	ed - $(PROJECT)/Lib/test/test_httpservers.py < $(FIX)/Lib_test_test_httpservers.py.ed && \
	ed - $(PROJECT)/Lib/test/test_platform.py < $(FIX)/Lib_test_test_platform.py.ed && \
	ed - $(PROJECT)/Mac/Makefile.in  < $(FIX)/Mac_Makefile.in.ed && \
	patch $(PROJECT)/Mac/Modules/cf/_CFmodule.c $(FIX)/_CFmodule.c.patch && \
	ed - $(PROJECT)/Mac/Modules/cg/_CGmodule.c  < $(FIX)/_CGmodule.c.ed && \
	ed - $(PROJECT)/Mac/Tools/pythonw.c < $(FIX)/pythonw.c.ed && \
	ed - $(PROJECT)/Makefile.pre.in < $(FIX)/Makefile.pre.in.ed && \
	patch $(PROJECT)/Misc/python.man $(FIX)/python.man.patch && \
	ed - $(PROJECT)/Modules/_cursesmodule.c < $(FIX)/_cursesmodule.c.ed && \
	ed - $(PROJECT)/Modules/dlmodule.c < $(FIX)/dlmodule.c.ed && \
	ed - $(PROJECT)/Modules/errnomodule.c < $(FIX)/errnomodule.c.ed && \
	rm $(PROJECT)/Modules/expat/expat.h $(PROJECT)/Modules/expat/expat_external.h && \
	ed - $(PROJECT)/Modules/getpath.c < $(FIX)/getpath.c.ed && \
	ed - $(PROJECT)/Modules/_localemodule.c < $(FIX)/_localemodule.c.ed && \
	ed - $(PROJECT)/Modules/posixmodule.c < $(FIX)/posixmodule.c.ed && \
	ed - $(PROJECT)/Modules/pwdmodule.c < $(FIX)/pwdmodule.c.ed && \
	ed - $(PROJECT)/Modules/pyexpat.c < $(FIX)/Modules_pyexpat.c.ed && \
	sed 's/@VERSION@/$(VERS)/' $(FIX)/python.c.ed | ed - $(PROJECT)/Modules/python.c && \
	ed - $(PROJECT)/Modules/readline.c < $(FIX)/readline.c.ed && \
	ex - $(PROJECT)/Modules/selectmodule.c < $(FIX)/selectmodule.c.ex && \
	patch $(PROJECT)/Objects/bufferobject.c $(FIX)/bufferobject.c.patch && \
	ed - $(PROJECT)/pyconfig.h.in < $(FIX)/pyconfig.h.in.ed && \
	ed - $(PROJECT)/Python/ceval.c < $(FIX)/ceval.c.ed && \
	ed - $(PROJECT)/Python/dynload_shlib.c < $(FIX)/dynload_shlib.c.ed && \
	ed - $(PROJECT)/Python/mactoolboxglue.c < $(FIX)/mactoolboxglue.c.ed && \
	patch $(PROJECT)/setup.py $(FIX)/setup.py-21106613.patch && \
	ed - $(PROJECT)/setup.py < $(FIX)/setup.py.ed && \
	for i in `find $(PROJECT)/Mac -name setup.py | xargs fgrep -l "'-framework', 'Carbon'"`; do \
	    ed - $$i < $(FIX)/Mac_setup.py.ed || exit 1; \
	done && \
	for i in `find $(PROJECT)/Lib -name __init__.py -size 0c`; do \
	    echo '#' > $$i; \
	done && \
	echo > $(PROJECT)/Lib/test/nullcert.pem
ifdef PYTHON_AUTOCONF
	@set -x && \
	cd '$(OBJROOT)' && \
	ed - $(PROJECT)/configure.in < $(FIX)/configure.in.ed
ifeq ($(PYTHON_AUTOCONF),YES)
	cd '$(OBJROOT)/$(PROJECT)' && autoconf
endif
else # !PYTHON_AUTOCONF
	tar -xof $(FIX)/autoconf.tar.bz2 -C '$(OBJROOT)/$(PROJECT)'
endif
	dtrace -h -s $(FIX)/pydtrace.d -o '$(PYDTRACE_H)'

install: installpython
	install $(FIX)/audiotest.au $(DSTROOT)$(LIBPYTHONVERS)/email/test/data/audiotest.au
	install $(FIX)/audiotest.au $(DSTROOT)$(LIBPYTHONVERS)/test/audiotest.au

##---------------------------------------------------------------------
# PR-3478215 - for backwards compatibility with non-framework python, we
# create symbolic links in /usr/include and /usr/lib.  We now create the
# links w.r.t. multiple versioning.  In addition, the PYTHON_DEFAULT
# environment variable will be set to YES if this version is the default
# version of python.
##---------------------------------------------------------------------
installpython: $(OBJROOT)/$(PROJECT)
	install -d '$(DSTROOT)/usr/bin'
	DSTROOT='$(DSTROOT)' OBJROOT='$(OBJROOT)/$(PROJECT)' \
	    SRCROOT='$(OBJROOT)' SYMROOT='$(SYMROOT)' RC_ARCHS='$(RC_ARCHS)' \
	    DYLD_NEW_LOCAL_SHARED_REGIONS=1 \
	    $(MAKE) -C '$(OBJROOT)' -f Makefile install \
	    DSTROOT='$(DSTROOT)' OBJROOT='$(OBJROOT)/$(PROJECT)' \
	    SRCROOT='$(OBJROOT)' SYMROOT='$(SYMROOT)' RC_ARCHS='$(RC_ARCHS)' \
	    VERS=$(VERS)
	@obj= && \
	install -d '$(DSTROOT)$(USRINCLUDE)'
	ln -sf ../..$(INCLUDEPYTHONVERS) '$(DSTROOT)$(USRINCLUDE)/$(PROJECT)$(VERS)'
	install -d '$(DSTROOT)$(USRLIB)'
	ln -sf ../..$(LIBPYTHONVERS) '$(DSTROOT)$(USRLIB)/$(PROJECT)$(VERS)'
	ln -sf ../..$(VERSIONSVERS)/Python '$(DSTROOT)$(USRLIB)/$(DYLIB)'
ifeq ($(PYTHON_DEFAULT),YES)
	ln -sf $(DYLIB) '$(DSTROOT)$(USRLIB)/lib$(PROJECT).dylib'
endif
	install -d '$(DSTROOT)$(SITEPACKAGES)'

.DEFAULT:
	@$(MAKE) -f Makefile $@
