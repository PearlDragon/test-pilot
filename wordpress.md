# Benchmark tests

To try to check the impact of performance enhancements we need some baseline metrics based on architecture


![network diagram](https://github.com/PearlDragon/test-pilot/blob/master/network.svg "Network Diagram")
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
 
