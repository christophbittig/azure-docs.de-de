---
title: Abrufen von Protokollen im CEF-Format von Ihrem Gerät oder Ihrer Appliance in Microsoft Sentinel | Microsoft-Dokumentation
description: Verwenden Sie den Log Analytics-Agent, der auf einer Linux-basierten Protokollweiterleitung installiert ist, um Protokolle zu erfassen, die im CEF-Format (Common Event Format) über Syslog in Ihren Microsoft Sentinel-Arbeitsbereich gesendet werden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 734ed8aaa1ad3557981c76f380227e7574861086
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521888"
---
# <a name="get-cef-formatted-logs-from-your-device-or-appliance-into-microsoft-sentinel"></a>Abrufen von Protokollen im CEF-Format von Ihrem Gerät oder Ihrer Appliance in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Viele Netzwerk- und Sicherheitsgeräte und -Appliances senden ihre Systemprotokolle über das Syslog-Protokoll in einem speziellen Format, das als Common Event Format (CEF) bezeichnet wird. Dieses Format enthält mehr Informationen als das Syslog-Standardformat und stellt diese in einer analysierten Schlüssel-Wert-Anordnung dar. Der Log Analytics-Agent akzeptiert CEF-Protokolle und formatiert sie speziell für die Verwendung mit Microsoft Sentinel, bevor er sie an Ihren Microsoft Sentinel-Arbeitsbereich weiterleitet.

In diesem Artikel wird beschrieben, wie Sie Protokolle im CEF-Format verwenden, um eine Verbindung mit Ihren Datenquellen herzustellen. Informationen zu Datenconnectors, die diese Methode verwenden, finden Sie unter [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md).

Es gibt zwei Hauptschritte zum Herstellen dieser Verbindung, die im Folgenden ausführlich erläutert werden:

- Sie müssen einen Linux-Computer oder einen virtuellen Computer als dedizierte Protokollweiterleitung bestimmen, den Log Analytics-Agent darauf installieren und den Agent so konfigurieren, dass die Protokolle an Ihren Microsoft Sentinel-Arbeitsbereich weitergeleitet werden. Die Installation und Konfiguration des Agents werden von einem Bereitstellungsskript verarbeitet.

- Sie müssen Ihr Gerät für das Senden seiner Protokolle im CEF-Format an einen Syslog-Server konfigurieren.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Microsoft Sentinel ausführen.

## <a name="supported-architectures"></a>Unterstützte Architekturen

Die folgende Abbildung beschreibt die Einrichtung mit einer Linux-VM in Azure:

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativ verwenden Sie das folgende Setup, wenn Sie eine VM in einer anderen Cloud oder auf einem lokalen Computer verwenden:

 ![CEF lokal](./media/connect-cef/cef-syslog-onprem.png)

## <a name="prerequisites"></a>Voraussetzungen

Für das Erfassen von CEF-Daten in Log Analytics wird ein Microsoft Sentinel-Arbeitsbereich benötigt.

- Sie müssen über Lese- und Schreibberechtigungen für diesen Arbeitsbereich verfügen.

- Sie müssen über Leseberechtigungen für die freigegebenen Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/agents/agent-windows.md)

## <a name="designate-a-log-forwarder-and-install-the-log-analytics-agent"></a>Festlegen einer Protokollweiterleitung und Installieren des Log Analytics-Agents

In diesem Abschnitt wird beschrieben, wie Sie den Linux-Computer bestimmen und konfigurieren, der die Protokolle von Ihrem Gerät an Ihren Microsoft Sentinel-Arbeitsbereich weiterleiten soll.

Bei Ihrem Linux-Computer kann es sich um einen physischen oder virtuellen Computer in Ihrer lokalen Umgebung, eine Azure-VM oder eine VM in einer anderen Cloud handeln.

Verwenden Sie den Link auf der **CEF-Datenconnectorseite (Common Event Format)** , um ein Skript auf dem dedizierten Computer auszuführen und die folgenden Aufgaben auszuführen:

- **Installiert den Log Analytics-Agent für Linux** (auch als OMS-Agent bezeichnet) und konfiguriert ihn für die folgenden Zwecke:
    - Lauschen auf CEF-Nachrichten vom integrierten Linux-Syslog-Daemon am TCP-Port 25226
    - Sicheres Senden der Nachrichten über TLS an Ihren Microsoft Sentinel-Arbeitsbereich, wo sie analysiert und ergänzt werden

- **Konfiguriert den integrierten Linux-Syslog-Daemon** (rsyslog.d/syslog-ng) für die folgenden Zwecke:
    - Lauschen auf Syslog-Nachrichten von Ihren Sicherheitslösungen an TCP-Port 514
    - Weiterleiten nur der Nachrichten, die mit dem Log Analytics-Agent auf localhost als CEF identifiziert werden, über TCP-Port 25226

Weitere Informationen finden Sie unter [Bereitstellung einer Protokollweiterleitung zum Erfassen von Syslog- und CEF-Protokollen in Microsoft Sentinel](connect-log-forwarder.md).

### <a name="security-considerations"></a>Sicherheitshinweise

