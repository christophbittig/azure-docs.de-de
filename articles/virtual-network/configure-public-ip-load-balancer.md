---
title: Verwalten einer öffentlichen IP-Adresse mit einem Lastenausgleich
titleSuffix: Azure Virtual Network
description: Hier finden Sie Informationen zur Verwendung einer öffentlichen IP-Adresse mit einer Azure Load Balancer-Instanz sowie zum Ändern der Konfiguration.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 04b1b4b9b9dd859a2f4b4515c2896163b8f9a698
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038477"
---
# <a name="manage-a-public-ip-address-with-a-load-balancer"></a>Verwalten einer öffentlichen IP-Adresse mit einem Lastenausgleich

Ein öffentlicher Lastenausgleich ist eine Lösung der Ebene 4 zum Verteilen von TCP- und UDP-Datenverkehr an einen Back-End-Pool. Für den Lastenausgleich sind SKUs vom Typ „Basic“ und „Standard“ verfügbar. Diese SKUs entsprechen den Basic- und Standard-SKUs einer öffentlichen IP-Adresse.

Eine öffentliche IP-Adresse, die einem Lastenausgleich zugeordnet ist, dient als Front-End-IP-Konfiguration mit Internetzugriff. Das Front-End wird verwendet, um auf Ressourcen im Back-End-Pool zuzugreifen. Die Front-End-IP-Adresse kann für Member des Back-End-Pools verwendet werden, um ins Internet zu gelangen. 

Eine Azure Load Balancer-Instanz mit Basic-SKU ist in Bezug auf Verfügbarkeitsoptionen und Featuregruppen begrenzt. Für produktive Workloads wird die Bereitstellung einer Kombination aus Lastenausgleich mit Standard-SKU und IP-Adresse empfohlen. IP-Adressen der Standard-SKU unterstützen Verfügbarkeitszonen in unterstützten Regionen. 

In diesem Artikel erfahren Sie, wie Sie einen Lastenausgleich mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. 

Sie erfahren, wie Sie die aktuelle öffentliche IP-Adresse ändern, die einem Lastenausgleich zugeordnet ist. 

Sie erfahren, wie Sie die Front-End-Konfiguration eines ausgehenden Back-End-Pools in ein öffentliches IP-Präfix ändern.  

Schließlich werden in diesem Artikel die eindeutigen Aspekte der Verwendung öffentlicher IP-Adressen und Präfixe für öffentliche IP-Adressen mit einem Lastenausgleich behandelt. 

> [!NOTE]
> Für die Beispiele in diesem Artikel werden der Lastenausgleich der Standard-SKU und die öffentliche IP-Adresse verwendet. Für Lastenausgleiche der Basic-SKU sind die Verfahren identisch, mit Ausnahme der Auswahl der SKU bei der Erstellung des Lastenausgleichs und der öffentlichen IP-Ressource. Lastenausgleiche der Basic-SKU unterstützen keine Ausgangsregeln oder öffentliche IP-Präfixe. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Zwei öffentliche IP-Adressen der Standard-SKU in Ihrem Abonnement. Die IP-Adressen dürfen keinen Ressourcen zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP-1** und **myStandardPublicIP-2**.
- Ein öffentliches IP-Präfix in Ihrem Abonnement. Weitere Informationen zum Erstellen eines Präfix für eine öffentliche IP-Adresse finden Sie unter [Erstellen eines Präfix für eine öffentliche IP-Adresse mithilfe des Azure-Portals](create-public-ip-prefix-portal.md).
    - Für die Zwecke des Beispiels in diesem Artikel nennen Sie das neue öffentliche IP-Präfix **myPublicIPPrefixOutbound**.

## <a name="create-load-balancer-existing-public-ip"></a>Erstellen einer vorhandenen öffentlichen IP-Adresse für den Lastenausgleich

In diesem Abschnitt wird ein Lastenausgleich der Standard-SKU erstellt. Sie wählen die IP-Adresse, die Sie in den Voraussetzungen erstellt haben, als Front-End-IP-Adresse des Lastenausgleichs aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Lastenausgleich** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Lastenausgleich erstellen** die folgenden Informationen ein oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupIP** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myLoadBalancer** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | type | Übernehmen Sie den Standardwert **Öffentlich**. |
    | SKU | Übernehmen Sie den Standardwert **Standard**. |
    | Tarif | Übernehmen Sie den Standardwert **Regional**. |
    | **Öffentliche IP-Adresse** |   |
    | Öffentliche IP-Adresse | Wählen Sie **Vorhandenes verwenden** aus. |
    | Öffentliche IP-Adresse wählen | Wählen Sie **myStandardPublicIP-1** aus. |
    | Öffentliche IPv6-Adresse hinzufügen | Übernehmen Sie den Standardwert **Nein**. |

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die blaue Schaltfläche **Überprüfen + erstellen** aus. 

7. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung eines Lastenausgleichs. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer über das Azure-Portal](../load-balancer/quickstart-load-balancer-standard-public-portal.md).
>
> Weitere Informationen zu Azure Load Balancer finden Sie unter [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md).

## <a name="change-or-remove-public-ip-address"></a>Ändern oder Entfernen öffentlicher IP-Adressen

In diesem Abschnitt melden Sie sich beim Azure-Portal an und ändern die IP-Adresse des Lastenausgleichs. 

Eine Azure Load Balancer-Instanz muss über eine IP-Adresse verfügen, die einem Front-End zugeordnet ist. Eine separate öffentliche IP-Adresse kann als Front-End für ein- und ausgehenden Datenverkehr verwendet werden. 

Um die IP-Ändern zu ändern, ordnen Sie eine neue öffentliche IP-Adresse zu, die Sie zuvor mit dem Front-End des Lastenausgleichs erstellt haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Lastenausgleich** aus.

4. Wählen Sie unter **Lastenausgleichsmodule** die Option **myLoadBalancer** oder den Lastenausgleich aus, den Sie ändern möchten.

5. Wählen Sie in den Einstellungen von **myLoadBalancer** die Option **Front-End-IP-Konfiguration**.

6. Wählen Sie in **Front-End-IP-Konfiguration** die Option **LoadBalancerFrontend** oder Ihr Front-End für den Lastenausgleich aus.

7. Wählen Sie in der Konfiguration des Front-Ends des Lastenausgleichs unter **Öffentliche IP-Adresse** die Option **myStandardPublicIP-2** aus.

8. Wählen Sie **Speichern** aus.

9. Überprüfen Sie, ob das Front-End des Lastenausgleichs die neue IP-Adresse namens **myStandardPublicIP-2** anzeigt.

    > [!NOTE]
    > Diese Verfahren sind für einen regionsübergreifenden Lastenausgleich gültig. Weitere Informationen zum regionsübergreifenden Lastenausgleich finden Sie unter **[Regionsübergreifender Lastenausgleich](../load-balancer/cross-region-overview.md)** .


## <a name="add-public-ip-prefix"></a>Hinzufügen eines Präfix für öffentliche IP-Adressen

Ein Standardlastenausgleich unterstützt Ausgangsregeln für die Quellnetzwerkadressenübersetzung (Source Network Address Translation, SNAT). SNAT ermöglicht den Membern des Back-End-Pools den ausgehenden Zugriff auf das Internet. Präfixe öffentlicher IP-Adressen erweitern die Erweiterbarkeit von SNAT, indem mehrere IP-Adressen für ausgehende Verbindungen zugelassen werden. 

Die Verfügbarkeit mehrerer IP-Adressen vermeidet eine SNAT-Portauslastung. Jede Front-End-IP-Adresse stellt 64.000 kurzlebige Ports zur Verfügung, die der Lastenausgleich verwenden kann. Weitere Informationen finden Sie unter [Ausgangsregeln](../load-balancer/outbound-rules.md).

In diesem Abschnitt ändern Sie die Front-End-Konfiguration, die für ausgehende Verbindungen verwendet wird, um ein Präfix für öffentliche IP-Adressen zu verwenden.

> [!IMPORTANT]
> Um diesen Abschnitt abzuschließen, müssen Sie einen Lastenausgleich mit einer ausgehenden Front-End-Konfiguration und Ausgangsregeln bereitstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Lastenausgleich** aus.

4. Wählen Sie unter **Lastenausgleichsmodule** die Option **myLoadBalancer** oder den Lastenausgleich aus, den Sie ändern möchten.

5. Wählen Sie in den Einstellungen von **myLoadBalancer** die Option **Front-End-IP-Konfiguration**.

6. Wählen Sie in **Front-End-IP-Konfiguration** die Option **LoadBalancerFrontendOutbound** oder Ihr ausgehendes Front-End für den Lastenausgleich aus.

7. Wählen Sie für **IP-Typ** die Option **Präfix für öffentliche IP-Adressen** aus.

8. Wählen Sie unter **Präfix für öffentliche IP-Adressen** das Präfix für öffentliche IP-Adressen aus, das Sie zuvor erstellt haben, **myPublicIPPrefixOutbound**.

9. Wählen Sie **Speichern** aus.

10. Bestätigen Sie in der **Front-End-IP-Konfiguration**, dass das IP-Präfix zur ausgehenden Front-End-Konfiguration hinzugefügt wurde.

## <a name="more-information"></a>Weitere Informationen

