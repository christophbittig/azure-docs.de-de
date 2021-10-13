---
title: Verwenden privater Endpunkte für den sicheren Zugriff auf Purview
description: In diesem Artikel wird allgemein beschrieben, wie Sie einen privaten Endpunkt für Ihr Purview-Konto nutzen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 9d92a1baddbd12f80084dbbdb9a9205edb3f56b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272849"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Verwenden privater Endpunkte für Ihr Azure Purview-Konto

> [!IMPORTANT]
> Wenn Sie **vor dem 27. September 2021, 15:30 Uhr UTC** einen privaten _Portalendpunkt_ für Ihr Purview-Konto erstellt haben, müssen Sie die unter [Neukonfigurieren des DNS für private Portalendpunkte](#reconfigure-dns-for-portal-private-endpoints) beschriebenen erforderlichen Maßnahmen ergreifen. **Diese Aktionen müssen vor dem 11. Oktober 2021 abgeschlossen sein. Ansonsten funktionieren vorhandene private Portalendpunkte nicht mehr.**


In diesem Artikel wird beschrieben, wie private Endpunkte für Azure Purview konfiguriert werden.

## <a name="conceptual-overview"></a>Konzeptionelle Übersicht
Sie können [private Azure-Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Azure Purview-Konten verwenden, um Benutzern in einem virtuellen Netzwerk (VNet) den sicheren Zugriff auf den Katalog über eine private Verbindung zu ermöglichen. Ein privater Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihr Purview-Konto. Der Netzwerkdatenverkehr zwischen den Clients im VNet und dem Purview-Konto wird über das VNet und eine private Verbindung im Microsoft-Backbone-Netzwerk geleitet. 

Sie können den privaten Endpunkt des Azure Purview-_Kontos_ bereitstellen, um nur Clientaufrufe an Azure Purview zuzulassen, die aus dem privaten Netzwerk stammen.

Der privaten Azure Purview-Endpunkt _Portal_ ist erforderlich, um die Verbindung zu Azure Purview Studio über eine private Netzwerkkonnektivität zu ermöglichen.

Sie können private Endpunkte für die _Datenerfassung_ bereitstellen, wenn Sie Azure IaaS- und PaaS-Datenquellen in virtuellen Azure-Netzwerken und lokalen Datenquellen über eine private Verbindung überprüfen müssen. Diese Methode stellt die Netzwerkisolation für Ihre Metadaten sicher, die von den Datenquellen an Azure Purview Data Map fließen.

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="Screenshot: Azure Purview mit privaten Endpunkten"::: 

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor der Bereitstellung privater Endpunkte für das Azure Purview-Konto sicher, dass die folgenden Voraussetzungen erfüllt sind:

1. Ein Azure-Konto mit einem aktiven Abonnement. [Kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. Eine vorhandene Azure Virtual Network-Instanz Stellen Sie ein neues [virtuelles Azure-Netzwerk](../virtual-network/quick-create-portal.md) bereit, wenn Sie noch keins besitzen.
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Bereitstellungsszenarios für private Azure Purview-Endpunkte

Verwenden Sie die folgende empfohlene Checkliste, um die Bereitstellung des Azure Purview-Kontos mit privaten Endpunkten durchzuführen:

|Szenario  |Ziele  |
|---------|---------|
|**Szenario 1**[: Privates und sicheres Herstellen einer Verbindung mit Azure Purview und Überprüfen von Datenquellen](./catalog-private-link-end-to-end.md) |Sie müssen den Zugriff auf Ihr Azure Purview-Konto nur über einen privaten Endpunkt beschränken, einschließlich des Zugriffs auf Azure Purview Studio, Atlas-APIs, und Datenquellen in lokalen Umgebungen und Azure überprüfen, hinter einem virtuellen Netzwerk mithilfe der selbstgehosteten Integration Runtime, um die durchgängige Netzwerkisolation sicherzustellen. (Bereitstellen von privaten Endpunkten für _Konto_, _Portal_ und _Erfassung_)   |
|**Szenario 2**[: Privates und sicheres Herstellen einer Verbindung mit Ihrem Purview-Konto](./catalog-private-link-account-portal.md)   | Sie müssen den Zugriff auf Ihr Azure Purview-Konto aktivieren, einschließlich des Zugriffs auf _Azure Purview Studio_ und die Atlas-API über private Endpunkte. (Bereitstellen privater Endpunkte für Azure Purview-_Konto_ und _Portal_)   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>Unterstützungsmatrix für das Überprüfen von Datenquellen über einen privaten Endpunkt für _Erfassung_

In Szenarios, in denen der private Endpunkt für _Erfassung_ in Ihrem Azure Purview-Konto verwendet wird und der öffentliche Zugriff auf Ihre Datenquellen deaktiviert ist, kann Azure Purview die folgenden Datenquellen hinter einem privaten Endpunkt überprüfen:

|Datenquelle hinter einem privaten Endpunkt  |Integration Runtime-Typ  |Anmeldeinformationstyp  |
|---------|---------|---------|
|Azure Blob Storage | Selbstgehostete Integration Runtime | Dienstprinzipal|
|Azure Blob Storage | Selbstgehostete Integration Runtime | Kontoschlüssel|
|Azure Data Lake Storage Gen 2 | Selbstgehostete Integration Runtime| Dienstprinzipal|
|Azure Data Lake Storage Gen 2 | Selbstgehostete Integration Runtime| Kontoschlüssel|
|Azure SQL-Datenbank | Selbstgehostete Integration Runtime| SQL-Authentifizierung|
|Azure SQL-Datenbank | Selbstgehostete Integration Runtime| Dienstprinzipal|
|Verwaltete Azure SQL-Instanz | Selbstgehostete Integration Runtime| SQL-Authentifizierung|
|Azure Cosmos DB| Selbstgehostete Integration Runtime| Kontoschlüssel|
|SQL Server | Selbstgehostete Integration Runtime| SQL-Authentifizierung|
|Azure Synapse Analytics | Selbstgehostete Integration Runtime| Dienstprinzipal|
|Azure Synapse Analytics | Selbstgehostete Integration Runtime| SQL-Authentifizierung|

## <a name="reconfigure-dns-for-portal-private-endpoints"></a>Neukonfigurieren des DNS für private Portalendpunkte

Wenn Sie **vor dem 27. September 2021, 15:30 Uhr UTC** einen privaten _Portalendpunkt_ für Ihr Purview-Konto erstellt haben, ergreifen Sie die in diesem Abschnitt beschriebenen erforderlichen Maßnahmen.

- Wenn Sie **ein benutzerdefiniertes DNS verwenden oder erforderliche A-DNS-Einträge direkt** in der Hostdatei Ihrer Computer hinzugefügt haben, ist **keine Aktion erforderlich**. 

- Wenn Sie die **Integration von Azure Private DNS-Zonen** für Ihr Purview-Konto konfiguriert haben, führen Sie die folgenden Schritte aus, um private Endpunkte erneut bereitzustellen, um die DNS-Einstellungen neu zu konfigurieren: 

    1. Bereitstellen eines neuen privaten Portalendpunkts:
       
        1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Klicken Sie auf Ihr Azure Purview-Konto und wählen Sie unter **Einstellungen** **Netzwerk** die Option **Private Endpunktverbindungen** aus.

            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts im Portal.":::

        2. Wählen Sie **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen.

        3. Geben Sie die grundlegenden Informationen an.

        4. Wählen Sie auf der Registerkarte **Ressource** für **Ressourcentyp** die Option **Microsoft.Purview/account** aus.

        5. Wählen Sie für **Ressource** das Azure Purview-Konto und für **Untergeordnete Zielressource** die Option **Portal** aus.

        6. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk aus, und klicken Sie dann auf „Azure Private DNS zone“ (Azure Private DNS-Zone), um eine neue Azure DNS-Zone zu erstellen.
            
            :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="Screenshot: Erstellen eines privaten Portalendpunkts und DNS-Einstellungen":::

        7. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt des Portals zu erstellen.

    2. Löschen Sie den vorherigen privaten Portalendpunkt, der dem Purview-Konto zugeordnet ist. 

    3. Stellen Sie sicher, dass während der Bereitstellung des privaten Portalendpunkts eine neue Azure Private DNS-Zone (privatelink.purviewstudio.azure.com) erstellt wird und dass in der Private DNS-Zone ein entsprechender A-Eintrag (Web) vorhanden ist. 
    
    4. Stellen Sie sicher, dass Sie Azure Purview Studio erfolgreich laden können. Es kann einige Minuten (ca. 10 Minuten) dauern, bis das neue DNS-Routing nach der Neukonfiguration des DNS wirksam wird. Wenn es nicht sofort geladen wird, können Sie einige Minuten warten und es dann noch mal versuchen.
    
    5. Wenn bei der Navigation ein Fehler auftritt, führen Sie nslookup web.purview.azure.com aus. Dies sollte in eine private IP-Adresse aufgelöst werden, die dem privaten Portalendpunkt zugeordnet ist.
  
    6. Wiederholen Sie die oben erläuterten Schritte 1 bis 3 für alle vorhandenen privaten Portalendpunkte. 

- Wenn Sie eine **lokale oder benutzerdefinierte DNS-Auflösung konfiguriert** haben (z. B. wenn Sie Ihre eigenen DNS-Server verwenden oder Hostdateien konfiguriert haben), ergreifen Sie die folgenden Maßnahmen: 

  - Wenn Ihr DNS-Eintrag `web.purview.azure.com` ist, **ist keine Aktion erforderlich**.  
  - Wenn Ihr DNS-Eintrag `web.privatelink.purview.azure.com` ist, ändern Sie ihn in `web.privatelink.purviewstudio.azure.com`. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  

Häufig gestellte Fragen zu Bereitstellungen privater Endpunkte in Azure Purview finden Sie unter [Häufig gestellte Fragen zu privaten Azure Purview-Endpunkten](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung 
Informationen zur Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten finden Sie unter [Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Bekannte Einschränkungen
Eine Liste der aktuellen Einschränkungen im Zusammenhang mit privaten Azure Purview-Endpunkten finden Sie unter [Bekannte Einschränkungen für private Azure Purview-Endpunkte](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Nächste Schritte

- [Stellen Sie ein privates End-to-End-Netzwerk bereit.](./catalog-private-link-end-to-end.md)
- [Stellen Sie ein privates Netzwerk für Purview Studio bereit.](./catalog-private-link-account-portal.md)
