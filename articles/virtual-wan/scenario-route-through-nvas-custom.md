---
title: Weiterleiten von Datenverkehr über virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) mithilfe benutzerdefinierter Einstellungen
titleSuffix: Azure Virtual WAN
description: Erfahren Sie mehr über Virtual WAN-Routingszenarien zum Weiterleiten von Datenverkehr über virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs). In diesem Szenario leiten Sie Datenverkehr über NVAs mithilfe eines anderen NVAs für Internetdatenverkehr sowie mit benutzerdefinierten Einstellungen weiter.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 1602207c83f8ff4a632428c0308b514c6d552058
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428252"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Szenario: Weiterleiten von Datenverkehr über virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) mithilfe benutzerdefinierter Einstellungen

Wenn Sie mit dem Routing virtueller Hubs von Azure Virtual WAN arbeiten, stehen Ihnen viele Optionen zur Verfügung. Der Schwerpunkt dieses Artikels liegt darauf, wenn Sie Datenverkehr für Kommunikation zwischen virtuellen Netzwerken und Filialen durch ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) leiten möchten und für Internetdatenverkehr ein anderes NVA verwenden möchten. Weitere Informationen finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

>[!Note]
> Beachten Sie, dass sich für die unten beschriebenen Routingszenarien der Virtual WAN-Hub und das Spoke-VNet, die das NVA enthalten, in derselben Azure-Region befinden müssen.

## <a name="design"></a>Entwurf

* **Spokes** für virtuelle Netzwerke, die mit dem virtuellen Hub verbunden sind. (Beispiel: VNET 1, VNET 2 und VNET 3 im Diagramm weiter unten in diesem Artikel.)
* **Dienst-VNET** für virtuelle Netzwerke, in denen Benutzer ein NVA bereitgestellt haben, um den Nicht-Internetdatenverkehr zu überprüfen, und möglicherweise mit gemeinsamen Diensten, auf die über Spokes zugegriffen wird. (Beispiel: VNET 4 im Diagramm weiter unten in diesem Artikel.)
* **Umkreis-VNET** für virtuelle Netzwerke, in denen Benutzer ein NVA für die Untersuchung von Internetdatenverkehr bereitgestellt haben. (Beispiel: VNET 5 im Diagramm weiter unten in diesem Artikel.)
* **Hubs** für von Microsoft verwaltete Virtual WAN-Hubs.

In der folgenden Tabelle werden die in diesem Szenario unterstützten Verbindungen zusammengefasst:

| From          | Beschreibung|Spokes|Dienst-VNET|Branches|Internet|
|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| ->| direkt |direkt | über Dienst-VNET |über Umkreis-VNET |
| **Dienst-VNET**| ->| direkt |–| direkt | |
| **Branches** | ->| über Dienst-VNET |direkt| direkt |  |

Jede der Zellen in der Konnektivitätsmatrix beschreibt, ob Konnektivität direkt über Virtual WAN oder über eines der virtuellen Netzwerke mit einem NVA bereitgestellt wird.

Beachten Sie Folgendes:

* Spokes:
  * Spokes können andere Spokes direkt über Virtual WAN-Hubs erreichen.
  * Spokes erhalten die Konnektivität mit Branches über eine statische Route, die auf das Dienst-VNET verweist. Sie erlernen keine spezifischen Präfixe von den Filialen, weil diese Präfixe spezifischer sind und die Zusammenfassung außer Kraft setzen.
  * Spokes senden Internetdatenverkehr über ein direktes VNET-Peering an das Umkreis-VNET.
* Branches erreichen Spokes per statischem Routing, wobei auf das Dienst-VNET verwiesen wird. Sie erlernen keine spezifischen Präfixe von den VNETs, die die zusammengefasste statische Route außer Kraft setzen.
* Das Dienst-VNET ähnelt einem VNET für gemeinsame Dienste, das für jedes VNET und jede Filiale erreichbar sein muss.
* Das Umkreis-VNET benötigt keine Konnektivität über Virtual WAN, da der einzige Datenverkehr, den es unterstützt, über direkte VNET-Peerings erfolgt. Um die Konfiguration zu vereinfachen, verwenden Sie jedoch dasselbe Konnektivitätsmodell wie für das Umkreis-VNET.

