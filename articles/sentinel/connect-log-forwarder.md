---
title: Bereitstellung eines Protokoll-Forwarders zum Einlesen von Syslog- und CEF-Protokollen in Microsoft Sentinel | Microsoft-Dokumentation
description: Lernen Sie, wie Sie einen Protokoll-Forwarder, bestehend aus einem Syslog-Daemon und dem Log Analytics-Agenten, als Teil des Prozesses zum Einlesen von Syslog- und CEF-Protokollen in Microsoft Sentinel bereitstellen.
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
ms.openlocfilehash: 6279e4cee368a36d66db5f41a326194fb04fe397
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518582"
---
# <a name="deploy-a-log-forwarder-to-ingest-syslog-and-cef-logs-to-microsoft-sentinel"></a>Bereitstellung eines Protokoll-Forwarders zum Einlesen von Syslog- und CEF-Protokollen in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Um Syslog- und CEF-Protokolle in Microsoft Sentinel zu erfassen, insbesondere von Geräten und Appliances, auf denen Sie den Log Analytics-Agenten nicht direkt installieren können, müssen Sie einen Linux Computer bestimmen und konfigurieren, der die Protokolle von Ihren Geräten sammelt und sie an Ihren Azure Sentinel-Arbeitsbereich weiterleitet. Bei diesem Computer kann es sich um einen physischen oder virtuellen Computer in Ihrer lokalen Umgebung, eine Azure-VM oder eine VM in einer anderen Cloud handeln. 

Dieser Computer hat zwei Komponenten, die an diesem Prozess beteiligt sind:

- Ein Syslog-Daemon, entweder **rsyslog** oder **syslog-ng**, der die Protokolle sammelt.
- Der **Log Analytics Agent** (auch bekannt als OMS Agent), der die Protokolle an Microsoft Sentinel weiterleitet.

Über den unten angegebenen Link führen Sie ein Skript auf dem angegebenen Computer aus, das die folgenden Aufgaben durchführt:

- Es installiert den Log Analytics-Agent für Linux (auch als OMS-Agent bezeichnet) und konfiguriert ihn für die folgenden Zwecke:
    - Lauschen auf CEF-Nachrichten vom integrierten Linux-Syslog-Daemon am TCP-Port 25226
    - Sicheres Senden der Nachrichten über TLS an Ihren Microsoft Sentinel-Arbeitsbereich, wo sie analysiert und ergänzt werden

- Es konfiguriert den integrierten Linux-Syslog-Daemon (rsyslog.d/syslog-ng) für die folgenden Zwecke:
    - Lauschen auf Syslog-Nachrichten von Ihren Sicherheitslösungen an TCP-Port 514
    - Weiterleiten nur der Nachrichten, die mit dem Log Analytics-Agent auf localhost als CEF identifiziert werden, über TCP-Port 25226
 
## <a name="prerequisites"></a>Voraussetzungen

Ihr Computer muss die folgenden Anforderungen erfüllen:

- **Hardware (physisch/virtuell)**

    - Ihr Linux-Computer muss mindestens **4 CPU-Kerne und 8 GB RAM** aufweisen.

        > [!NOTE]
        > - Ein einzelner Computer für die Protokollweiterleitung, der den **rsyslog**-Daemon verwendet, unterstützt insgesamt eine Kapazität von **bis zu 8.500 Ereignissen pro Sekunde (EPS)** .

- **Betriebssystem**

    - CentOS 7 und 8 (nicht6), einschließlich Nebenversionen (64-Bit/32-Bit)
    - Amazon Linux 2017.09 (nur 64-Bit)
    - Oracle Linux 7 (64-Bit/32-Bit)
    - Red Hat Enterprise Linux (RHEL) Server 7 und 8 (not 6), einschließlich Nebenversionen (64-Bit/32-Bit)
    - Debian GNU/Linux 8 und 9 (64-Bit/32-Bit)
    - Ubuntu Linux 14.04 LTS und 16.04 LTS (64-Bit/32-Bit), und 18.04 LTS (nur 64-Bit)
    - SUSE Linux Enterprise Server 12, 15 (nur 64-Bit)

- **Daemonversionen**

    - Rsyslog: v8
    - Syslog-ng: 2.1 bis 3.22.1

- **Pakete**
    - Auf dem Linux-Computer muss **Python 2.7** oder **3** installiert sein.<br>Verwenden Sie zur Überprüfung den Befehl `python --version` oder `python3 --version`.

