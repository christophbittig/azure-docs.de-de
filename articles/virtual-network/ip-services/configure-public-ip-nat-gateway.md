---
title: Verwalten einer öffentlichen IP-Adresse mit einem NAT-Gateway
titleSuffix: Azure Virtual Network
description: Hier erfahren Sie, wie eine öffentliche IP-Adresse mit einem Azure Virtual Network NAT-Gateway verwendet wird und wie Sie die Konfiguration ändern können.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 2fd41f3459469474c100657747c4873ee99dc057
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368245"
---
# <a name="manage-a-public-ip-address-with-a-nat-gateway"></a>Verwalten einer öffentlichen IP-Adresse mit einem NAT-Gateway

Azure NAT-Gatewayressourcen ermöglichen aus Subnetzen in einem virtuellen Netzwerk ausgehende Internetverbindungen. Ressourcen, die im Subnetz des virtuellen Netzwerks des NAT-Gateways bereitgestellt werden, müssen die Standard-SKU aufweisen. Die Bereitstellung eines NAT-Gateways in einem Subnetz eines virtuellen Netzwerks, das Ressourcen der Basic-SKU enthält, wird nicht unterstützt. 

Das NAT-Gateway ermöglicht Source Network Address Translation-Verbindungen (SNAT) von Ressourcen aus, die das NAT-Gateway verwenden. Das NAT-Gateway unterstützt öffentliche IP-Adressen und Präfixe für öffentliche IP-Adressen der Standard-SKU. Jede Kombination wird unterstützt. Die Anzahl der angegebenen IP-Adressen darf jedoch nicht höher als 16 sein. Durch das Hinzufügen von IP-Adressen oder einem Präfix für öffentliche IP-Adressen werden SNAT-Verbindungen skaliert, die von Ressourcen ausgehen, die das NAT-Gateway nutzen. 

In diesem Artikel erfahren Sie, wie Sie ein NAT-Gateway mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. 

Sie erfahren außerdem, wie Sie die aktuelle öffentliche IP-Adresse ändern, die einem NAT-Gateway zugeordnet ist. 

