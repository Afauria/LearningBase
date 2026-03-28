http://10.22.10.56:8080/HISI570_AN15__dev/xref/android/system/core/init/README.md

1) 源码：system/core/init/*，init 进程的入口： main.cpp
2) 配置文件：system/core/rootdir/init.rc，
3) 说明：init进程是linux系统中用户空间的第一个进程，进程号为1。当kernel启动完后，在用户空间启动init进程，再通过init进程，来读取init.rc中的相关配置，从而来启动其他相关进程以及其他操作：
--->fork一些系统关键进程，如surfaceflinger, servicemanager，zygote等；
--->初始化Selinux；
--->提供property  service来管理系统属性；


init进程在kernel_init中启动，依次尝试`/init、/sbin/init、/etc/init、/bin/init`

http://10.22.10.56:8080/HISI570_AN15__dev/xref/android/vendor/open_source/common-kernel-6.6/init/main.c

```c
static char *execute_command;
static char *ramdisk_execute_command = "/init";

static int __ref kernel_init(void *unused)
{
	int ret;

	//...

	if (ramdisk_execute_command) {
		ret = run_init_process(ramdisk_execute_command);
		if (!ret)
			return 0;
		pr_err("Failed to execute %s (error %d)\n",
		       ramdisk_execute_command, ret);
	}
    //...
	if (!try_to_run_init_process("/sbin/init") ||
	    !try_to_run_init_process("/etc/init") ||
	    !try_to_run_init_process("/bin/init") ||
	    !try_to_run_init_process("/bin/sh"))
		return 0;

	panic("No working init found.  Try passing init= option to kernel. "
	      "See Linux Documentation/admin-guide/init.rst for guidance.");
}
```

/system/bin/init 
/system/bin/init selinux_setup
/system/bin/init second_stage

解析init.rc文件

android/system/core/init/init.cpp

按路径加载rc文件

```c
static void LoadBootScripts(ActionManager& action_manager, ServiceList& service_list) {
    Parser parser = CreateParser(action_manager, service_list);

    std::string bootscript = GetProperty("ro.boot.init_rc", "");
    if (bootscript.empty()) {
        parser.ParseConfig("/system/etc/init/hw/init.rc");
        if (!parser.ParseConfig("/system/etc/init")) {
            late_import_paths.emplace_back("/system/etc/init");
        }
        // late_import is available only in Q and earlier release. As we don't
        // have system_ext in those versions, skip late_import for system_ext.
        parser.ParseConfig("/system_ext/etc/init");
        if (!parser.ParseConfig("/vendor/etc/init")) {
            late_import_paths.emplace_back("/vendor/etc/init");
        }
        if (!parser.ParseConfig("/odm/etc/init")) {
            late_import_paths.emplace_back("/odm/etc/init");
        }
        if (!parser.ParseConfig("/product/etc/init")) {
            late_import_paths.emplace_back("/product/etc/init");
        }
    } else {
        parser.ParseConfig(bootscript);
    }
}
```

/system/etc/init/hw/init.rc

