
SUBDIRS = . tests

################################################################################
# common
################################################################################

sbin_PROGRAMS =
noinst_LTLIBRARIES =
EXTRA_DIST =
BUILT_SOURCES =
CLEANFILES =

AM_CFLAGS = \
	$(WARN_CFLAGS) \
	$(MM_CFLAGS) \
	$(CODE_COVERAGE_CFLAGS) \
	$(GUDEV_CFLAGS) \
	-I$(top_srcdir) \
	-I$(top_srcdir)/include \
	-I$(top_builddir)/include \
	-I$(top_srcdir)/libqcdm/src \
	-I$(top_srcdir)/libmm-glib \
	-I${top_builddir}/libmm-glib/generated \
	-I${top_builddir}/libmm-glib/generated/tests \
	-I$(srcdir)/kerneldevice \
	$(NULL)

AM_LDFLAGS = \
	$(WARN_LDFLAGS) \
	$(MM_LIBS) \
	$(CODE_COVERAGE_LDFLAGS) \
	$(GUDEV_LIBS) \
	$(NULL)

if WITH_QRTR
AM_CFLAGS  += $(QRTR_CFLAGS)
AM_LDFLAGS += $(QRTR_LIBS)
endif

if WITH_QMI
AM_CFLAGS  += $(QMI_CFLAGS)
AM_LDFLAGS += $(QMI_LIBS)
endif

if WITH_MBIM
AM_CFLAGS  += $(MBIM_CFLAGS)
AM_LDFLAGS += $(MBIM_LIBS)
endif

if WITH_POLKIT
AM_CFLAGS  += $(POLKIT_CFLAGS)
AM_LDFLAGS += $(POLKIT_LIBS)
endif

if WITH_SYSTEMD_JOURNAL
AM_CFLAGS += $(LIBSYSTEMD_CFLAGS)
AM_LDFLAGS += $(LIBSYSTEMD_LIBS)
endif

################################################################################
# generic udev rules
################################################################################

udevrulesdir = $(UDEV_BASE_DIR)/rules.d
udevrules_DATA = \
	80-mm-candidate.rules \
	$(NULL)

EXTRA_DIST += $(udevrules_DATA)

################################################################################
# helpers library
################################################################################

noinst_LTLIBRARIES += libhelpers.la

HELPER_ENUMS_INPUTS = \
	$(srcdir)/mm-sms-part.h \
	$(srcdir)/mm-modem-helpers.h \
	$(NULL)

HELPER_ENUMS_GENERATED = \
	mm-helper-enums-types.h \
	mm-helper-enums-types.c \
	$(NULL)

mm-helper-enums-types.h: Makefile.am $(HELPER_ENUMS_INPUTS) $(top_srcdir)/build-aux/mm-enums-types.h.template
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"mm-sms-part.h\"\n#include \"mm-modem-helpers.h\"\n#ifndef __MM_HELPER_ENUMS_TYPES_H__\n#define __MM_HELPER_ENUMS_TYPES_H__\n" \
		--template $(top_srcdir)/build-aux/mm-enums-types.h.template \
		--ftail "#endif /* __MM_HELPER_ENUMS_TYPES_H__ */\n" \
		$(HELPER_ENUMS_INPUTS) > $@

mm-helper-enums-types.c: Makefile.am $(top_srcdir)/build-aux/mm-enums-types.c.template mm-helper-enums-types.h
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"mm-helper-enums-types.h\"" \
		--template $(top_srcdir)/build-aux/mm-enums-types.c.template \
		$(HELPER_ENUMS_INPUTS) > $@

libhelpers_la_SOURCES = \
	mm-log-object.h \
	mm-log-object.c \
	mm-log.c \
	mm-log.h \
	mm-log-test.h \
	mm-error-helpers.c \
	mm-error-helpers.h \
	mm-modem-helpers.c \
	mm-modem-helpers.h \
	mm-charsets.c \
	mm-charsets.h \
	mm-sms-part.h \
	mm-sms-part.c \
	mm-sms-part-3gpp.h \
	mm-sms-part-3gpp.c \
	mm-sms-part-cdma.h \
	mm-sms-part-cdma.c \
	$(NULL)

