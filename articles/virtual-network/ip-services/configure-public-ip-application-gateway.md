---
title: Verwalten einer öffentlichen IP-Adresse mit Azure Application Gateway
titleSuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie eine öffentliche IP-Adresse mit einer Azure Application Gateway-Instanz verwenden und die Konfiguration ändern und verwalten.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 8139d6c6309bf971ed9a955f5ef02f0c674b152b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368165"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>Verwalten einer öffentlichen IP-Adresse mit Azure Application Gateway

Azure Application Gateway ist ein Lastenausgleich für Webdatenverkehr, der den an Ihre Webanwendung fließenden Datenverkehr verwaltet. Application Gateway kann Routingentscheidungen auf der Grundlage von Attributen einer HTTP-Anforderung treffen. Beispiele für solche Attribute sind der URI-Pfad oder Hostheader.  Das Front-End einer Application Gateway-Instanz ist der Verbindungspunkt für die Anwendungen im Back-End-Pool. 

Ein Application Gateway-Front-End kann eine private IP-Adresse und/oder eine öffentliche IP-Adresse sein.  Die V1-SKU von Application Gateway unterstützt grundlegende öffentliche IP-Adressen – sowohl statisch als auch dynamisch.  Die V2-SKU unterstützt öffentliche IP-Adressen in der Standard-SKU, die nur statisch sein dürfen. Die V2-SKU von Application Gateway unterstützt keine interne IP-Adresse, da es sich nur um ein Front-End handelt.  Weitere Informationen finden Sie unter [Konfiguration der IP-Adresse des Application Gateway-Front-Ends](../../application-gateway/configuration-front-end-ip.md).  

In diesem Artikel erfahren Sie, wie Sie eine Application Gateway-Instanz mit einer vorhandenen öffentlichen IP-Adresse in Ihrem Abonnement erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Zwei öffentliche IP-Adressen der Standard-SKU in Ihrem Abonnement. Die IP-Adressen dürfen keinen Ressourcen zugeordnet sein. Weitere Informationen zum Erstellen einer öffentlichen IP-Adresse der Standard-SKU finden Sie unter [Erstellen einer öffentlichen IP-Adresse: Azure-Portal](../../virtual-network/create-public-ip-portal.md).
    - Benennen Sie die neuen öffentlichen IP-Adressen für die Beispiele in diesem Artikel mit **myStandardPublicIP-1** und **myStandardPublicIP-2**.

## <a name="create-application-gateway-existing-public-ip"></a>Erstellen einer Application Gateway-Instanz mit einer vorhandenen öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie eine Application Gateway-Ressource. Sie wählen die IP-Adresse aus, die Sie in den Voraussetzungen als öffentliche IP-Adresse für Application Gateway erstellt haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Application Gateway** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Anwendungsgateways** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie unter **Anwendungsgateway erstellen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**.<ul><li>Geben Sie **myResourceGroupAppGW** ein.</li></ul>Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name des Anwendungsgateways | Geben Sie **myAppGateway** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | Tarif | Übernehmen Sie den Standardwert **Standard V2**. |
    | Aktivieren der automatischen Skalierung | Übernehmen Sie den Standardwert **Ja**. |
    | Mindestanzahl der Instanzen | Übernehmen Sie den Standardwert **0**. |
    | Maximale Anzahl von Instanzen | Übernehmen Sie den Standardwert **10**. |
    | Verfügbarkeitszone | Übernehmen Sie den Standardwert **Keine**. |
    | HTTP2 | Übernehmen Sie den Standardwert **Deaktiviert**. |
    | Virtuelles Netzwerk | Wählen Sie **Neu erstellen**. <ul><li>Geben Sie unter **Virtuelles Netzwerk erstellen** als Namen **myVNet** ein.</li><li>Lassen Sie den Standardadressraum in **ADRESSRAUM** unverändert.</li><li>Ändern Sie den **Standardwert** unter **SUBNETZE** in **myAGSubnet**.</li><li>Geben Sie als zweiten Subnetznamen **myBackendSubnet** ein.</li><li>Geben Sie unter **Adressbereich** einen Bereich innerhalb des Standardadressraums ein.</li></ul>Klicken Sie auf **OK**.|

