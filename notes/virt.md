## [KVM cookbook](https://lwn.net/Articles/658511/)
* 一个最简单的“虚拟机”，汇编，结果输出到 I/O port
```asm
mov $0x3f8, %dx
add %bl, %al
add $'0', %al
out %al, (%dx)
mov $'\n', %al
out %al, (%dx)
hlt
```
* guest 与 hypervisor 之间通过`struct kvm_run`传递 CPU 状态，包括 guest vmexit 的原因等等
* 设置 guest CPU 寄存器状态，rflags 各状态的详解 https://blog.csdn.net/jn1158359135/article/details/7761011
```c
struct kvm_regs regs = {
  .rip = 0x1000, // 指令寄存器
  .rflags = 0x2  // 指令执行状态寄存器
};
ioctl(vcpufd, KVM_SET_REGS, &regs);
```
* 启动 guest CPU，`ioctl(vcpufd, KVM_RUN, NULL)`
> To run a multi-CPU VM, the user-space process must spawn multiple threads, and call KVM_RUN for different virtual CPUs in different threads.
* `kvm_run->exit_reason` 表示 guest CPU 中断执行的原因（halt、I/O）

## [use KVM to intercept guest VM](https://drive.google.com/file/d/1nFoCM62BWKSz2TKhNkrOjVwD8gP51VGK/view)
* KVM-VMI: https://github.com/KVM-VMI/kvm-vmi
* custom KVM and QEMU
