Linux is based on file systems
The __boot loader loads both the _kernel_ and an initial RAM–based__ file system (initramfs) into memory, so it can be used directly by the kernel. After boot the kernel runs **/sbin/init**

__init__ is responsible for keeping the system running and for shutting it down cleanly. One of its responsibilities is to act when necessary as a manager for all non-kernel processes; it cleans up after them upon completion, and restarts user login services as needed when users log in and out, and does the same for other background system services.