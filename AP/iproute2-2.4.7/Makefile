# Path to parent kernel include files directory
DESTDIR=
SBINDIR=/sbin
CONFDIR=/etc/iproute2
DOCDIR=/usr/doc/iproute2

include ../../make.def
include ../../define/PATH
include ../../target.def

#KERNEL_INCLUDE=/usr/src/linux/include
KERNEL_INCLUDE=$(LINUXDIR)/include

#LIBC_INCLUDE=/opt/crosstool/gcc-3.3.1-glibc-2.3.2/i386-linux-uclibc/include
LIB_INCLUDE=$(CROSSDIR)/uclibc/include

DEFINES= #-DRESOLVE_HOSTNAMES

#options if you have a bind>=4.9.4 libresolv (or, maybe, glibc)
#LDLIBS=-lresolv
#ADDLIB=

#options if you compile with libc5, and without a bind>=4.9.4 libresolv
LDLIBS=
ADDLIB=inet_ntop.o inet_pton.o

#options for decnet
ADDLIB+=dnet_ntop.o dnet_pton.o

#options for ipx
ADDLIB+=ipx_ntop.o ipx_pton.o

#ifeq ($(LIBC_INCLUDE)/socketbits.h,$(wildcard $(LIBC_INCLUDE)/socketbits.h))
#  ifeq ($(LIBC_INCLUDE)/net/if_packet.h,$(wildcard $(LIBC_INCLUDE)/net/if_packet.h))
#    GLIBCFIX=-I../include-glibc -include ../include-glibc/glibc-bugs.h
#    GLIBCFIX=-include ../include-glibc/glibc-bugs.h
#  endif
#endif
#ifeq ($(LIBC_INCLUDE)/bits/socket.h,$(wildcard $(LIBC_INCLUDE)/bits/socket.h))
#GLIBCFIX=-I../include-glibc -I/usr/include/db3 -include ../include-glibc/glibc-bugs.h
GLIBCFIX= -I../include-glibc -include ../include-glibc/glibc-bugs.h
#endif

#CC = gcc
CCOPTS = -D_GNU_SOURCE -Os -Wstrict-prototypes -Werror  
#CCOPTS = -D_GNU_SOURCE -Os -Wstrict-prototypes -Werror 
CFLAGS = $(CCOPTS) $(GLIBCFIX) -I$(KERNEL_INCLUDE) -I../include $(DEFINES)

LDLIBS += -L../lib -lnetlink -lutil 

SUBDIRS=lib ip tc #misc
#SUBDIRS=lib ip tc

LIBNETLINK=../lib/libnetlink.a ../lib/libutil.a

all: check-kernel
	@set -e; \
	for i in $(SUBDIRS); \
	do $(MAKE) -C $$i; done

check-kernel:
ifeq ($(KERNEL_INCLUDE),)
	@echo "Please, set correct KERNEL_INCLUDE"; false
else
	@set -e; \
	if [ ! -r $(KERNEL_INCLUDE)/linux/autoconf.h ]; then \
		echo "Please, compile the kernel first"; false; fi
endif

install: all
	install -m 0755 -d $(DESTDIR)$(SBINDIR)
	install -m 0755 -d $(DESTDIR)$(CONFDIR)
	install -m 0755 -d $(DESTDIR)$(DOCDIR)/examples
	install -m 0755 -d $(DESTDIR)$(DOCDIR)/examples/diffserv
	install -m 0644 README.iproute2+tc $(shell find examples -type f -maxdepth 1) $(DESTDIR)$(DOCDIR)/examples
	install -m 0644 $(shell echo examples/diffserv/*) $(DESTDIR)$(DOCDIR)/examples/diffserv
	@for i in $(SUBDIRS) doc; do $(MAKE) -C $$i install; done
	@cd etc/iproute2; for i in *; do \
		if [ ! -e $(DESTDIR)$(CONFDIR)/$$i ]; then \
			echo install -m 0644 $$i $(DESTDIR)$(CONFDIR); \
			install -m 0644 $$i $(DESTDIR)$(CONFDIR); fi; done

clean:
	for i in $(SUBDIRS) doc; \
	do $(MAKE) -C $$i clean; done

.EXPORT_ALL_VARIABLES:
