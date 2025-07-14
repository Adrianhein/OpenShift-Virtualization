
### VMI running on OpenShift

![Photo-1](https://github.com/Adrianhein/OpenShift-Virtualization/blob/main/image/UI-1.png)
#
![Photo-2](https://github.com/Adrianhein/OpenShift-Virtualization/blob/main/image/UI-2.png)
#
#### Go to "Console Tab" , Click on "Get login credentials" VM's credentials.
![Photo-2](https://github.com/Adrianhein/OpenShift-Virtualization/blob/main/image/Check-default-login-credentials.png)
#
![Photo-2](https://github.com/Adrianhein/OpenShift-Virtualization/blob/main/image/Stopping-state-1.png)
#
![Photo-2](https://github.com/Adrianhein/OpenShift-Virtualization/blob/main/image/Stopping-state-2.png)
#

### Access log history to the VM within the specific project on OpenShift Platform



        [sysadm@openshift-local-01 ~]$ oc get vm -A
        NAMESPACE    NAME                  AGE   STATUS    READY
        a-test-vms   centos-stream9-test   8d    Stopped   False
# 
        [sysadm@openshift-local-01 ~]$ oc describe vm 
        Name:         centos-stream9-test
        Namespace:    a-test-vms
        Labels:       app=centos-stream9-test
                      kubevirt.io/dynamic-credentials-support=true
                      vm.kubevirt.io/template=centos-stream9-server-small
                      vm.kubevirt.io/template.namespace=openshift
                      vm.kubevirt.io/template.revision=1
                      vm.kubevirt.io/template.version=v0.26.0
        Annotations:  kubemacpool.io/transaction-timestamp: 2025-07-05T07:48:19.758214365Z
                      kubevirt.io/latest-observed-api-version: v1
                      kubevirt.io/storage-observed-api-version: v1
                      vm.kubevirt.io/validations:
                        [
                          {
                            "name": "minimal-required-memory",
                            "path": "jsonpath::.spec.domain.memory.guest",
                            "rule": "integer",
                            "message": "This VM requires more memory.",
                            "min": 1610612736
                          }
                        ]
        API Version:  kubevirt.io/v1
        Kind:         VirtualMachine
        Metadata:
          Creation Timestamp:  2025-07-05T06:22:12Z
          Finalizers:
            kubevirt.io/virtualMachineControllerFinalize
          Generation:        2
          Resource Version:  364431
          UID:               363718f7-afab-467f-a8c3-a78878253f9d
        Spec:
          Data Volume Templates:
            API Version:  cdi.kubevirt.io/v1beta1
            Kind:         DataVolume
            Metadata:
              Creation Timestamp:  <nil>
              Name:                centos-stream9-test
            Spec:
              Source:
                Pvc:
                  Name:       centos-stream9-5f1def28ce34
                  Namespace:  openshift-virtualization-os-images
              Storage:
                Resources:
                  Requests:
                    Storage:  30Gi
          Running:            false
          Template:
            Metadata:
              Annotations:
                vm.kubevirt.io/flavor:    small
                vm.kubevirt.io/os:        centos-stream9
                vm.kubevirt.io/workload:  server
              Creation Timestamp:         <nil>
              Labels:
                kubevirt.io/domain:  centos-stream9-test
                kubevirt.io/size:    small
            Spec:
              Architecture:  amd64
              Domain:
                Cpu:
                  Cores:    1
                  Sockets:  1
                  Threads:  1
                Devices:
                  Disks:
                    Disk:
                      Bus:  virtio
                    Name:   rootdisk
                    Disk:
                      Bus:  virtio
                    Name:   cloudinitdisk
                  Interfaces:
                    Mac Address:  02:20:f7:00:00:0c
                    Masquerade:
                    Model:                       virtio
                    Name:                        default
                  Network Interface Multiqueue:  true
                  Rng:
                Machine:
                  Type:  pc-q35-rhel9.2.0
                Memory:
                  Guest:  2Gi
                Resources:
              Networks:
                Name:  default
                Pod:
              Termination Grace Period Seconds:  180
              Volumes:
                Data Volume:
                  Name:  centos-stream9-test
                Name:    rootdisk
                Cloud Init Config Drive:
                  User Data:  #cloud-config
        user: centos
        password: bdli-1wyc-vthu
        chpasswd: { expire: False }
                Name:  cloudinitdisk
        Status:
          Conditions:
            Last Probe Time:       2025-07-05T07:48:56Z
            Last Transition Time:  2025-07-05T07:48:56Z
            Message:               VMI does not exist
            Reason:                VMINotExists
            Status:                False
            Type:                  Ready
            Last Probe Time:       <nil>
            Last Transition Time:  <nil>
            Message:               cannot migrate VMI: PVC centos-stream9-test is not shared, live migration requires that all PVCs must be shared (using ReadWriteMany access mode)
            Reason:                DisksNotLiveMigratable
            Status:                False
            Type:                  LiveMigratable
          Desired Generation:      2
          Observed Generation:     2
          Printable Status:        Stopped
          Volume Snapshot Statuses:
            Enabled:  false
            Name:     rootdisk
            Reason:   No VolumeSnapshotClass: Volume snapshots are not configured for this StorageClass [crc-csi-hostpath-provisioner] [rootdisk]
            Enabled:  false
            Name:     cloudinitdisk
            Reason:   Snapshot is not supported for this volumeSource type [cloudinitdisk]
        Events:       <none>
#        
        [sysadm@openshift-local-01 ~]$ virtctl start centos-stream9-test 
        VM centos-stream9-test was scheduled to start
#        
        [sysadm@openshift-local-01 ~]$ oc get pods
        NAME                                      READY   STATUS    RESTARTS   AGE
        virt-launcher-centos-stream9-test-mkclx   1/1     Running   0          4m18s
#        
        [sysadm@openshift-local-01 ~]$ oc exec -it virt-launcher-centos-stream9-test-mkclx -- bash
        bash-5.1$ 
        bash-5.1$ virsh list --all
        Authorization not available. Check if polkit service is running or see debug message for more information.
         Id   Name                             State
        ------------------------------------------------
         1    a-test-vms_centos-stream9-test   running
        
        bash-5.1$ virsh domifaddr a-test-vms_centos-stream9-test
        Authorization not available. Check if polkit service is running or see debug message for more information.
        error: Failed to query for interfaces addresses
        error: Failed to connect socket to '/var/run/libvirt/virtnetworkd-sock': No such file or directory
        
        bash-5.1$ exit
        exit
        command terminated with exit code 1
        [sysadm@openshift-local-01 ~]$ 
      
#        
        [sysadm@openshift-local-01 ~]$ 
        [sysadm@openshift-local-01 ~]$ virtctl console centos-stream9-test 
        Successfully connected to centos-stream9-test console. The escape sequence is ^]
        [  OK  ] Finished Setup Virtual Console.
                 Starting dracut cmdline hook...
        [  OK  ] Finished dracut cmdline hook.
                 Starting dracut pre-udev hook...
        [  161.097641] device-mapper: core: CONFIG_IMA_DISABLE_HTABLE is disabled. Duplicate IMA measurements will not be recorded in the IMA log.
        [  161.256542] device-mapper: uevent: version 1.0.3
        [  161.634707] device-mapper: ioctl: 4.49.0-ioctl (2025-01-17) initialised: dm-devel@lists.linux.dev
        [  163.053698] RPC: Registered named UNIX socket transport module.
        [  163.053941] RPC: Registered udp transport module.
        [  163.054154] RPC: Registered tcp transport module.
        [  163.054154] RPC: Registered tcp-with-tls transport module.
        [  163.054154] RPC: Registered tcp NFSv4.1 backchannel transport module.
        [  OK  ] Finished dracut pre-udev hook.
                 Starting Rule-based Manager for Device Events and Files...
        [  OK  ] Started Rule-based Manager for Device Events and Files.
                 Starting dracut pre-trigger hook...
        [  OK  ] Finished dracut pre-trigger hook.
                 Starting Coldplug All udev Devices...
        [  OK  ] Finished Coldplug All udev Devices.
        [  OK  ] Reached target System Initialization.
        [  OK  ] Reached target Basic System.
        [  OK  ] Reached target Network.
                 Starting dracut initqueue hook...
        [  168.472435] virtio_blk virtio3: 1/0/0 default/read/poll queues
        [  168.704705] virtio_blk virtio3: [vda] 62914560 512-byte logical blocks (32.2 GB/30.0 GiB)
        [  169.043604] scsi host0: Virtio SCSI HBA
        [  169.341565]  vda: vda1
        [  169.566368] virtio_blk virtio4: 1/0/0 default/read/poll queues
        [  169.710408] virtio_blk virtio4: [vdb] 2048 512-byte logical blocks (1.05 MB/1.00 MiB)
        [  175.073435] clocksource: Long readout interval, skipping watchdog check: cs_nsec: 3416726025 wd_nsec: 3416726182
        [  187.612583] clocksource: Long readout interval, skipping watchdog check: cs_nsec: 7037049409 wd_nsec: 7037049178
        [  201.281556] ACPI: \_SB_.GSIA: Enabled at IRQ 16
        [  201.526531] ahci 0000:00:1f.2: AHCI vers 0001.0000, 32 command slots, 1.5 Gbps, SATA mode
        [  201.527567] ahci 0000:00:1f.2: 6/6 ports implemented (port mask 0x3f)
        [  201.528590] ahci 0000:00:1f.2: flags: 64bit ncq only 
        [  202.034434] scsi host1: ahci
        [  202.110324] scsi host2: ahci
        [  202.182553] scsi host3: ahci
        [  202.259323] scsi host4: ahci
        [  202.369182] scsi host5: ahci
        [  202.494557] scsi host6: ahci
        [  202.524411] ata1: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c100 irq 50 lpm-pol 0
        [  202.525417] ata2: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c180 irq 50 lpm-pol 0
        [  202.526384] ata3: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c200 irq 50 lpm-pol 0
        [  202.527554] ata4: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c280 irq 50 lpm-pol 0
        [  202.530439] ata5: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c300 irq 50 lpm-pol 0
        [  202.531354] ata6: SATA max UDMA/133 abar m4096@0xfea1c000 port 0xfea1c380 irq 50 lpm-pol 0
        [  203.067464] ata3: SATA link down (SStatus 0 SControl 300)
        [  203.085511] ata2: SATA link down (SStatus 0 SControl 300)
        [  203.101301] ata1: SATA link down (SStatus 0 SControl 300)
        [  203.530672] ata6: SATA link down (SStatus 0 SControl 300)
        [  203.570698] ata5: SATA link down (SStatus 0 SControl 300)
        [  203.615700] ata4: SATA link down (SStatus 0 SControl 300)
        [  OK  ] Found device /dev/disk/by-uuid/5dab258e-36fa-4847-8546-efaae4d31553.
        [  OK  ] Reached target Initrd Root Device.
        [  OK  ] Finished dracut initqueue hook.
        [  OK  ] Reached target Preparation for Remote File Systems.
        [  OK  ] Reached target Remote Encrypted Volumes.
        [  OK  ] Reached target Remote File Systems.
                 Starting dracut pre-mount hook...
        [  OK  ] Finished dracut pre-mount hook.
                 Starting File System Check on /dev…258e-36fa-4847-8546-efaae4d31553...
        [  OK  ] Finished File System Check on /dev…ab258e-36fa-4847-8546-efaae4d31553.
                 Mounting /sysroot...
        [   ***] A start job is running for /sysroot (2min 17s / no limit)
        [  237.179334] SGI XFS with ACLs, security attributes, scrub, quota, no debug enabled
        [     *] A start job is running for /sysroot (2min 18s / no limit)
        [  OK  ] Mounted /sysroot.
        [  OK  ] Reached target Initrd Root File System.
                 Starting Mountpoints Configured in the Real Root...
        [  OK  ] Finished Mountpoints Configured in the Real Root.
        [  OK  ] Reached target Initrd File Systems.
        [  OK  ] Reached target Initrd Default Target.
                 Starting dracut mount hook...
        [  OK  ] Finished dracut mount hook.
                 Starting dracut pre-pivot and cleanup hook...
        [  OK  ] Finished dracut pre-pivot and cleanup hook.
                 Starting Cleaning Up and Shutting Down Daemons...
        [  OK  ] Finished Cleaning Up and Shutting Down Daemons.
        [  OK  ] Stopped target Network.
        [  OK  ] Stopped target Remote Encrypted Volumes.
        [  OK  ] Stopped target Timer Units.
        [  OK  ] Closed D-Bus System Message Bus Socket.
        [  OK  ] Stopped dracut pre-pivot and cleanup hook.
        [  OK  ] Stopped target Initrd Default Target.
        [  OK  ] Stopped target Basic System.
        [  OK  ] Stopped target Initrd Root Device.
        [  OK  ] Stopped target Initrd /usr File System.
        [  OK  ] Stopped target Path Units.
        [  OK  ] Stopped target Remote File Systems.
        [  OK  ] Stopped target Preparation for Remote File Systems.
        [  OK  ] Stopped target Slice Units.
        [  OK  ] Stopped target Socket Units.
        [  OK  ] Stopped target System Initialization.
        [  OK  ] Stopped target Local File Systems.
        [  OK  ] Stopped target Swaps.
        [  OK  ] Stopped dracut mount hook.
        [  OK  ] Stopped dracut pre-mount hook.
        [  OK  ] Stopped target Local Encrypted Volumes.
        [  OK  ] Stopped Dispatch Password Requests to Console Directory Watch.
        [  OK  ] Stopped dracut initqueue hook.
        [  OK  ] Stopped Apply Kernel Variables.
        [  OK  ] Stopped Create Volatile Files and Directories.
        [  OK  ] Stopped Coldplug All udev Devices.
        [  OK  ] Stopped dracut pre-trigger hook.
                 Stopping Rule-based Manager for Device Events and Files...
        [  OK  ] Stopped Setup Virtual Console.
        [  OK  ] Stopped Rule-based Manager for Device Events and Files.
        [  OK  ] Closed udev Control Socket.
        [  OK  ] Closed udev Kernel Socket.
        [  OK  ] Stopped dracut pre-udev hook.
        [  OK  ] Stopped dracut cmdline hook.
                 Starting Cleanup udev Database...
        [  OK  ] Stopped Create Static Device Nodes in /dev.
        [  OK  ] Stopped Create List of Static Device Nodes.
        [  OK  ] Stopped Create System Users.
        [  OK  ] Finished Cleanup udev Database.
        [  OK  ] Reached target Switch Root.
                 Starting Switch Root...
        [  250.491578] systemd-journald[239]: Received SIGTERM from PID 1 (systemd).
        [  254.117772] audit: type=1404 audit(1752456886.524:2): enforcing=1 old_enforcing=0 auid=4294967295 ses=4294967295 enabled=1 old-enabled=1 lsm=selinux res=1
        [  254.733709] SELinux:  policy capability network_peer_controls=1
        [  254.733947] SELinux:  policy capability open_perms=1
        [  254.733958] SELinux:  policy capability extended_socket_class=1
        [  254.733958] SELinux:  policy capability always_check_network=0
        [  254.733958] SELinux:  policy capability cgroup_seclabel=1
        [  254.733958] SELinux:  policy capability nnp_nosuid_transition=1
        [  254.733958] SELinux:  policy capability genfs_seclabel_symlinks=1
        [  255.887703] audit: type=1403 audit(1752456888.294:3): auid=4294967295 ses=4294967295 lsm=selinux res=1
        [  256.049360] systemd[1]: Successfully loaded SELinux policy in 1.934002s.
        [  257.116773] systemd[1]: Relabelled /dev, /dev/shm, /run, /sys/fs/cgroup in 695.074ms.
        [  257.391098] systemd[1]: systemd 252-53.el9 running in system mode (+PAM +AUDIT +SELINUX -APPARMOR +IMA +SMACK +SECCOMP +GCRYPT +GNUTLS +OPENSSL +ACL +BLKID +CURL +ELFUTILS      +FIDO2 +IDN2 -IDN -IPTC +KMOD +LIBCRYPTSETUP +LIBFDISK +PCRE2 -PWQUALITY +P11KIT -QRENCODE +TPM2 +BZIP2 +LZ4 +XZ +ZLIB +ZSTD -BPF_FRAMEWORK +XKBCOMMON +UTMP +SYSVINIT      default-hierarchy=unified)
        [  257.786695] systemd[1]: Detected virtualization kvm.
        [  257.792719] systemd[1]: Detected architecture x86-64.
        
        Welcome to CentOS Stream 9!
        
        [  258.000660] systemd[1]: Hostname set to <centos-stream9-test>.
        [  268.871872] systemd-rc-local-generator[536]: /etc/rc.d/rc.local is not marked executable, skipping.
        [  277.765534] systemd[1]: initrd-switch-root.service: Deactivated successfully.
        [  277.886574] systemd[1]: Stopped Switch Root.
        [  OK  ] Stopped Switch Root.
        [  278.016728] systemd[1]: systemd-journald.service: Scheduled restart job, restart counter is at 1.
        [  278.097580] systemd[1]: Created slice Slice /system/getty.
        [  OK  ] Created slice Slice /system/getty.
        [  278.206545] systemd[1]: Created slice Slice /system/modprobe.
        [  OK  ] Created slice Slice /system/modprobe.
        [  278.328711] systemd[1]: Created slice Slice /system/serial-getty.
        [  OK  ] Created slice Slice /system/serial-getty.
        [  278.461422] systemd[1]: Created slice Slice /system/sshd-keygen.
        [  OK  ] Created slice Slice /system/sshd-keygen.
        [  278.589700] systemd[1]: Created slice User and Session Slice.
        [  OK  ] Created slice User and Session Slice.
        [  278.731716] systemd[1]: Started Dispatch Password Requests to Console Directory Watch.
        [  OK  ] Started Dispatch Password Requests to Console Directory Watch.
        [  278.958608] systemd[1]: Started Forward Password Requests to Wall Directory Watch.
        [  OK  ] Started Forward Password Requests to Wall Directory Watch.
        [  279.171784] systemd[1]: Set up automount Arbitrary Executable File Formats File System Automount Point.
        [  OK  ] Set up automount Arbitrary Executa…ormats File System Automount Point.
        [  279.452769] systemd[1]: Reached target Local Encrypted Volumes.
        [  OK  ] Reached target Local Encrypted Volumes.
        [  279.615547] systemd[1]: Stopped target Switch Root.
        [  OK  ] Stopped target Switch Root.
        [  279.740231] systemd[1]: Stopped target Initrd File Systems.
        [  OK  ] Stopped target Initrd File Systems.
        [  279.879609] systemd[1]: Stopped target Initrd Root File System.
        [  OK  ] Stopped target Initrd Root File System.
        [  280.001579] systemd[1]: Reached target Local Integrity Protected Volumes.
        [  OK  ] Reached target Local Integrity Protected Volumes.
        [  280.139602] systemd[1]: Reached target Path Units.
        [  OK  ] Reached target Path Units.
        [  280.252549] systemd[1]: Reached target rpc_pipefs.target.
        [  OK  ] Reached target rpc_pipefs.target.
        [  280.369738] systemd[1]: Reached target Slice Units.
        [  OK  ] Reached target Slice Units.
        [  280.460681] systemd[1]: Reached target Swaps.
        [  OK  ] Reached target Swaps.
        [  280.670788] systemd[1]: Reached target Local Verity Protected Volumes.
        [  OK  ] Reached target Local Verity Protected Volumes.
        [  281.042707] systemd[1]: Listening on RPCbind Server Activation Socket.
        [  OK  ] Listening on RPCbind Server Activation Socket.
        [  281.280633] systemd[1]: Reached target RPC Port Mapper.
        [  OK  ] Reached target RPC Port Mapper.
        [  281.449555] systemd[1]: Listening on Process Core Dump Socket.
        [  OK  ] Listening on Process Core Dump Socket.
        [  281.616780] systemd[1]: Listening on initctl Compatibility Named Pipe.
        [  OK  ] Listening on initctl Compatibility Named Pipe.
        [  OK  ] Listening on udev Control Socket.
        [  OK  ] Listening on udev Kernel Socket.
        [  281.883475] systemd[1]: Listening on udev Control Socket.
                 Mounting Huge Pages File System...
                 Mounting POSIX Message Queue File System...
        [  281.942599] systemd[1]: Listening on udev Kernel Socket.
                 Mounting Kernel Debug File System...
                 Mounting Kernel Trace File System...
                 Starting Create List of Static Device Nodes...
                 Starting Load Kernel Module configfs...[  282.113610] systemd[1]: Mounting Huge Pages File System...
        [  282.281630] systemd[1]: Mounting POSIX Message Queue File System...
        [  282.639629] systemd[1]: Mounting Kernel Debug File System...
        [  282.814682] systemd[1]: Mounting Kernel Trace File System...
        [  282.907707] systemd[1]: Kernel Module supporting RPCSEC_GSS was skipped because of an unmet condition check (ConditionPathExists=/etc/krb5.keytab).
        [  283.045729] systemd[1]: Starting Create List of Static Device Nodes...
        [  283.205725] systemd[1]: Starting Load Kernel Module configfs...
        
        [  284.520398] systemd[1]: Starting Load Kernel Module drm...
                 Starting Load Kernel Module drm...
        [  284.895650] systemd[1]: Starting Load Kernel Module efi_pstore...
                 Starting Load Kernel Module efi_pstore...
        [  285.275589] systemd[1]: Starting Load Kernel Module fuse...
                 Starting Load Kernel Module fuse...
        [  285.493527] systemd[1]: Starting Read and set NIS domainname from /etc/sysconfig/network...
                 Starting Read and set NIS domainname from /etc/sysconfig/network...
        [  285.703684] systemd[1]: systemd-fsck-root.service: Deactivated successfully.
        [  285.797555] systemd[1]: Stopped File System Check on Root Device.
        [  OK  ] Stopped File System Check on Root Device.
        [  285.939523] systemd[1]: Stopped Journal Service.
        [  OK  ] Stopped Journal Service.
        [  286.172601] fuse: init (API version 7.37)
                 Starting Journal Service...
                 Starting Generate network units from Kernel command line...[  286.224746] systemd[1]: Starting Journal Service...
        [  286.274606] systemd[1]: Load Kernel Modules was skipped because no trigger condition checks were met.
        [  286.280633] systemd[1]: Starting Generate network units from Kernel command line...
        
        [  287.325458] systemd[1]: TPM2 PCR Machine ID Measurement was skipped because of an unmet condition check (ConditionPathExists=/sys/firmware/efi/efivars/      StubPcrKernelImage-4a67b082-0a4c-41cf-b6c7-440b29bb8c4f).
                 Starting Remount Root and Kernel File Systems...
        [  287.751541] systemd[1]: Starting Remount Root and Kernel File Systems...
                 Starting Apply Kernel Variables...
                 Starting Coldplug All udev Devices...
        [  OK  ] Mounted Huge Pages File System.[  287.930584] systemd[1]: Repartition Root Disk was skipped because no trigger condition checks were met.
        [  288.143764] systemd[1]: Starting Apply Kernel Variables...
        [  288.422834] systemd[1]: Starting Coldplug All udev Devices...
        [  288.485456] systemd[1]: Mounted Huge Pages File System.
        
        [  289.346373] systemd[1]: Mounted POSIX Message Queue File System.
        [  289.538138] xfs filesystem being remounted at / supports timestamps until 2038 (0x7fffffff)
        [  OK  ] Mounted POSIX Message Queue File System.[  290.016758] ACPI: bus type drm_connector registered
        
        [  290.162655] systemd[1]: Started Journal Service.
        [  OK  ] Started Journal Service.
        [  OK  ] Mounted Kernel Debug File System.
        [  OK  ] Mounted Kernel Trace File System.
        [  OK  ] Finished Create List of Static Device Nodes.
        [  OK  ] Finished Load Kernel Module configfs.
        [  OK  ] Finished Load Kernel Module drm.
        [  OK  ] Finished Load Kernel Module efi_pstore.
        [  OK  ] Finished Load Kernel Module fuse.
        [  OK  ] Finished Read and set NIS domainname from /etc/sysconfig/network.
        [  OK  ] Finished Generate network units from Kernel command line.
        [  OK  ] Finished Remount Root and Kernel File Systems.
        [  OK  ] Finished Apply Kernel Variables.
        [  OK  ] Finished Coldplug All udev Devices.
                 Mounting FUSE Control File System...
                 Mounting Kernel Configuration File System...
                 Starting Flush Journal to Persistent Storage...
        [  292.163715] systemd-journald[577]: Received client request to flush runtime journal.
                 Starting Load/Save OS Random Seed...
                 Starting Create Static Device Nodes in /dev...
        [  OK  ] Mounted FUSE Control File System.
        [  OK  ] Mounted Kernel Configuration File System.
        [  OK  ] Finished Flush Journal to Persistent Storage.
        [  OK  ] Finished Load/Save OS Random Seed.
        [  OK  ] Finished Create Static Device Nodes in /dev.
        [  OK  ] Reached target Preparation for Local File Systems.
        [  OK  ] Reached target Local File Systems.
                 Starting Automatic Boot Loader Update...
                 Starting Create Volatile Files and Directories...
                 Starting Rule-based Manager for Device Events and Files...
        [  OK  ] Finished Automatic Boot Loader Update.
        [  OK  ] Finished Create Volatile Files and Directories.
                 Starting Security Auditing Service...
                 Starting RPC Bind...
        [  OK  ] Started Rule-based Manager for Device Events and Files.
                 Starting Load Kernel Module configfs...
        [  OK  ] Started RPC Bind.
        [  OK  ] Finished Load Kernel Module configfs.
        [  OK  ] Started Security Auditing Service.
                 Starting Record System Boot/Shutdown in UTMP...
        [  OK  ] Finished Record System Boot/Shutdown in UTMP.
        [  OK  ] Reached target System Initialization.
        [  OK  ] Started dnf makecache --timer.
        [  OK  ] Started Daily rotation of log files.
        [  OK  ] Started Daily Cleanup of Temporary Directories.
        [  OK  ] Reached target Timer Units.
        [  OK  ] Listening on D-Bus System Message Bus Socket.
        [  OK  ] Listening on SSSD Kerberos Cache Manager responder socket.
        [  OK  ] Reached target Socket Units.
                 Starting D-Bus System Message Bus...
        [  OK  ] Started D-Bus System Message Bus.
        [  OK  ] Reached target Basic System.
        [  312.752553] input: PC Speaker as /devices/platform/pcspkr/input/input5
                 Starting NTP client/server...
                 Starting Cloud-init: Local Stage (pre-network)...
                 Starting Restore /run/initramfs on shutdown...
        [  OK  ] Started QEMU Guest Agent.
        [  OK  ] Reached target sshd-keygen.target.
        [  OK  ] Reached target User and Group Name Lookups.
                 Starting User Login Management...
                 Starting Rotate log files...
        [  OK  ] Finished Restore /run/initramfs on shutdown.
        [  OK  ] Started User Login Management.
        [  316.320336] lpc_ich 0000:00:1f.0: I/O space for GPIO uninitialized
        [  316.688547] i801_smbus 0000:00:1f.3: SMBus using PCI interrupt
        [  316.694368] i2c i2c-0: 1/1 memory slots populated (from DMI)
        [  316.694674] i2c i2c-0: Memory type 0x07 not supported yet, not instantiating SPD
        [  317.383238] RAPL PMU: API unit is 2^-32 Joules, 0 fixed counters, 10737418240 ms ovfl timer
        [  OK  ] Finished Rotate log files.
        [  320.399341] bochs-drm 0000:00:01.0: vgaarb: deactivate vga console
        [  320.589522] Console: switching to colour dummy device 80x25
        [  OK  ] Started NTP client/server.
        [  320.793412] [drm] Found bochs VGA, ID 0xb0c5.
        [  320.793755] [drm] Framebuffer size 16384 kB @ 0xfa000000, mmio @ 0xfea10000.
        [  328.479334] [drm] Found EDID data blob.
        [  328.603206] [drm] Initialized bochs-drm 1.0.0 for 0000:00:01.0 on minor 0
        [  328.691417] fbcon: bochs-drmdrmfb (fb0) is primary device
        [  329.884229] iTCO_vendor_support: vendor-support=0
        [  330.421249] iTCO_wdt iTCO_wdt.1.auto: Found a ICH9 TCO device (Version=2, TCOBASE=0x0660)
        [  330.697254] iTCO_wdt iTCO_wdt.1.auto: initialized. heartbeat=30 sec (nowayout=0)
        [  331.419132] Console: switching to colour frame buffer device 160x50
        [  331.483222] bochs-drm 0000:00:01.0: [drm] fb0: bochs-drmdrmfb frame buffer device
        [  326.032569] cloud-init[734]: Cloud-init v. 24.4-6.el9 running 'init-local' at Mon, 14 Jul 2025 01:36:11 +0000. Up 325.77 seconds.
        [  OK  ] Finished Cloud-init: Local Stage (pre-network).
        [  OK  ] Reached target Preparation for Network.
                 Starting Network Manager...
                 Starting Hostname Service...
        [  OK  ] Started Hostname Service.
                 Starting Network Manager Script Dispatcher Service...
        [  OK  ] Started Network Manager.
        [  OK  ] Started Network Manager Script Dispatcher Service.
        [  OK  ] Reached target Network.
        [  OK  ] Listening on Load/Save RF Kill Switch Status /dev/rfkill Watch.
                 Starting Network Manager Wait Online...
                 Starting GSSAPI Proxy Daemon...
        [  OK  ] Finished Network Manager Wait Online.
                 Starting Cloud-init: Network Stage...
        [  OK  ] Started GSSAPI Proxy Daemon.
        [  OK  ] Reached target NFS client services.
        [  OK  ] Reached target Preparation for Remote File Systems.
        [  OK  ] Reached target Remote File Systems.
        [  357.706804] cloud-init[802]: Cloud-init v. 24.4-6.el9 running 'init' at Mon, 14 Jul 2025 01:36:43 +0000. Up 356.85 seconds.
        [  358.852880] cloud-init[802]: ci-info: ++++++++++++++++++++++++++++++++++++Net device info+++++++++++++++++++++++++++++++++++++
        [  359.027779] cloud-init[802]: ci-info: +--------+------+-------------------------+---------------+--------+-------------------+
        [  359.111880] cloud-init[802]: ci-info: | Device |  Up  |         Address         |      Mask     | Scope  |     Hw-Address    |
        [  359.187861] cloud-init[802]: ci-info: +--------+------+-------------------------+---------------+--------+-------------------+
        [  359.266511] cloud-init[802]: ci-info: |  eth0  | True |         10.0.2.2        | 255.255.255.0 | global | 02:20:f7:00:00:0c |
        [  359.353853] cloud-init[802]: ci-info: |  eth0  | True | fe80::20:f7ff:fe00:c/64 |       .       |  link  | 02:20:f7:00:00:0c |
        [  359.432609] cloud-init[802]: ci-info: |   lo   | True |        127.0.0.1        |   255.0.0.0   |  host  |         .         |
        [  359.504818] cloud-init[802]: ci-info: |   lo   | True |         ::1/128         |       .       |  host  |         .         |
        [  359.683617] cloud-init[802]: ci-info: +--------+------+-------------------------+---------------+--------+-------------------+
        [  359.864814] cloud-init[802]: ci-info: +++++++++++++++++++++++++++Route IPv4 info++++++++++++++++++++++++++++
        [  359.947714] cloud-init[802]: ci-info: +-------+-------------+----------+---------------+-----------+-------+
        [  360.075875] cloud-init[802]: ci-info: | Route | Destination | Gateway  |    Genmask    | Interface | Flags |
        [  360.145796] cloud-init[802]: ci-info: +-------+-------------+----------+---------------+-----------+-------+
        [  360.218553] cloud-init[802]: ci-info: |   0   |   0.0.0.0   | 10.0.2.1 |    0.0.0.0    |    eth0   |   UG  |
        [  360.283864] cloud-init[802]: ci-info: |   1   |   10.0.2.0  | 0.0.0.0  | 255.255.255.0 |    eth0   |   U   |
        [  360.422543] cloud-init[802]: ci-info: +-------+-------------+----------+---------------+-----------+-------+
        [  360.506754] cloud-init[802]: ci-info: +++++++++++++++++++Route IPv6 info+++++++++++++++++++
        [  360.775605] cloud-init[802]: ci-info: +-------+-------------+---------+-----------+-------+
        [  360.903811] cloud-init[802]: ci-info: | Route | Destination | Gateway | Interface | Flags |
        [  360.984800] cloud-init[802]: ci-info: +-------+-------------+---------+-----------+-------+
        [  361.324767] cloud-init[802]: ci-info: |   1   |  fe80::/64  |    ::   |    eth0   |   U   |
        [  361.440851] cloud-init[802]: ci-info: |   3   |    local    |    ::   |    eth0   |   U   |
        [  361.654628] cloud-init[802]: ci-info: |   4   |  multicast  |    ::   |    eth0   |   U   |
        [  361.973802] cloud-init[802]: ci-info: +-------+-------------+---------+-----------+-------+
        [  OK  ] Finished Cloud-init: Network Stage.
        [  OK  ] Reached target Cloud-config availability.
        [  OK  ] Reached target Network is Online.
                 Starting Cloud-init: Config Stage...
                 Starting Notify NFS peers of a restart...
                 Starting System Logging Service...
                 Starting OpenSSH server daemon...
                 Starting Permit User Sessions...
        [  OK  ] Started Notify NFS peers of a restart.
        [  OK  ] Finished Permit User Sessions.
        [  OK  ] Started System Logging Service.
        [  OK  ] Started Command Scheduler.
        [  OK  ] Started Getty on tty1.
        [  OK  ] Started Serial Getty on ttyS0.
        [  OK  ] Reached target Login Prompts.
        [  OK  ] Started OpenSSH server daemon.
        [  OK  ] Reached target Multi-User System.
                 Starting Record Runlevel Change in UTMP...
        [  OK  ] Finished Record Runlevel Change in UTMP.
               
        [  402.046710] clocksource: Long readout interval, skipping watchdog check: cs_nsec: 15143239024 wd_nsec: 15143239760
        CentOS Stream 9
        Kernel 5.14.0-592.el9.x86_64 on an x86_64
        
        Activate the web console with: systemctl enable --now cockpit.socket
        
        centos-stream9-test login: [  389.992911] cloud-init[851]: Cloud-init v. 24.4-6.el9 running 'modules:config' at Mon, 14 Jul 2025 01:37:00 +0000. Up 373.86 seconds.
        [  392.777535] cloud-init[855]: Cloud-init v. 24.4-6.el9 running 'modules:final' at Mon, 14 Jul 2025 01:37:19 +0000. Up 392.54 seconds.
        [  393.249983] cloud-init[855]: Cloud-init v. 24.4-6.el9 finished at Mon, 14 Jul 2025 01:37:19 +0000. Datasource DataSourceConfigDrive [net,ver=2][source=/dev/vdb].  Up 392.97         seconds
        
        centos-stream9-test login: centos
        Password: 
        Last login: Sat Jul  5 03:33:57 on ttyS0
        [centos@centos-stream9-test ~]$ 
        [centos@centos-stream9-test ~]$ df -h
        Filesystem      Size  Used Avail Use% Mounted on
        devtmpfs        4.0M     0  4.0M   0% /dev
        tmpfs           854M     0  854M   0% /dev/shm
        tmpfs           342M  4.9M  337M   2% /run
        /dev/vda1        30G  1.4G   29G   5% /
        tmpfs           171M     0  171M   0% /run/user/1000
        [centos@centos-stream9-test ~]$ 
        [centos@centos-stream9-test ~]$ cat /etc/os-release 
        NAME="CentOS Stream"
        VERSION="9"
        ID="centos"
        ID_LIKE="rhel fedora"
        VERSION_ID="9"
        PLATFORM_ID="platform:el9"
        PRETTY_NAME="CentOS Stream 9"
        ANSI_COLOR="0;31"
        LOGO="fedora-logo-icon"
        CPE_NAME="cpe:/o:centos:centos:9"
        HOME_URL="https://centos.org/"
        BUG_REPORT_URL="https://issues.redhat.com/"
        REDHAT_SUPPORT_PRODUCT="Red Hat Enterprise Linux 9"
        REDHAT_SUPPORT_PRODUCT_VERSION="CentOS Stream"
        [centos@centos-stream9-test ~]$ 
        [centos@centos-stream9-test ~]$ ip a
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
            link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
            inet 127.0.0.1/8 scope host lo
               valid_lft forever preferred_lft forever
            inet6 ::1/128 scope host 
               valid_lft forever preferred_lft forever
        2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc fq_codel state UP group default qlen 1000
            link/ether 02:20:f7:00:00:0c brd ff:ff:ff:ff:ff:ff
            altname enp1s0
            inet 10.0.2.2/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
               valid_lft 86312897sec preferred_lft 86312897sec
            inet6 fe80::20:f7ff:fe00:c/64 scope link noprefixroute 
               valid_lft forever preferred_lft forever
        [centos@centos-stream9-test ~]$ 
#

        [root@centos-stream9-test ~]# exit
        logout
       
        CentOS Stream 9
        Kernel 5.14.0-592.el9.x86_64 on an x86_64

        Activate the web console with: systemctl enable --now cockpit.socket

        centos-stream9-test login: [sysadm@openshift-local-01 ~]$ 
        [sysadm@openshift-local-01 ~]$ 
        [sysadm@openshift-local-01 ~]$ 
        [sysadm@openshift-local-01 ~]$ 
#
        [sysadm@openshift-local-01 ~]$ virtctl stop centos-stream9-test 
        VM centos-stream9-test was scheduled to stop
       
        [sysadm@openshift-local-01 ~]$ oc get pods
        No resources found in a-test-vms namespace.
        
        [sysadm@openshift-local-01 ~]$ oc get vm
        NAME                  AGE   STATUS    READY
        centos-stream9-test   8d    Stopped   False
        [sysadm@openshift-local-01 ~]$ 

#
