---
title: Syslog-Daten mit Microsoft Sentinel verbinden | Microsoft Docs
description: Verbinden Sie jede Maschine oder Appliance, die Syslog unterstützt, mit Microsoft Sentinel, indem Sie einen Agenten auf einem Linux-Rechner zwischen der Appliance und Microsoft Sentinel einsetzen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2342c2b3959ecb4ff9c174e769ce27f06ff36483
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524548"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** ist ein gängiges Protokoll zur Ereignisprotokollierung für Linux. Sie können den in Linux-Geräte und -Appliances integrierten Syslog-Daemon verwenden, um lokale Ereignisse der von Ihnen angegebenen Typen zu sammeln, und diese Ereignisse mithilfe des **Log Analytics-Agenten für Linux** an Microsoft Sentinel senden. (früher bekannt als OMS-Agent).

Dieser Artikel beschreibt, wie Sie Ihre Datenquellen über Syslog mit Microsoft Sentinel verbinden können. Weitere Informationen zu unterstützten Connectors für diese Methode finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

## <a name="architecture"></a>Aufbau

Wenn der Log Analytics-Agent auf Ihrer VM oder Ihrer Appliance installiert ist, konfiguriert das Installationsskript den lokalen Syslog-Daemon für die Weiterleitung von Nachrichten an den Agent an UDP-Port 25224. Nachdem der Agent die Nachrichten empfangen hat, sendet er sie über HTTPS an Ihren Log Analytics-Arbeitsbereich, wo sie in die Syslog-Tabelle in **Microsoft Sentinel > Logs** aufgenommen werden.

Weitere Informationen finden Sie unter [Syslog-Datenquellen in Azure Monitor](../azure-monitor/agents/data-sources-syslog.md).

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="Dieses Diagramm zeigt den Datenfluss von Syslog-Quellen zum Microsoft Sentinel-Arbeitsbereich, wo der Log Analytics-Agent direkt auf dem Datenquellengerät installiert ist.":::

Bei einigen Gerätetypen, die die lokale Installation des Log Analytics-Agents nicht zulassen, kann der Agent stattdessen auf einer dedizierten Linux-basierten Protokollweiterleitung installiert werden. Das Ursprungsgerät muss so konfiguriert sein, dass Syslog-Ereignisse an den Syslog-Daemon in dieser Weiterleitung statt an den lokalen Daemon gesendet werden. Der Syslog-Daemon in der Weiterleitung sendet Ereignisse über UDP an den Log Analytics-Agent. Wenn diese Linux-Weiterleitung wahrscheinlich eine große Menge von Syslog-Ereignissen erfasst, sendet der Syslog-Daemon Ereignisse stattdessen über TCP an den Agent. In beiden Fällen sendet der Agent die Ereignisse von dort an Ihren Log Analytics-Arbeitsbereich in Microsoft Sentinel.

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="Dieses Diagramm zeigt den Datenfluss von Syslog-Quellen zum Microsoft Sentinel-Arbeitsbereich, wo der Log Analytics-Agent auf einem separaten Log-Forwarding-Gerät installiert ist.":::

> [!NOTE]
> - Wenn Ihre Appliance das **Common Event Format (CEF) über Syslog** unterstützt, wird ein umfangreicheres Dataset erfasst, und die Daten werden bei der Erfassung analysiert. Sie sollten diese Option auswählen und die Anweisungen unter [CEF-formatierte Protokolle von Ihrem Gerät oder Ihrer Appliance in Microsoft Sentinel abrufen](connect-common-event-format.md) befolgen.
>
> - Log Analytics unterstützt die Erfassung der von den Daemons **rsyslog** oder **syslog-ng** gesendeten Meldungen, wobei „rsyslog“ der Standarddaemon ist. Der standardmäßige Syslog-Daemon in Version 5 von Red Hat Enterprise Linux (RHEL), CentOS und in der Oracle Linux-Version (**sysklog**) wird für die Erfassung von Syslog-Ereignissen *nicht unterstützt*. Der rsyslog-Daemon sollte installiert und so konfiguriert werden, dass er sysklog ersetzt, um syslog-Daten von dieser Version dieser Verteilung zu sammeln.

Es gibt drei Schritte zum Konfigurieren der Syslog-Sammlung:

- **Konfigurieren Sie Ihren Linux-Computer oder Ihre Linux-Appliance**. Damit ist das Gerät gemeint, auf dem der Log Analytics-Agent installiert wird, unabhängig davon, ob es sich um dasselbe Gerät handelt, das die Ereignisse generiert, oder um einen Protokollsammler, der sie weiterleitet.

- **Konfigurieren Sie die Protokollierungseinstellungen Ihrer Anwendung** entsprechend dem Speicherort des Syslog-Daemons, der Ereignisse an den Agent senden soll.

