#  $Id: GNUmakefile,v a91011fbbc27 2015/01/07 20:39:03 fdelahoyde $
#  $Version: 3.0.3 $
#  Makefile for libgswteos-10 on Linux/GNU.

.PHONY: all clean install dist new-rpm new-release
.PHONY: show-release getdocs

          STSPackage :=	libgswteos-10
        _makeVersion :=	$(shell $(MAKE) -v -f /dev/null 2>&1 |\
			head -1 | awk '{if ($$3 >= 3.76) printf "%.2f\n",$$3}')
  ifeq (,$(_makeVersion))
	@echo "This Makefile requires GNU make version 3.76 or greater."; \
	echo "Please read the file README in this directory"; \
	echo "for details."; exit 1; \
	fi;
  endif
          STSVersion :=	$(shell if [ -d .hg ]; then \
			hg tip --template "{latesttag}"; \
			elif [ -d .git ]; then \
			git describe --tags --abbrev=0 HEAD; \
			else basename `pwd` | sed -e 's/$(STSPackage)-//'; fi)
  ifeq (,$(STSVersion))
	@echo "There is currently no version for $(STSPackage)."; \
	 echo "You need to set one."; exit 1; \
	 fi;
  endif
	  STSRelease := $(shell echo $(STSVersion)|sed -e 's/[^-]*-\?//')
  ifeq (,$(STSRelease))
          STSRelease := 1
  endif
	  STSVersion :=	$(shell echo $(STSVersion)|sed -e 's/-.*//')
                 ARCH:=	$(shell uname -m)
  ifeq (x86_64,$(ARCH))
           libdirname:=	lib64
  else
           libdirname:=	lib
  endif
               CFLAGS:=	-O3
            CINCLUDES:=	-I.
              Library:=	libgswteos-10.so
           LibVersion:=	$(shell echo $(STSVersion) | \
			awk -F . '{printf "%d.%d\n",$$1,$$2}')
              Program:=	gsw_check_functions
      $(Program)_SRCS:=	gsw_check_functions.c
      $(Program)_LIBS:=	-L. -lgswteos-10 -lm
      $(Library)_SRCS:=	gsw_oceanographic_toolbox.c \
			gsw_saar.c
      $(Library)_OBJS:=	gsw_oceanographic_toolbox.o \
			gsw_saar.o
             INCLUDES:=	gswteos-10.h
              DESTDIR:=	/usr
           DESTBINDIR:=	$(DESTDIR)/bin
           DESTINCDIR:=	$(DESTDIR)/include
           DESTLIBDIR:= $(DESTDIR)/$(libdirname)
             TARFILES:=	README LICENSE gsw_check_functions.c \
			gsw_oceanographic_toolbox.c gsw_saar.c \
			gsw_saar_data.c gswteos-10.h GNUmakefile \
			html
             ZIPFILES:= README LICENSE gsw_check_functions.c \
			gsw_oceanographic_toolbox.c gsw_saar.c \
			gsw_saar_data.c gswteos-10.h Makefile
              ZIPLINK:=	gsw_c_v$(shell echo $(STSVersion) | \
				sed -e 's/\.[^.]*$$//')

all:	$(Library) $(Program)

$(Program):	$($(Program)_SRCS)
	gcc $(CFLAGS) $(CINCLUDES) -o $(Program) $($(Program)_SRCS) \
		$($(Program)_LIBS)

$(Library):	$($(Library)_SRCS)
	gcc -fPIC -c $(CFLAGS) $(CINCLUDES) $($(Library)_SRCS)
	gcc -shared -o $(Library).$(LibVersion) $($(Library)_OBJS) -lm
	rm -f $(Library)
	ln -s $(Library).$(LibVersion) $(Library)

install:	$(Library) $(Program)
	mkdir -p $(INSTALL_ROOT)$(DESTLIBDIR) $(INSTALL_ROOT)$(DESTBINDIR) \
		$(INSTALL_ROOT)$(DESTINCDIR)
	install $(Library).$(LibVersion) $(INSTALL_ROOT)$(DESTLIBDIR)
	rm -f $(INSTALL_ROOT)$(DESTLIBDIR)/$(Library)
	ln -s $(Library).$(LibVersion) $(INSTALL_ROOT)$(DESTLIBDIR)/$(Library)
	install -s $(Program) $(INSTALL_ROOT)$(DESTBINDIR)
	install $(INCLUDES) $(INSTALL_ROOT)$(DESTINCDIR)

dist:
	rm -f gsw_c_v*;
	ln -s . $(ZIPLINK)
	zip -r $(ZIPLINK).zip $(addprefix $(ZIPLINK)/,$(ZIPFILES))
	
clean:
	rm -f $(Program) $(Library) $(Library).$(LibVersion) $($(Library)_OBJS)


new-release:	$(STSPackage).spec.proto
		@rm -f $(STSPackage).spec; \
		sed -e 's/@VERSION@/$(STSVersion)/' \
		    -e 's/@RELEASE@/$(STSRelease)/' \
			$(STSPackage).spec.proto >$(STSPackage).spec; \
		rm -f gsw_c_v*; \
		ln -s . $(ZIPLINK); \
		zip -r $(ZIPLINK).zip $(addprefix $(ZIPLINK)/,$(ZIPFILES))

new-rpm:	new-release
		@rm -f $(STSPackage)-*; \
		ln -s . $(STSPackage)-$(STSVersion); \
		tar cvzf $(STSPackage)-$(STSVersion).tar.gz \
		    --dereference \
		    $(addprefix $(STSPackage)-$(STSVersion)/,$(TARFILES)); \
		if [ -d /space/RPMBUILD/SOURCES ]; then \
		    cp $(STSPackage)-$(STSVersion).tar.gz \
			/space/RPMBUILD/SOURCES; \
		    cp $(STSPackage).spec /space/RPMBUILD/SPECS; \
		fi

show-release:
		@echo $(STSVersion)-$(STSRelease)

getdocs:
		@wget -k -np -p -m -nH --cut-dirs=2 \
			http://teos-10.org/pubs/gsw/html/gsw_contents.html
