# Project: TowelRoot and Injection code into linux kernel

**This README gives some explications about the exploit 'src/towelroot_inject_code.c',
more details about the project please read file 'repport.pdf'.**


### Exploit

The goal of the exploit which leverages the futex vulnerability is to inject code into kernel where 'loadable kernel modules(or LKM)' is not avaliable. The exploit is modified from [here](https://github.com/lieanu/CVE2014-3153), optimized (avoid the brute force) and developped to realize the goal.

##### The exploit requires environment as below:

* Arch x86
* Linux kernel version < 3.14 (we tested on 3.13.0)
* Non-PAE pached for kernel

##### It does following tasks:

* Triggering the futex vulnerability (*[CVE-2014-3153](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-3153)*) to get a thread which has `0xffffffff`
as the vaule of its `addr_limit`
* Searching et collecting some useful information about all threads in current processus
* Doing Escalation privilege to get root permission
* Injecting code into kernel space and hacking syscall `sys_getpgid`
* Fixing the corrupt waiter list using collected threads' information (not working)

##### Please note that:

* Thread executable kernel stack is required, beacuse the first payload will be executed on
one of threads kernel stack
* The addresses of `sys_call_table`, kernel functions `__kmalloc()` and `vprintk_emit()` should be changed when you test on your machine because these addresses are diverse from different kernel version. (You could find them in file `System.map` for example)
* Fixing waiter list is not working and needs more work on it. (It is important because the termination of processus will cause **kernel panic** without a correct waiter list)

---

### To Do

* To make exploit work on a PAE pacthed kernel (ex: use ROP to call `set_pages_nx`)
* To fix the corrupt waiter list
* ...