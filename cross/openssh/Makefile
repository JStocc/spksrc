PKG_NAME = openssh
PKG_VERS = 10.6p2
PKG_EXT = tar.gz
PKG_DIST_NAME = $(PKG_NAME)-$(PKG_VERS).$(PKG_EXT)
PKG_DIST_SITE = https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable
PKG_DIR = $(PKG_NAME)-$(PKG_VERS)

DEPENDS = cross/zlib cross/openssl3

HOMEPAGE = https://www.openssh.com/
COMMENT  = Open source version of SSH connectivity tools.
LICENSE  = BSD-style

GNU_CONFIGURE = 1
INSTALL_ARGS = install-nosysconf DESTDIR=$(INSTALL_DIR) prefix=$(INSTALL_PREFIX)
POST_INSTALL_TARGET = openssh_post_install

CONFIGURE_ARGS = --with-privsep-path=$(INSTALL_PREFIX)/var/empty
CONFIGURE_ARGS += --disable-lastlog --disable-strip
CONFIGURE_ARGS += --disable-utmp --disable-utmpx
CONFIGURE_ARGS += --disable-wtmp --disable-wtmpx
# need to disable use of system getentropy for kernels < 3.17
CONFIGURE_ARGS += ac_cv_func_getentropy=no

include ../../mk/spksrc.cross-cc.mk

# Standalone OpenSSH package (spk/openssh): DSM has no "sshd" account for privilege
# separation. Use the dedicated, unprivileged account DSM 7 creates for the package.
ifeq ($(INSTALL_PREFIX),/var/packages/openssh/target)
CONFIGURE_ARGS += --with-privsep-user=sc-openssh
# The DSM kernel rejects seccomp filters (prctl(PR_SET_SECCOMP): Invalid argument),
# which made every pre-auth child die. The rlimit sandbox still runs the pre-auth
# process chrooted, unprivileged, unable to open files or fork.
CONFIGURE_ARGS += --with-sandbox=rlimit
# PAM: account and session checks through the DSM PAM stack (/etc/pam.d/sshd, read-only).
# Headers come from cross/linux-pam (listed first in spk/openssh DEPENDS); at runtime
# sshd uses the DSM /lib/libpam.so.0, the package does not ship its own libpam.
CONFIGURE_ARGS += --with-pam
endif

.PHONY: openssh_post_install
openssh_post_install:
	cd $(STAGING_INSTALL_PREFIX)/bin && ln -sf ssh slogin
