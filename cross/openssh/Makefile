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

# Standalone OpenSSH package (spk/openssh) only. SynoCli Network Tools is not affected.
ifeq ($(INSTALL_PREFIX),/var/packages/openssh/target)
# DSM has no "sshd" account: use the unprivileged account DSM 7 creates for the package.
CONFIGURE_ARGS += --with-privsep-user=sc-openssh
# PAM: account and session checks through the DSM PAM stack (/etc/pam.d/sshd, read-only).
# Headers come from cross/linux-pam (listed first in spk/openssh DEPENDS); at runtime
# sshd uses the DSM /lib/libpam.so.0, the package does not ship its own libpam.
CONFIGURE_ARGS += --with-pam
# Sandbox: rlimit. The DSM kernel rejects the seccomp filter (prctl(PR_SET_SECCOMP):
# Invalid argument). configure refuses rlimit on Linux because its test sets
# RLIMIT_NOFILE to 0, which breaks poll(2); the patch below uses 1 instead (as the
# seccomp sandbox does). So configure without sandbox, then select rlimit in config.h.
CONFIGURE_ARGS += --with-sandbox=no
PATCHES += patches-standalone/001-sandbox-rlimit-nofile-one.patch
POST_CONFIGURE_TARGET = openssh_standalone_post_configure
endif

include ../../mk/spksrc.cross-cc.mk

.PHONY: openssh_standalone_post_configure
openssh_standalone_post_configure:
	@grep -q '^#define SANDBOX_NULL 1$$' $(WORK_DIR)/$(PKG_DIR)/config.h || \
	  { echo "ERROR: SANDBOX_NULL not found in config.h"; exit 1; }
	sed -i 's|^#define SANDBOX_NULL 1$$|#define SANDBOX_RLIMIT 1|' $(WORK_DIR)/$(PKG_DIR)/config.h
	@grep '^#define SANDBOX_' $(WORK_DIR)/$(PKG_DIR)/config.h


.PHONY: openssh_post_install
openssh_post_install:
	cd $(STAGING_INSTALL_PREFIX)/bin && ln -sf ssh slogin