- **Konfigurieren Sie den Log Analytics-Agent selbst**. Dies geschieht innerhalb von Microsoft Sentinel, und die Konfiguration wird an alle installierten Agenten gesendet.

## <a name="configure-your-linux-machine-or-appliance"></a>Konfigurieren Ihres Computers oder Ihrer Appliance unter Linux

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Datenkonnektoren**.

1. Wählen Sie im Connectorkatalog **Syslog** aus, und klicken Sie anschließend auf **Connectorseite öffnen**.

    Wenn Ihr Gerätetyp in der Galerie der Microsoft Sentinel **Datenkonnektoren** aufgeführt ist, wählen Sie den Konnektor für Ihr Gerät anstelle des generischen Syslog-Konnektors. Wenn es zusätzliche oder spezielle Anweisungen für Ihren Gerätetyp gibt, werden diese zusammen mit benutzerdefinierten Inhalten wie Arbeitsmappen und Analyseregelvorlagen auf der Connectorseite für Ihr Gerät angezeigt.

1. Installieren Sie den Linux-Agent. Unter **Wählen Sie aus, wo der Agent installiert werden soll:**

    |Computertyp  |Anweisungen  |
    |---------|---------|
    |**Für eine Azure-Linux-VM**     |    1. Erweitern Sie **Agent auf virtuellem Linux-Computer (Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für virtuelle Linux-Computer (Azure) herunterladen und installieren >** .<br><br>3. Klicken Sie auf dem Blatt **Virtuelle Computer** auf eine VM, auf der der Agent installiert werden soll, und klicken Sie dann auf **Verbinden**. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten.     |
    |**Für jeden weiteren Linux-Computer**     |     1. Erweitern Sie **Agent auf einem Linux-Computer (kein Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für Linux-Computer (kein Azure) herunterladen und installieren**.<br><br>3. Klicken Sie auf dem Blatt **Agent-Verwaltung** auf die Registerkarte **Linux-Server**, kopieren Sie dann den Befehl für **Agent für Linux herunterladen und Onboarding durchführen**, und führen Sie ihn auf Ihrem Linux-Computer aus.<br><br>        Wenn Sie eine lokale Kopie der Linux-Agent-Installationsdatei beibehalten möchten, klicken Sie auf den Link **Linux-Agent herunterladen** über dem Befehl „Agent herunterladen und integrieren“ aus. |
    |     |         |

   > [!NOTE]
   > Konfigurieren Sie die Sicherheitseinstellungen für diese Geräte entsprechend der Sicherheitsrichtlinie Ihrer Organisation. Beispielsweise können Sie die Netzwerkeinstellungen so konfigurieren, dass sie der Sicherheitsrichtlinie für das Netzwerk der Organisation entsprechen, und die Ports und Protokolle im Daemon Ihren Sicherheitsanforderungen entsprechend ändern.

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>Verwenden desselben Computers zum Weiterleiten von unformatierten Syslog-Nachrichten *und* CEF-Nachrichten

Sie können Ihren vorhandenen [Computer für die CEF-Protokollweiterleitung](connect-log-forwarder.md) verwenden, um Protokolle auch aus einfachen Syslog-Quellen zu erfassen und weiterzuleiten. Sie müssen jedoch die folgenden Schritte ausführen, um zu vermeiden, dass Ereignisse in beiden Formaten an Microsoft Sentinel gesendet werden, da dies zu einer Verdoppelung der Ereignisse führen würde.

Wenn Sie die [Datensammlung von Ihren CEF-Quellen](connect-common-event-format.md) eingerichtet und den Log Analytics-Agent konfiguriert haben, gehen Sie folgendermaßen vor:

1. Sie müssen auf jedem Computer, der Protokolle im CEF-Format sendet, die Syslog-Konfigurationsdatei bearbeiten, um die Einrichtungen zu entfernen, die zum Senden von CEF-Meldungen verwendet werden. Auf diese Weise werden die in CEF gesendeten Einrichtungen nicht auch in Syslog gesendet. Detaillierte Anweisungen hierzu finden Sie unter [Konfigurieren von Syslog auf dem Linux-Agent](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent).

1. Sie müssen den folgenden Befehl auf diesen Rechnern ausführen, um die Synchronisierung des Agenten mit der Syslog-Konfiguration in Microsoft Sentinel zu deaktivieren. Dadurch wird sichergestellt, dass die von Ihnen im vorherigen Schritt vorgenommene Konfigurationsänderung nicht überschrieben wird.

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>Konfigurieren der Protokollierungseinstellungen Ihres Geräts

Für viele Gerätetypen werden eigene Datenconnectors im Katalog für **Datenconnectors** angezeigt. Einige dieser Konnektoren erfordern spezielle zusätzliche Anweisungen, um die Protokollsammlung in Microsoft Sentinel ordnungsgemäß einzurichten. Diese Anweisungen können die Implementierung eines Parsers basierend auf einer Kusto-Funktion beinhalten.

Alle in der Galerie aufgeführten Konnektoren zeigen alle spezifischen Anweisungen auf ihren jeweiligen Konnektorenseiten im Portal sowie in ihren Abschnitten auf der Seite [Microsoft Sentinel Datenkonnektoren Referenz](data-connectors-reference.md).


## <a name="configure-the-log-analytics-agent"></a>Konfigurieren des Log Analytics-Agent

1. Klicken Sie unten auf dem Blatt des Syslog-Connectors auf den Link **Konfiguration der Arbeitsbereichs-Agents öffnen >** .

1. Wählen Sie auf dem Blatt **Agents-Konfiguration** die Registerkarte **Syslog** aus. Fügen Sie dann die Einrichtungen hinzu, die der Connector erfassen soll. Wählen Sie **Einrichtung hinzufügen** und dann in der Dropdownliste die Einrichtungen aus.

    - Fügen Sie die Einrichtungen hinzu, die Ihre Syslog-Appliance in ihren Protokollheadern enthält.

    - Wenn Sie für die zu sammelnden Daten die Erkennung ungewöhnlicher SSH-Anmeldungen verwenden möchten, fügen Sie **auth** und **authpriv** hinzu. Im [folgenden Abschnitt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) finden Sie weitere Details.

1. Wenn Sie alle Elemente hinzugefügt haben, die Sie überwachen möchten, deaktivieren Sie die Kontrollkästchen für alle Schweregrade, die Sie nicht erfassen möchten. Standardmäßig sind sie alle aktiviert.

1. Klicken Sie auf **Übernehmen**.

1. Stellen Sie auf Ihrem virtuellen Computer oder der Appliance sicher, dass die von Ihnen angegebenen Einrichtungen gesendet werden.

## <a name="find-your-data"></a>Suchen von Daten

1. Um die Syslog-Protokolldaten in **Protokolle** abzufragen, geben Sie `Syslog` in das Abfragefenster ein.

1. Sie können mit den in [Verwenden von Funktionen in Azure Monitor-Protokollabfragen](../azure-monitor/logs/functions.md) beschriebenen Abfrageparametern Ihre Syslog-Meldungen analysieren. Sie können die Abfrage dann als neue Log Analytics-Funktion speichern und als neuen Datentyp verwenden.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurieren des Syslog-Connectors für die Erkennung ungewöhnlicher SSH-Anmeldungen

> [!IMPORTANT]
> Die Erkennung ungewöhnlicher SSH-Anmeldungen befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Microsoft Sentinel kann maschinelles Lernen (ML) auf die Syslog-Daten anwenden, um anomale Secure Shell (SSH)-Anmeldeaktivitäten zu identifizieren. Mögliche Szenarien:

- Unmöglicher Ortswechsel – wenn zwei erfolgreiche Anmeldeereignisse von zwei Standorten aus auftreten, die innerhalb des Zeitraums zwischen den beiden Anmeldeereignissen nicht erreichbar sind.

- Unerwarteter Standort – der Standort, von dem aus eine erfolgreiche Anmeldung durchgeführt wurde, ist verdächtig. Beispielsweise ist der Standort in jüngster Zeit nicht aufgetreten.
 
Diese Erkennung erfordert eine bestimmte Konfiguration des Syslog-Datenconnectors: 

1. Vergewissern Sie sich in Schritt 2 unter [Konfigurieren des Log Analytics-Agents](#configure-the-log-analytics-agent), dass **auth** und **authpriv** als zu überwachende Einrichtungen und dazu alle Schweregrade ausgewählt sind. 

2. Lassen Sie ausreichend Zeit zum Sammeln der Syslog-Informationen. Navigieren Sie dann zu **Microsoft Sentinel - Logs**, und kopieren Sie die folgende Abfrage und fügen Sie sie ein:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Ändern Sie bei Bedarf den **Zeitbereich**, und wählen Sie **Ausführen** aus.
    
    Wenn die resultierende Anzahl 0 (null) ist, überprüfen Sie die Konfiguration des Connectors, und überprüfen Sie, ob in dem Zeitraum, den Sie für die Abfrage angegeben haben, auf den überwachten Computern erfolgreiche Anmeldungen durchgeführt wurden.
    
    Wenn die resultierende Anzahl größer als 0 (null) ist, eignen sich die Syslog-Daten für die Erkennung ungewöhnlicher SSH-Anmeldungen. Sie aktivieren diese Erkennung über **Analyse** >  **Rule templates** (Regelvorlagen)  >  **(Preview) Anomalous SSH Login Detection** ((Vorschau) Erkennung ungewöhnlicher SSH-Anmeldungen).

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie lokale Syslog-Appliances mit Microsoft Sentinel verbinden. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Erste Schritte [Erkennung von Bedrohungen mit Microsoft Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
