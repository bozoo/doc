NC
==

# Scan port with nc :
nc -z -v -n 192.168.1.1 80
nc -n -z -v 192.168.1.1 1-1024 2>&1 | grep succeeded

# File transfert with nc :
server : nc -l 1567 < file.txt
client : nc -n 172.31.100.7 1567 > file.txt
# or
server : nc -l 1567 > file.txt
client : nc 172.31.100.23 1567 < file.txt

# Directory transfert with nc :
server : tar -cvf – dir_name | nc -l 1567
client : nc -n 172.31.100.7 1567 | tar -xvf -
#or
server : tar -cvf – dir_name| bzip2 -z | nc -l 1567
client : nc -n 172.31.100.7 1567 | bzip2 -d |tar -xvf -

# Encrypted transfert with nc :
server : nc localhost 1567 | mcrypt –flush –bare -F -q -d -m ecb > file.txt
client : mcrypt –flush –bare -F -q -m ecb < file.txt | nc -l 1567
