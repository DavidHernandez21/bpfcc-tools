# bpfcc-tools

**WARNING** [Coding performance tools in BCC Python is now considered deprecated](https://www.brendangregg.com/blog/2020-11-04/bpf-co-re-btf-libbpf.html) in favor of libbpf C with BTF and CO-RE ( BPF Compile-Once Run-Everywhere)

Inspired by the awesome work by [Brendan Gregg](https://brendangregg.com/overview.html) and specially his pioneering work on [BPF](https://www.brendangregg.com/ebpf.html), I have created this repository to keep track of the tools that I have found useful while working with BPF.

After reading [System performance enterprise and the cloud](https://www.brendangregg.com/systems-performance-2nd-edition-book.html) which I highly recommend, I have started to use BPF more and more to troubleshoot performance issues in the systems that I work with.

Since many of the tools were written targetting a previous kernel version (<= 5.x) and the probes names have changed, I have created this repository to keep track of the tools that I have found useful and that work with the latest kernel versions 6.x.

## How to find the probes names for the current kernel version

To find the probes names for the current kernel version, you can use the following command:

```bash
sudo cat /sys/kernel/debug/tracing/available_filter_functions | grep -i <function_name>
```

## Headers files has changed

The headers files have changed and the tools that use them need to be updated as an example I have faced the following error when trying to run the `biolatency` tool:

```bash
/virtual/main.c:48:15: error: no member named 'state' in 'struct task_struct'; did you mean 'stats'?
    if (prev->state == TASK_RUNNING) {
              ^~~~~
              stats
include/linux/sched.h:826:34: note: 'stats' declared here
        struct sched_statistics         stats;
                                        ^
/virtual/main.c:48:21: error: invalid operands to binary expression ('struct sched_statistics' and 'int')
    if (prev->state == TASK_RUNNING) {
        ~~~~~~~~~~~ ^  ~~~~~~~~~~~~
3 warnings and 2 errors generated.
Traceback (most recent call last):
  File "/usr/sbin/cpudist-bpfcc", line 161, in <module>
    b = BPF(text=bpf_text, cflags=["-DMAX_PID=%d" % max_pid])
  File "/usr/lib/python3/dist-packages/bcc/__init__.py", line 364, in __init__
    raise Exception("Failed to compile BPF module %s" % (src_file or "<text>"))
Exception: Failed to compile BPF module <text>
```

This was solved by looking into `sched.h` file e.g. `code /usr/src/linux-hwe-6.8-headers-6.8.0-40/include/linux/sched.h`
inspecting the file I found that the `'task_struct' has a field '__state'` instead of `'state'` and I have updated the code accordingly.

## How to use the tools

I would recommend to install the tools using the package manager of your distribution, e.g. `sudo apt-get install bpfcc-tools linux-headers-$(uname -r) bpftrace` and then modify the tools as needed.

## Other tools

- [Ply](https://github.com/iovisor/ply)
- [0xtools](https://github.com/tanelpoder/0xtools)