TSM
===

Format Output of dsmadmc command: `dsmadmc [-tabd] [-commad] [-dataonly=yes]`

Query SQL TSM
-------------

Return status of volumes in library:

```sql
select volumes.volume_name,volumes.access,volumes.status
from volumes,libvolumes
where volumes.volume_name=libvolumes.volume_name
```

Return storage used by nodes:

```sql
select node_name,stgpool_name,sum(physical_mb) as SIZEinMB
from occupancy
where stgpool_name='$STGPOOL' group by node_name,stgpool_name
```

Return nodes without associated schedules:

```sql
SELECT node_name
FROM nodes
WHERE node_name NOT IN (SELECT node_name FROM associations)
```

Return total data stored and tapes used per node in one storage pool:

```sql
select vu.node_name, ao.total_mb, count(distinct vu.volume_name) as tapes, ao.total_mb/count(distinct vu.volume_name) as "AVG MB/tape"
from volumeusage vu, auditocc ao
where vu.stgpool_name='TAPEPOOL' and vu.node_name=ao.node_name group by vu.node_name, ao.total_mb
order by 4
```

Return volumes needed to restore a file:

```sql
select volume_name,node_name,filespace_name,file_name
from contents
where node_name='$SRV' and filespace_name='$FILE' and file_name='$PATH/*'
```

Configuration triggers
----------------------

Trigger to mirror TSM DB:

    define spacetrigger db fullpct=85 spaceexpansion=10 expansionprefix=/tsmdb/ mirrorprefixes="/tsmdbm/" maximumsize=100000

Trigger to mirror TSM logs:

    define spacetrigger log fullpct=75 spaceexpansion=10 expansionprefix=/tsmlog/ mirrorprefixes="/tsmlogm/" maximumsize=13000

Trigger to backup TSM DB when logs are full:

    define dbbackuptrigger log fullpct=75 devclass=file numincremental=20 mininterval=120 minlogfreepct=10
