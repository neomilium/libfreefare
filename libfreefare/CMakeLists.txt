set(LIBRARY_SOURCES
		felica
		freefare
		freefare_internal
		mad
		mifare_application
		mifare_classic
		mifare_desfire
		mifare_desfire_aid
		mifare_desfire_crypto
		mifare_desfire_error
		mifare_desfire_key
		mifare_key_deriver
		mifare_ultralight
		ntag21x
		ntag21x_error
		tlv
		../contrib/libutil/hexdump
		)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_library(freefare ${LIBRARY_SOURCES})
target_link_libraries(freefare ${LIBS})

set(installheaders freefare.h)

install(FILES ${installheaders} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/freefare COMPONENT headers)
install(TARGETS freefare LIBRARY COMPONENT libraries)
