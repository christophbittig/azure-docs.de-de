---
title: Verwenden privater Endpunkte für den sicheren Zugriff auf Purview
description: In diesem Artikel wird allgemein beschrieben, wie Sie einen privaten Endpunkt für Ihr Purview-Konto nutzen können.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b65820ab72c0a20953a7e3e9e4a2ee964df51e07
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831611"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Verwenden privater Endpunkte für Ihr Azure Purview-Konto

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
|**Szenario 1** - [Privates und sicheres Herstellen einer Verbindung mit Ihrem Purview-Konto](./catalog-private-link-account-portal.md)   | Sie müssen den Zugriff auf Ihr Azure Purview-Konto aktivieren, einschließlich des Zugriffs auf _Azure Purview Studio_ und die Atlas-API über private Endpunkte. (Bereitstellen privater Endpunkte für Azure Purview-_Konto_ und _Portal_)   |
|**Szenario 2** - [Privates und sicheres Überprüfen Ihrer Datenquellen](./catalog-private-link-ingestion.md)  | Sie müssen Datenquellen lokal und in Azure hinter einem virtuellen Netzwerk mithilfe der selbstgehosteten Integration Runtime überprüfen. (Bereitstellen privater Endpunkte für die _Erfassung_)    |
|**Szenario 3** - [Privates und sicheres Herstellen einer Verbindung mit Azure Purview und Überprüfen von Datenquellen](./catalog-private-link-end-to-end.md) |Sie müssen den Zugriff auf Ihr Azure Purview-Konto nur über einen privaten Endpunkt beschränken, einschließlich des Zugriffs auf Azure Purview Studio, Atlas-APIs, und Datenquellen in lokalen Umgebungen und Azure überprüfen, hinter einem virtuellen Netzwerk mithilfe der selbstgehosteten Integration Runtime, um die durchgängige Netzwerkisolation sicherzustellen. (Bereitstellen von privaten Endpunkten für _Konto_, _Portal_ und _Erfassung_)   |

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

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen  

Häufig gestellte Fragen zu Bereitstellungen privater Endpunkte in Azure Purview finden Sie unter [Häufig gestellte Fragen zu privaten Azure Purview-Endpunkten](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung 
Informationen zur Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten finden Sie unter [Problembehandlung bei der Konfiguration privater Endpunkte für Purview-Konten](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Bekannte Einschränkungen
Eine Liste der aktuellen Einschränkungen im Zusammenhang mit privaten Azure Purview-Endpunkten finden Sie unter [Bekannte Einschränkungen für private Azure Purview-Endpunkte](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen privater Endpunkte für die Erfassung](./catalog-private-link-ingestion.md)