* Regionsübergreifende Lastenausgleiche sind ein spezieller Typ von öffentlichem Standardlastenausgleich, der mehrere Regionen umfassen kann. Das Front-End eines regionsübergreifenden Lastenausgleichs kann nur mit der Tarifopiton „Global“ der öffentlichen IP-Adressen der Standard-SKU verwendet werden. Der Datenverkehr, der an die Front-End-IP-Adresse eines regionsübergreifenden Lastenausgleichs gesendet wird, wird auf die regionalen öffentlichen Lastenausgleiche verteilt. Die regionalen Front-End-IP-Adressen sind im Back-End-Pool des regionsübergreifenden Lastenausgleichs enthalten. Weitere Informationen finden Sie unter [Regionsübergreifender Lastenausgleich](../load-balancer/cross-region-overview.md).

* Standardmäßig lässt ein öffentlicher Lastenausgleich nicht zu, dass Sie mehrere Lastenausgleichsregeln mit demselben Back-End-Port verwenden. Wenn eine Konfiguration mit mehreren Regeln für denselben Back-End-Port erforderlich ist, aktivieren Sie die Floating IP-Option für eine Lastenausgleichsregel. Diese Einstellung überschreibt die IP-Zieladresse des Datenverkehrs, der an den Back-End-Pool gesendet wird. Ohne aktivierte Floating IP-Adresse ist das Ziel die private IP-Adresse des Back-End-Pools. Wenn die Floating IP-Adresse aktiviert ist, ist die Ziel-IP-Adresse die öffentliche IP-Adresse des Front-Ends des Lastenausgleichs. Diese öffentliche IP-Adresse muss in der Netzwerkkonfiguration der Back-End-Instanz konfiguriert sein, um diesen Datenverkehr ordnungsgemäß zu empfangen. Eine Loopbackschnittstelle mit der in der Instanz konfigurierten Front-End-IP-Adresse. Weitere Informationen finden Sie unter [Konfiguration der Floating IP-Adresse von Azure Load Balancer](../load-balancer/load-balancer-floating-ip.md).

* Bei der Einrichtung eines Lastenausgleichs können Membern des Back-End-Pools häufig auch öffentliche IP-Adressen auf Instanzebene zugewiesen werden. Wenn diese Architektur verwendet wird, kann der Datenverkehr direkt an diese IP-Adressen gesendet werden, wodurch der Lastenausgleich umgangen wird. 

* Sowohl beim öffentlichen Standardlastenausgleich als auch bei öffentlichen IP-Adressen kann ein TCP-Timeoutwert zugewiesen werden, der angibt, wie lange eine Verbindung geöffnet bleiben soll, bevor KeepAlives empfangen werden. Wenn eine öffentliche IP-Adresse als Front-End für den Lastenausgleich zugewiesen wird, hat der Timeoutwert der IP-Adresse Vorrang. Beachten Sie, dass diese Einstellung nur für eingehende Verbindungen mit dem Lastenausgleich gilt. Weitere Informationen finden Sie unter [Lastenausgleich mit TCP-Rücksetzung](../load-balancer/load-balancer-tcp-reset.md).

## <a name="caveats"></a>Vorbehalte

* Öffentliche Lastenausgleichsmodule im Standard-Tarif können statische IPv6-Adressen der Standard-SKU als öffentliche Front-End-IP-Adressen oder Präfixe von öffentlichen IP-Adressen verwenden.  Jede Bereitstellung muss einen dualen Stapel mit IPv4- und IPv6-Front-Ends aufweisen. Die NAT64-Übersetzung ist nicht verfügbar. Weitere Informationen finden Sie unter [Bereitstellen einer IPv6-Anwendung mit dualem Stapel in Azure – PowerShell](./virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). (Hinweis: Öffentliche Lastenausgleichsmodule im Basic-Tarif können dynamische IPv6-Adressen der Basic-SKU als öffentliche Front-End-IP-Adressen verwenden.)

* Wenn einem öffentlichen Lastenausgleich mehrere Front-Ends zugewiesen werden, gibt es keine Methode zum Zuweisen von Datenflüssen von bestimmten Back-End-Instanzen zu ausgehendem Datenverkehr für eine bestimmte IP-Adresse.  Weitere Informationen finden Sie unter [Mehrere Front-Ends für Azure Load Balancer](../load-balancer/load-balancer-multivip-overview.md).
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen Lastenausgleich erstellen und eine vorhandene öffentliche IP-Adresse verwenden. 

Sie haben die IP-Adresse in einer Front-End-Konfiguration des Lastenausgleichs ersetzt. 

Schließlich haben Sie eine Front-End-Konfiguration für ausgehenden Datenverkehr geändert, um ein Präfix für öffentliche IP-Adressen zu verwenden.

- Weitere Informationen zu Azure Load Balancer finden Sie unter [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md).
- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](public-ip-addresses.md).
