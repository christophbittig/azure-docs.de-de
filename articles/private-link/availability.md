---
title: Azure Private Link-Verfügbarkeit
description: In diesem Artikel erfahren Sie, welche Azure-Dienste Private Link unterstützen.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: ff4b23ffaf366ac9c1583b6fb9eb87f87b7db0e0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428936"
---
# <a name="azure-private-link-availability"></a>Azure Private Link-Verfügbarkeit

Mit Azure Private Link können Sie über einen [privaten Endpunkt](private-endpoint-overview.md) in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste (beispielsweise Azure Storage und SQL Database) sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen. 

> [!IMPORTANT]
> Azure Private Link ist jetzt allgemein verfügbar. Sowohl der Dienst für private Endpunkte als auch der Dienst „Private Link“ (Dienst im Hintergrund von Load Balancer Standard) ist allgemein verfügbar. Für unterschiedliche Azure PaaS-Instanzen wird das Onboarding für Azure Private Link basierend auf verschiedenen Zeitplänen durchgeführt. Weitere Informationen zu bekannten Einschränkungen finden Sie unter [Privater Endpunkt](private-endpoint-overview.md#limitations) und [Private Link-Dienst](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Dienstverfügbarkeit

In den folgenden Tabellen sind die Private Link-Dienste und die Regionen, in denen sie verfügbar sind, aufgelistet. 

### <a name="ai--machine-learning"></a>KI + Machine Learning

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Alle öffentlichen Regionen    |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Machine Learning erstellen.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analyse

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Alle öffentlichen Regionen <br/> Alle Government-Regionen |  Wird für die Proxy-[Verbindungsrichtlinie](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt |Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Synapse Analytics erstellen.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Alle öffentlichen Regionen<br/>Alle Government-Regionen      |   | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Event Hub erstellen.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics und Application Insights) | Alle öffentlichen Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Monitor erstellen.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen    | Anmeldeinformationen müssen in einem Azure-Schlüsseltresor gespeichert werden.| Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Data Factory erstellen.](../data-factory/data-factory-private-link.md)   |
|Azure HDInsight | Alle öffentlichen Regionen<br/>Alle Government-Regionen      |   | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure HDInsight erstellen.](../hdinsight/hdinsight-private-link.md)  |

### <a name="compute"></a>Compute

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Alle öffentlichen Regionen      |  | Allgemein verfügbar  </br> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure App Configuration erstellen.](../azure-app-configuration/concept-private-endpoint.md) |
|Verwaltete Azure-Datenträger | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen    | [Auswählen, um bekannte Einschränkungen aufzurufen](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Allgemein verfügbar   <br/> Informationen zum Erstellen eines privaten Endpunkts für Azure Managed Disks finden Sie [hier](../virtual-machines/disks-enable-private-links-for-import-export-portal.md).   |

### <a name="containers"></a>Container

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Alle öffentlichen Regionen<br/> Alle Government-Regionen    | Wird für den Premium-Tarif der Containerregistrierung unterstützt [Für Tarife auswählen](../container-registry/container-registry-skus.md)| Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Container Registry erstellen.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service: Kubernetes-API | Alle öffentlichen Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Kubernetes Service erstellen.](../aks/private-clusters.md)   |

### <a name="databases"></a>Datenbanken

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL-Datenbank         | Alle öffentlichen Regionen <br/> Alle Government-Regionen<br/>Alle China-Regionen      |  Wird für die Proxy-[Verbindungsrichtlinie](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure SQL erstellen.](./tutorial-private-endpoint-sql-portal.md)      |
|Azure Cosmos DB|  Alle öffentlichen Regionen<br/> Alle Government-Regionen</br> Alle China-Regionen | |Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Cosmos DB erstellen.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL (Einzelserver)         | Alle öffentlichen Regionen <br/> Alle Government-Regionen<br/>Alle China-Regionen     | Unterstützt für universelle und arbeitsspeicheroptimierte Tarife | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for PostgreSQL erstellen.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen      |  | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for MySQL erstellen.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen     |  | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for MariaDB erstellen.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integration

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Event Grid erstellen.](../event-grid/network-security.md) |
|Azure-Servicebus | Alle öffentlichen Regionen<br/>Alle Government-Regionen  | Wird für den Premium-Tarif von Azure Service Bus unterstützt [Für Tarife auswählen](../service-bus-messaging/service-bus-premium-messaging.md) | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Service Bus erstellen.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet der Dinge (IoT, Internet of Things)

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Alle öffentlichen Regionen    |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure IoT Hub erstellen.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Alle von Azure Digital Twins unterstützten öffentlichen Regionen     |  | Vorschau <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Digital Twins erstellen.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Verwaltung und Governance

