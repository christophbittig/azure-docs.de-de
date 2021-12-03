---
title: Verwenden privater Endpunkte für den sicheren Zugriff auf Purview
description: In diesem Artikel wird allgemein beschrieben, wie Sie einen privaten Endpunkt für Ihr Purview-Konto nutzen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: dba76c0b2d25c8bc962dd847e0d2ffdaa5bdd3eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234225"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Verwenden privater Endpunkte für Ihr Azure Purview-Konto

> [!IMPORTANT]
> Wenn Sie **vor dem 27. September 2021, 15:30 Uhr UTC** einen privaten _Portalendpunkt_ für Ihr Purview-Konto erstellt haben, müssen Sie die unter [Neukonfigurieren des DNS für private Portalendpunkte](#reconfigure-dns-for-portal-private-endpoints) beschriebenen erforderlichen Maßnahmen ergreifen. **Diese Maßnahmen müssen vor dem 12. November 2021 abgeschlossen sein. Andernfalls werden die vorhandenen privaten Portalendpunkte nicht mehr funktionieren**.


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

### <a name="review-your-current-dns-settings"></a>Überprüfen der aktuellen DNS-Einstellungen

1. Suchen Sie im Azure-Portal nach Ihrem Purview-Konto. Klicken Sie im Menü auf der linken Seite auf **Netzwerk**, und wählen Sie die Option **Verbindungen mit privatem Endpunkt** aus. Klicken Sie in der Liste auf jeden privaten Endpunkt, und führen Sie die folgenden Schritte aus.

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-1.png" alt-text="Screenshot: Privater Purview-Endpunkt."lightbox="media/catalog-private-link/purview-pe-dns-updates-1.png":::

2. Wenn die Zielunterressource das _Portal_ ist, überprüfen Sie die **DNS-Konfiguration**. Gehen Sie andernfalls zurück zum vorherigen Schritt, und wählen Sie den nächsten privaten Endpunkt aus, bis Sie alle privaten Endpunkte überprüft und alle privaten Endpunkte validiert haben, die dem Portal zugeordnet sind.

    :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-2.png" alt-text="Screenshot: Privater Purview-Endpunkt des Portals."lightbox="media/catalog-private-link/purview-pe-dns-updates-2.png":::

3. Überprüfen Sie im Fenster **DNS-Konfiguration** die aktuellen Einstellungen:
   
    - Wenn im Abschnitt **Benutzerdefinierte DNS-Einträge** Einträge vorhanden sind, führen Sie die Schritte unter [Wartungsszenarien 1](#scenario-1) und [Wiederherstellungsszenario 2](#scenario-2) aus.
    
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-3.png" alt-text="Screenshot: Benutzerdefinierte DNS-Konfiguration des privaten Purview-Endpunkts des Portals."lightbox="media/catalog-private-link/purview-pe-dns-updates-3.png":::

    - Wenn im Abschnitt **Konfigurationsname** Einträge vorhanden sind und die DNS-Zone `privatelink.purviewstudio.azure.com` lautet, ist für diesen privaten Endpunkt keine Aktion erforderlich. Kehren Sie zu **Schritt 1** zurück, und überprüfen Sie die verbleibenden privaten Portalendpunkte.
  
        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-4.png" alt-text="Screenshot: Privater Purview-Endpunkt des Portals mit neuer DNS-Zone."lightbox="media/catalog-private-link/purview-pe-dns-updates-4.png":::
    
    - Wenn im Abschnitt **Konfigurationsname** Einträge vorhanden sind und die DNS-Zone `privatelink.purview.azure.com` lautet, führen Sie die Schritte in [Wartungsszenario 3](#scenario-3) aus.

        :::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-5.png" alt-text="Screenshot: Privater Purview-Endpunkt des Portals mit alter DNS-Zone."lightbox="media/catalog-private-link/purview-pe-dns-updates-5.png":::

### <a name="remediation-scenarios"></a>Wartungsszenarien

#### <a name="scenario-1"></a>Szenario 1

Wenn Sie **erforderliche DNS-A-Einträge direkt Ihrem DNS oder der Hostdatei Ihrer Computer hinzugefügt haben**, ist **keine Aktion erforderlich**.
    
:::image type="content" source="media/catalog-private-link/purview-pe-dns-updates-host.png" alt-text="Screenshot: Hostdatei mit A-Einträgen."lightbox="media/catalog-private-link/purview-pe-dns-updates-host.png":::

#### <a name="scenario-2"></a>Szenario 2

Wenn Sie **lokale DNS-Server**, **DNS-Weiterleitungen oder benutzerdefinierte DNS-Auflösung** konfiguriert haben, überprüfen Sie Ihre DNS-Einstellungen,und ergreifen Sie die entsprechenden Aktionen:

1. Überprüfen Sie Ihren DNS-Server. Wenn Ihr DNS-Eintrag `web.purview.azure.com` lautet oder Ihre bedingte Weiterleitung `purview.azure.com` ist, **ist keine Aktion erforderlich**. 

2. Wenn Ihr DNS-Eintrag `web.privatelink.purview.azure.com` ist, ändern Sie ihn in `web.privatelink.purviewstudio.azure.com`.

3. Wenn Ihre bedingte Weiterleitung `privatelink.purview.azure.com` ist, ENTFERNEN Sie die Zone NICHT. Sie müssen `privatelink.purviewstudio.azure.com` eine neue bedingte Weiterleitung hinzufügen.

#### <a name="scenario-3"></a>Szenario 3

Wenn Sie die **Integration von privaten Azure-DNS-Zonen** für Ihr Purview-Konto konfiguriert haben, führen Sie die folgenden Schritte aus, um private Endpunkte erneut bereitzustellen, um die DNS-Einstellungen neu zu konfigurieren:

1. Bereitstellen eines neuen privaten Portalendpunkts:
       
    1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). Klicken Sie auf Ihr Azure Purview-Konto und wählen Sie unter **Einstellungen** **Netzwerk** die Option **Private Endpunktverbindungen** aus.

        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal.png" alt-text="Screenshot: Erstellen eines privaten Endpunkts im Portal."lightbox="media/catalog-private-link/purview-pe-reconfigure-portal.png":::

    2. Wählen Sie **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen.

    3. Geben Sie die grundlegenden Informationen an.

    4. Wählen Sie auf der Registerkarte **Ressource** für **Ressourcentyp** die Option **Microsoft.Purview/account** aus.

    5. Wählen Sie für **Ressource** das Azure Purview-Konto und für **Untergeordnete Zielressource** die Option **Portal** aus.

    6. Wählen Sie auf der Registerkarte **Konfiguration** das virtuelle Netzwerk aus, und klicken Sie dann auf „Azure Private DNS zone“ (Azure Private DNS-Zone), um eine neue Azure DNS-Zone zu erstellen.
            
        :::image type="content" source="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png" alt-text="Screenshot: Erstellen eines privaten Portalendpunkts und DNS-Einstellungen"lightbox="media/catalog-private-link/purview-pe-reconfigure-portal-dns.png":::

    7. Wechseln Sie zur Zusammenfassungsseite, und wählen Sie **Erstellen** aus, um den privaten Endpunkt des Portals zu erstellen.

2. Löschen Sie den vorherigen privaten Portalendpunkt, der dem Purview-Konto zugeordnet ist. 

3. Stellen Sie sicher, dass während der Bereitstellung des privaten Portalendpunkts eine neue private Azure-DNS-Zone `privatelink.purviewstudio.azure.com` erstellt wird und dass in der privaten DNS-Zone ein entsprechender A-Eintrag (Web) vorhanden ist. 
    
4. Stellen Sie sicher, dass Sie Azure Purview Studio erfolgreich laden können. Es kann einige Minuten (ca. 10 Minuten) dauern, bis das neue DNS-Routing nach der Neukonfiguration des DNS wirksam wird. Wenn es nicht sofort geladen wird, können Sie einige Minuten warten und es dann noch mal versuchen.
    
5. Wenn bei der Navigation ein Fehler auftritt, führen Sie nslookup web.purview.azure.com aus. Dies sollte in eine private IP-Adresse aufgelöst werden, die dem privaten Portalendpunkt zugeordnet ist.
  
6. Wiederholen Sie die oben erläuterten Schritte 1 bis 3 für alle vorhandenen privaten Portalendpunkte. 

### <a name="validation-steps"></a>Überprüfungsschritte

1. Stellen Sie sicher, dass Sie Azure Purview Studio erfolgreich laden können. Es kann einige Minuten (ca. 10 Minuten) dauern, bis das neue DNS-Routing nach der Neukonfiguration des DNS wirksam wird. Wenn es nicht sofort geladen wird, können Sie einige Minuten warten und es dann noch mal versuchen.

2. Wenn bei der Navigation ein Fehler auftritt, führen Sie nslookup `web.purview.azure.com` aus. Diese Angabe sollte in eine private IP-Adresse aufgelöst werden, die dem privaten Portalendpunkt zugeordnet ist.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  

Häufig gestellte Fragen zu Bereitstellungen privater Endpunkte in Azure Purview finden Sie unter [Häufig gestellte Fragen zu privaten Azure Purview-Endpunkten](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung 
Informationen zur Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten finden Sie unter [Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Bekannte Einschränkungen
Eine Liste der aktuellen Einschränkungen im Zusammenhang mit privaten Azure Purview-Endpunkten finden Sie unter [Bekannte Einschränkungen für private Azure Purview-Endpunkte](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Nächste Schritte

- [Stellen Sie ein privates End-to-End-Netzwerk bereit.](./catalog-private-link-end-to-end.md)
- [Stellen Sie ein privates Netzwerk für Purview Studio bereit.](./catalog-private-link-account-portal.md)
