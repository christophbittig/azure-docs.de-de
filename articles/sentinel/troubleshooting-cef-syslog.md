---
title: Problembehandlung bei einer Verbindung zwischen Microsoft Sentinel und einem CEF- oder Syslog-Datenconnector| Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Probleme mit Ihrem CEF- oder Syslog-Datenconnector für Microsoft Sentinel beheben.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 627660330493b2d26c003145fc41dbadaa780052
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517253"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>Problembehandlung für CEF- oder Syslog-Datenconnectors

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden gängige Methoden für die Überprüfung und Problembehandlung eines CEF- oder Syslog-Datenconnectors für Microsoft Sentinel beschrieben.

Wenn Ihre Protokolle beispielsweise nicht in Microsoft Sentinel angezeigt werden (entweder in der Syslog- oder der CommonSecurityLog-Tabelle), kann Ihre Datenquelle möglicherweise keine Verbindung herstellen, oder es kann einen anderen Grund geben, warum Ihre Daten nicht erfasst werden.

Wenn die Datei **security_events.conf** oder **security-omsagent.config.conf** fehlt oder der rsyslog-Server nicht an Port 514 lauscht, sind dies weitere Anzeichen für eine fehlerhafte Connectorbereitstellung.

Weitere Informationen finden Sie unter [Verbinden der externen Lösung mithilfe von Common Event Format](connect-common-event-format.md) und [Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog](connect-syslog.md).

> [!NOTE]
> Der Log Analytics-Agent für Windows wird häufig als *Microsoft Monitoring Agent (MMA)* bezeichnet. Der Log Analytics-Agent für Linux wird häufig *OMS-Agent* genannt.
>

> [!TIP]
> Bei der Problembehandlung wird empfohlen, dass Sie die Schritte in diesem Artikel in der angezeigten Reihenfolge ausführen, um Probleme mit Ihrem Syslog-Collector, Betriebssystem oder OMS-Agent zu überprüfen und zu beheben.
>
> Wenn Sie Ihren Connector mit einer anderen Methode als dem dokumentierten Vorgang bereitgestellt haben und Probleme auftreten, empfiehlt es sich, die Bereitstellung zu löschen und wie dokumentiert erneut zu installieren.
>

## <a name="validate-cef-connectivity"></a>Überprüfen der CEF-Konnektivität

Nachdem Sie Ihre [Protokollweiterleitung bereitgestellt](connect-common-event-format.md) und Ihre [Sicherheitslösung zum Senden von CEF-Nachrichten konfiguriert haben](./connect-common-event-format.md), führen Sie die Schritte in diesem Abschnitt aus, um die Konnektivität zwischen Ihrer Sicherheitslösung und Microsoft Sentinel zu überprüfen.

1. Stellen Sie sicher, dass Sie folgenden Voraussetzungen erfüllt sind:

    - Sie müssen über erhöhte Berechtigungen (sudo) auf dem Computer mit der Protokollweiterleitung verfügen.

    - Auf dem Computer mit der Protokollweiterleitung muss **Python 2.7** oder **3** installiert sein. Verwenden Sie den Befehl `python –version` zum Überprüfen dieser Voraussetzung.

    - Möglicherweise benötigen Sie während dieses Vorgangs die ID und den Primärschlüssel des Arbeitsbereichs. Sie finden diese in der Arbeitsbereichsressource unter **Agent-Verwaltung**.

1. Öffnen Sie im Microsoft Sentinel-Navigationsmenü den Menüpunkt **Protokolle**. Führen Sie mithilfe des Schemas **CommonSecurityLog** eine Abfrage aus, um zu überprüfen, ob Sie Protokolle von Ihrer Sicherheitslösung erhalten.

    Es kann ca. 20 Minuten dauern, bis Ihre Protokolle in **Log Analytics** angezeigt werden.

1. Wenn keine Ergebnisse der Abfrage angezeigt werden, vergewissern Sie sich, dass von Ihrer Sicherheitslösung Ereignisse generiert werden, oder versuchen Sie, einige Ergebnisse zu generieren, und vergewissern Sie sich, dass sie an den von Ihnen festgelegten Syslog-Weiterleitungscomputer weitergeleitet werden.