- **Syslog RFC Support**
  - Syslog RFC 3164
  - Syslog RFC 5424
 
- **Configuration**
    - Sie müssen über erhöhte Berechtigungen (sudo) auf dem festgelegten Linux-Computer verfügen.
    - Der Linux-Computer darf nicht mit Azure-Arbeitsbereichen verbunden sein, bevor Sie den Log Analytics-Agent installieren.

- **Daten**
    - Möglicherweise benötigen Sie zu einem bestimmten Zeitpunkt in diesem Prozess die **Arbeitsbereich-ID** und den Arbeitsbereich-Primärschlüssel **Ihres Microsoft Sentinel-Arbeitsbereichs**. Sie finden sie in den Arbeitsbereichseinstellungen unter **Agents management**.

### <a name="security-considerations"></a>Sicherheitshinweise

Stellen Sie sicher, dass Sie die Sicherheit des Computers gemäß der Sicherheitsrichtlinie Ihrer Organisation konfigurieren. Beispielsweise können Sie das Netzwerk so konfigurieren, dass es der Sicherheitsrichtlinie für das Unternehmensnetzwerk entspricht, und die Ports und Protokolle im Daemon Ihren Anforderungen entsprechend ändern. Anhand der folgenden Anleitungen können Sie die Sicherheitskonfiguration Ihrer Computer verbessern:  [Sichern und Verwenden von Richtlinien auf virtuellen Computern in Azure](../virtual-machines/security-policy.md), [Bewährte Methoden für die Netzwerksicherheit in Azure](../security/fundamentals/network-best-practices.md).

