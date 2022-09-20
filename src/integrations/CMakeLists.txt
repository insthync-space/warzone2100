cmake_minimum_required (VERSION 3.5)

##################################################
# WZ Discord RPC support
# To build, define CMAKE variables:
#  - ENABLE_DISCORD (to build with Discord RPC support)
#  - DISCORD_RPC_APPID (to supply the Discord RPC configuration)

if(ENABLE_DISCORD)
	if (TARGET discord-rpc)
		add_library(wz-discord-integration STATIC "wzdiscordrpc.h" "wzdiscordrpc.cpp")
		set_property(TARGET wz-discord-integration PROPERTY FOLDER "integrations")
		include(WZTargetConfiguration)
		WZ_TARGET_CONFIGURATION(wz-discord-integration)

		target_include_directories(wz-discord-integration PRIVATE "${CMAKE_SOURCE_DIR}/3rdparty/discord-rpc/include")
		target_link_libraries(wz-discord-integration discord-rpc)
		target_compile_definitions(wz-discord-integration PUBLIC "ENABLE_DISCORD")
		if(DEFINED DISCORD_RPC_APPID AND NOT "${DISCORD_RPC_APPID}" STREQUAL "")
			set(WZ_DISCORD_RPC_APPID "${DISCORD_RPC_APPID}")
		else()
			unset(WZ_DISCORD_RPC_APPID)
		endif()
		configure_file("wz-discord-rpc-config.h.in" "${CMAKE_CURRENT_BINARY_DIR}/wz-discord-config/wz-discord-config.h" @ONLY)
		target_include_directories(wz-discord-integration PRIVATE "${CMAKE_CURRENT_BINARY_DIR}/wz-discord-config")

		target_link_libraries(wz-discord-integration gamelib netplay framework)
		target_link_libraries(wz-discord-integration EmbeddedJSONSignature)
		target_link_libraries(wz-discord-integration nlohmann_json)
		target_link_libraries(wz-discord-integration optional-lite)
	else()
		message(WARNING "Missing target: discord-rpc")
	endif()
endif()