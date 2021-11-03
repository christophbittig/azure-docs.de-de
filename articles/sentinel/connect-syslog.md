---
title: Verbinden von Syslog-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Verbinden Sie einen Computer oder eine Appliance mit Syslog-Unterstützung mit Azure Sentinel, indem Sie einen Agent auf einem Linux-Computer zwischen der Appliance und Azure Sentinel verwenden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9e9460620c3d18c8e7d35663e5e89ca7aa024360
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004327"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** ist ein gängiges Protokoll zur Ereignisprotokollierung für Linux. Sie können den in Linux-Geräten und -Appliances integrierten Syslog-Daemon verwenden, um lokale Ereignisse der von Ihnen angegebenen Typen zu erfassen und über den **Log Analytics-Agent für Linux** (ehemals OMS-Agent) an Azure Sentinel zu senden.

In diesem Artikel wird beschrieben, wie Sie Ihre Datenquellen über Syslog mit Azure Sentinel verbinden. Weitere Informationen zu unterstützten Connectors für diese Methode finden Sie in der [Referenz zu Datenconnectors](data-connectors-reference.md).

## <a name="architecture"></a>Aufbau

Wenn der Log Analytics-Agent auf Ihrer VM oder Ihrer Appliance installiert ist, konfiguriert das Installationsskript den lokalen Syslog-Daemon für die Weiterleitung von Nachrichten an den Agent an UDP-Port 25224. Wenn er die Nachrichten erhalten hat, sendet der Agent diese über HTTPS an Ihren Log Analytics-Arbeitsbereich, wo sie in der Syslog-Tabelle unter **Azure Sentinel > Protokolle** erfasst werden.

Weitere Informationen finden Sie unter [Syslog-Datenquellen in Azure Monitor](../azure-monitor/agents/data-sources-syslog.md).

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="Dieses Diagramm zeigt den Datenfluss von Syslog-Quellen zum Azure Sentinel-Arbeitsbereich, wobei der Log Analytics-Agent direkt auf dem Datenquellengerät installiert wird.":::

Bei einigen Gerätetypen, die die lokale Installation des Log Analytics-Agents nicht zulassen, kann der Agent stattdessen auf einer dedizierten Linux-basierten Protokollweiterleitung installiert werden. Das Ursprungsgerät muss so konfiguriert sein, dass Syslog-Ereignisse an den Syslog-Daemon in dieser Weiterleitung statt an den lokalen Daemon gesendet werden. Der Syslog-Daemon in der Weiterleitung sendet Ereignisse über UDP an den Log Analytics-Agent. Wenn diese Linux-Weiterleitung wahrscheinlich eine große Menge von Syslog-Ereignissen erfasst, sendet der Syslog-Daemon Ereignisse stattdessen über TCP an den Agent. In beiden Fällen sendet der Agent anschließend die Ereignisse von dort aus an Ihren Log Analytics-Arbeitsbereich in Azure Sentinel.

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="Dieses Diagramm zeigt den Datenfluss von Syslog-Quellen zum Azure Sentinel-Arbeitsbereich, wobei der Log Analytics-Agent auf einem separaten Gerät zur Protokollweiterleitung installiert wird.":::

> [!NOTE]
> - Wenn Ihre Appliance das **Common Event Format (CEF) über Syslog** unterstützt, wird ein umfangreicheres Dataset erfasst, und die Daten werden bei der Erfassung analysiert. Wählen Sie diese Option aus, und befolgen Sie die Anweisungen unter [Abrufen von Protokollen im CEF-Format von Ihrem Gerät oder Ihrer Appliance in Azure Sentinel](connect-common-event-format.md).
>
> - Log Analytics unterstützt die Erfassung der von den Daemons **rsyslog** oder **syslog-ng** gesendeten Meldungen, wobei „rsyslog“ der Standarddaemon ist. Der standardmäßige Syslog-Daemon in Version 5 von Red Hat Enterprise Linux (RHEL), CentOS und in der Oracle Linux-Version (**sysklog**) wird für die Erfassung von Syslog-Ereignissen *nicht unterstützt*. Der rsyslog-Daemon sollte installiert und so konfiguriert werden, dass er sysklog ersetzt, um syslog-Daten von dieser Version dieser Verteilung zu sammeln.

Es gibt drei Schritte zum Konfigurieren der Syslog-Sammlung:

- **Konfigurieren Sie Ihren Linux-Computer oder Ihre Linux-Appliance**. Damit ist das Gerät gemeint, auf dem der Log Analytics-Agent installiert wird, unabhängig davon, ob es sich um dasselbe Gerät handelt, das die Ereignisse generiert, oder um einen Protokollsammler, der sie weiterleitet.

- **Konfigurieren Sie die Protokollierungseinstellungen Ihrer Anwendung** entsprechend dem Speicherort des Syslog-Daemons, der Ereignisse an den Agent senden soll.

- **Konfigurieren Sie den Log Analytics-Agent selbst**. Dies erfolgt innerhalb von Azure Sentinel, und die Konfiguration wird an alle installierten Agents gesendet.

## <a name="configure-your-linux-machine-or-appliance"></a>Konfigurieren Ihres Computers oder Ihrer Appliance unter Linux

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im Connectorkatalog **Syslog** aus, und klicken Sie anschließend auf **Connectorseite öffnen**.

    Wenn Ihr Gerätetyp im **Datenconnectorkatalog** von Azure Sentinel aufgeführt ist, wählen Sie anstelle des generischen Syslog-Connectors den Connector für Ihr Gerät aus. Wenn es zusätzliche oder spezielle Anweisungen für Ihren Gerätetyp gibt, werden diese zusammen mit benutzerdefinierten Inhalten wie Arbeitsmappen und Analyseregelvorlagen auf der Connectorseite für Ihr Gerät angezeigt.

