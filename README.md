```
Note from Jim Raden
===================
If and when 1.2.11 is released, I will merge with that tag, or perhaps
abandon this completely.

We now resume the Ron Grabowski README.txt.


Project Status
==============

Apache log4net is a sub project of the Apache Logging Services project. 
Apache log4net graduated from the Apache Incubator in February 2007.
Web site: http://logging.apache.org/log4net


Documentation
=============

For local documentation, which is correct for this release see:
doc/index.html

For the latest documentation see the log4net web site at:
http://logging.apache.org/log4net
```

## 增加几个自定义功能
* UdpAppender 增加 Serialization 属性(bool值)，表示数据发送格式是否是序列化 LoggingEvent 对象
* RemoteSyslogAppender 继承 UdpAppender，Serialization 属性同样有效；对信息格式 syslog RFC 5424 协议支持
* RollingFileAppender 增加 MaxReserveFileCount，MaxReserveFileDays 属性，对保存的日志文件数量限制

## 配置示例
```XML
<?xml version="1.0" encoding="utf-8" ?>
<configuration>

    <!--
    <appSettings>
        <add key="log4net.Internal.Emit" value="False"/>
        <add key="log4net.Internal.Debug" value="False"/>
        <add key="log4net.Internal.Quiet" value="False"/>
        
        <add key="log4net.Config.Watch" value="True"/>
        <add key="log4net.Config" value="Log4Net.Config"/>
    </appSettings>
    -->

    <configSections>
        <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net"/>
    </configSections>

    <!-- Config Examples : http://logging.apache.org/log4net/release/config-examples.html -->
    <log4net debug="false">
        <!--Root Logger-->
        <root>
            <!-- 
            日志等级：OFF > FATAL(致命错误) > ERROR(一般错误) > WARN(警告) > INFO(一般信息) > DEBUG(调试信息) > ALL 
            跟据项目需求，开启/引用输出源名称
            -->
            <level value="ALL" />
            <appender-ref ref="UdpAppender" />
            <!--appender-ref ref="SmtpAppender" /-->
            <appender-ref ref="LogFileAppender" />
            <!--appender-ref ref="ColoredConsoleAppender"/-->

            <!--appender-ref ref="TraceAppender" /-->
            <appender-ref ref="ConsoleAppender" />
            <appender-ref ref="DebugStringAppender"/>
            <!--appender-ref ref="RemoteSyslogAppender"/-->
        </root>

        <!-- Library Logger-->
        <logger name="Library.Logger" additivity="true">
            <level value="INFO"/>
            <appender-ref ref="LogFileAppender" />
        </logger>

        <!-- Sub Logger-->
        <logger name="Sub.Logger" additivity="true">
            <level value="INFO"/>
            <appender-ref ref="LogFileAppender" />
        </logger>

        <!-- 日志文本文件 输出 -->
        <appender  name="LogFileAppender" type="log4net.Appender.RollingFileAppender,log4net" >
            <!-- 日志文件名 -->
            <param name="File" value="Logs/" />
            <!--是否是向文件中追加日志-->
            <param name="AppendToFile" value="true" />
            <!--单个文件最大的大小，单位:KB|MB|GB-->
            <param name="MaximumFileSize" value="8MB" />
            <!--当日志文件达到MaxFileSize大小，就自动创建备份文件-->
            <param name="MaxSizeRollBackups" value="10" />
            <!--按照何种方式产生多个日志文件(日期[Date],文件大小[Size],混合[Composite])-->
            <param name="RollingStyle" value="Composite" />
            <!--日志文件名格式 yyyy-MM-dd.log-->
            <param name="DatePattern" value="'logger.'yyyy-MM-dd'.log'" />
            <!--日志文件名是否是固定不变的-->
            <param name="StaticLogFileName" value="false" />
            <!--使用UTF-8编码-->
            <param name="Encoding" value="UTF-8" />
            <!-- 以下两个属性是个修改源码后的版本 version: 2.0.8.1 -->
            <!--日志文件的保留数量及方式，-1表示不处理，或可使用叠加方式-->
            <param name="MaxReserveFileDays" value="-1"/>
            <param name="MaxReserveFileCount" value="30"/>
            <!--记录日志写入文件时，不锁定文本文件，防止多线程时不能写Log,官方说线程非安全-->
            <lockingModel type="log4net.Appender.FileAppender+MinimalLock" />
            <!--定义输出布局风格-->
            <layout type="log4net.Layout.PatternLayout,log4net">
                <param name="ConversionPattern" value="[%date{HH:mm:ss}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
                <param name="Header" value="&#13;&#10;[Header]&#13;&#10;" />
                <param name="Footer" value="[Footer]&#13;&#10;&#13;&#10;" />
            </layout>
        </appender>

        <!-- 控制台程序 输出 -->
        <appender name="ColoredConsoleAppender" type="log4net.Appender.ColoredConsoleAppender">
            <mapping>
                <level value="ERROR" />
                <foreColor value="Red" />
            </mapping>
            <mapping>
                <level value="WARN" />
                <foreColor value="Yellow" />
            </mapping>
            <mapping>
                <level value="INFO" />
                <foreColor value="White" />
            </mapping>
            <mapping>
                <level value="DEBUG" />
                <foreColor value="Blue" />
            </mapping>
            <layout type="log4net.Layout.PatternLayout">
                <conversionPattern value="[%date{yyyy-MM-dd HH:mm:ss}] [%thread] %level %logger [%method(%line)] - %message (%r) %newline" />
            </layout>
            <filter type="log4net.Filter.LevelRangeFilter">
                <param name="LevelMin" value="Info" />
                <param name="LevelMax" value="Fatal" />
            </filter>
        </appender>

        <!-- UDP远程 输出 -->
        <!-- 2019.12.20 增加属性 serialization, 表示输出格式为序列化的 LoggingEvent 对象，还是 layout 字符格式序列，默认为字符格式 -->
        <appender name="UdpAppender" type="log4net.Appender.UdpAppender">
            <!--encoding value="gb2312" /-->
            <remotePort value="6666" />
            <remoteAddress value="127.0.0.1" />
            <serialization value="false"/>
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%date{yyyy-MM-dd HH:mm:ss}] [%property{log4net:HostName}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- Stmp邮件 输出 -->
        <!-- (授权码) exmail.qq.com 设置—帐户—帐户安全(开启帐户安全)—微信绑定—客户端专用密码(生成客户端专用密码)-->
        <appender name="SmtpAppender" type="log4net.Appender.SmtpAppender,log4net">
            <to value="huangmin@spacecg.cn" />
            <from value="liaoyunhui@spacecg.cn" />
            <username value="liaoyunhui@spacecg.cn" />
            <!--授权码(非密码)-->
            <password value="9XZeuSzznTSBiENU" />
            <subject value="XX位置XX项目日志信息" />
            <!-- SmtpHost 使用SSL(具体看邮件服务器是否开启 SSL ) -->
            <enableSsl value="true" />
            <authentication value="Basic" />
            <smtpHost value="smtp.exmail.qq.com" />
            <!-- 未达到 bufferSize 大小信息将被丢弃 -->
            <lossy value="true" />
            <bufferSize value="1024" />
            <evaluator type="log4net.Core.LevelEvaluator,log4net">
                <threshold value="WARN"/>
            </evaluator>
            <layout type="log4net.Layout.PatternLayout">
                <conversionPattern value="[%date{yyyy-MM-dd HH:mm:ss}] [%2thread] [%5level] [%method(%line)] %logger [%ndc] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- SysLog 输出 -->
        <!-- 2019/11/06 修改了log4net RemoteSyslogAppender(继承 UdpAppender，属性 serialization 可用) 部份源码 version: 2.0.8.1 -->
        <!-- 原版本 syslog 协议版本为 RFC3164 数据编码为 ACSII 码，不支持中文消息；后面将 syslog 版本修为 RFC5424 数据编码改为系统默认编码，可以支持中文了 -->
        <appender name="RemoteSyslogAppender" type="log4net.Appender.RemoteSyslogAppender,log4net">
            <remoteAddress value="127.0.0.1" />
            <remotePort value="514" />
            <serialization value="false"/>
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%property{log4net:HostName}] [%thread] [%level] %logger [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- Trace 输出 -->
        <appender name="TraceAppender" type="log4net.Appender.TraceAppender,log4net">
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="Trace:[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>
        <!-- Console 输出 -->
        <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender,log4net">
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>
        <!-- Debug 输出 -->
        <appender name="DebugStringAppender" type="log4net.Appender.OutputDebugStringAppender,log4net" >
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="Debug:[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

    </log4net>
</configuration>

```