Stellen Sie sicher, dass Sie die Sicherheit des Computers gemäß der Sicherheitsrichtlinie Ihrer Organisation konfigurieren. Beispielsweise können Sie das Netzwerk so konfigurieren, dass es der Sicherheitsrichtlinie für das Unternehmensnetzwerk entspricht, und die Ports und Protokolle im Daemon Ihren Anforderungen entsprechend ändern.

Weitere Informationen finden Sie unter [Sichern einer VM in Azure](../virtual-machines/security-policy.md) und [Bewährte Methoden für die Netzwerksicherheit](../security/fundamentals/network-best-practices.md).

Wenn Ihre Geräte Syslog- und CEF-Protokolle über TLS senden (z. B. wenn sich Ihre Protokollweiterleitung in der Cloud befindet), müssen Sie den Syslog-Daemon (rsyslog oder syslog-ng) so konfigurieren, dass er über TLS kommuniziert. 

Weitere Informationen finden Sie unter:

- [Verschlüsselung des Syslog-Datenverkehrs mit TLS – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Verschlüsselung von Protokollnachrichten mit TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="configure-your-device"></a>Konfigurieren Ihres Geräts

Suchen Sie nach den Konfigurationsanweisungen Ihres Geräteanbieters zum Senden von Protokollen im CEF-Format an einen SIEM- oder Protokollserver, und befolgen Sie diese. 

Wenn Ihr Produkt im Katalog der Datenconnectors angezeigt wird, können Sie die [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md) lesen, um Unterstützung zu erhalten, wobei die Konfigurationsanweisungen die Einstellungen aus der folgenden Liste enthalten sollten.

   - Protokoll = TCP
   - Port = 514
   - Format = CEF
   - IP-Adresse: Achten Sie darauf, die CEF-Nachrichten an die IP-Adresse des virtuellen Computers zu senden, den Sie für diesen Zweck reserviert haben.

Diese Lösung unterstützt Syslog RFC 3164 und RFC 5424.

> [!TIP]
> Definieren Sie bei Bedarf ein anderes Protokoll oder eine andere Portnummer auf Ihrem Gerät, solange Sie auch dieselben Änderungen am Syslog-Daemon auf der Protokollweiterleitung vornehmen.
>

## <a name="find-your-data"></a>Suchen von Daten

Nach Herstellung der Verbindung kann es bis zu 20 Minuten dauern, bis Daten in Log Analytics angezeigt werden.

Wenn Sie in Log Analytics nach CEF-Ereignissen möchten, fragen Sie die Tabelle `CommonSecurityLog` im Abfragefenster ab.

Einige Produkte, die im Katalog der Datenconnectors aufgeführt sind, erfordern die Verwendung zusätzlicher Parser, um optimale Ergebnisse zu erzielen. Diese Parser werden durch die Verwendung von Kusto-Funktionen implementiert. Weitere Informationen finden Sie im Abschnitt zu Ihrem Produkt auf der Seite [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md).

Um CEF-Ereignisse für diese Produkte zu finden, geben Sie den Namen der Kusto-Funktion als Abfragebetreff anstelle von „CommonSecurityLog“ ein.

Hilfreiche Beispielabfragen, Arbeitsmappen und Analyseregelvorlagen, die speziell für Ihr Produkt erstellt wurden, finden Sie auf der Registerkarte **Nächste Schritte** der Datenconnectorseite Ihres Produkts im Microsoft Sentinel-Portal.

Wenn keine Daten angezeigt werden, finden Sie eine Anleitung auf der [CEF-Problembehandlungsseite](./troubleshooting-cef-syslog.md).

### <a name="changing-the-source-of-the-timegenerated-field"></a>Ändern der Quelle des Felds „TimeGenerated“

Standardmäßig füllt der Log Analytics-Agent das Feld *TimeGenerated* im Schema mit der Zeit auf, zur der der Agent das Ereignis vom Syslog-Daemon empfangen hat. Folglich wird die Zeit, zu der das Ereignis im Quellsystem generiert wurde, nicht in Microsoft Sentinel erfasst.

Sie können jedoch den folgenden Befehl ausführen, um das Skript `TimeGenerated.py` herunterzuladen und auszuführen. Mit diesem Skript wird der Log Analytics-Agent so konfiguriert, dass das Feld *TimeGenerated* mit der ursprünglichen Zeit des Ereignisses aus dem Quellsystem aufgefüllt wird, und nicht mit der Zeit, zu der es vom Agent empfangen wurde.

```bash
wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Microsoft Sentinel CEF-Protokolle von Geräten und Appliances sammelt. Weitere Informationen zum Verbinden Ihres Produkts mit Microsoft Sentinel finden Sie in den folgenden Artikeln:

- [Bereitstellen einer Syslog-/CEF-Weiterleitung](connect-log-forwarder.md)
- [Referenz zu Microsoft Sentinel-Datenconnectors](data-connectors-reference.md)
- [Problembehandlung der Konnektivität der Protokollweiterleitung](troubleshooting-cef-syslog.md#validate-cef-connectivity)

Weitere Informationen zu Aktivitäten, die Sie mit den in Microsoft Sentinel gesammelten Daten ausführen können, finden Sie in den folgenden Artikeln:

- Erfahren Sie mehr über die [CEF- und CommonSecurityLog-Feldzuordnung](cef-name-mapping.md).
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
