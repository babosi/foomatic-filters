
# Variables
prefix=@prefix@
exec_prefix=@exec_prefix@
libdir=@libdir@
bindir=@bindir@
sbindir=@sbindir@
mandir=@mandir@
sysconfdir=@sysconfdir@

PREFIX=$(prefix)
SRC=@srcdir@

BINDIR=$(bindir)
SBINDIR=$(sbindir)
MANDIR=$(mandir)
ETCDIR=$(sysconfdir)/foomatic
INSTALL=@INSTALL@

# Paths for CUPS
CUPS=@CUPS@
CUPS_FILTERS=@CUPS_FILTERS@
CUPS_BACKENDS=@CUPS_BACKENDS@

# Paths for PPR
PPR=@PPR@
PPR_INTERFACES=@PPR_INTERFACES@
PPR_LIB=@PPR_LIB@

# This is mainly useful for building a binary foomatic package
#DESTDIR=

bin_PROGRAMS = foomatic-rip

foomatic_ripdir = .
foomatic_rip_SOURCES = \
	foomaticrip.c \
	foomaticrip.h \
	options.c \
	options.h \
	pdf.c \
	pdf.h \
	postscript.c \
	postscript.h \
	util.c \
	util.h \
	spooler.h \
	spooler.c \
	process.h \
	process.c \
	renderer.c \
	renderer.h \
	fileconverter.c \
	fileconverter.h

if BUILD_DBUS
foomatic_rip_SOURCES += \
	colord.c \
	colord.h

foomatic_rip_CFLAGS = $(DBUS_CFLAGS) -DHAVE_DBUS
foomatic_rip_LDADD = $(DBUS_LIBS)
endif

AM_CPPFLAGS = -DCONFIG_PATH='"$(sysconfdir)/foomatic"'

# Masks for trash files which have to be removed before packaging Foomatic
TRASHFILES="*~" "*\#*" ".??*" "*.rej"

# The install rule should check for kitloads and avoid stomping.  It doesn't
install: install-main install-cups install-ppr

install-main: install-bin install-man

install-cups:	install-bin
	${SRC}/mkinstalldirs $(DESTDIR)$(CUPS_FILTERS)
	${SRC}/mkinstalldirs $(DESTDIR)$(CUPS_BACKENDS)
	ln -sf $(BINDIR)/foomatic-rip $(DESTDIR)$(CUPS_FILTERS)
	${INSTALL} -m 755 beh $(DESTDIR)$(CUPS_BACKENDS)

install-ppr:	install-bin
	${SRC}/mkinstalldirs $(DESTDIR)$(PPR_INTERFACES)
	${SRC}/mkinstalldirs $(DESTDIR)$(PPR_LIB)
	ln -sf $(BINDIR)/foomatic-rip $(DESTDIR)$(PPR_INTERFACES)
	ln -sf $(BINDIR)/foomatic-rip $(DESTDIR)$(PPR_LIB)

install-bin:	install-etc
	${SRC}/mkinstalldirs $(DESTDIR)$(BINDIR)
	(for FILE in $(bin_PROGRAMS) ; do \
		${INSTALL} -m 755 $$FILE $(DESTDIR)$(BINDIR) ; done)

install-etc:
	${SRC}/mkinstalldirs $(DESTDIR)$(ETCDIR)/direct
	${INSTALL} -m 644 ${SRC}/filter.conf $(DESTDIR)$(ETCDIR)/filter.conf.sample
	if [ ! -f  $(DESTDIR)$(ETCDIR)/filter.conf ] ; then ${INSTALL} -m 644 ${SRC}/filter.conf $(DESTDIR)$(ETCDIR); fi;

install-man:
	${SRC}/mkinstalldirs $(DESTDIR)$(MANDIR)
	${SRC}/mkinstalldirs $(DESTDIR)$(MANDIR)/man1
	(for FILE in *.1 ; do \
		${INSTALL} -m 444 $$FILE $(DESTDIR)$(MANDIR)/man1 ; done)

# Clean up the source directory
clean:	remove-trash
	rm -f *.o foomatic-rip foomatic-rip.1 beh 
	rm -f .testing-stamp stamp-h1
	rm -f test/*.out

distclean:	clean
	rm -f $(AC_OUTPUT_FILES) config.log config.status config.cache configure.lineno
	rm -rf autom*.cache confdefs.h config.h
	rm -f Makefile test/Makefile
	rm -rf .deps

maintainer-clean:	distclean
	rm -f configure config.h.in Makefile.in aclocal.m4 INSTALL VERSION.full
	rm -f depcomp missing 
# Remove editor backup and temporary files
remove-trash:
	for m in $(TRASHFILES); do \
	  find . -name "$$m" -xtype f -exec rm -f "{}" \; ; \
	done

# Uninstall an installed Foomatic

uninstall:	uninstall-cups uninstall-ppr uninstall-main

uninstall-main:	uninstall-bin uninstall-man

uninstall-bin:
	( cd $(DESTDIR)$(BINDIR) && \
	  rm -f $(bin_PROGRAMS) \
	)

uninstall-etc:
	rm -f $(DESTDIR)$(ETCDIR)/filter.conf
	rm -f $(DESTDIR)$(ETCDIR)/filter.conf.sample
	rmdir $(DESTDIR)$(ETCDIR)/direct || :
	rmdir $(DESTDIR)$(ETCDIR) || :

uninstall-cups:
	rm -f $(DESTDIR)$(CUPS_FILTERS)/foomatic-rip
	rm -f $(DESTDIR)$(CUPS_BACKENDS)/beh
	rmdir $(DESTDIR)$(CUPS_FILTERS) || :
	rmdir $(DESTDIR)$(CUPS_BACKENDS) || :
	rmdir $(DESTDIR)$(CUPS) || :

uninstall-ppr:
	rm -f $(DESTDIR)$(PPR_INTERFACES)/foomatic-rip
	rmdir $(DESTDIR)$(PPR_INTERFACES) || :
	rm -f $(DESTDIR)$(PPR_LIB)/foomatic-rip
	rmdir $(DESTDIR)$(PPR_LIB) || :
	rmdir $(DESTDIR)$(PPR) || :

uninstall-man:	check-config
	for m in $(bin_PROGRAMS); do \
	  rm -f $(DESTDIR)$(MANDIR)/man1/$$m.*; \
	done

# Various testing/debugging/etc targets
inplace:	all
	chmod a+rx $(bin_PROGRAMS)

testing tests:	inplace
	cd tests ; $(MAKE)

.PHONY:	all install install-bin clean distclean maintainer-clean tests inplace 