Es gibt drei verschiedene Konnektivitätsmuster, aus denen sich drei Routingtabellen ergeben. Die Zuordnungen zu den verschiedenen virtuellen Netzwerken sind wie folgt:

* Spokes:
  * Zugeordnete Routingtabelle: **RT_V2B**
  * Weitergabe an Routingtabellen: **RT_V2B** und **RT_SHARED**
* NVA-VNETs (Dienst-VNET und Umkreis-VNET):
  * Zugeordnete Routingtabelle: **RT_SHARED**
  * Weitergabe an Routingtabellen: **RT_SHARED**
* Branches:
  * Zugeordnete Routingtabelle: **Standard**
  * Weitergabe an Routingtabellen: **RT_SHARED** und **Standard**

> [!NOTE]
> Stellen Sie sicher, dass die Spoke-VNets nicht an die Standardbezeichnung weitergegeben werden. Dadurch wird sichergestellt, dass der Datenverkehr von Filialen zu Spoke-VNets an die virtuellen Netzwerkgeräte weitergeleitet wird.

Diese statischen Routen stellen sicher, dass der Datenverkehr zum und vom virtuellen Netzwerk und der Filiale über das NVA im Dienst-VNET (VNET 4) läuft:

| BESCHREIBUNG | Routingtabelle | Statische Route              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| NVA-Spokes  | Standard     | 10.1.0.0/16 -> vnet4conn  |

Jetzt können Sie mit Virtual WAN die richtige Verbindung auswählen, an die die Pakete gesendet werden sollen. Sie müssen Virtual WAN auch verwenden, um die richtige Aktion auszuwählen, die beim Empfang dieser Pakete erfolgen soll. Dazu nutzen Sie die Verbindungsroutingtabellen wie folgt:

| BESCHREIBUNG | Verbindung | Statische Route            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

Weitere Informationen finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).

## <a name="architecture"></a>Aufbau

Das folgende Diagramm zeigt die Architektur, die weiter oben in diesem Artikel beschrieben wurde.

Das Diagramm enthält einen Hub namens **Hub 1**.

* **Hub 1** ist direkt mit den NVA-VNETs **VNET 4** und **VNET 5** verbunden.

* Datenverkehr zwischen den VNETs 1, 2, 3 und den Filialen wird über das virtuelle Netzwerkgerät für **VNet 4** (10.4.0.5) erwartet.

* Der gesamte Internetdatenverkehr aus den VNETs 1, 2 und 3 wird über **NVA-VNET 5** (10.5.0.5) erwartet.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagramm der Netzwerkarchitektur":::

## <a name="workflow"></a><a name="workflow"></a>Workflow

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagramm des Workflows." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Führen Sie zum Einrichten des Routings über ein virtuelles Netzwerkgerät die folgenden Schritte aus.

