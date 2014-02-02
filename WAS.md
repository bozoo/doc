WAS
===

### Java System Dump generation on AIX

    # Set Sudo environnment to allow chdev commands via sudo :
    # add "Defaults !env_reset" to /etc/sudoers

    # Enable fullcore :
    lsattr -El sys0 | grep fullcore
    chdev -l sys0 -a fullcore=true

    # Generate coredump :
    gencore JVM_PID /path_of_binary_coredump

    # Extract coredump :
    ${WAS_HOME}/java/jre/bin/jextract /path_of_binary_coredump /path_of_the_output -v

[How to process an IBM SDK core dump with Jextract (AIX, Linux, Windows)](http://www-01.ibm.com/support/docview.wss?uid=swg21577379)
