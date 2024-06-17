cmake_minimum_required(VERSION 3.13)

project(fake-openssh-portable)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_C_FLAGS "-g -O2 -pipe -Wno-error=format-truncation -Wall -Wextra -Wpointer-arith -Wuninitialized -Wsign-compare -Wformat-security -Wsizeof-pointer-memaccess -Wno-pointer-sign -Wno-unused-parameter -Wno-unused-result -Wimplicit-fallthrough -Wmisleading-indentation -fno-strict-aliasing -D_FORTIFY_SOURCE=2 -ftrapv -fzero-call-used-regs=used -ftrivial-auto-var-init=zero -fno-builtin-memset -fstack-protector-strong -fPIE")
set(CMAKE_C_FLAGS_NOPIE "-g -O2 -pipe -Wno-error=format-truncation -Wall -Wextra -Wpointer-arith -Wuninitialized -Wsign-compare -Wformat-security -Wsizeof-pointer-memaccess -Wno-pointer-sign -Wno-unused-parameter -Wno-unused-result -Wimplicit-fallthrough -Wmisleading-indentation -fno-strict-aliasing -D_FORTIFY_SOURCE=2 -ftrapv -fzero-call-used-regs=used -ftrivial-auto-var-init=zero -fno-builtin-memset -fstack-protector-strong")
set(CMAKE_CXX_FLAGS "-I. -I.. -I$(srcdir) -I$(srcdir)/..  -D_XOPEN_SOURCE=600 -D_BSD_SOURCE -D_DEFAULT_SOURCE -D_GNU_SOURCE -DOPENSSL_API_COMPAT=0x10100000L -DHAVE_CONFIG_H")
set(CMAKE_CXX_LINK_LIBRARY_FLAG "-L. -Lopenbsd-compat/  -Wl,-z,relro -Wl,-z,now -Wl,-z,noexecstack -fstack-protector-strong")

add_subdirectory(openbsd-compat)

include_directories(${CMAKE_CURRENT_LIST_DIR})

set(XMSS_SRCS
        ssh-xmss.c
        sshkey-xmss.c
        xmss_commons.c
        xmss_fast.c
        xmss_hash.c
        xmss_hash_address.c
        xmss_wots.c
)

set(LIBOPENSSH_SRCS
        ssh_api.c
        ssherr.c
        sshbuf.c
        sshkey.c
        sshbuf-getput-basic.c
        sshbuf-misc.c
        sshbuf-getput-crypto.c
        krl.c
        bitmap.c
        ${XMSS_SRCS}
)

set(LIBSSH_SRCS
        ${LIBOPENSSH_SRCS}
        authfd.c authfile.c
        canohost.c channels.c cipher.c cipher-aes.c cipher-aesctr.c
        cleanup.c
        compat.c fatal.c hostfile.c
        log.c match.c moduli.c nchan.c packet.c
        readpass.c ttymodes.c xmalloc.c addr.c addrmatch.c
        atomicio.c dispatch.c mac.c misc.c utf8.c
        monitor_fdpass.c rijndael.c ssh-dss.c ssh-ecdsa.c ssh-ecdsa-sk.c
        ssh-ed25519-sk.c ssh-rsa.c dh.c
        msg.c progressmeter.c dns.c entropy.c gss-genr.c umac.c umac128.c
        ssh-pkcs11.c smult_curve25519_ref.c
        poly1305.c chacha.c cipher-chachapoly.c cipher-chachapoly-libcrypto.c
        ssh-ed25519.c digest-openssl.c digest-libc.c
        hmac.c ed25519.c hash.c
        kex.c kex-names.c kexdh.c kexgex.c kexecdh.c kexc25519.c
        kexgexc.c kexgexs.c
        kexsntrup761x25519.c sntrup761.c kexgen.c
        sftp-realpath.c platform-pledge.c platform-tracing.c platform-misc.c
        sshbuf-io.c
)

set(SK_SRCS ssh-sk-client.c)

set(SSH_SRCS
        ssh.c readconf.c clientloop.c sshtty.c
        sshconnect.c sshconnect2.c mux.c ${SK_SRCS}
)

set(SSHD_SRCS
        sshd.c platform-listen.c
        servconf.c sshpty.c srclimit.c groupaccess.c auth2-methods.c
        dns.c fatal.c compat.c utf8.c authfd.c canohost.c ${SK_SRCS}
)