nodist_libhelpers_la_SOURCES = $(HELPER_ENUMS_GENERATED)

if WITH_QMI
libhelpers_la_SOURCES += \
	mm-modem-helpers-qmi.c \
	mm-modem-helpers-qmi.h \
	$(NULL)
endif

if WITH_MBIM
libhelpers_la_SOURCES += \
	mm-modem-helpers-mbim.c \
	mm-modem-helpers-mbim.h \
	$(NULL)
endif

# Request to build enum types before anything else
BUILT_SOURCES += $(HELPER_ENUMS_GENERATED)
CLEANFILES    += $(HELPER_ENUMS_GENERATED)

################################################################################
# kerneldevice library
################################################################################

noinst_LTLIBRARIES += libkerneldevice.la

libkerneldevice_la_CPPFLAGS = \
	-DUDEVRULESDIR=\"$(udevrulesdir)\" \
	$(NULL)

libkerneldevice_la_SOURCES = \
	kerneldevice/mm-kernel-device.h \
	kerneldevice/mm-kernel-device.c \
	kerneldevice/mm-kernel-device-helpers.h \
	kerneldevice/mm-kernel-device-helpers.c \
	kerneldevice/mm-kernel-device-generic.h \
	kerneldevice/mm-kernel-device-generic.c \
	kerneldevice/mm-kernel-device-generic-rules.h \
	kerneldevice/mm-kernel-device-generic-rules.c \
	$(NULL)

if WITH_QRTR
libkerneldevice_la_SOURCES += \
	kerneldevice/mm-kernel-device-qrtr.h \
	kerneldevice/mm-kernel-device-qrtr.c \
	$(NULL)
endif

if WITH_UDEV
libkerneldevice_la_SOURCES += \
	kerneldevice/mm-kernel-device-udev.h \
	kerneldevice/mm-kernel-device-udev.c \
	$(NULL)
endif

libkerneldevice_la_LIBADD = \
	$(top_builddir)/libmm-glib/libmm-glib.la \
	$(builddir)/libhelpers.la \
	$(NULL)

################################################################################
# ports library
################################################################################

noinst_LTLIBRARIES += libport.la

PORT_ENUMS_INPUTS = \
	$(srcdir)/mm-port.h \
	$(srcdir)/mm-port-serial-at.h \
	$(NULL)

if WITH_QMI
PORT_ENUMS_INPUTS += $(srcdir)/mm-port-qmi.h
endif

PORT_ENUMS_GENERATED = \
	mm-port-enums-types.h \
	mm-port-enums-types.c \
	$(NULL)

mm-port-enums-types.h: Makefile.am $(PORT_ENUMS_INPUTS) $(top_srcdir)/build-aux/mm-enums-types.h.template
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"config.h\"\n#include \"mm-port.h\"\n#include \"mm-port-serial-at.h\"\n#if defined WITH_QMI\n#include \"mm-port-qmi.h\"\n#endif\n#ifndef __MM_PORT_ENUMS_TYPES_H__\n#define __MM_PORT_ENUMS_TYPES_H__\n" \
		--template $(top_srcdir)/build-aux/mm-enums-types.h.template \
		--ftail "#endif /* __MM_PORT_ENUMS_TYPES_H__ */\n" \
		$(PORT_ENUMS_INPUTS) > $@

mm-port-enums-types.c: Makefile.am $(top_srcdir)/build-aux/mm-enums-types.c.template mm-port-enums-types.h
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"mm-port-enums-types.h\"" \
		--template $(top_srcdir)/build-aux/mm-enums-types.c.template \
		$(PORT_ENUMS_INPUTS) > $@

libport_la_SOURCES = \
	mm-port.c \
	mm-port.h \
	mm-port-net.c \
	mm-port-net.h \
	mm-port-serial.c \
	mm-port-serial.h \
	mm-port-serial-at.c \
	mm-port-serial-at.h \
	mm-port-serial-qcdm.c \
	mm-port-serial-qcdm.h \
	mm-port-serial-gps.c \
	mm-port-serial-gps.h \
	mm-serial-parsers.c \
	mm-serial-parsers.h \
	mm-netlink.h \
	mm-netlink.c \
	$(NULL)