Wenn Ihre Geräte Syslog- und CEF-Protokolle über TLS senden (z. B. weil sich Ihr Protokoll-Forwarder in der Cloud befindet), müssen Sie den Syslog-Daemon (rsyslog oder syslog-ng) so konfigurieren, dass er über TLS kommuniziert. Siehe die folgende Dokumentation für Details:  
- [Verschlüsselung des Syslog-Datenverkehrs mit TLS – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Verschlüsselung von Protokollnachrichten mit TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="run-the-deployment-script"></a>Ausführen des Bereitstellungsskripts
 
1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus. Wählen Sie in den Konnektorenkatalog den Konnektor für Ihr Produkt aus (oder das **Common Event Format (CEF)** , wenn Ihr Produkt nicht aufgeführt ist), und klicken Sie dann unten rechts auf die Schaltfläche **Konnektorenseite öffnen**. 

1. Kopieren Sie auf der Konnektorenseite in den Anweisungen unter **1.2 Installieren Sie den CEF-Collector auf dem Linux Computer** den unter **Ausführen des folgenden Skripts, um den CEF-Collector zu installieren und anzuwenden**.  
Wenn Sie keinen Zugang zu dieser Seite haben, kopieren Sie den Link aus dem unten stehenden Text (kopieren und fügen Sie die **Arbeitsbereich-ID** und **Primärschlüssel** von oben anstelle der Platzhalter ein):

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```
1. Fügen Sie den Link oder den Text in die Befehlszeile Ihres Protokoll-Forwarders ein, und führen Sie ihn aus.

1. Wenn das Skript ausgeführt wird, stellen Sie sicher, dass keine Fehler- oder Warnmeldungen angezeigt werden.
    - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der Zuordnung im Feld *Computer* zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Bereitstellungsskript](#mapping-command).

1. [Konfigurieren Ihres Gerätes zum Senden von CEF-Nachrichten](connect-common-event-format.md#configure-your-device).

    > [!NOTE]
    > **Verwenden desselben Computers zum Weiterleiten von unformatierten Syslog-Nachrichten *und* CEF-Nachrichten**
    >
    > Wenn Sie beabsichtigen, den Computer mit der Protokollweiterleitung zu verwenden, um [Syslog-Nachrichten](connect-syslog.md) und CEF-Nachrichten weiterzuleiten, gehen Sie folgendermaßen vor, um das Duplizieren von Ereignissen in den Syslog- und CommonSecurityLog-Tabellen zu vermeiden:
    >
    > 1. Sie müssen auf jedem Quellcomputer, der Protokolle im CEF-Format an die Weiterleitung sendet, die Syslog-Konfigurationsdatei bearbeiten, um die Einrichtungen zu entfernen, die zum Senden von CEF-Nachrichten verwendet werden. Auf diese Weise werden die in CEF gesendeten Einrichtungen nicht auch in Syslog gesendet. Detaillierte Anweisungen hierzu finden Sie unter [Konfigurieren von Syslog auf dem Linux-Agent](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent).
    >
    > 1. Sie müssen auf diesen Computern den folgenden Befehl ausführen, um die Synchronisierung des Agents mit der Syslog-Konfiguration in Microsoft Sentinel zu deaktivieren. Dadurch wird sichergestellt, dass die von Ihnen im vorherigen Schritt vorgenommene Konfigurationsänderung nicht überschrieben wird.<br>
    > `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="deployment-script-explained"></a>Erläuterung des Bereitstellungsskripts

Im Folgenden finden Sie eine Beschreibung der Aktionen des Bereitstellungsskripts in einzelnen Befehlen.

Wählen Sie einen Syslog-Daemon aus, um die entsprechende Beschreibung anzuzeigen.

# <a name="rsyslog-daemon"></a>[rsyslog-Daemon](#tab/rsyslog)

1. **Herunterladen und Installieren des Log Analytics-Agents:**

    - Herunterladen des Installationsskripts für den Log Analytics-Linux-Agent (OMS)

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Installieren des Log Analytics-Agents
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Festlegen der Konfiguration des Log Analytics-Agents zum Lauschen an Port 25226 und Weiterleiten von CEF-Nachrichten an Microsoft Sentinel:**

    - Herunterladen der Konfiguration aus dem GitHub-Repository für den Log Analytics-Agent

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurieren des Syslog-Daemons:**

    - Öffnen von Port 514 für TCP-Kommunikation mithilfe der Syslog-Konfigurationsdatei `/etc/rsyslog.conf`

    - Konfigurieren des Daemons zum Weiterleiten von CEF-Nachrichten an den Log Analytics-Agent an TCP-Port 25226 durch Einfügen einer speziellen Konfigurationsdatei `security-config-omsagent.conf` in das Verzeichnis `/etc/rsyslog.d/` des Syslog-Daemons

        Inhalt der Datei `security-config-omsagent.conf`:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Neustarten des Syslog-Daemons und des Log Analytics-Agents:**

    - Neustarten des rsyslog-Daemons
    
        ```bash
        service rsyslog restart
        ```

    - Neustarten des Log Analytics-Agents

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Überprüfen, ob das Feld *Computer* wie erwartet zugeordnet wurde:**

    - Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle ordnungsgemäß im Log Analytics-Agent zugeordnet ist: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Herunterladen und Installieren des Log Analytics-Agents:**

    - Herunterladen des Installationsskripts für den Log Analytics-Linux-Agent (OMS)

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Installieren des Log Analytics-Agents
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Festlegen der Konfiguration des Log Analytics-Agents zum Lauschen an Port 25226 und Weiterleiten von CEF-Nachrichten an Microsoft Sentinel:**

    - Herunterladen der Konfiguration aus dem GitHub-Repository für den Log Analytics-Agent

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurieren des Syslog-Daemons:**

    - Öffnen von Port 514 für TCP-Kommunikation mithilfe der Syslog-Konfigurationsdatei `/etc/syslog-ng/syslog-ng.conf`

    - Konfigurieren des Daemons zum Weiterleiten von CEF-Nachrichten an den Log Analytics-Agent an TCP-Port 25226 durch Einfügen einer speziellen Konfigurationsdatei `security-config-omsagent.conf` in das Verzeichnis `/etc/syslog-ng/conf.d/` des Syslog-Daemons

        Inhalt der Datei `security-config-omsagent.conf`:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Neustarten des Syslog-Daemons und des Log Analytics-Agents:**

    - Neustarten des syslog-ng-Daemons
    
        ```bash
        service syslog-ng restart
        ```

    - Neustarten des Log Analytics-Agents

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Überprüfen, ob das Feld *Computer* wie erwartet zugeordnet wurde:**

    - Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle ordnungsgemäß im Log Analytics-Agent zugeordnet ist: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie den Log Analytics-Agent bereitstellen, um CEF-Appliances mit Microsoft Sentinel zu verbinden. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie mehr über die [CEF- und CommonSecurityLog-Feldzuordnung](cef-name-mapping.md).
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