set(SSHD_SESSION_SRCS
        sshd-session.c auth-rhosts.c auth-passwd.c
        audit.c audit-bsm.c audit-linux.c platform.c
        sshpty.c sshlogin.c servconf.c serverloop.c
        auth.c auth2.c auth2-methods.c auth-options.c session.c
        auth2-chall.c groupaccess.c
        auth-bsdauth.c auth2-hostbased.c auth2-kbdint.c
        auth2-none.c auth2-passwd.c auth2-pubkey.c auth2-pubkeyfile.c
        monitor.c monitor_wrap.c auth-krb5.c
        auth2-gss.c gss-serv.c gss-serv-krb5.c
        loginrec.c auth-pam.c auth-shadow.c auth-sia.c
        sftp-server.c sftp-common.c
        sandbox-null.c sandbox-rlimit.c sandbox-systrace.c sandbox-darwin.c
        sandbox-seccomp-filter.c sandbox-capsicum.c sandbox-pledge.c
        sandbox-solaris.c uidswap.c ${SK_SRCS}
)

set(SFTP_CLIENT_SRCS
        sftp-common.c sftp-client.c sftp-glob.c
)

set(SCP_SRCS
        scp.c progressmeter.c ${SFTP_CLIENT_SRCS}
)

set(SSHADD_SRCS
        ssh-add.c ${SK_SRCS}
)

set(SSHAGENT_SRCS
        ssh-agent.c ssh-pkcs11-client.c ${SK_SRCS}
)

set(SSHKEYGEN_SRCS
        ssh-keygen.c sshsig.c ${SK_SRCS}
)

set(SSHKEYSIGN_SRCS
        ssh-keysign.c readconf.c uidswap.c ${SK_SRCS}
)

set(P11HELPER_SRCS
        ssh-pkcs11-helper.c ssh-pkcs11.c ${SK_SRCS}
)

set(SKHELPER_SRCS
        ssh-sk-helper.c ssh-sk.c sk-usbhid.c
)

set(SSHKEYSCAN_SRCS
        ssh-keyscan.c ${SK_SRCS}
)

set(SFTPSERVER_SRCS
        sftp-common.c sftp-server.c sftp-server-main.c
)

set(SFTP_SRCS
        sftp.c sftp-usergroup.c progressmeter.c ${SFTP_CLIENT_SRCS}
)

# 实际目标
find_package(OpenSSL REQUIRED)
find_package(ZLIB REQUIRED)

add_library(libssh config.h ${LIBSSH_SRCS})
target_link_libraries(libssh PUBLIC openbsd-compat)
set_target_properties(libssh PROPERTIES PREFIX "")

add_executable(ssh config.h ${SSH_SRCS})
target_link_libraries(ssh PUBLIC libssh OpenSSL::Crypto ZLIB::ZLIB)

add_executable(sshd config.h ${SSHD_SRCS})
target_link_libraries(sshd PUBLIC libssh OpenSSL::Crypto ZLIB::ZLIB)

add_executable(sshd-session config.h ${SSHD_SESSION_SRCS})
target_link_libraries(sshd-session PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB crypt)

add_executable(scp config.h ${SCP_SRCS})
target_link_libraries(scp PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-add config.h ${SSHADD_SRCS})
target_link_libraries(ssh-add PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-agent config.h ${SSHAGENT_SRCS})
target_link_libraries(ssh-agent PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-keygen config.h ${SSHKEYGEN_SRCS})
target_link_libraries(ssh-keygen PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-keysign config.h ${SSHKEYSIGN_SRCS})
target_link_libraries(ssh-keysign PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-pkcs11-helper config.h ${P11HELPER_SRCS})
target_link_libraries(ssh-pkcs11-helper PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-sk-helper config.h ${SKHELPER_SRCS})
target_link_libraries(ssh-sk-helper PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(ssh-keyscan config.h ${SSHKEYSCAN_SRCS})
target_link_libraries(ssh-keyscan PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(sftp-server config.h ${SFTPSERVER_SRCS})
target_link_libraries(sftp-server PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)

add_executable(sftp config.h ${SFTP_SRCS})
target_link_libraries(sftp PUBLIC libssh openbsd-compat OpenSSL::Crypto ZLIB::ZLIB)