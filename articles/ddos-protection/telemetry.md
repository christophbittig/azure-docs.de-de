---
title: Anzeigen und Konfigurieren von DDoS Protection-Telemetrie für Azure DDoS Protection Standard
description: Erfahren Sie, wie Sie DDoS Protection-Telemetrie für Azure DDoS Protection Standard anzeigen und konfigurieren.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 728943aa79e208eb2d6cd6dcdcfaf7876033e2f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347406"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Anzeigen und Konfigurieren der DDoS Protection-Telemetrie

Die Standardversion von Azure DDoS Protection liefert per Analyse von DDoS-Angriffen ausführliche Erkenntnisse zu Angriffen und ermöglicht eine Visualisierung. Kunden, die ihre virtuellen Netzwerke vor DDoS-Angriffen schützen, verfügen über detaillierte Einblicke in den Datenverkehr von Angriffen und die Aktionen, die zur Eindämmung des Angriffs durchgeführt werden. Hierfür werden Berichte zur Angriffsentschärfung und Protokolle zum Verlauf der Entschärfung genutzt. Über Azure Monitor werden umfangreiche Telemetriedaten bereitgestellt. Diese umfassen u. a. detaillierte Metriken während der Dauer eines DDoS-Angriffs. Warnungen können für beliebige durch den DDoS-Schutz verfügbar gemachte Azure Monitor-Metriken konfiguriert werden. Die Protokollierung kann zudem mit [Microsoft Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics und Azure Storage für die erweiterte Analyse über die Schnittstelle für die Azure Monitor-Diagnose integriert werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von DDoS Protection-Telemetriedaten
> * Anzeigen von DDoS-Entschärfungsrichtlinien
> * Überprüfen und Testen von DDoS Protection-Telemetriedaten

### <a name="metrics"></a>Metriken

Die Metriknamen stellen verschiedene Pakettypen und Bytes im Vergleich zu Paketen mit einem grundlegenden Konstrukt von Tagnamen für jede Metrik dar:

- **Gelöschter Tagname** (Beispiel: **Als DDoS eingehende gelöschte Pakete**): Die Anzahl der durch das DDoS-Schutzsystem gelöschten/bereinigten Pakete.
- **Weitergeleiteter Tagname** (Beispiel: **Weitergeleitete als DDoS eingehende Pakete**): Die Anzahl der durch das DDoS-System an das Ziel-VIP weitergeleiteten Pakete – Datenverkehr, der nicht gefiltert wurde.
- **Kein Tagname** (Beispiel: **Als DDoS eingehende Pakete**): Die gesamte Anzahl von Paketen, die in das Bereinigungssystem gelangt sind, welche die Summe der gelöschten und bereinigten Pakete darstellt.

> [!NOTE]
> Im Azure-Portal werden mehrere Optionen für die **Aggregation** angezeigt, aber nur die in der folgenden Tabelle aufgeführten Aggregationstypen werden für jede Metrik unterstützt. Wir entschuldigen uns für diese Unübersichtlichkeit und arbeiten bereits an ihrer Behebung.

Die folgenden [Metriken](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) sind für Azure DDoS Protection verfügbar. Diese Metriken können auch über Diagnoseeinstellungen exportiert werden. (Weitere Informationen finden Sie unter [Anzeigen und Konfigurieren der DDoS-Diagnoseprotokollierung](diagnostic-logging.md).)


| Metrik | Metrikanzeigename | Einheit | Aggregationstyp | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Als DDoS eingehende gelöschte Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende gelöschte Bytes| 
| BytesForwardedDDoS | Weitergeleitete als DDoS eingehende Bytes | Bytes pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende Bytes |
| BytesInDDoS | Als DDoS eingehende Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende Bytes |
| DDoSTriggerSYNPackets | Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen | Anzahl pro Sekunde | Maximum | Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen |
| DDoSTriggerTCPPackets | Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen | Anzahl pro Sekunde | Maximum | Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen |
| DDoSTriggerUDPPackets | Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen | Anzahl pro Sekunde | Maximum | Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen |
| IfUnderDDoSAttack | Unter DDoS-Angriff oder nicht | Anzahl | Maximum | Unter DDoS-Angriff oder nicht |
| PacketsDroppedDDoS | Als DDoS eingehende gelöschte Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende gelöschte Pakete |
| PacketsForwardedDDoS | Weitergeleitete als DDoS eingehende Pakete | Anzahl pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende Pakete |
| PacketsInDDoS | Als DDoS eingehende Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende Pakete |
| TCPBytesDroppedDDoS | Als DDoS eingehende gelöschte TCP-Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende gelöschte TCP-Bytes |
| TCPBytesForwardedDDoS | Weitergeleitete als DDoS eingehende TCP-Bytes | Bytes pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende TCP-Bytes |
| TCPBytesInDDoS | Als DDoS eingehende TCP-Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende TCP-Bytes |
| TCPPacketsDroppedDDoS | Als DDoS eingehende gelöschte TCP-Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende gelöschte TCP-Pakete |
| TCPPacketsForwardedDDoS | Weitergeleitete als DDoS eingehende TCP-Pakete | Anzahl pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende TCP-Pakete |
| TCPPacketsInDDoS | Als DDoS eingehende TCP-Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende TCP-Pakete |
| UDPBytesDroppedDDoS | Als DDoS eingehende gelöschte UDP-Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende gelöschte UDP-Bytes |
| UDPBytesForwardedDDoS | Weitergeleitete als DDoS eingehende UDP-Bytes | Bytes pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende UDP-Bytes |
| UDPBytesInDDoS | Als DDoS eingehende UDP-Bytes | Bytes pro Sekunde | Maximum | Als DDoS eingehende UDP-Bytes |
| UDPPacketsDroppedDDoS | Als DDoS eingehende gelöschte UDP-Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende gelöschte UDP-Pakete |
| UDPPacketsForwardedDDoS | Weitergeleitete als DDoS eingehende UDP-Pakete | Anzahl pro Sekunde | Maximum | Weitergeleitete als DDoS eingehende UDP-Pakete |
| UDPPacketsInDDoS | Als DDoS eingehende UDP-Pakete | Anzahl pro Sekunde | Maximum | Als DDoS eingehende UDP-Pakete |

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Damit Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst einen [Azure DDoS Protection Standard-Schutzplan](manage-ddos-protection.md) erstellen. Außerdem muss DDoS Protection Standard in einem virtuellen Netzwerk aktiviert sein.
- DDoS überwacht öffentliche IP-Adressen, die Ressourcen in einem virtuellen Netzwerk zugewiesen sind. Wenn Sie keine Ressourcen mit öffentlichen IP-Adressen im virtuellen Netzwerk besitzen, müssen Sie zunächst eine Ressource mit einer öffentlichen IP-Adresse erstellen. Sie können die öffentliche IP-Adresse aller Ressourcen (einschließlich Azure Load Balancer-Instanzen, bei denen sich die virtuellen Back-End-Computer im virtuellen Netzwerk befinden) überwachen, die über Resource Manager (nicht klassisch) bereitgestellt werden und unter [virtuelles Netzwerk für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) aufgeführt sind, mit Ausnahme der Ressourcen für Azure App Service-Umgebungen. Um mit diesem Tutorial fortzufahren, können Sie schnell einen virtuellen [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer erstellen.  

## <a name="view-ddos-protection-telemetry"></a>Anzeigen von DDoS Protection-Telemetriedaten

Die Telemetrie für einen Angriff wird in Echtzeit durch Azure Monitor bereitgestellt. Während [Entschärfungstrigger](#view-ddos-mitigation-policies) für TCP SYN, TCP & UDP in Zeiten ohne Vorkommnisse verfügbar sind, sind andere Telemetriedaten nur verfügbar, wenn eine öffentliche IP-Adresse entschärft wurde. 

Sie können DDoS-Telemetriedaten für eine geschützte öffentliche IP-Adresse über drei verschiedene Ressourcentypen anzeigen: DDoS-Schutzplan, virtuelles Netzwerk und öffentliche IP-Adresse.

### <a name="ddos-protection-plan"></a>DDoS-Schutzplan
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem DDoS-Schutzplan.
2. Wählen Sie unter **Überwachung** die Option **Metriken** aus.
3. Wählen Sie **Bereich** aus. Wählen Sie das **Abonnement** aus, das die öffentliche IP-Adresse enthält, die Sie protokollieren möchten, und wählen Sie **Öffentliche IP-Adresse** als **Ressourcentyp** aus. Wählen Sie anschließend die öffentliche IP-Adresse aus, für die Sie Metriken protokollieren möchten, und wählen Sie dann **Anwenden** aus.
4. Wählen Sie als Typ der **Aggregation** die Option **Max** aus. 

### <a name="virtual-network"></a>Virtuelles Netzwerk
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem virtuellen Netzwerk, für das ein DDoS-Schutzplan aktiviert ist.
2. Wählen Sie unter **Überwachung** die Option **Metriken** aus.
3. Wählen Sie **Bereich** aus. Wählen Sie das **Abonnement** aus, das die öffentliche IP-Adresse enthält, die Sie protokollieren möchten, und wählen Sie **Öffentliche IP-Adresse** als **Ressourcentyp** aus. Wählen Sie anschließend die öffentliche IP-Adresse aus, für die Sie Metriken protokollieren möchten, und wählen Sie dann **Anwenden** aus.
4. Wählen Sie als Typ der **Aggregation** die Option **Max** aus.
5. Klicken Sie auf **Filter hinzufügen**. Wählen Sie unter **Eigenschaft** die Option **Geschützte IP-Adresse** aus. Der Operator sollte auf **=** festgelegt sein. Unter **Werte** wird eine Dropdownliste mit öffentlichen IP-Adressen angezeigt, die dem virtuellen Netzwerk zugeordnet sind und für die der DDoS-Schutz aktiviert wurde. 

![DDoS-Diagnoseeinstellungen](./media/ddos-attack-telemetry/vnet-ddos-metrics.png)

### <a name="public-ip-address"></a>Öffentliche IP-Adresse
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrer öffentlichen IP-Adresse.
2. Wählen Sie unter **Überwachung** die Option **Metriken** aus.
3. Wählen Sie als Typ der **Aggregation** die Option **Max** aus.

## <a name="view-ddos-mitigation-policies"></a>Anzeigen von DDoS-Entschärfungsrichtlinien

DDoS Protection Standard wendet drei automatisch optimierte Entschärfungsrichtlinien (TCP-SYN, TCP und UDP) auf jede öffentliche IP-Adresse der geschützten Ressource in dem virtuellen Netzwerk an, für das der DDoS-Schutz aktiviert ist. Sie können die Richtlinienschwellenwerte anzeigen, indem Sie die Metriken **Eingehende TCP-Pakete zum Auslösen der DDoS-Entschärfung** und **Eingehende UDP-Pakete zum Auslösen der DDoS-Entschärfung** mit dem **Aggregation** styp als „Max“ auswählen, wie in der folgenden Abbildung gezeigt wird:

![Anzeigen von Entschärfungsrichtlinien](./media/manage-ddos-protection/view-mitigation-policies.png)

## <a name="validate-and-test"></a>Überprüfen und Testen

Informationen dazu, wie Sie zum Überprüfen der DDoS Protection-Telemetriedaten einen DDoS-Angriff simulieren, finden Sie unter [Überprüfen der DDoS-Erkennung](test-through-simulations.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

- Konfigurieren von Warnungen für DDoS-Schutzmetriken
- Anzeigen von DDoS Protection-Telemetriedaten
- Anzeigen von DDoS-Entschärfungsrichtlinien
- Überprüfen und Testen von DDoS Protection-Telemetriedaten

Weitere Informationen zur Konfiguration von Risikominderungsberichten und Datenflussprotokollen finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS-Diagnoseprotokollierung](diagnostic-logging.md)