6. Wählen Sie **Weiter: Front-Ends** aus.

7. Wählen Sie auf der Registerkarte **Front-Ends** unter **Öffentliche IP-Adresse** die Option **myStandardPublicIP-1** oder Ihre öffentliche IP-Adresse aus.

8. Klicken Sie auf **Weiter: Back-Ends**. 

9. Wählen Sie **Back-End-Pool hinzufügen** aus.

10. Geben Sie unter **Back-End-Pool hinzufügen** als Namen **myBackendPool** ein.

11. Klicken Sie auf **Weiter: Konfiguration** aus.

12. Wählen Sie **+ Routingregel hinzufügen** aus. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Regelname | Geben Sie **myRoutingRule** ein. |
    | **Listener** |    |
    | Name des Listeners | Geben Sie **myListener** ein. |
    | Front-End-IP | Wählen Sie **Öffentlich** aus. |
    | Front-End-IP-Protokoll | Übernehmen Sie den Standardwert **HTTP**. |
    | Port | Übernehmen Sie den Standardwert **80**. |
    | **Zusätzliche Einstellungen** |   |
    | Listenertyp | Übernehmen Sie die Standardeinstellung **Basic**. |
    | URL zur Fehlerseite | Übernehmen Sie den Standardwert **Nein**. |
    | **Back-End-Ziele** |    |
    | Zieltyp | Übernehmen Sie die Standardeinstellung **Back-End-Pool**. |
    | Back-End-Ziel | Wählen Sie **myBackendPool** aus. |
    | HTTP-Einstellungen | Wählen Sie **Neue hinzufügen** aus.<ul><li>Geben Sie unter **Name** die Zeichenfolge **myHTTPsetting** ein.</li><li>Behalten Sie für die anderen Einstellungen die Standardwerte bei.</li></ul>Wählen Sie **Hinzufügen**.|

13. Wählen Sie **Hinzufügen**.

14. Wählen Sie **Weiter: Tags** und dann **Weiter: Überprüfen + erstellen** aus.

15. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Dies ist eine einfache Bereitstellung einer Application Gateway-Instanz. Informationen zur erweiterten Konfiguration und Einrichtung finden Sie unter [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](../../application-gateway/quick-create-portal.md).
>
> Weitere Informationen zu Azure Application Gateway finden Sie unter [Was ist Azure Application Gateway?](../../application-gateway/overview.md).

## <a name="change-or-remove-public-ip-address"></a>Ändern oder Entfernen öffentlicher IP-Adressen

Application Gateway unterstützt das Ändern der öffentlichen IP-Adresse nach der Erstellung nicht.

## <a name="more-information"></a>Weitere Informationen

* Wenn eine dynamische IP-Adresse der Basic-SKU einem Application Gateway-Front-End zugeordnet wurde, ändert sie sich nur, wenn das Gateway beendet oder gestartet wird. Der einem Application Gateway-Front-End zugeordnete DNS-Name ändert sich nicht. 

## <a name="caveats"></a>Vorbehalte

* Öffentliche IPv6-Adressen werden derzeit in Application Gateway-Instanzen nicht unterstützt.  

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Application Gateway-Instanz erstellen und eine vorhandene öffentliche IP-Adresse verwenden. 

- Weitere Informationen zu Azure Virtual Network NAT finden Sie unter [Was ist Azure Virtual Network NAT?](../nat-gateway/nat-overview.md).
- Weitere Informationen zu öffentlichen IP-Adressen in Azure finden Sie unter [Öffentliche IP-Adressen](../../virtual-network/public-ip-addresses.md).