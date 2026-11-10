SPK_NAME = openssh
# SPK_VERS mirrors the OpenSSH portable version: 10.6p2 -> 10.6.2
# Bumped automatically by .github/workflows/openssh-update.yml
# SPK_REV always increments (never reset), as required by spksrc
SPK_VERS = 10.5.1
SPK_REV = 1
SPK_ICON = src/openssh.png

DEPENDS = cross/openssh

# DSM 7 only: the package never runs anything as root by itself.
REQUIRED_MIN_DSM = 7.0
UNSUPPORTED_ARCHS = $(ARMv5_ARCHS) $(OLD_PPC_ARCHS)

MAINTAINER = local
DISPLAY_NAME = OpenSSH (standalone)
DESCRIPTION = "Recent OpenSSH Portable server and client tools, installed side by side with the DSM native SSH service. Nothing from DSM is replaced: own binaries, own configuration, own host keys, own port \(2222 by default\). The server is started as root by a DSM Task Scheduler task calling openssh-ctl. See the documentation before use."
STARTABLE = no
CHANGELOG = "1. Standalone OpenSSH package."

HOMEPAGE = https://www.openssh.com/
LICENSE  = BSD-style

# Only the control script is exposed in /usr/local/bin.
# ssh, scp, sftp... are NOT linked, to avoid clashing with SynoCli Network Tools.
# Use them with their full path: /var/packages/openssh/target/bin/ssh
SPK_COMMANDS = bin/openssh-ctl

POST_STRIP_TARGET = openssh_extra_install

include ../../mk/spksrc.spk.mk

.PHONY: openssh_extra_install
openssh_extra_install:
	install -m 755 -d $(STAGING_DIR)/bin $(STAGING_DIR)/share/openssh-spk
	install -m 755 src/openssh-ctl $(STAGING_DIR)/bin/openssh-ctl
	install -m 644 src/sshd_config.default $(STAGING_DIR)/share/openssh-spk/sshd_config.default