1. Damit Internetdatenverkehr über VNET 5 geleitet wird, müssen die VNETs 1, 2 und 3 per VNET-Peering eine direkte Verbindung mit VNET 5 herstellen. Außerdem benötigen Sie eine benutzerdefinierte Route, die in den virtuellen Netzwerken für 0.0.0.0/0 und den nächsten Hop 10.5.0.5 eingerichtet ist.

   Wenn Sie die VNets 1, 2 und 3 nicht mit VNet 5 verbinden und stattdessen nur das virtuelle Netzwerkgerät in VNet 4 zum Weiterleiten von 0.0.0.0/0-Datenverkehr aus Filialen (lokales VPN oder ExpressRoute-Verbindungen) verwenden möchten, wechseln Sie zum [alternativen Workflow](#alternate).

   Wenn jedoch VNet-to-VNet-Datenverkehr über das virtuelle Netzwerkgerät weitergeleitet werden soll, müssen Sie die VNets 1, 2 und 3 vom virtuellen Hub trennen und über das Spoke-VNet 4 des virtuellen Netzwerkgeräts verbinden oder stapeln. In Virtual WAN verläuft der VNet-to-VNet-Datenverkehr über den Virtual WAN-Hub oder die Azure Firewall-Instanz (sicherer Hub) eines Virtual WAN-Hubs. Wenn für VNets ein direktes VNet-Peering durchgeführt wird, können diese direkt kommunizieren und die Übertragung über den virtuellen Hub umgehen.

1. Wechseln Sie im Azure-Portal zu Ihrem virtuellen Hub, und erstellen Sie eine benutzerdefinierte Routingtabelle mit dem Namen **RT_Shared**. Diese Tabelle erlernt Routen über die Weitergabe aus allen virtuellen Netzwerken und Filialverbindungen. Diese leere Tabelle wird im folgenden Diagramm gezeigt.

   * **Routen:** Sie müssen keine statischen Routen hinzufügen.

   * **Zuordnung:** Wählen Sie die VNETs 4 und 5 aus, was bedeutet, dass die Verbindungen dieser virtuellen Netzwerke der Routingtabelle **RT_Shared** zugeordnet werden.

   * **Weitergabe:** Da Sie möchten, dass alle Filialen und VNET-Verbindungen ihre Routen dynamisch an diese Routingtabelle weitergeben, wählen Sie Filialen und alle VNETs aus.

1. Erstellen Sie die benutzerdefinierte Routingtabelle **RT_V2B**, um den Datenverkehr von den VNETs 1, 2 und 3 an Filialen weiterzuleiten.

   * **Routen:** Fügen Sie einen aggregierten statischen Routeneintrag für Filialen mit dem nächsten Hop als VNET 4-Verbindung hinzu. Konfigurieren Sie eine statische Route in der VNet 4-Verbindung für Filialenpräfixe. Geben Sie den nächsten Hop als spezielle IP-Adresse des virtuellen Netzwerkgeräts in VNet 4 an.

   * **Zuordnung:** Wählen Sie die **VNets 1, 2 und 3** aus. Dies bedeutet, dass die VNet-Verbindungen 1, 2 und 3 dieser Routingtabelle zugeordnet werden und in dieser Routingtabelle Routen (statisch und dynamisch über die Weitergabe) erlernen können.

   * **Weitergabe:** Verbindungen geben Routen an Routingtabellen weiter. Durch Auswahl der VNets 1, 2 und 3 wird die Weitergabe von Routen von den VNets 1, 2 und 3 an diese Routingtabelle ermöglicht. Es ist nicht erforderlich, Routen von Filialverbindungen an **RT_V2B** weiterzugeben, da der Datenverkehr des Filial-VNET über das NVA in VNET 4 geleitet wird.
  
1. Bearbeiten Sie die Standardroutingtabelle **DefaultRouteTable**.

   Alle VPN-, Azure ExpressRoute- und Benutzer-VPN-Verbindungen sind der Standardroutingtabelle zugeordnet. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselbe Gruppe von Routingtabellen weiter.

   * **Routen:** Fügen Sie einen aggregierten statischen Routeneintrag für die VNETs 1, 2 und 3 mit dem nächsten Hop als VNET 4-Verbindung hinzu. Konfigurieren Sie eine statische Route in der VNet 4-Verbindung für die aggregierten Präfixe der VNets 1, 2 und 3. Geben Sie den nächsten Hop als spezielle IP-Adresse des virtuellen Netzwerkgeräts in VNet 4 an.

   * **Zuordnung:** Vergewissern Sie sich, dass die Option für Filialen (VPN/ER/P2S) ausgewählt ist, und dass lokale Filialverbindungen der Standardroutingtabelle zugeordnet sind.

   * **Weitergabe von:** Vergewissern Sie sich, dass die Option für Filialen (VPN/ER/P2S) ausgewählt ist, und dass lokale Verbindungen Routen an die Standardroutingtabelle weitergeben.

## <a name="alternate-workflow"></a><a name="alternate"></a>Alternativer Workflow

In diesem Workflow verbinden Sie die VNets 1, 2 und 3 nicht mit VNet 5. Stattdessen verwenden Sie das virtuelle Netzwerkgerät in VNet 4, um 0.0.0.0/0-Datenverkehr aus Filialen (lokales VPN oder ExpressRoute-Verbindungen) weiterzuleiten.

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagramm für alternativen Workflow." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Führen Sie zum Einrichten des Routings über ein virtuelles Netzwerkgerät die folgenden Schritte aus.

1. Wechseln Sie im Azure-Portal zu Ihrem virtuellen Hub, und erstellen Sie eine benutzerdefinierte Routentabelle namens  **RT_NVA** zum Weiterleiten von Datenverkehr über das virtuelle Netzwerkgerät 10.4.0.5.  

   * **Routen:**  Keine weiteren Maßnahmen erforderlich.

   * **Zuordnung:**  Wählen Sie **VNet 4** aus. Dies bedeutet, dass die VNet-Verbindung 4 dieser Routingtabelle zugeordnet wird und in dieser Routingtabelle Routen (statisch und dynamisch über die Weitergabe) erlernen kann.

   * **Weitergabe:**  Verbindungen geben Routen an Routingtabellen weiter. Durch Auswahl der VNets 1, 2 und 3 wird die Weitergabe von Routen von den VNets 1, 2 und 3 an diese Routingtabelle ermöglicht. Die Auswahl von Filialen (VPN/ER/P2S) ermöglicht die Weitergabe von Routen von Filialen/lokalen Verbindungen an diese Routingtabelle. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselbe Gruppe von Routingtabellen weiter.

1. Erstellen Sie eine benutzerdefinierte Routingtabelle namens  **RT_VNET**, um den Datenverkehr aus den VNets 1, 2 und 3 an Filialen oder das Internet (0.0.0.0/0) über das virtuelle Netzwerkgerät von VNet 4 weiterzuleiten. VNet-to-VNet-Datenverkehr verläuft direkt und nicht über das virtuelle Netzwerkgerät von VNet 4. Wenn Datenverkehr über das virtuelle Netzwerkgerät geleitet werden soll, trennen Sie die VNets 1, 2 und 3, und verbinden Sie diese per VNet-Peering mit VNet 4.

   * **Routen:**  

     * Fügen Sie eine aggregierte Route „10.2.0.0/16“ mit dem nächsten Hop als VNet 4-Verbindung für den Datenverkehr aus den VNets 1, 2 und 3 in Richtung Filialen hinzu. Konfigurieren Sie in der VNet 4-Verbindung eine Route für „10.2.0.0/16“, und geben Sie den nächsten Hop als die spezifische IP-Adresse des virtuellen Netzwerkgeräts in VNet 4 an.

     * Fügen Sie eine Route „0.0.0.0/0“ mit dem nächsten Hop als VNet 4-Verbindung hinzu. „0.0.0.0/0“ wird hinzugefügt, um das Senden von Datenverkehr an das Internet einzuschließen. Spezifische Adresspräfixe für VNets oder Filialen sind darin nicht enthalten. Konfigurieren Sie in der VNet 4-Verbindung eine Route für „0.0.0.0/0“, und geben Sie den nächsten Hop als die spezifische IP-Adresse des virtuellen Netzwerkgeräts in VNet 4 an.

   * **Zuordnung:**  Wählen Sie die **VNets 1, 2 und 3** aus. Dies bedeutet, dass die VNet-Verbindungen 1, 2 und 3 dieser Routingtabelle zugeordnet werden und in dieser Routingtabelle Routen (statisch und dynamisch über die Weitergabe) erlernen können.

   * **Weitergabe:**  Verbindungen geben Routen an Routingtabellen weiter. Durch Auswahl der VNETs 1, 2 und 3 wird die Weitergabe von Routen von den VNETs 1, 2 und 3 an diese Routingtabelle ermöglicht. Stellen Sie sicher, dass die Option für Filialen (VPN/ER/P2S) nicht ausgewählt ist. Dadurch wird sichergestellt, dass die VNets 1, 2 und 3 für lokale Verbindungen nicht direkt zugänglich sind.

1. Bearbeiten Sie die Standardroutingtabelle  **DefaultRouteTable**.

   Alle VPN-, Azure ExpressRoute- und Benutzer-VPN-Verbindungen sind der Standardroutingtabelle zugeordnet. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselbe Gruppe von Routingtabellen weiter.

   * **Routen:**  

     * Fügen Sie eine aggregierte Route „10.1.0.0/16“ für die **VNETs 1, 2 und 3** mit dem nächsten Hop als **VNET 4-Verbindung** hinzu.

     * Fügen Sie eine Route „0.0.0.0/0“ mit dem nächsten Hop als **VNet 4-Verbindung** hinzu. „0.0.0.0/0“ wird hinzugefügt, um das Senden von Datenverkehr an das Internet einzuschließen. Spezifische Adresspräfixe für VNets oder Filialen sind darin nicht enthalten. Im vorherigen Schritt für die VNet 4-Verbindung haben Sie bereits eine Route für „0.0.0.0/0“ konfiguriert, wobei der nächste Hop der spezifischen IP-Adresse des virtuellen Netzwerkgeräts in VNet 4 entspricht.

   * **Zuordnung:**  Stellen Sie sicher, dass die Option für Filialen **(VPN/ER/P2S)** ausgewählt ist. Dadurch wird sichergestellt, dass lokale Filialenverbindungen der Standardroutingtabelle zugeordnet werden. Alle VPN-, Azure ExpressRoute- und Benutzer-VPN-Verbindungen sind nur der Standardroutingtabelle zugeordnet.

   * **Weitergabe von:**  Stellen Sie sicher, dass die Option für Filialen **(VPN/ER/P2S)** ausgewählt ist. Dadurch wird sichergestellt, dass lokale Verbindungen Routen an die Standardroutingtabelle weitergeben. Alle VPN-, ExpressRoute- und Benutzer-VPN-Verbindungen geben Routen an dieselbe Gruppe von Routingtabellen weiter.

   >[!Note]
   >
   > * Portalbenutzer müssen für Verbindungen (VPN/ER/P2S/VNet) die Option „An Standardroute weitergeben“ aktivieren, damit die Route „0.0.0.0/0“ wirksam wird.
   > * PS-/CLI-/REST-Benutzer müssen das Flag „enableinternetsecurity“ auf TRUE setzen, damit die Route „0.0.0.0/0“ wirksam wird.
   > * Die VNET-Verbindung unterstützt nicht „mehrere/eindeutig“ für die IP-Adresse des nächsten Hops zum „selben“ virtuellen Netzwerkgerät in einem SPOKE-VNET, „wenn“ eine der Routen mit der IP-Adresse des nächsten Hops als öffentliche IP-Adresse oder 0.0.0.0/0 (Internet) angegeben ist.
   > * Wenn 0.0.0.0/0 als statische Route für eine VNet-Verbindung konfiguriert ist, wird diese Route auf den gesamten Datenverkehr angewendet, einschließlich der Ressourcen innerhalb des Spokes selbst. Dies bedeutet, dass der gesamte Datenverkehr an die IP-Adresse des nächsten Hops der statischen Route (private IP-Adresse des virtuellen Netzwerkgeräts) weitergeleitet wird. Geben Sie daher in Bereitstellungen mit einer 0.0.0.0/0-Route, bei der die IP-Adresse des virtuellen Netzwerkgeräts am nächsten Hop für eine Spoke-VNet-Verbindung konfiguriert ist, eine /32-Route für die Spoke-VNet-Verbindung an, um direkt auf Workloads in demselben VNet wie das virtuelle Netzwerkgerät zuzugreifen (damit der Datenverkehr nicht über das virtuelle Netzwerkgerät geleitet wird). Wenn Sie beispielsweise direkt auf 10.1.3.1 zugreifen möchten, geben Sie 10.1.3.1/32, nächster Hop 10.1.3.1, für die Spoke-VNet-Verbindung an.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Virtual WAN finden Sie unter [Häufig gestellte Fragen](virtual-wan-faq.md).
* Weitere Informationen zum Routing für virtuelle Hubs finden Sie unter [Informationen zum Routing virtueller Hubs](about-virtual-hub-routing.md).
