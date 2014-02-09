AIX WPAR
========

Admin Menu for WPAR Management: `smit wpar`

Create Application WPAR: `wparexec $application_file`

Create Sytem WPAR: `mkwpar -n $wpar_name -N address=192.168.2.1`

List WPAR: `lswpar`

Start WPAR: `startwpar $wpar_name`

Stop WPAR: `stopwpar $wpar_name`

Reboot WPAR: `rebootwpar $wpar_name`

Change WPAR settings: `chwpar`

Connect to WPAR: `clogin $wpar_name`

Logs WPAR:

    /var/adm/ras/
    /var/adm/wpars/
