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

## ���Ӽ����Զ��幦��
* UdpAppender ���� Serialization ����(boolֵ)����ʾ���ݷ��͸�ʽ�Ƿ������л� LoggingEvent ����
* RemoteSyslogAppender �̳� UdpAppender��Serialization ����ͬ����Ч������Ϣ��ʽ syslog RFC 5424 Э��֧��
* RollingFileAppender ���� MaxReserveFileCount��MaxReserveFileDays ���ԣ��Ա������־�ļ���������

## ����ʾ��
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
            ��־�ȼ���OFF > FATAL(��������) > ERROR(һ�����) > WARN(����) > INFO(һ����Ϣ) > DEBUG(������Ϣ) > ALL 
            ������Ŀ���󣬿���/�������Դ����
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

        <!-- ��־�ı��ļ� ��� -->
        <appender  name="LogFileAppender" type="log4net.Appender.RollingFileAppender,log4net" >
            <!-- ��־�ļ��� -->
            <param name="File" value="Logs/" />
            <!--�Ƿ������ļ���׷����־-->
            <param name="AppendToFile" value="true" />
            <!--�����ļ����Ĵ�С����λ:KB|MB|GB-->
            <param name="MaximumFileSize" value="8MB" />
            <!--����־�ļ��ﵽMaxFileSize��С�����Զ����������ļ�-->
            <param name="MaxSizeRollBackups" value="10" />
            <!--���պ��ַ�ʽ���������־�ļ�(����[Date],�ļ���С[Size],���[Composite])-->
            <param name="RollingStyle" value="Composite" />
            <!--��־�ļ�����ʽ yyyy-MM-dd.log-->
            <param name="DatePattern" value="'logger.'yyyy-MM-dd'.log'" />
            <!--��־�ļ����Ƿ��ǹ̶������-->
            <param name="StaticLogFileName" value="false" />
            <!--ʹ��UTF-8����-->
            <param name="Encoding" value="UTF-8" />
            <!-- �������������Ǹ��޸�Դ���İ汾 version: 2.0.8.1 -->
            <!--��־�ļ��ı�����������ʽ��-1��ʾ���������ʹ�õ��ӷ�ʽ-->
            <param name="MaxReserveFileDays" value="-1"/>
            <param name="MaxReserveFileCount" value="30"/>
            <!--��¼��־д���ļ�ʱ���������ı��ļ�����ֹ���߳�ʱ����дLog,�ٷ�˵�̷߳ǰ�ȫ-->
            <lockingModel type="log4net.Appender.FileAppender+MinimalLock" />
            <!--����������ַ��-->
            <layout type="log4net.Layout.PatternLayout,log4net">
                <param name="ConversionPattern" value="[%date{HH:mm:ss}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
                <param name="Header" value="&#13;&#10;[Header]&#13;&#10;" />
                <param name="Footer" value="[Footer]&#13;&#10;&#13;&#10;" />
            </layout>
        </appender>

        <!-- ����̨���� ��� -->
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

        <!-- UDPԶ�� ��� -->
        <!-- 2019.12.20 �������� serialization, ��ʾ�����ʽΪ���л��� LoggingEvent ���󣬻��� layout �ַ���ʽ���У�Ĭ��Ϊ�ַ���ʽ -->
        <appender name="UdpAppender" type="log4net.Appender.UdpAppender">
            <!--encoding value="gb2312" /-->
            <remotePort value="6666" />
            <remoteAddress value="127.0.0.1" />
            <serialization value="false"/>
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%date{yyyy-MM-dd HH:mm:ss}] [%property{log4net:HostName}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- Stmp�ʼ� ��� -->
        <!-- (��Ȩ��) exmail.qq.com ���á��ʻ����ʻ���ȫ(�����ʻ���ȫ)��΢�Ű󶨡��ͻ���ר������(���ɿͻ���ר������)-->
        <appender name="SmtpAppender" type="log4net.Appender.SmtpAppender,log4net">
            <to value="huangmin@spacecg.cn" />
            <from value="liaoyunhui@spacecg.cn" />
            <username value="liaoyunhui@spacecg.cn" />
            <!--��Ȩ��(������)-->
            <password value="9XZeuSzznTSBiENU" />
            <subject value="XXλ��XX��Ŀ��־��Ϣ" />
            <!-- SmtpHost ʹ��SSL(���忴�ʼ��������Ƿ��� SSL ) -->
            <enableSsl value="true" />
            <authentication value="Basic" />
            <smtpHost value="smtp.exmail.qq.com" />
            <!-- δ�ﵽ bufferSize ��С��Ϣ�������� -->
            <lossy value="true" />
            <bufferSize value="1024" />
            <evaluator type="log4net.Core.LevelEvaluator,log4net">
                <threshold value="WARN"/>
            </evaluator>
            <layout type="log4net.Layout.PatternLayout">
                <conversionPattern value="[%date{yyyy-MM-dd HH:mm:ss}] [%2thread] [%5level] [%method(%line)] %logger [%ndc] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- SysLog ��� -->
        <!-- 2019/11/06 �޸���log4net RemoteSyslogAppender(�̳� UdpAppender������ serialization ����) ����Դ�� version: 2.0.8.1 -->
        <!-- ԭ�汾 syslog Э��汾Ϊ RFC3164 ���ݱ���Ϊ ACSII �룬��֧��������Ϣ�����潫 syslog �汾��Ϊ RFC5424 ���ݱ����ΪϵͳĬ�ϱ��룬����֧�������� -->
        <appender name="RemoteSyslogAppender" type="log4net.Appender.RemoteSyslogAppender,log4net">
            <remoteAddress value="127.0.0.1" />
            <remotePort value="514" />
            <serialization value="false"/>
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%property{log4net:HostName}] [%thread] [%level] %logger [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

        <!-- Trace ��� -->
        <appender name="TraceAppender" type="log4net.Appender.TraceAppender,log4net">
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="Trace:[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>
        <!-- Console ��� -->
        <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender,log4net">
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>
        <!-- Debug ��� -->
        <appender name="DebugStringAppender" type="log4net.Appender.OutputDebugStringAppender,log4net" >
            <layout type="log4net.Layout.PatternLayout, log4net">
                <conversionPattern value="Debug:[%date{HH:mm:ss.fff}] [%2thread] [%5level] [%logger] [%method(%line)] - %message (%r) %newline" />
            </layout>
        </appender>

    </log4net>
</configuration>

```