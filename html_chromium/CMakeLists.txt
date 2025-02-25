# TODO: Update Resources for CEF 95+
if(WIN32)
	set(BINARIES
		${CEF_PATH}/Release/chrome_elf.dll
		${CEF_PATH}/Release/d3dcompiler_47.dll
		${CEF_PATH}/Release/libcef.dll
		${CEF_PATH}/Release/libEGL.dll
		${CEF_PATH}/Release/libGLESv2.dll
		${CEF_PATH}/Release/snapshot_blob.bin
		${CEF_PATH}/Release/v8_context_snapshot.bin
		${CEF_PATH}/Resources/icudtl.dat)
elseif(UNIX AND NOT APPLE)
	set(BINARIES
		${CEF_PATH}/Release/chrome-sandbox
		${CEF_PATH}/Release/libcef.so
		${CEF_PATH}/Release/libEGL.so
		${CEF_PATH}/Release/libGLESv2.so
		${CEF_PATH}/Release/snapshot_blob.bin
		${CEF_PATH}/Release/v8_context_snapshot.bin
		${CEF_PATH}/Resources/icudtl.dat)
else()
	message(FATAL_ERROR "No IMPORTED_LOCATION for libcef")
endif()

if(WIN32)
	set(RESOURCES
		${CEF_PATH}/Resources/cef.pak
		${CEF_PATH}/Resources/cef_100_percent.pak
		${CEF_PATH}/Resources/cef_200_percent.pak
		${CEF_PATH}/Resources/cef_extensions.pak
		${CEF_PATH}/Resources/devtools_resources.pak)
elseif(UNIX AND NOT APPLE)
	set(RESOURCES
		${CEF_PATH}/Resources/chrome_100_percent.pak
		${CEF_PATH}/Resources/chrome_200_percent.pak
		${CEF_PATH}/Resources/resources.pak)
else()
	message(FATAL_ERROR "No RESOURCES set")
endif()

install(FILES ${BINARIES} DESTINATION ${GAME_BIN_DIR})

if(WIN32 OR APPLE)
	install(FILES ${RESOURCES} DESTINATION chromium)
	install(DIRECTORY ${CEF_PATH}/Resources/locales DESTINATION chromium)
else()
	install(FILES ${RESOURCES} DESTINATION linux32/chromium)
	install(DIRECTORY ${CEF_PATH}/Resources/locales DESTINATION linux32/chromium)
endif()

# Actual lib
set(SOURCES
	cef_end.h
	cef_start.h
	ChromiumBrowser.cpp
	ChromiumBrowser.h
	ChromiumClient.cpp
	ChromiumClient.h
	ChromiumSystem.cpp
	ChromiumSystem.h
	HtmlResourceHandler.cpp
	HtmlResourceHandler.h
	ImageData.h
	JSObjects.cpp
	JSObjects.h
	MessageQueue.h
	ResourceHandler.cpp
	ResourceHandler.h)

# Lame
include_directories(${CEF_PATH})

add_library(html_chromium SHARED ${SOURCES})
target_link_libraries(html_chromium html libcef_imp libcef_dll_wrapper)
SET_TARGET_PROPERTIES(html_chromium PROPERTIES PREFIX "")

if(UNIX)
	set_target_properties(html_chromium PROPERTIES OUTPUT_NAME "html_chromium_client")
endif()
install(TARGETS html_chromium LIBRARY DESTINATION ${GAME_BIN_DIR})
