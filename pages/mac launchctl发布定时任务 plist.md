- plist编辑
  
  
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
  <plist version="1.0">
    <dict>
      <!-- <key>KeepAlive</key>
      <true/> -->
      <key>Label</key>
      <string>com.jun.logseq.auto.sync.plist</string>
  
      <key>ProgramArguments</key>
      <array>
        <string>/Users/chujun/my/project/gitBackupConfig/mysh/logseqautosync.sh</string>
      </array>
  
      <!--指定时间执行-->
      <!-- <key>StartCalendarInterval</key>
      <dict>
  
        <key>Minute</key>
        <integer>1</integer>
  
       <key>Hour</key>
        <integer>15</integer> 
  
      </dict> -->
      <!--间隔时间执行,单位:秒-->
      <key>StartInterval</key>
      <integer>60</integer>
  
      <!-- 登录的时候启动 -->
      <key>RunAtLoad</key>  
      <true/>
  
      <!--日志信息-->
      <key>StandardOutPath</key>
      <string>/tmp/plist/com.jun.logsq.auto.sync.plist.log</string>
      <key>StandardErrorPath</key>
      <string>/tmp/plist/com.jun.logsq.auto.sync.plist.err</string>
  
    </dict>
  </plist>
  ```
  Plist部分参数说明：
  Label：对应的需要保证全局唯一性；
  Program：要运行的程序；
  ProgramArguments：命令语句
  StartCalendarInterval：运行的时间，单个时间点使用dict，多个时间点使用 array
  StartInterval：时间间隔，与StartCalendarInterval使用其一，单位为秒
  StandardInPath、StandardOutPath、StandardErrorPath：标准的输入输出错误文件
- plist文件摆放位置 
  赋值到launchctl的执行位置,有如下几种
  ~/Library/LaunchAgents 由用户自己定义的任务项
  /Library/LaunchAgents 由管理员为用户定义的任务项
  /Library/LaunchDaemons由管理员定义的守护进程任务项
  /System/Library/LaunchAgents 由Mac OS X为用户定义的任务项
  /System/Library/LaunchDaemons 由Mac OS X定义的守护进程任务项
  我的路径: ~/Library/LaunchAgents/com.jun.logseq.auto.sync.plist
- launchctl命令发布定时任务
  ```shell
  # 加载任务, -w选项会将plist文件中无效的key覆盖掉，建议加上
  $ launchctl load -w com.demo.plist
   
  # 删除任务,每次编辑过后都需要unload和load操作
  $ launchctl unload -w com.demo.plist
   
  # 查看任务列表, 使用 grep '任务部分名字' 过滤
  $ launchctl list | grep 'com.demo'
   
  # 开始任务，可以立刻执行任务，用于测试
  $ launchctl start  com.demo.plist
   
  # 结束任务
  $ launchctl stop   com.demo.plist
  
  ```
  console.app->system.log->搜索plist->可以查询launchctl执行失败的日志信息
- 缺点:
  launchctl定时任务执行失败