nodist_libport_la_SOURCES = $(PORT_ENUMS_GENERATED)

if WITH_QMI
libport_la_SOURCES += \
	mm-port-qmi.c \
	mm-port-qmi.h \
	$(NULL)
endif

if WITH_MBIM
libport_la_SOURCES += \
	mm-port-mbim.c \
	mm-port-mbim.h \
	$(NULL)
endif

libport_la_LIBADD = \
	$(top_builddir)/libqcdm/src/libqcdm.la \
	$(top_builddir)/libmm-glib/libmm-glib.la \
	$(builddir)/libkerneldevice.la \
	$(NULL)

# Request to build enum types before anything else
BUILT_SOURCES += $(PORT_ENUMS_GENERATED)
CLEANFILES    += $(PORT_ENUMS_GENERATED)

################################################################################
# ModemManager daemon
################################################################################

sbin_PROGRAMS += ModemManager

DAEMON_ENUMS_INPUTS = \
	$(srcdir)/mm-filter.h \
	$(srcdir)/mm-base-bearer.h \
	$(srcdir)/mm-port-probe.h  \
	$(NULL)

DAEMON_ENUMS_GENERATED = \
	mm-daemon-enums-types.h \
	mm-daemon-enums-types.c \
	$(NULL)

mm-daemon-enums-types.h: Makefile.am $(DAEMON_ENUMS_INPUTS) $(top_srcdir)/build-aux/mm-enums-types.h.template
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"mm-filter.h\"\n#include \"mm-base-bearer.h\"\n#include \"mm-port-probe.h\"\n#ifndef __MM_DAEMON_ENUMS_TYPES_H__\n#define __MM_DAEMON_ENUMS_TYPES_H__\n" \
		--template $(top_srcdir)/build-aux/mm-enums-types.h.template \
		--ftail "#endif /* __MM_DAEMON_ENUMS_TYPES_H__ */\n" \
		$(DAEMON_ENUMS_INPUTS) > $@

mm-daemon-enums-types.c: Makefile.am $(top_srcdir)/build-aux/mm-enums-types.c.template mm-daemon-enums-types.h
	$(AM_V_GEN) $(GLIB_MKENUMS) \
		--fhead "#include \"mm-daemon-enums-types.h\"" \
		--template $(top_srcdir)/build-aux/mm-enums-types.c.template \
		$(DAEMON_ENUMS_INPUTS) > $@

# Request to build enum types before anything else
BUILT_SOURCES += $(DAEMON_ENUMS_GENERATED)
CLEANFILES    += $(DAEMON_ENUMS_GENERATED)

ModemManager_CPPFLAGS = \
	-DPLUGINDIR=\"$(pkglibdir)\" \
	-DFCCUNLOCKDIRPACKAGE=\"${pkglibdir}/fcc-unlock.d\" \
	-DFCCUNLOCKDIRUSER=\"${pkgsysconfdir}/fcc-unlock.d\" \
	-DCONNECTIONDIRPACKAGE=\"${pkglibdir}/connection.d\" \
	-DCONNECTIONDIRUSER=\"${pkgsysconfdir}/connection.d\" \
	-DMM_COMPILATION \
	$(NULL)

ModemManager_LDADD = \
	$(top_builddir)/libqcdm/src/libqcdm.la \
	$(top_builddir)/libmm-glib/libmm-glib.la \
	$(top_builddir)/libmm-glib/generated/tests/libmm-test-generated.la \
	$(builddir)/libport.la \
	$(NULL)

