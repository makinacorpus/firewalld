SUBDIRS = config doc po shell-completion src

EXTRA_DIST = \
	COPYING \
	README \
	autogen.sh \
	${PACKAGE_NAME}.spec \
	fix_python_shebang.sh

CLEANFILES = *~ *\# .\#* *.tar*

DISTCLEANFILES = config.log intltool-*

DISTCLEANDIRS = autom4te.cache ${PACKAGE_NAME}-*

all: report

tag:
	@spec_ver=`awk '/Version:/ { print $$2}' ${PACKAGE_NAME}.spec`; \
	if test "$$spec_ver" != "${PACKAGE_VERSION}"; then \
		echo "Spec file and package versions differ: $$spec_ver != ${PACKAGE_VERSION}"; \
		secs=10; \
		echo -n "Using ./autogen.sh in $$secs seconds: "; \
		for i in `seq $$secs -1 1`; do echo -n "."; sleep 1; done; echo; \
		./autogen.sh; \
		echo; \
		echo "Please run make again to apply version changes."; \
		exit 1; \
	fi
	@if ! git diff --quiet --exit-code; then \
		clear; \
		echo -n "========================================"; \
		echo "========================================"; \
		PAGER= git diff; \
		echo -n "========================================"; \
		echo "========================================"; \
		echo "Do you want to commit these changes? (y/N)"; \
		read answer; \
		[ "$$answer" == "Y" -o "$$answer" == "y" ] || exit 1; \
		git commit -a -m "$(PACKAGE_TAG)"; \
	fi
	git tag -f $(PACKAGE_TAG)
	git push --all
	git push --tags

dist: clean-docs update-docs

update-docs:
	$(MAKE) -C doc/xml

clean-docs:
	$(MAKE) -C doc/xml clean

pull-translations:
	zanata-cli -B pull

push-translations:
	zanata-cli -B push

archive: pull-translations update-po push-translations $(desktop_DATA) tag dist
	scp "${PACKAGE_NAME}-$(PACKAGE_VERSION).tar.bz2" fedorahosted.org:${PACKAGE_NAME}
	@echo "The archive is in ${PACKAGE_NAME}-$(PACKAGE_VERSION).tar.bz2"
	@echo "Also have a look at https://fedorahosted.org/released/${PACKAGE_NAME}/"

local: distclean
	@rm -rf ${PACKAGE_NAME}-$(PACKAGE_VERSION).tar.bz2
	@rm -rf /tmp/${PACKAGE_NAME}-$(PACKAGE_VERSION) /tmp/${PACKAGE_NAME}
	@dir=$$PWD; cd /tmp; cp -a $$dir ${PACKAGE_NAME}
	@mv /tmp/${PACKAGE_NAME} /tmp/${PACKAGE_NAME}-$(PACKAGE_VERSION)
	@dir=$$PWD; cd /tmp; tar --bzip2 -cSpf $$dir/${PACKAGE_NAME}-$(PACKAGE_VERSION).tar.bz2 ${PACKAGE_NAME}-$(PACKAGE_VERSION)
	@rm -rf /tmp/${PACKAGE_NAME}-$(PACKAGE_VERSION)	
	@echo "The archive is in ${PACKAGE_NAME}-$(PACKAGE_VERSION).tar.bz2"

test-rpm: dist
	@rpmbuild -ta $(PACKAGE_NAME)-$(PACKAGE_VERSION).tar.bz2

test-srpm: dist
	@rpmbuild -ts $(PACKAGE_NAME)-$(PACKAGE_VERSION).tar.bz2

update-po:
	ls $(top_srcdir)/po/*.po | sed 's/.*\/po\///;s/.po//' > $(top_srcdir)/po/LINGUAS
	$(MAKE) -C po update-po ${PACKAGE_NAME}.pot

clean-po:
	@for cat in `cat ${top_srcdir}/po/LINGUAS`; do \
		msgattrib --translated --no-fuzzy --no-obsolete --force-po --no-location --clear-previous --strict $(top_srcdir)/po/$$cat.po -o $(top_srcdir)/po/$$cat.out; \
		mv -f $(top_srcdir)/po/$$cat.out $(top_srcdir)/po/$$cat.po; \
	done

report:
	@for cat in `cat ${top_srcdir}/po/LINGUAS`; do \
		echo -n "$$cat: "; \
		$(MSGFMT) --statistics -o /dev/null $(top_srcdir)/po/$$cat.po; \
	done

distclean-local:
	-test -z "$(DISTCLEANDIRS)" || rm -rf $(DISTCLEANDIRS)
