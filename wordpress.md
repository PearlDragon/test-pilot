# Benchmark tests

To try to check the impact of performance enhancements we need some baseline metrics based on architecture


![network diagram](https://github.com/PearlDragon/test-pilot/blob/master/network.png "Network Diagram")
 * Disk IO: Raw throughput vs GFS vs NFS
 * Tuning changes: client-side sync, async
 * Web hosting platform
    * Static File pull
    * php file pull
    * php with autoloader file pull (lots of stat calls)
    * wordpress home page pull
    
Possible tuning changes (note that we already know the stat calls are killing wordpress)
 * remove open_basedir
 * change NFS to async (if it isn't already)
 * add memcache/redis to wordpress
 
Notes:
 diagrams by draw.io
 
Results

Raw IO (xfs on EBS GP Magnetic)
```
time dd if=/dev/zero of=/bricks/brick1/gv0/test.img bs=1M count=10 oflag=direct
10+0 records in
10+0 records out
10485760 bytes (10 MB) copied, 0.0978131 s, 107 MB/s
```

Raw IOPS
```
raw.rpt:  read : io=117904KB, bw=21325KB/s, iops=1332, runt=  5529msec
raw.rpt:  write: io=13168KB, bw=2381.7KB/s, iops=148, runt=  5529msec
gfs.rpt:  read : io=117904KB, bw=12542KB/s, iops=783, runt=  9401msec
gfs.rpt:  write: io=13168KB, bw=1400.8KB/s, iops=87, runt=  9401msec
nfs-l.rpt:  read : io=117904KB, bw=55773KB/s, iops=3485, runt=  2114msec
nfs-l.rpt:  write: io=13168KB, bw=6228.1KB/s, iops=389, runt=  2114msec
nfs-r.rpt:  read : io=117904KB, bw=8733.7KB/s, iops=545, runt= 13500msec
nfs-r.rpt:  write: io=13168KB, bw=998817B/s, iops=60, runt= 13500msec
```
| Layer      | Read BW | R-IOPs | Write BW | W-IOPS |
|------------|---------|--------|----------|--------|
|XFS         |21.32MB/s|1332    |2.3MB/s   |148     |
|GFS         |12.54MB/s|783     |1.40MB/s  |87      |
|NFS - Local |55.77MB/s|3485    |6.23MB/s  |389     |
|NFS - Remote|8.73MB/s|545      |1.00MB/s  |60      |

NFS Over GFS
```
time (find /srv/hosting3/sentora/| wc -l)
151697

real    7m47.114s
user    0m0.518s
sys     0m3.043s
```