1. Installieren Sie den Linux-Agent. Unter **Wählen Sie aus, wo der Agent installiert werden soll:**

    |Computertyp  |Anweisungen  |
    |---------|---------|
    |**Für eine Azure-Linux-VM**     |    1. Erweitern Sie **Agent auf virtuellem Linux-Computer (Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für virtuelle Linux-Computer (Azure) herunterladen und installieren >** .<br><br>3. Klicken Sie auf dem Blatt **Virtuelle Computer** auf eine VM, auf der der Agent installiert werden soll, und klicken Sie dann auf **Verbinden**. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten.     |
    |**Für jeden weiteren Linux-Computer**     |     1. Erweitern Sie **Agent auf einem Linux-Computer (kein Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für Linux-Computer (kein Azure) herunterladen und installieren**.<br><br>3. Klicken Sie auf dem Blatt **Agent-Verwaltung** auf die Registerkarte **Linux-Server**, kopieren Sie dann den Befehl für **Agent für Linux herunterladen und Onboarding durchführen**, und führen Sie ihn auf Ihrem Linux-Computer aus.<br><br>        Wenn Sie eine lokale Kopie der Linux-Agent-Installationsdatei beibehalten möchten, klicken Sie auf den Link **Linux-Agent herunterladen** über dem Befehl „Agent herunterladen und integrieren“ aus. |
    |     |         |

   > [!NOTE]
   > Konfigurieren Sie die Sicherheitseinstellungen für diese Geräte entsprechend der Sicherheitsrichtlinie Ihrer Organisation. Beispielsweise können Sie die Netzwerkeinstellungen so konfigurieren, dass sie der Sicherheitsrichtlinie für das Netzwerk der Organisation entsprechen, und die Ports und Protokolle im Daemon Ihren Sicherheitsanforderungen entsprechend ändern.

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>Verwenden desselben Computers zum Weiterleiten von unformatierten Syslog-Nachrichten *und* CEF-Nachrichten

Sie können Ihren vorhandenen [Computer für die CEF-Protokollweiterleitung](connect-log-forwarder.md) verwenden, um Protokolle auch aus einfachen Syslog-Quellen zu erfassen und weiterzuleiten. Sie müssen jedoch die folgenden Schritte ausführen, um zu vermeiden, dass Ereignisse in beiden Formaten an Azure Sentinel gesendet werden, da dies zu einer Duplizierung von Ereignissen führt.

Wenn Sie die [Datensammlung von Ihren CEF-Quellen](connect-common-event-format.md) eingerichtet und den Log Analytics-Agent konfiguriert haben, gehen Sie folgendermaßen vor:

1. Sie müssen auf jedem Computer, der Protokolle im CEF-Format sendet, die Syslog-Konfigurationsdatei bearbeiten, um die Einrichtungen zu entfernen, die zum Senden von CEF-Meldungen verwendet werden. Auf diese Weise werden die in CEF gesendeten Einrichtungen nicht auch in Syslog gesendet. Detaillierte Anweisungen hierzu finden Sie unter [Konfigurieren von Syslog auf dem Linux-Agent](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent).

1. Sie müssen auf diesen Computern den folgenden Befehl ausführen, um die Synchronisierung des Agents mit der Syslog-Konfiguration in Azure Sentinel zu deaktivieren. Dadurch wird sichergestellt, dass die von Ihnen im vorherigen Schritt vorgenommene Konfigurationsänderung nicht überschrieben wird.

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>Konfigurieren der Protokollierungseinstellungen Ihres Geräts

Für viele Gerätetypen werden eigene Datenconnectors im Katalog für **Datenconnectors** angezeigt. Für einige dieser Connectors gelten spezielle zusätzliche Anweisungen zum ordnungsgemäßen Einrichten der Protokollsammlung in Azure Sentinel. Diese Anweisungen können die Implementierung eines Parsers basierend auf einer Kusto-Funktion beinhalten.

Für alle im Katalog aufgeführten Connectors werden spezifische Anweisungen ggf. auf den jeweiligen Connectorseiten im Portal sowie in den entsprechenden Abschnitten der [Referenzseite zu Azure Sentinel-Datenconnectors](data-connectors-reference.md) angezeigt.


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

Azure Sentinel kann Machine Learning (ML) auf die Syslog-Daten anwenden, um ungewöhnliche SSH-Anmeldungen (Secure Shell) zu identifizieren. Mögliche Szenarien:

- Unmöglicher Ortswechsel – wenn zwei erfolgreiche Anmeldeereignisse von zwei Standorten aus auftreten, die innerhalb des Zeitraums zwischen den beiden Anmeldeereignissen nicht erreichbar sind.

- Unerwarteter Standort – der Standort, von dem aus eine erfolgreiche Anmeldung durchgeführt wurde, ist verdächtig. Beispielsweise ist der Standort in jüngster Zeit nicht aufgetreten.
 
Diese Erkennung erfordert eine bestimmte Konfiguration des Syslog-Datenconnectors: 

1. Vergewissern Sie sich in Schritt 2 unter [Konfigurieren des Log Analytics-Agents](#configure-the-log-analytics-agent), dass **auth** und **authpriv** als zu überwachende Einrichtungen und dazu alle Schweregrade ausgewählt sind. 

2. Lassen Sie ausreichend Zeit zum Sammeln der Syslog-Informationen. Navigieren Sie dann zu **Azure Sentinel – Protokolle**, kopieren Sie die folgende Abfrage, und fügen Sie sie ein:
    
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
In diesem Artikel haben Sie gelernt, wie Sie lokale Syslog-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
