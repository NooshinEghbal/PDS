# PDS
Parallel Data Streams Tool

PDS is a tool on top of which other client-server programs like rsync can run.
It transfers data between client and server through parallel TCP streams like
the mechanism used by GridFTP for transferring files.

# Compile
make clean
make


Then put the folder containing PDS_client.out, PDS_server.out and transceiver.out in the PATH on both end hosts.
NOTE: Update rsync to the latest version (3.1.1 or above) to get a beffer performance.

# rsync over PDS

rsync -v --progress --rsh="PDS_client.out (number of TCP streams) (block size) (connection type) (path to the source file) (IP addr of the server):(path to the destination file)

Example for parallel TCP connections:  
rsync -v --progress --rsh="PDS_client.out 5 10000 TCP" /dev/shm/eghbal/test.blob 192.168.154.21:/dev/shm/eghbal/test.blob

Example for parallel SSH connections:  
rsync -v --progress --rsh="PDS_client.out 5 4000 SSH" /dev/shm/eghbal/test.blob 192.168.154.21:/dev/shm/eghbal/test.blob


NOTE: 
- The block size argument should be <=10000 for parallel TCP connections and <=4000 for parallel SSH connections. 
- If you want to run PDS with more than 9 parallel SSH connections you should modify sshd_config file to increase the maximum parallel SSH connections from the default value which is 10.

# NFS over PDS

At NFS server node:

Edit /etc/exports to share a folder. For example if you want to share /dev/shm/eghbal, you should add the following line:

/dev/shm/eghbal *(insecure,fsid=0,rw,sync,no_subtree_check)

then run "sudo exportfs -a".

At NFS client node:

Run PDS_client to create a channel between NFS client and server nodes:

PDS_client.out <number of TCP streams> <block size> <connection type> <IP addr of the server> PF <local port> <remote port>

For example:  
PDS_client.out 5 10000 TCP 192.168.154.21 PF 3049 2049

Then mount a folder (e.g. /mnt/nfs-share):

mount -vt nfs4 -o port=3049 localhost:/ /mnt/nfs-share

NOTE: 
- Like running NFS over SSH, after finishing the task, you can Ctrl+C PDS client to exit the channel 
and also you should kill the PDS server process running on the other side.
- Sometimes the local port is still busy after exiting PDS client. You can wait for a while or try another port number.
