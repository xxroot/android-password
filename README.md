# android-password
在android系统中，支持locksettings(等价cmd lock_settings)命令，可用于命令行增加/删除/修改password/pin/pattern.

1、进入adb shell，查看locksettings支持哪些命令
locksettings命令

C:\Users\user1>
C:\Users\user1>adb shell
Projects:/ # locksettings
usage: locksettings set-pattern [--old OLD_CREDENTIAL] NEW_PATTERN
       locksettings set-pin [--old OLD_CREDENTIAL] NEW_PIN
       locksettings set-password [--old OLD_CREDENTIAL] NEW_PASSWORD
       locksettings clear [--old OLD_CREDENTIAL]
       locksettings verify [--old OLD_CREDENTIAL]
       locksettings set-disabled DISABLED
       locksettings get-disabled

flags:
       --user USER_ID: specify the user, default value is current user

locksettings set-pattern: sets a pattern
    A pattern is specified by a non-separated list of numbers that index the cell
    on the pattern in a 1-based manner in left to right and top to bottom order,
    i.e. the top-left cell is indexed with 1, whereas the bottom-right cell
    is indexed with 9. Example: 1234

locksettings set-pin: sets a PIN

locksettings set-password: sets a password

locksettings clear: clears the unlock credential

locksettings verify: verifies the credential and unlocks the user

locksettings set-disabled: sets whether the lock screen should be disabled

locksettings get-disabled: retrieves whether the lock screen is disabled

Projects:/ #
Projects:/ #


cmd lock_settings命令:

255|Projects:/ # cmd lock_settings
lockSettings service commands:

NOTE: when lock screen is set, all commands require the --old <CREDENTIAL> argument.

  help
    Prints this help text.

  get-disabled [--old <CREDENTIAL>] [--user USER_ID]
    Checks whether lock screen is disabled.

  set-disabled [--old <CREDENTIAL>] [--user USER_ID] <true|false>
    When true, disables lock screen.

  set-pattern [--old <CREDENTIAL>] [--user USER_ID] <PATTERN>
    Sets the lock screen as pattern, using the given PATTERN to unlock.

  set-pin [--old <CREDENTIAL>] [--user USER_ID] <PIN>
    Sets the lock screen as PIN, using the given PIN to unlock.

  set-pin [--old <CREDENTIAL>] [--user USER_ID] <PASSWORD>
    Sets the lock screen as password, using the given PASSOWRD to unlock.

  sp [--old <CREDENTIAL>] [--user USER_ID]
    Gets whether synthetic password is enabled.

  sp [--old <CREDENTIAL>] [--user USER_ID] <1|0>
    Enables / disables synthetic password.

  clear [--old <CREDENTIAL>] [--user USER_ID]
    Clears the lock credentials.

  verify [--old <CREDENTIAL>] [--user USER_ID]
    Verifies the lock credentials.

  remove-cache [--user USER_ID]
    Removes cached unified challenge for the managed profile.

255|Projects:/ #

事实上，locksettings命令也是调用的cmd lock_settings命令

2、locksettings命令的使用方法


改变密码：

locksettings set-pin --old xxxx xxxx

Example: locksettings set-pin --old 0000 8888


locksettings set-password --old xxxx xxxx

Example: locksettings set-password --old currentpassword newpassword


locksettings set-pattern --old xxxx xxxx

Example: locksettings set-pattern --old 1456 6987
清除密码:
locksettings clear --old xxxx

如果是添加密码，参照"改变密码"不加–old选项即可

一些示例:

Projects:/ # locksettings set-disabled DISABLED
Lock screen disabled set to false
Projects:/ #
Projects:/ # locksettings set-disabled true
Lock screen disabled set to true
Projects:/ #
Projects:/ # cmd lock_settings set-password 1234
Password set to '1234'
Projects:/ #
Projects:/ # cmd lock_settings verify --old 1234
Lock credential verified successfully
Projects:/ #
Projects:/ # cmd lock_settings clear --old 1234
Lock credential cleared
Projects:/ #
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
当然，当年命令敲得不对的时候，会抛出例如一下这样的异常:

Projects:/ # locksettings set-disabled DISABLED
Error while executing command: set-disabled
java.lang.IllegalArgumentException: Credential can't be null or empty
        at com.android.server.locksettings.LockSettingsService.doVerifyCredential(LockSettingsService.java:2016)
        at com.android.server.locksettings.LockSettingsService.doVerifyCredential(LockSettingsService.java:2003)
        at com.android.server.locksettings.LockSettingsService.checkCredential(LockSettingsService.java:1976)
        at com.android.internal.widget.LockPatternUtils.checkCredential(LockPatternUtils.java:428)
        at com.android.server.locksettings.LockSettingsShellCommand.checkCredential(LockSettingsShellCommand.java:311)
        at com.android.server.locksettings.LockSettingsShellCommand.onCommand(LockSettingsShellCommand.java:89)
        at android.os.BasicShellCommandHandler.exec(BasicShellCommandHandler.java:98)
        at android.os.ShellCommand.exec(ShellCommand.java:44)
        at com.android.server.locksettings.LockSettingsService.onShellCommand(LockSettingsService.java:2377)
        at android.os.Binder.shellCommand(Binder.java:932)
        at android.os.Binder.onTransact(Binder.java:816)
        at com.android.internal.widget.ILockSettings$Stub.onTransact(ILockSettings.java:1010)
        at android.os.Binder.execTransactInternal(Binder.java:1169)
        at android.os.Binder.execTransact(Binder.java:1126)
Projects:/ #

3、locksettings命令的源代码位置
frameworks/base/cmds/locksettings/src/com/android/commands/locksettings/LockSettingsCmd.java
frameworks/base/services/core/java/com/android/server/locksettings/LockSettingsShellCommand.java
————————————————
