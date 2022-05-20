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
- plist文件摆放位置
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
-