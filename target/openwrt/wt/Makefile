#

include $(TOPDIR)/rules.mk

PKG_NAME:=wt
PKG_VERSION:=4.1.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/emweb/wt/archive
PKG_MD5SUM:=6f4faa4bc117a53449bca446c2e7a92f

PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

PKG_INSTALL:=1

define Package/wt/Default
  SECTION:=libs
  CATEGORY:=Libraries
  SUBMENU:=Web toolkit
  URL:=http://www.webtoolkit.eu/wt
endef

define Package/wt
  $(call Package/wt/Default)
  MENU:=1
  DEPENDS:=+boost-date_time +boost-system +boost-filesystem +boost-thread +boost-program_options +libopenssl +libstdcpp +libpthread +librt
  TITLE:=C++ Web Toolkit
endef

define Package/wt-resources
  $(call Package/wt/Default)
  DEPENDS:=wt
  TITLE:=Optional Resource Files
endef

define Package/wt-fcgi
  $(call Package/wt/Default)
  DEPENDS:=wt +fcgixx
  TITLE:=FastCGI Connector
endef

define Package/wt-http
  $(call Package/wt/Default)
  DEPENDS:=wt +PACKAGE_wt_http_zlib:zlib +PACKAGE_wt_http_ssl:libopenssl
  TITLE:=Built in HTTP Server
endef

define Package/wt-http/config
config PACKAGE_wt_http_zlib
    bool "Enable zlib compression option"
    depends on PACKAGE_wt-http
    default n
config PACKAGE_wt_http_ssl
    bool "Enable ssl option"
    depends on PACKAGE_wt-http
    default n
endef

define Package/wt-dbo
  $(call Package/wt/Default)
  DEPENDS:=wt
  TITLE:=Object Relational Mapping
endef

define Package/wt-dbosqlite3
  $(call Package/wt/Default)
  DEPENDS:=wt +wt-dbo
  TITLE:=Sqlite3 Dbo Backend
endef

define Package/wt-resources/conffiles
/usr/share/Wt
endef

TARGET_CFLAGS += $(FPIC)
CMAKE_OPTIONS += \
	-DCMAKE_BUILD_TYPE=MinSizeRel \
	-DCMAKE_CXX_FLAGS_MINSIZEREL:STRING="-DNDEBUG" \
	-DSHARED_LIBS:BOOL=ON \
	-DBUILD_TESTS=OFF \
	-DBUILD_EXAMPLES=OFF \
	-DINSTALL_RESOURCES=ON \
	-DINSTALL_DOCUMENTATION=OFF \
	-DINSTALL_EXAMPLES=OFF \
	-DENABLE_HARU=OFF \
	-DENABLE_PANGO=OFF \
	-DENABLE_EXT=OFF \
	-DENABLE_SQLITE=ON \
	-DENABLE_POSTGRES=OFF \
	-DENABLE_FIREBIRD=OFF \
	-DENABLE_MYSQL=OFF \
	-DENABLE_MSSQLSERVER=OFF \
	-DENABLE_QT5=OFF \
	-DENABLE_QT4=OFF \
	-DENABLE_LIBWTTEST=OFF \
	-DENABLE_OPENGL=OFF \
	-DENABLE_UNWIND=OFF \
	-DWT_NO_STD_LOCALE=ON \
	-DWT_NO_STD_WSTRING=ON

ifndef CONFIG_PACKAGE_wt-http
CMAKE_OPTIONS += \
	-DCONNECTOR_HTTP=OFF
endif

ifndef CONFIG_PACKAGE_wt_http_zlib
CMAKE_OPTIONS += \
	-DHTTP_WITH_ZLIB=OFF
endif

ifndef CONFIG_PACKAGE_wt_http_ssl
CMAKE_OPTIONS += \
	-DENABLE_SSL=OFF
endif

ifndef CONFIG_PACKAGE_wt-fcgi
CMAKE_OPTIONS += \
	-DCONNECTOR_FCGI=OFF
endif

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) -a $(PKG_INSTALL_DIR)/usr/include/Wt $(1)/usr/include

# TODO install FindWt.cmake
#	$(INSTALL_DIR) $(1)/usr/share/cmake-2.4/Modules
#	$(CP) $(PKG_INSTALL_DIR)/usr/share/cmake-2.4/Modules/FindWt.cmake $(1)/usr/share/cmake-2.4/Modules

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libwt*.so* $(1)/usr/lib
endef

define BuildPlugin
  define Package/$(1)/install
	$(INSTALL_DIR) $$(1)/usr/lib
	for m in $(2); do \
		$(CP) $(PKG_INSTALL_DIR)/usr/lib/libwt$$$$$$$${m}.so* $$(1)/usr/lib/ ; \
	done
  endef

  $$(eval $$(call BuildPackage,$(1)))
endef

define Package/wt/install
	$(INSTALL_DIR) $(1)/etc/wt
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/wt_config.xml $(1)/etc/wt

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libwt.so* $(1)/usr/lib
endef

define Package/wt/conffiles
/etc/wt
endef

define Package/wt-resources/install
	$(INSTALL_DIR) $(1)/usr/share
	$(CP) -a $(PKG_INSTALL_DIR)/usr/share/Wt $(1)/usr/share
endef

$(eval $(call BuildPackage,wt))
$(eval $(call BuildPackage,wt-resources))
$(eval $(call BuildPlugin,wt-http,http))
$(eval $(call BuildPlugin,wt-fcgi,fcgi))
$(eval $(call BuildPlugin,wt-dbo,dbo))
$(eval $(call BuildPlugin,wt-dbosqlite3,dbosqlite3))