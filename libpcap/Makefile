PCAP_VERSION=1.6.2

$(COMPILED)/libpcap.so: $(build_tmp)/libpcap-$(PCAP_VERSION)/libpcap.so.$(PCAP_VERSION)
	@ cp $(build_tmp)/libpcap-$(PCAP_VERSION)/libpcap.so.$(PCAP_VERSION) $(COMPILED)/libpcap.so
	@ cp -a $(build_tmp)/libpcap-install/usr/local/include/pcap* $(cwd)/deps/libpcap/ >> build.log

$(build_tmp)/libpcap-$(PCAP_VERSION)/libpcap.so.$(PCAP_VERSION):
	@ [ -d $(build_tmp) ] || mkdir $(build_tmp)
	@ [ -f $(build_tmp)/libpcap-$(PCAP_VERSION)/configure ] || \
		tar -C $(build_tmp) -xzf $(cwd)/deps/libpcap/libpcap-$(PCAP_VERSION).tar.gz
	@ (cd $(build_tmp)/libpcap-$(PCAP_VERSION) && \
		CFLAGS=-Os ./configure \
		    ac_cv_header_linux_net_tstamp_h=no \
		    ac_cv_header_sys_bittypes_h=no \
		    ac_cv_func_strlcpy=yes \
		    ac_cv_netfilter_can_compile=no \
		    --disable-bluetooth  \
		    --disable-can \
		    --disable-canusb \
	            --disable-dbus \
		    --without-libnl > build.log)
	@ echo '#undef HAVE_DECL_ETHER_HOSTTON' >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ echo '#undef HAVE_SYS_BITYPES_H' >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ echo '#undef PCAP_SUPPORT_CAN' >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ echo '#undef PCAP_SUPPORT_USB' >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ echo '#undef HAVE_ETHER_HOSTTON'  >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ echo '#define _STDLIB_H this_works_around_malloc_definition_in_grammar_dot_c' >> $(build_tmp)/libpcap-$(PCAP_VERSION)/config.h
	@ sed -i -e s/pcap-usb-linux.c//g -e s/fad-getad.c/fad-gifc.c/g $(build_tmp)/libpcap-$(PCAP_VERSION)/Makefile
	@ sed -i -e s^"CC = gcc"^"CC = gcc $(PCAP_CFLAGS)"^g $(build_tmp)/libpcap-$(PCAP_VERSION)/Makefile
	@ (cd $(build_tmp)/libpcap-$(PCAP_VERSION) && patch -p1 < $(cwd)/deps/libpcap/64-bit-tpacket-v1.diff)
	@ (cd $(build_tmp)/libpcap-$(PCAP_VERSION) && patch -p0 < $(cwd)/deps/libpcap/pcap_nametoaddr_fix.diff)
	@ $(MAKE) -C $(build_tmp)/libpcap-$(PCAP_VERSION) >> build.log
	@ $(MAKE) -C $(build_tmp)/libpcap-$(PCAP_VERSION) install DESTDIR=$(cwd)/$(build_tmp)/libpcap-install >> build.log

clean-pcap:
	#(cd $(build_tmp)/libpcap-$(PCAP_VERSION)/ && make clean)
	# This avoids the pcap target trying to patch the same file more than once.
	# It's a pretty small tar, so untar'ing goes pretty quickly anyway, in
	# contrast to openssl.
	rm -fr $(COMPILED)/libpcap.so $(build_tmp)/libpcap-$(PCAP_VERSION)
