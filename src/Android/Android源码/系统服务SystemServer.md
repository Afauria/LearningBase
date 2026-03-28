


| 服务名             | Java 类                     | AIDL 接口             | 主要功能                         |
| ------------------ | --------------------------- | --------------------- | -------------------------------- |
| activity           | ActivityManagerService      | IActivityManager      | 管理四大组件生命周期，组件间通信 |
| package            | PackageManagerService       | IPackageManager       | 包安装、卸载、查询等             |
| window             | WindowManagerService        | IWindowManager        | 管理窗口层级和显示               |
| input              | InputManagerService         | IInputManager         | 触摸、按键等输入事件分发         |
| input_method       | InputMethodManagerService   | IInputMethodManager   | 输入法管理                       |
| power              | PowerManagerService         | IPowerManager         | 电源管理                         |
| location           | LocationManagerService      | ILocationManager      | 定位服务                         |
| vibrator           | VibratorService             | IVibratorService      | 震动控制                         |
| alarm              | AlarmManagerService         | IAlarmManager         | 定时任务                         |
| notification       | NotificationManagerService  | INotificationManager  | 通知管理                         |
| telephony.registry | TelephonyRegistry           | ITelephony            | 电话服务                         |
| display            | DisplayManagerService       | IDisplayManager       | 显示管理                         |
| sensorservice      | SensorService               | ISensorManager        | 传感器管理                       |
| audio              | AudioService                | IAudioService         | 音频管理                         |
| batterystats       | BatteryStatsService         | IBatteryStats         | 电池统计                         |
| cameraservice      | CameraService               | ICameraService        | 相机管理                         |
| accessibility      | AccessibilityManagerService | IAccessibilityManager | 无障碍服务                       |
| job_scheduler      | JobSchedulerService         | IJobScheduler         | 后台任务调度                     |
| dropbox            | DropBoxManagerService       | IDropBoxManager       | 日志收集                         |
| account            | AccountManagerService       | IAccountManager       | 账户管理                         |

ServiceManager.getService(String name)	通过名称获取系统服务的 IBinder
Context.getSystemService(String name)	从 Context 中获取服务实例



// 获取 ActivityManagerService
`IActivityManager am = ActivityManager.getService();`

`IActivityManager.Stub.asInterface(ServiceManager.getService(Context.ACTIVITY_SERVICE)`

// 获取 PackageManagerService

`IPackageManager pm = IPackageManager.Stub.asInterface(ServiceManager.getService("package"));`