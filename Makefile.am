# Copyright (C) 2015 Tobias Oetiker
# GPL V3 licensed

AUTOMAKE_OPTIONS =  foreign

MJ_SCRIPT = smb_passwd_web

BIN = bin/$(MJ_SCRIPT).pl

THIRDPARTY_DIR := $(shell pwd)/thirdparty

THIRDPARTY_DIST := $(shell test -d thirdparty/CPAN && find thirdparty/CPAN -type d -name ".??*" -prune -o -not -name ".*" -a -not -name "*~" -a -not -name "*.tmp"  -a -type f -print )

EXTRA_DIST = VERSION README.md PERL_MODULES COPYRIGHT LICENSE CHANGES AUTHORS bootstrap $(PUB) 	$(wildcard t/*.t) $(BIN) $(THIRDPARTY_DIST) $(wildcard thirdparty/bin/cpanm)

YEAR := $(shell date +%Y)
DATE := $(shell date +%Y-%m-%d)

datadir = $(prefix)
nobase_data_DATA = $(PUB) $(TEMPL)

dist_bin_SCRIPTS = $(BIN)

all-local:
	$(AM_V_GEN)[ "$(MISSING_PERL_MODULES)" == "" ] || $(GMAKE) thirdparty/touch

dist-hook: thirdparty/CPAN/touch
	$(PERL) -i -p -e 's/#VERSION#/$(PACKAGE_VERSION)/g;s/#YEAR#/$(YEAR)/g;s/#DATE#/$(DATE)/g;'  $(distdir)/README.md $(distdir)/COPYRIGHT

install-exec-hook:
	[ "$(PERL5LIB)" == "" ] || cd "$(DESTDIR)$(exec_prefix)" && $(PERL) -i -p -e 's{.*# PERL5LIB}{use lib qw($(PERL5LIB)); # PERL5LIB}' $(BIN) || true
	cd "$(DESTDIR)$(exec_prefix)" && $(PERL) -i -p -e 's{.*# LIBDIR}{use lib qw($(libdir)); # LIBDIR}' $(BIN)
	cd "$(DESTDIR)$(exec_prefix)" && $(PERL) -i -p -e 's{^#!.*perl.*}{#!$(PERL)};' $(BIN)
	[ ! -d $(THIRDPARTY_DIR)/lib/perl5 ] || mkdir -p  $(DESTDIR)$(libdir) && cp -fr $(THIRDPARTY_DIR)/lib/perl5/* $(DESTDIR)$(libdir)

thirdparty/touch: thirdparty/CPAN/touch
	$(AM_V_GEN)echo $(MISSING_PERL_MODULES) | PERL_CPANM_HOME=$(THIRDPARTY_DIR) xargs $(PERL) $(THIRDPARTY_DIR)/bin/cpanm -q --notest --local-lib-contained $(THIRDPARTY_DIR)  --mirror file://$(THIRDPARTY_DIR)/CPAN --mirror-only
	$(AM_V_GEN)touch thirdparty/touch

thirdparty/CPAN/touch: PERL_MODULES
	echo "POPULATING OUR LOCAL micro CPAN"
	$(AM_V_GEN)$(URL_CAT) https://cpanmin.us | PERL_CPANM_HOME=$(THIRDPARTY_DIR) $(PERL) - -q --notest --local-lib $(THIRDPARTY_DIR) --save-dists $(THIRDPARTY_DIR)/CPAN --force App::cpanminus
	$(AM_V_GEN)PERL_CPANM_HOME=$(THIRDPARTY_DIR)/Ore $(THIRDPARTY_DIR)/bin/cpanm -q --notest  --local-lib $(THIRDPARTY_DIR)/Ore OrePAN
	$(AM_V_GEN)cat PERL_MODULES | PERL_CPANM_HOME=$(THIRDPARTY_DIR) xargs $(PERL) $(THIRDPARTY_DIR)/bin/cpanm -q --self-contained --notest --local-lib-contained $(THIRDPARTY_DIR) --save-dists $(THIRDPARTY_DIR)/CPAN
	$(AM_V_GEN)PERL5LIB=$(THIRDPARTY_DIR)/Ore/lib/perl5 $(THIRDPARTY_DIR)/Ore/bin/orepan_index.pl --repository $(THIRDPARTY_DIR)/CPAN
# Ore fails to extract the version form DBI
	$(AM_V_GEN)gunzip -c thirdparty/CPAN/modules/02packages.details.txt.gz | perl -pe 's{^(DBI\s+)undef(\s+\S+/DBI-)(\d+\.\d+)(\.tar)}{$$1$$3$$2$$3$$4}' |  gzip | cat > x.gz && mv x.gz thirdparty/CPAN/modules/02packages.details.txt.gz
	$(AM_V_GEN)touch thirdparty/CPAN/touch


