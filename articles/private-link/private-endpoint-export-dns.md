---
title: Exportieren von DNS-Einträgen für einen privaten Endpunkt über das Azure-Portal
titleSuffix: Azure Private Link
description: In diesem Tutorial erfahren Sie, wie Sie die DNS-Einträge für einen privaten Endpunkt im Azure-Portal exportieren.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713092"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>Exportieren von DNS-Einträgen für einen privaten Endpunkt über das Azure-Portal

Ein privater Endpunkt in Azure erfordert DNS-Einträge für die Namensauflösung des Endpunkts. Der DNS-Eintrag löst die private IP-Adresse des Endpunkts für die konfigurierte Ressource auf. Wenn Sie DNS-Einträge des Endpunkts exportieren möchten, können Sie das Private Link Center im Portal verwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ein privater Endpunkt, der in Ihrem Abonnement konfiguriert ist. Für das Beispiel in diesem Artikel wird ein privater Endpunkt für eine Azure-Web-App verwendet. Weitere Informationen zum Erstellen eines privaten Endpunkts für eine Web-App finden Sie unter [Tutorial: Herstellen einer Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts](tutorial-private-endpoint-webapp-portal.md).

## <a name="export-endpoint-dns-records"></a>Exportieren von DNS-Einträgen für Endpunkte

In diesem Abschnitt melden Sie sich beim Azure-Portal an und suchen nach Private Link Center.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Private Link** in das Suchfeld ein.

3. Wählen Sie **Private Link** aus.

4. Wählen Sie in Private Link Center im linken Menü die Option **Private Endpunkte** aus.

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="Auswählen von „Private Endpunkte“ in Private Link Center":::

5. Wählen Sie unter **Private Endpunkte** den Endpunkt aus, für den Sie die DNS-Einträge exportieren möchten. Wählen Sie **Download hostfile** (Hostdatei herunterladen) aus, um die DNS-Einträge für den Endpunkt in einem Hostdateiformat herunterzuladen.
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="Herunterladen der DNS-Einträge des Endpunkts":::

6. Die Einträge der heruntergeladenen Hostdatei sehen in etwa wie folgt aus:

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Private Link und DNS finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](private-endpoint-dns.md).

Weitere Informationen zu Azure Private Link finden Sie hier:

* [Was ist Azure Private Link?](private-link-overview.md)
* [Was ist der Azure Private Link-Dienst?](private-link-service-overview.md)
* [Häufig gestellte Fragen (FAQ) zu Azure Private Link](private-link-faq.yml)