ModemManager_SOURCES = \
	main.c \
	mm-context.h \
	mm-context.c \
	mm-utils.h \
	mm-private-boxed-types.h \
	mm-private-boxed-types.c \
	mm-auth-provider.h \
	mm-auth-provider.c \
	mm-dispatcher.h \
	mm-dispatcher.c \
	mm-dispatcher-connection.h \
	mm-dispatcher-connection.c \
	mm-dispatcher-fcc-unlock.h \
	mm-dispatcher-fcc-unlock.c \
	mm-filter.h \
	mm-filter.c \
	mm-base-manager.c \
	mm-base-manager.h \
	mm-device.c \
	mm-device.h \
	mm-plugin-manager.c \
	mm-plugin-manager.h \
	mm-base-sim.h \
	mm-base-sim.c \
	mm-base-bearer.h \
	mm-base-bearer.c \
	mm-broadband-bearer.h \
	mm-broadband-bearer.c \
	mm-bearer-list.h \
	mm-bearer-list.c \
	mm-base-modem-at.h \
	mm-base-modem-at.c \
	mm-base-modem.h \
	mm-base-modem.c \
	mm-base-sms.h \
	mm-base-sms.c \
	mm-base-call.h \
	mm-base-call.c \
	mm-sms-list.h \
	mm-sms-list.c \
	mm-call-list.h \
	mm-call-list.c \
	mm-iface-modem.h \
	mm-iface-modem.c \
	mm-iface-modem-3gpp.h \
	mm-iface-modem-3gpp.c \
	mm-iface-modem-3gpp-profile-manager.h \
	mm-iface-modem-3gpp-profile-manager.c \
	mm-iface-modem-3gpp-ussd.h \
	mm-iface-modem-3gpp-ussd.c \
	mm-iface-modem-cdma.h \
	mm-iface-modem-cdma.c \
	mm-iface-modem-simple.h \
	mm-iface-modem-simple.c \
	mm-iface-modem-location.h \
	mm-iface-modem-location.c \
	mm-iface-modem-messaging.h \
	mm-iface-modem-messaging.c \
	mm-iface-modem-voice.h \
	mm-iface-modem-voice.c \
	mm-iface-modem-time.h \
	mm-iface-modem-time.c \
	mm-iface-modem-firmware.h \
	mm-iface-modem-firmware.c \
	mm-iface-modem-sar.h \
	mm-iface-modem-sar.c \
	mm-iface-modem-signal.h \
	mm-iface-modem-signal.c \
	mm-iface-modem-oma.h \
	mm-iface-modem-oma.c \
	mm-broadband-modem.h \
	mm-broadband-modem.c \
	mm-port-probe.h \
	mm-port-probe.c \
	mm-port-probe-at.h \
	mm-port-probe-at.c \
	mm-plugin.c \
	mm-plugin.h \
	mm-shared.h \
	$(NULL)

if WITH_QRTR
ModemManager_SOURCES += \
	mm-qrtr-bus-watcher.h \
	mm-qrtr-bus-watcher.c \
	$(NULL)
endif

nodist_ModemManager_SOURCES = $(DAEMON_ENUMS_GENERATED)

# Additional suspend/resume support via systemd
if WITH_SYSTEMD_SUSPEND_RESUME
ModemManager_SOURCES += \
	mm-sleep-monitor.h \
	mm-sleep-monitor-systemd.c \
	$(NULL)
endif

if WITH_POWERD_SUSPEND_RESUME
ModemManager_SOURCES += \
	mm-sleep-monitor.h \
	mm-sleep-monitor-powerd.c \
	$(NULL)
endif

# Additional QMI support in ModemManager
if WITH_QMI
ModemManager_SOURCES += \
	mm-shared-qmi.h \
	mm-shared-qmi.c \
	mm-sms-qmi.h \
	mm-sms-qmi.c \
	mm-sim-qmi.h \
	mm-sim-qmi.c \
	mm-call-qmi.h \
	mm-call-qmi.c \
	mm-bearer-qmi.h \
	mm-bearer-qmi.c \
	mm-broadband-modem-qmi.h \
	mm-broadband-modem-qmi.c \
	$(NULL)
endif

# Additional MBIM support in ModemManager
if WITH_MBIM
ModemManager_SOURCES += \
	mm-sms-mbim.h \
	mm-sms-mbim.c \
	mm-sim-mbim.h \
	mm-sim-mbim.c \
	mm-bearer-mbim.h \
	mm-bearer-mbim.c \
	mm-broadband-modem-mbim.h \
	mm-broadband-modem-mbim.c \
	$(NULL)
endif
