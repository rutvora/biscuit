1) Does not work with latest go versions as they require a go module setup.
   The source also uses relative paths, which go dislikes (and has disabled in the modules setup)

2) Needs (outdated) python2 which needs to be explicitly installed in Ubuntu 22

3) `biscuit/user/c/usertests.c` Line 3652 has an uninitialized character buffer which needed to be initialised

4) `biscuit/GNUmakefile` uses `ide-drive` which is deprecated and replaced by `ide-hd`

5) With the above changes, qemu boots and the kernel panics

6) `biscuit/src/kernel/syscall.go` seems broken!
   Line #3314 `hdata := make([]uint8, 512)` initialises a random array of 512 bytes
   Line #3317 reads the `bin/init` file in this buffer
   Line #3328 checks if this buffer is an elf file
   Line #4418 (inside the function called from #3328) fails because the dlen (512 bytes) is less than phend (568 bytes)!
   Looks like their buffer is too small? I checked with setting `1024` in Line #3314. The kernel seems to work now!
