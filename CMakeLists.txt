cmake_minimum_required(VERSION 3.13)

project(fake-openssh-portable)

include_directories(${CMAKE_CURRENT_LIST_DIR})

add_library(XMSS OBJECT
        ssh-xmss.c
        sshkey-xmss.c
        xmss_commons.c
	    xmss_fast.c
	    xmss_hash.c
	    xmss_hash_address.c
	    xmss_wots.c
)

add_library(LIBOPENSSH OBJECT
        ssh_api.c
        ssherr.c
        sshbuf.c
        sshkey.c
        sshbuf-getput-basic.c
        sshbuf-misc.c
        sshbuf-getput-crypto.c
        krl.c
        bitmap.c
)
target_link_libraries(LIBOPENSSH PUBLIC XMSS)

add_library(LIBSSH OBJECT
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

add_library(SK OBJECT
    ssh-sk-client.c
)

add_library(SSHD OBJECT
        platform-listen.c 
        servconf.c sshpty.c srclimit.c groupaccess.c auth2-methods.c 
        dns.c fatal.c compat.c utf8.c authfd.c canohost.c 
)
target_link_libraries(SSHD PUBLIC SK)

add_library(SSHD_SESSION OBJECT
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
        sandbox-solaris.c uidswap.c
)
target_link_libraries(SSHD_SESSION PUBLIC SK)

add_library(SFTP_CLIENT OBJECT
        sftp-common.c sftp-client.c sftp-glob.c
)

add_library(SCP OBJECT
        scp.c progressmeter.c
)
target_link_libraries(SCP PUBLIC SFTP_CLIENT)

add_library(SSHADD OBJECT
        ssh-add.c
)
target_link_libraries(SSHADD PUBLIC SK)

add_library(SSHAGENT OBJECT
        ssh-agent.c ssh-pkcs11-client.c
)
target_link_libraries(SSHAGENT PUBLIC SK)

add_library(SSHKEYGEN OBJECT
        ssh-keygen.c sshsig.c
)
target_link_libraries(SSHKEYGEN PUBLIC SK)

add_library(SSHKEYSIGN OBJECT
        ssh-keysign.c readconf.c uidswap.c
)
target_link_libraries(SSHKEYSIGN PUBLIC SK)

add_library(P11HELPER OBJECT
        ssh-pkcs11-helper.c ssh-pkcs11.c
)
target_link_libraries(P11HELPER PUBLIC SK)

add_library(SKHELPER OBJECT
        ssh-sk-helper.c ssh-sk.c sk-usbhid.c
)

add_library(SSHKEYSCAN OBJECT
        ssh-keyscan.c
)
target_link_libraries(SSHKEYSCAN PUBLIC SK)

add_library(SFTPSERVER OBJECT
        sftp-common.c sftp-server.c sftp-server-main.c
)

add_library(SFTP OBJECT
        sftp.c sftp-usergroup.c progressmeter.c
)
target_link_libraries(SFTP PUBLIC SFTP_CLIENT)