| Unterstützte Dienste | Verfügbare Regionen | Andere Aspekte | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure-Automatisierung  | Alle öffentlichen Regionen<br/> Alle Government-Regionen |  | Allgemein verfügbar </br> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Automation erstellen.](../automation/how-to/private-link-security.md)|
|Azure Backup | Alle öffentlichen Regionen<br/> Alle Government-Regionen   |  | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Backup erstellen.](../backup/private-endpoints.md)   |
|Azure Purview | „Asien, Südosten“, „Australien, Osten“, „Brasilien, Süden“, „Europa, Norden“, „Europa, Westen“, „Kanada, Mitte“, „USA, Osten“, „USA, Osten 2“, „USA, Osten 2 EUAP“, „USA, Süden-Mitte“, „USA, Westen-Mitte“, „USA, Westen 2“, „Indien, Mitte“, „Vereinigtes Königreich, Süden“   | [Auswählen, um bekannte Einschränkungen aufzurufen](../purview/catalog-private-link-troubleshoot.md#known-limitations) | Allgemein verfügbar <br/> [Informationen dazu, wie Sie einen privaten Endpunkt für Azure Purview erstellen](../purview/catalog-private-link.md)   |

### <a name="security"></a>Sicherheit

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure-Schlüsseltresor         | Alle öffentlichen Regionen<br/> Alle Government-Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Key Vault erstellen.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (einschließlich Data Lake Storage Gen2)       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird nur für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Blobspeicher erstellen.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alle öffentlichen Regionen<br/> Alle Government-Regionen      | |   Allgemein verfügbar <br/> [Erfahren Sie, wie Azure Files-Netzwerkendpunkte erstellt werden.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure-Dateisynchronisierung | Alle öffentlichen Regionen      | |   Allgemein verfügbar <br/> [Erfahren Sie, wie Azure Files-Netzwerkendpunkte erstellt werden.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue Storage       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird nur für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Warteschlangenspeicher erstellen.](tutorial-private-endpoint-storage-portal.md) |
| Azure-Tabellenspeicher       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird nur für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Tabellenspeicher erstellen.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Alle öffentlichen Regionen, außer: Deutschland, MITTE, Deutschland, NORDOSTEN <br/> Alle Government-Regionen  | | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Batch erstellen.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | USA, OSTEN; USA, SÜDEN-MITTE;<br/>USA, WESTEN 2; Alle China-Regionen      |  | Vorschau   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure SignalR erstellen.](../azure-signalr/howto-private-endpoints.md)   |
|Azure-Web-Apps | Alle öffentlichen Regionen<br/> China, Norden 2 und Osten 2    | Unterstützt mit PremiumV2-, PremiumV3- oder Functions-Premium-Plan  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure-Web-Apps erstellen.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Alle öffentlichen Regionen <br/> Alle Government-Regionen | Unterstützt mit Dienst im privaten Modus | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Search erstellen.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Alle öffentlichen Regionen      |  | Vorschau <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Relay erstellen.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Private Link-Dienst mit Load Balancer Standard

|Unterstützte Dienste  |Verfügbare Regionen | Andere Aspekte | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link-Dienste hinter Azure Load Balancer Standard | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen  | Wird von Load Balancer Standard unterstützt | Allgemein verfügbar <br/> [Erfahren Sie mehr über den Private Link-Dienst.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über den Azure Private Link-Dienst:
- [Was ist Azure Private Link?](private-link-overview.md)
- [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](create-private-endpoint-portal.md)