Zuletzt ändern Sie die IP-Konfiguration von einer öffentlichen IP-Adresse in ein Präfix für öffentliche IP-Adressen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Zwei öffentliche IP-Adressen der Standard-SKU in Ihrem Abonnement. Die IP-Adressen dürfen keinen Ressourcen zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](../../virtual-network/create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP-1** und **myStandardPublicIP-2**.
- Ein öffentliches IP-Präfix in Ihrem Abonnement. Weitere Informationen zum Erstellen eines Präfixes für öffentliche IP-Adressen finden Sie unter [Erstellen eines öffentlichen IP-Präfixes über das Azure-Portal](../../virtual-network/create-public-ip-prefix-portal.md).
    - Für das Beispiel in diesem Artikel nennen Sie das neue Präfix für öffentliche IP-Adressen **myPublicIPPrefixNAT**.

## <a name="create-nat-gateway-existing-public-ip"></a>Erstellen eines NAT-Gateways mit einer bestehenden öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie eine NAT-Gatewayressource. Sie wählen die im Abschnitt „Voraussetzungen“ erstellte IP-Adresse als öffentliche IP-Adresse für das NAT-Gateway aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupNAT** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myNATgateway** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Verfügbarkeitszone | Übernehmen Sie den Standardwert **Keine**. |
    | Leerlauftimeout | Übernehmen Sie den Standardwert **4**. |

6. Klicken Sie auf die Registerkarte **Outbound IP** (Ausgehende IP-Adresse) oder auf **Weiter: Ausgehende IP-Adresse**.

7. Klicken Sie auf der Registerkarte **Ausgehende IP-Adresse** unter **Öffentliche IP-Adresse** auf **myStandardPublicIP-1**.

6. Klicken Sie auf die Registerkarte **Überprüfen und erstellen** oder auf die blaue Schaltfläche **Überprüfen und erstellen**. 

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung eines NAT-Gateways. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie unter [Tutorial: Erstellen eines NAT-Gateways über das Azure-Portal](../nat-gateway/tutorial-create-nat-gateway-portal.md).
>
> Weitere Informationen zu Azure Virtual Network NAT finden Sie unter [Was ist Azure Virtual Network NAT](../nat-gateway/nat-overview.md).

## <a name="change-or-remove-public-ip-address"></a>Ändern oder Entfernen öffentlicher IP-Adressen

In diesem Abschnitt melden Sie sich beim Azure-Portal an und ändern die IP-Adresse des NAT-Gateway. 

Ordnen Sie dem NAT-Gateway eine neue öffentliche IP-Adresse zu, die Sie zuvor erstellt haben, um die IP-Adresse zu ändern. Einem NAT-Gateway muss mindestens eine IP-Adresse zugewiesen sein.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

4. Wählen Sie unter **NAT-Gateways** die Option **myNATgateway** oder das NAT-Gateway aus, das Sie ändern möchten.

5. Klicken Sie in den Einstellungen von **myNATgateway** auf **Ausgehende IP-Adresse**.

6. Klicken Sie unter **Ausgehende IP-Adresse** neben **Öffentliche IP-Adressen** auf **Ändern**.

7. Sie können die aktuelle IP-Adresse ersetzen oder die bestehende Adresse hinzufügen. Wählen Sie unter **Öffentliche IP-Adressen und Präfixe verwalten** neben **Öffentliche IP-Adressen** die Option **myStandardPublicIP-2** aus.

8. Klicken Sie auf **OK**.

9. Vergewissern Sie sich, dass **myStandardPublicIP-2** den öffentlichen IP-Adressen hinzugefügt wurde. Sie können die bereits zugewiesene IP-Adresse löschen, indem Sie bei Bedarf auf den Papierkorb klicken.

10. Wählen Sie **Speichern** aus.

## <a name="add-public-ip-prefix"></a>Hinzufügen eines Präfixes für öffentliche IP-Adressen

Präfixe für öffentliche IP-Adressen vergrößern die Erweiterbarkeit von SNAT auf vom NAT-Gateway ausgehende Verbindungen. Ein Präfix für öffentliche IP-Adressen vermeidet die SNAT-Portauslastung. Jede IP-Adresse stellt 64.000 kurzlebige Ports bereit, die verwendet werden können.

> [!NOTE] 
> Beim Zuweisen eines Präfixes für öffentliche IP-Adressen zu einem NAT-Gateway wird der gesamte Bereich verwendet. 

In diesem Abschnitt ändern Sie die Konfiguration der ausgehenden IP-Adresse so, dass sie ein Präfix für öffentliche IP-Adressen verwendet, das Sie zuvor erstellt haben.

> [!NOTE]
> Sie können die einzelne IP-Adresse, die dem NAT-Gateway zugeordnet ist, entfernen und wiederverwenden oder sie dem NAT-Gateway zugeordnet lassen, um die Anzahl der ausgehenden SNAT-Ports zu erhöhen. Das NAT-Gateway unterstützt eine Kombination aus öffentlichen IP-Adressen und Präfixen in der Konfiguration der ausgehenden IP-Adresse. Wenn Sie ein Präfix für öffentliche IP-Adressen mit 16 Adressen erstellt haben, entfernen Sie die einzelne öffentliche IP-Adresse, und speichern Sie die Konfiguration, bevor Sie das Präfix hinzufügen. Die Anzahl der zugeordneten IP-Adressen darf nicht höher als 16 sein.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **NAT-Gateway** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **NAT-Gateways** aus.

4. Wählen Sie unter **NAT-Gateways** die Option **myNATgateway** oder das NAT-Gateway aus, das Sie ändern möchten.

5. Klicken Sie in den Einstellungen von **myNATgateway** auf **Ausgehende IP-Adresse**.

6. Klicken Sie unter **Ausgehende IP-Adresse** neben **Präfixe für öffentliche IP-Adressen** auf **Ändern**.

7. Wählen Sie **myPublicIPPrefixNAT** oder Ihr Präfix aus.

8. Klicken Sie auf **OK**.

9. Vergewissern Sie sich, dass **myPublicIPPrefixNAT** den Präfixen für öffentliche IP-Adressen hinzugefügt wurde.

10. Wählen Sie **Speichern** aus.

## <a name="more-information"></a>Weitere Informationen

* Wenn Sie virtuelle Computer in einem virtuellen Netzwerk mit einem NAT-Gateway bereitstellen, verlässt der gesamte an das NAT-Gateway adressierte eingehende Datenverkehr das Netzwerk über das NAT-Gateway. Bei Verwendung eines NAT-Gateways mit einem öffentlichen Standardlastenausgleich verlässt der gesamte an die öffentliche(n) IP-Adresse(n) des NAT-Gateways adressierte eingehende Datenverkehr das Netzwerk über die NAT Gateway. 

    > [!NOTE] 
    > Alle Ausgangskonfigurationen einer Lastenausgleichsregel oder von Ausgangsregeln werden durch das NAT-Gateway ersetzt. Elemente des Back-End-Pools des Lastenausgleichs verwenden ebenfalls das NAT-Gateway für ausgehende Verbindungen. Weitere Informationen finden Sie unter [Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen](../nat-gateway/nat-gateway-resource.md).

* NAT-Gateways und öffentliche IP-Adressen können einen TCP-Timeoutwert aufweisen, der angibt, wie lange eine Verbindung geöffnet bleiben soll, bevor Keepalives empfangen werden.  Wenn eine öffentliche IP-Adresse einem NAT Gateway zugewiesen wird, hat der Timeoutwert der IP-Adresse Vorrang.  Weitere Informationen finden Sie unter [Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen](../nat-gateway/nat-gateway-resource.md#timers).

## <a name="caveats"></a>Vorbehalte

* Öffentliche IPv6-Adressen und zugehörige Präfixe werden derzeit für NAT-Gateways nicht unterstützt. NAT-Gateways können nicht im Subnetz eines virtuellen Netzwerks mit einem IPv6-Präfix bereitgestellt werden. Weitere Informationen finden Sie unter [Problembehandlung für Azure Virtual Network NAT-Verbindungen](../nat-gateway/troubleshoot-nat.md).
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie ein NAT-Ladegateway erstellen und eine bestehende öffentliche IP-Adresse verwenden. Sie haben die IP-Adresse in der Konfiguration der ausgehenden IP-Adresse eines NAT-Gateways ersetzt. Zuletzt haben Sie die Konfiguration einer ausgehenden IP-Adresse so geändert, dass ein Präfix für öffentliche IP-Adressen verwendet wird.

- Weitere Informationen zu Azure Virtual Network NAT finden Sie unter [Was ist Azure Virtual Network NAT](../nat-gateway/nat-overview.md).
- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](../../virtual-network/public-ip-addresses.md).