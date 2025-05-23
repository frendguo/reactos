
include_directories(
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl)

spec2def(netapi32.dll netapi32.spec ADD_IMPORTLIB)
add_rpc_files(client
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/atsvc.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/browser.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/dssetup.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/netdfs.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/netlogon.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/srvsvc.idl
    ${REACTOS_SOURCE_DIR}/sdk/include/reactos/idl/wkssvc.idl)

list(APPEND SOURCE
    apibuf.c
    browser.c
    dfs.c
    display.c
    dssetup.c
    group_new.c
    local_group.c
    misc.c
    nbcmdqueue.c
    nbnamecache.c
    nbt.c
    netapi32.c
    netbios.c
    netlogon.c
    obsolete.c
    schedule.c
    srvsvc.c
    user.c
    utils.c
    wksta.c
    wksta_new.c)

list(APPEND PCH_SKIP_SOURCE
    ${CMAKE_CURRENT_BINARY_DIR}/netapi32_stubs.c)

add_library(netapi32 MODULE
    ${SOURCE}
    ${PCH_SKIP_SOURCE}
    ${CMAKE_CURRENT_BINARY_DIR}/atsvc_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/browser_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/dssetup_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/netdfs_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/netlogon_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/srvsvc_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/wkssvc_c.c
    ${CMAKE_CURRENT_BINARY_DIR}/netapi32.def)

set_module_type(netapi32 win32dll)
target_link_libraries(netapi32 wine2ros ${PSEH_LIB})
add_delay_importlibs(netapi32 samlib secur32)
add_importlibs(netapi32 iphlpapi ws2_32 advapi32 rpcrt4 msvcrt kernel32 ntdll)
add_pch(netapi32 netapi32.h "${PCH_SKIP_SOURCE}")
add_cd_file(TARGET netapi32 DESTINATION reactos/system32 FOR all)

if(MSVC)
    # Disable warning C4267: '=': conversion from 'size_t' to 'USHORT', possible loss of data
    target_compile_options(netapi32 PRIVATE /wd4267)
endif()
