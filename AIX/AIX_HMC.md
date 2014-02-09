AIX HMC
=======

List systems connected to HMC: `lssyscfg -r sys --header -F name,state`

Show config of a specific system: `lssyscfg -r sys -m Server-XXXX-XXX-SNXXXXXXX`

List processor resources for each LPAR: `lshwres -m Server-XXXX-XXX-SNXXXXXXX -r proc --level lpar --header -F lpar_name,curr_min_procs,curr_max_procs,curr_procs`

List memory resources for each LPAR: `lshwres -m Server-XXXX-XXX-SNXXXXXXX -r mem --level lpar`

List lpars from a system: `lssyscfg -m Server-XXXX-XXX-SNXXXXXXX -r lpar --header -F name,lpar_id,state`

Show lpar config: `lssyscfg -m Server-XXXX-XXX-SNXXXXXXX -r lpar --filter "lpar_names=LPARSRV01"`

Stop LPAR: `chsysstate -m Server-XXXX-XXX-SNXXXXXXX -r lpar -o shutdown --id 1`

Start LPAR: `chsysstate -m Server-XXXX-XXX-SNXXXXXXX -r lpar -o shutdown --id 1 --immed --restart`

List profiles of a LPAR: `lssyscfg -m Server-XXXX-XXX-SNXXXXXXX -r lpar --header -F name,lpar_id,state,default_profile,curr_profile --filter "lpar_names=LPARSRV01"`

Enable a LPAR profile: `chsysstate -m Server-XXXX-XXX-SNXXXXXXX -r lpar -o on -b norm --id 1 -f "LPARSRV01 default"`

Add processor to LPAR (live): `chhwres -m Server-XXXX-XXX-SNXXXXXXX -r procs -o a -p LPARSRV01 --procs 1`

Remove processor to LPAR (live): `chhwres -m Server-XXXX-XXX-SNXXXXXXX -r procs -o r -p LPARSRV01 --procs 1`

Add memory to LPAR (live): `chhwres -m Server-XXXX-XXX-SNXXXXXXX -r mem -o a -p LPARSRV01 -q 128`

Remove memory to LPAR (live): `chhwres -m Server-XXXX-XXX-SNXXXXXXX -r mem -o r -p LPARSRV01 -q 128`

List adapters on a system: `lshwres -m Server-XXXX-XXX-SNXXXXXXX -r io --rsubtype bus`

List FC adapters with WWN: `lshwres -m Server-XXXX-XXX-SNXXXXXXX -r io --rsubtype slotchildren --header -F phys_loc,lpar_id,lpar_name,description,wwpn`

Open terminal on LPAR: `mkvterm -m Server-XXXX-XXX-SNXXXXXXX -p LPARSRV01`
                   or `vtmenu`

Restart HMC: `hmcshutdown -t now -r`

List HMC users: `lshmcusr`
