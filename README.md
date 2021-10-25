# "I wish my Raspberry Pi had a 360K floppy drive"
## -- Something nobody said, ever. (except smbaker)
## Scott Baker http://www.smbaker.com/

## Links:

* Blog Post: [https://www.smbaker.com/raspberry-pi-floppy-controller-board](https://www.smbaker.com/raspberry-pi-floppy-controller-board)
* Youtube Video: [https://www.youtube.com/watch?v=I6E7oM7EDcc](https://www.youtube.com/watch?v=I6E7oM7EDcc)
* smb-pi-lib library, containing the floppy driver: [https://github.com/sbelectronics/smb-pi-lib](https://github.com/sbelectronics/smb-pi-lib)

## Performance tuning

See https://www.raspberrypi.org/forums/viewtopic.php?t=228727 for background.

Raspbian is not a real-time OS, and the FDC is absolutely intolerant of bytes not being serviced
during read or write. If a context switch occurs, then a buffer overrun WILL occur. There are a few
tricks that can mitigate this.

* The `--realtime` option will set the process to SCHED_FIFO with maximum priority. This should
keep other processes from interrupting it. It also writes `-1` to `/proc/sys/kernel/sched_rt_runtime_us`,
which will prevent Linux's mandatory interruption of the process for 50ms every second.

* The `--pincpu` option will pin the process to a particular CPU. In and of itself this isn't useful,
but it makes a difference when combined with the isolcpus kernel option.

For maximum performance:

* Add `isolcpus=3` to /boot/cmdline.txt
* Use the `--realtime` and ``--pincpu 3` options for fdtool.py

It's still not perfect, and writing a floppy driver as a user-mode python program was a pretty silly
thing to do, but you can get a failure rate down to a couple of percent, and the automatic retry will
take care of that.

## Media
       1.44Mb disc use
            144 or pc144 or 14.4 or 1440 or pc1440
        720Kb use
            720 or pc720
        360Kb use
          360 or pc360
        120Kb use
           120 or pc120
        111kb use
            111 or pc111
           
## Examples
Taken from smbaker youtube video
### Formatting a Floppy Drive
sudo python ./fdtool --realtime --pincpu3 --media 360 --disk format
### Writing to Floppy drive - Disk image
sudo 