1. Führen Sie das folgende Skript für die Protokollweiterleitung aus (geben Sie die Arbeitsbereichs-ID anstelle des Platzhalters ein), um die Konnektivität zwischen Ihrer Sicherheitslösung, der Protokollweiterleitung und Microsoft Sentinel zu überprüfen. Das Skript überprüft, ob der Daemon an den richtigen Ports lauscht, die Weiterleitung ordnungsgemäß konfiguriert ist und die Kommunikation zwischen dem Daemon und dem Log Analytics-Agent nicht blockiert wird. Außerdem sendet es auch TestCommonEventFormat-Pseudonachrichten, um die End-to-End-Konnektivität zu überprüfen. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der **Zuordnung im Feld *Computer*** zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Validierungsskript](#mapping-command).

    - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der **Analyse von Cisco ASA-Firewallprotokollen** zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Validierungsskript](#parsing-command).

### <a name="cef-validation-script-explained"></a>Erläuterung des CEF-Überprüfungsskripts

Das Überprüfungsskript führt die folgenden Überprüfungen durch:

# <a name="rsyslog-daemon"></a>[rsyslog-Daemon](#tab/rsyslog)

1. Überprüft, ob die Datei<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    vorhanden und gültig ist.

1. Überprüft, ob die Datei den folgenden Text enthält:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Überprüft anhand des folgenden Befehls, ob die Analyse für Cisco ASA-Firewallereignisse wie erwartet konfiguriert ist:

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Wenn ein Problem mit der Analyse vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Analyse sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle im Log Analytics-Agent ordnungsgemäß zugeordnet ist:

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft, ob auf dem Computer Sicherheitsverbesserungen vorhanden sind, die möglicherweise den Netzwerkverkehr blockieren (z. B. eine Hostfirewall).

1. Überprüft, ob der Syslog-Daemon (rsyslog) ordnungsgemäß konfiguriert ist und Nachrichten, die er als CEF identifiziert, an den Log Analytics-Agent an TCP-Port 25226 senden kann:

    - Konfigurationsdatei: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. Startet den Syslog-Daemon und den Log Analytics-Agent neu:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Überprüft, ob die erforderlichen Verbindungen hergestellt wurden: TCP 514 für das Empfangen von Daten, TCP 25226 für die interne Kommunikation zwischen dem Syslog-Daemon und dem Log Analytics-Agent:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Überprüft, ob der Syslog-Daemon Daten an Port 514 empfängt und ob der Agent Daten an Port 25226 empfängt:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Sendet Pseudodaten an Port 514 von localhost. Diese Daten sollten mithilfe der folgenden Abfrage im Microsoft Sentinel-Arbeitsbereich zu erkennen sein:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Überprüft, ob die Datei<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    vorhanden und gültig ist.

1. Überprüft, ob die Datei den folgenden Text enthält:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Überprüft anhand des folgenden Befehls, ob die Analyse für Cisco ASA-Firewallereignisse wie erwartet konfiguriert ist:

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Wenn ein Problem mit der Analyse vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Analyse sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle im Log Analytics-Agent ordnungsgemäß zugeordnet ist:

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft, ob auf dem Computer Sicherheitsverbesserungen vorhanden sind, die möglicherweise den Netzwerkverkehr blockieren (z. B. eine Hostfirewall).

1. Überprüft, ob der Syslog-Daemon (rsyslog-ng) ordnungsgemäß konfiguriert ist und Nachrichten, die er (über einen regulären Ausdruck) als CEF identifiziert, an den Log Analytics-Agent an TCP-Port 25226 senden kann:

    - Konfigurationsdatei: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Startet den Syslog-Daemon und den Log Analytics-Agent neu:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Überprüft, ob die erforderlichen Verbindungen hergestellt wurden: TCP 514 für das Empfangen von Daten, TCP 25226 für die interne Kommunikation zwischen dem Syslog-Daemon und dem Log Analytics-Agent:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Überprüft, ob der Syslog-Daemon Daten an Port 514 empfängt und ob der Agent Daten an Port 25226 empfängt:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Sendet Pseudodaten an Port 514 von localhost. Diese Daten sollten mithilfe der folgenden Abfrage im Microsoft Sentinel-Arbeitsbereich zu erkennen sein:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="verify-cef-or-syslog-prerequisites"></a>Überprüfen der CEF- oder Syslog-Voraussetzungen

Verwenden Sie die folgenden Abschnitte, um die Voraussetzungen für den CEF- oder Syslog-Datenconnector zu überprüfen.

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>Virtueller Azure-Computer als Syslog-Collector

Wenn Sie einen virtuellen Azure-Computer als Syslog-Collector verwenden, überprüfen Sie Folgendes:

- Stellen Sie beim Einrichten des Syslog-Datenconnectors sicher, dass Sie die [Microsoft Defender für Cloud-Einstellungen für die automatische Bereitstellung](../security-center/security-center-enable-data-collection.md) für den [MMA-/OMS-Agent](connect-windows-security-events.md#connector-options) deaktivieren.

    Sie können sie wieder aktivieren, nachdem Ihr Datenconnector vollständig eingerichtet wurde.

- Stellen Sie vor dem Bereitstellen des [Python-Skripts für den Common Event Format-Datenconnector](./connect-log-forwarder.md) sicher, dass Ihr virtueller Computer noch nicht mit einem vorhandenen Syslog-Arbeitsbereich verbunden ist. Sie finden diese Informationen in der VM-Liste für den Log Analytics-Arbeitsbereich, in der eine mit einem Syslog-Arbeitsbereich verbundene VM als **Verbunden** aufgeführt wird.

- Stellen Sie sicher, dass Microsoft Sentinel mit dem richtigen Syslog-Arbeitsbereich verbunden und die **SecurityInsights**-Lösung installiert ist.

    Weitere Informationen finden Sie unter [Schritt 1: Bereitstellen der Protokollweiterleitung](./connect-log-forwarder.md).

- Stellen Sie sicher, dass Ihr Computer geringstenfalls mit den erforderlichen Mindestanforderungen ordnungsgemäß dimensioniert ist. Weitere Informationen finden Sie unter [Voraussetzungen](connect-common-event-format.md#prerequisites).

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>Lokale VM oder Nicht-Azure-VM

Wenn Sie einen lokalen Computer oder eine Nicht-Azure-VM für Ihren Datenconnector verwenden, stellen Sie sicher, dass Sie das Installationsskript unter einer Neuinstallation eines unterstützten Linux-Betriebssystems ausgeführt haben:

> [!TIP]
> Sie finden dieses Skript auch auf der Datenconnectorseite **Common Event Format** in Microsoft Sentinel.
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>Aktivieren der Syslog-Komponente und der Protokollschweregradsammlung

Der Syslog-Server, entweder rsyslog oder syslog-ng, leitet alle in der relevanten Konfigurationsdatei definierten Daten weiter, die automatisch durch die in Ihrem Log Analytics-Arbeitsbereich definierten Einstellungen aufgefüllt werden.

Stellen Sie sicher, dass Sie Details zu den Facilitys und Schweregraden der Protokolle hinzufügen, die Sie in Microsoft Sentinel erfassen möchten. Der Konfigurationsvorgang dauert etwa 20 Minuten.

Weitere Informationen finden Sie unter [Erläuterung des Bereitstellungsskripts](./connect-log-forwarder.md#deployment-script-explained) und [Sammeln von Syslog-Datenquellen mit dem Log Analytics-Agent](../azure-monitor/agents/data-sources-syslog.md).


Führen Sie **beispielsweise für einen rsyslog-Server** den folgenden Befehl aus, um die aktuellen Einstellungen für die Syslog-Weiterleitung anzuzeigen, und überprüfen Sie alle Änderungen an der Konfigurationsdatei:

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

In diesem Fall sollte für rsyslog eine Ausgabe ähnlich der folgenden angezeigt werden. Der Inhalt dieser Datei sollte die Definition in der Syslog-Konfiguration auf dem Bildschirm **Log Analytics Workspace Client configuration – Syslog facility settings** (Clientkonfiguration für den Log Analytics-Arbeitsbereich – Syslog-Facilityeinstellungen) widerspiegeln.


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


Führen Sie **für die CEF-Weiterleitung für einen rsyslog-Server** den folgenden Befehl aus, um die aktuellen Einstellungen für die Syslog-Weiterleitung anzuzeigen, und überprüfen Sie alle Änderungen an der Konfigurationsdatei:

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

In diesem Fall sollte für rsyslog eine Ausgabe ähnlich der folgenden angezeigt werden:

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>Beheben von Betriebssystemproblemen

Im Rahmen dieses Verfahrens wird beschrieben, wie Sie Probleme beheben, die sehr wahrscheinlich mit der Betriebssystemkonfiguration zusammenhängen.

**So beheben Sie Betriebssystemprobleme:**

1. Falls Sie diesen Schritt noch nicht ausgeführt haben, überprüfen Sie, ob Sie mit einem unterstützten Betriebssystem und einer unterstützten Python-Version arbeiten. Weitere Informationen finden Sie unter [Voraussetzungen](connect-common-event-format.md#prerequisites) und [Konfigurieren Ihres Computers oder Ihrer Appliance unter Linux](connect-syslog.md#configure-your-linux-machine-or-appliance).

1. Wenn sich Ihr virtueller Computer in Azure befindet, überprüfen Sie, ob die Netzwerksicherheitsgruppe (NSG) eingehende TCP/UDP-Verbindungen von Ihrem Protokollclient (Sender) an Port 514 zulässt.

1. Vergewissern Sie sich, dass Pakete beim Syslog-Collector eintreffen. Führen Sie Folgendes aus, um die Syslog-Pakete zu erfassen, die am Syslog-Collector eintreffen:

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. Führen Sie eines der folgenden Verfahren aus:

    - Wenn keine Pakete eintreffen, bestätigen Sie die Sicherheitsgruppenberechtigungen für die Netzwerksicherheitsgruppe (NSG) und den Routingpfad zum Syslog-Collector.

    - Wenn Pakete eintreffen, vergewissern Sie sich, dass sie nicht abgelehnt werden.

    Wenn abgelehnte Pakete angezeigt werden, vergewissern Sie sich, dass die IP-Tabellen die Verbindungen nicht blockieren.

    Führen Sie Folgendes aus, um zu bestätigen, dass Pakete nicht abgelehnt werden:

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Überprüfen Sie, ob der Syslog-Server die Protokolle verarbeitet. Führen Sie Folgendes aus:

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    Alle Syslog- oder CEF-Protokolle, die verarbeitet werden, werden im Nur-Text-Format angezeigt.

1. Vergewissern Sie sich, dass der rsyslog-Server an TCP/UDP-Port 514 lauscht. Führen Sie Folgendes aus:

    ```config
    netstat -anp | grep syslog
    ```

    Wenn CEF- oder ASA-Protokolle an Ihren Syslog-Collector gesendet werden, sollte für TCP-Port 25226 eine hergestellte Verbindung angezeigt werden.

    Zum Beispiel:

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    Wenn die Verbindung blockiert wird, verwenden Sie möglicherweise eine [blockierte SELinux-Verbindung mit dem OMS-Agent](#selinux-blocking-connection-to-the-oms-agent), oder ein [Firewallprozess wird blockiert](#blocked-firewall-policy). Verwenden Sie die folgenden Anweisungen, um das Problem zu ermitteln.

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>SELinux blockiert die Verbindung mit dem OMS-Agent

Im Rahmen dieses Verfahrens wird beschrieben, wie Sie überprüfen, ob SELinux derzeit den Status `permissive` aufweist oder eine Verbindung mit dem OMS-Agent blockiert. Dieses Verfahren ist relevant, wenn es sich bei Ihrem Betriebssystem um eine Distribution von RedHat oder CentOS handelt.

> [!NOTE]
> Die Microsoft Sentinel-Unterstützung für CEF und Syslog umfasst nur die FIPS-Härtung. Andere Härtungsmethoden wie SELinux oder CIS werden derzeit nicht unterstützt.
>

1. Führen Sie Folgendes aus:

    ```config
    sestatus
    ```

    Der Status wird mit einer der folgenden Optionen angezeigt:

    - `disabled`. Diese Konfiguration wird für Ihre Verbindung mit Microsoft Sentinel unterstützt.
    - `permissive`. Diese Konfiguration wird für Ihre Verbindung mit Microsoft Sentinel unterstützt.
    - `enforced`. Diese Konfiguration wird nicht unterstützt, und Sie müssen entweder den Status deaktivieren oder auf `permissive` festlegen.

1. Wenn der Status derzeit auf `enforced` festgelegt ist, deaktivieren Sie ihn vorübergehend, um zu überprüfen, ob die Verbindung deshalb blockiert wurde. Führen Sie Folgendes aus:

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > Durch diesen Schritt wird SELinux nur deaktiviert, bis der Server neu gestartet wird. Ändern Sie die SELinux-Konfiguration so, dass sie deaktiviert ist.
    >

1. Führen Sie Folgendes aus, um zu überprüfen, ob die Änderung erfolgreich war:

    ```
    getenforce
    ```

    Der Status `permissive` sollte zurückgegeben werden.

> [!IMPORTANT]
> Dieses Einstellungsupdate geht verloren, wenn das System neu gestartet wird. Ändern Sie die Datei **/etc/selinux/config**, indem Sie den Wert `SELINUX` in `SELINUX=permissive` ändern, um diese Einstellung dauerhaft auf `permissive` zu aktualisieren.
>
> Weitere Informationen finden Sie in der [RedHat-Dokumentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux).


### <a name="blocked-firewall-policy"></a>Blockierte Firewallrichtlinie

Im Rahmen dieses Verfahrens wird beschrieben, wie Sie überprüfen, ob eine Firewallrichtlinie die Verbindung zwischen dem Rsyslog-Daemon und dem OMS-Agent blockiert, und wie Sie sie bei Bedarf deaktivieren.


1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob in den IP-Tabellen Ablehnungen enthalten sind, und geben Sie den Datenverkehr an, der von der Firewallrichtlinie gelöscht wird:

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Erstellen Sie eine Richtlinienregel, um die Verbindungen zuzulassen und die Firewallrichtlinie aktiviert zu lassen. Fügen Sie nach Bedarf Regeln hinzu, um die TCP/UDP-Ports 25226 und 25224 über die aktive Firewall zuzulassen.

    Zum Beispiel:

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. Fügen Sie nach Bedarf Regeln hinzu, um eine Regel zu erstellen, die die TCP/UDP-Ports 25226 und 25224 über die aktive Firewall zulässt.

    1. Führen Sie zum Installieren des Firewallrichtlinien-Editors Folgendes aus:

        ```config
        yum install policycoreutils-python
        ```

    1. Fügen Sie der Firewallrichtlinie die Firewallregeln hinzu. Zum Beispiel:

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. Überprüfen Sie, ob die Ausnahme hinzugefügt wurde. Führen Sie Folgendes aus:

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. Laden Sie die Firewall erneut. Führen Sie Folgendes aus:

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> Führen Sie `sudo systemctl disable firewalld` aus, um die Firewall zu deaktivieren.
>

## <a name="linux-and-oms-agent-related-issues"></a>Probleme unter Linux und OMS-Agent-bezogene Probleme

Wenn das Problem mit den zuvor im Artikel beschriebenen Schritten nicht behoben werden kann, liegt möglicherweise ein Konnektivitätsproblem zwischen dem OMS-Agent und dem Microsoft Sentinel-Arbeitsbereich vor.

Setzen Sie in solchen Fällen die Problembehandlung fort, indem Sie Folgendes überprüfen:

- Stellen Sie sicher, dass Pakete, die am TCP/UDP-Port 514 eintreffen, im Syslog-Collector angezeigt werden.

- Stellen Sie sicher, dass Sie in die lokale Protokolldatei geschriebene Protokolle anzeigen können (entweder **/var/log/messages** oder **/var/log/syslog**).

- Stellen Sie sicher, dass Datenpakete über Port 25224, 25226 oder beide Ports übertragen werden.

- Stellen Sie sicher, dass Ihr virtueller Computer über eine ausgehende Verbindung mit Port 443 über TCP verfügt oder eine Verbindung mit den [Log Analytics-Endpunkten](../azure-monitor/agents/log-analytics-agent.md#network-requirements) herstellen kann.

- Stellen Sie sicher, dass Sie über Ihre Firewallrichtlinie Zugriff auf die erforderlichen URLs aus Ihrem Syslog-Collector haben. Weitere Informationen finden Sie unter [Firewallanforderungen](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements).

- Stellen Sie sicher, dass Ihr virtueller Azure-Computer in der Liste der virtuellen Computer Ihres Arbeitsbereichs als verbunden angezeigt wird.

Führen Sie den folgenden Befehl aus, um zu ermitteln, ob der Agent erfolgreich mit Azure kommuniziert oder ob die Verbindung des OMS-Agents mit dem Log Analytics-Arbeitsbereich blockiert ist.

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

Ein Protokolleintrag wird zurückgegeben, wenn der Agent erfolgreich kommuniziert. Andernfalls wird der OMS-Agent möglicherweise blockiert.

## <a name="next-steps"></a>Nächste Schritte

Wenn die Schritte zur Problembehandlung in diesem Artikel beim Beheben Ihres Problems nicht weitergeholfen haben, öffnen Sie ein Supportticket, oder verwenden Sie die Microsoft Sentinel-Communityressourcen. Weitere Informationen finden Sie unter [Nützliche Ressourcen für das Arbeiten mit Microsoft Sentinel](resources.md).

Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie mehr über die [CEF- und CommonSecurityLog-Feldzuordnung](cef-name-mapping.md).
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
