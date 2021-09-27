---
title: Speicherkontoübersicht
titleSuffix: Azure Storage
description: Erfahren Sie mehr über die verschiedenen Typen von Speicherkonten in Azure Storage. Überprüfen Sie Kontobenennung, Leistungsstufen, Zugriffsebenen, Redundanz, Verschlüsselung, Endpunkte und mehr.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: cb19372e5b97a9f508367366faa9ae49d0d11261
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596959"
---
# <a name="storage-account-overview"></a>Speicherkontoübersicht

Ein Azure Storage-Konto enthält alle Ihre Azure Storage-Datenobjekte: Blobs, Dateifreigaben, Warteschlangen, Tabellen und Datenträger. Das Speicherkonto stellt einen eindeutigen Namespace für Ihre Azure Storage-Daten bereit, auf den von jedem Ort der Welt aus über HTTP oder HTTPS zugegriffen werden kann. Daten in Ihrem Speicherkonto sind dauerhaft und hochverfügbar, sicher und extrem skalierbar.

Um zu erfahren, wie Sie ein Azure Storage-Konto erstellen, lesen Sie den Artikel [Erstellen eines Speicherkontos](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Speicherkontentypen

Azure Storage bietet mehrere Arten von Speicherkonten. Jeder Typ unterstützt unterschiedliche Features und verfügt über ein eigenes Preismodell. Informieren Sie sich vor dem Erstellen eines Speicherkontos genau über diese Unterschiede, um den Kontotyp zu ermitteln, der für Ihre Anwendungen am besten geeignet ist.

In der folgenden Tabelle werden die Typen von Speicherkonten beschrieben, die von Microsoft für die meisten Szenarien empfohlen werden. Bei allen diesen Typen wird das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Bereitstellungsmodell verwendet.

| Speicherkontotyp | Unterstützte Speicherdienste | Redundanzoptionen | Verbrauch |
|--|--|--|--|
| Standard „Allgemein v2“ | Blob (einschließlich Data Lake Storage<sup>1</sup>), Warteschlange und Tabellenspeicher, Azure Files  | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Standard-Speicherkontotyp für Blobs, Dateifreigaben, Warteschlangen und Tabellen. Empfohlen für die meisten Azure Storage-Szenarien. Hinweis: Wenn Sie NFS-Dateifreigaben in Azure Files unterstützen möchten, verwenden Sie den Kontotyp „Premium-Dateifreigaben“. |
| Premium-Blockblobs<sup>3</sup> | Blob Storage (einschließlich Data Lake Storage Storage<sup>1</sup>) | LRS<br /><br />ZRS<sup>2</sup> | Kontotyp „Premium Storage“ für Block- und Anfügeblobs. Empfohlen für Szenarien mit hohen Transaktionsraten oder Szenarien, die kleinere Objekte verwenden oder aber eine gleichbleibend geringe Speicherlatenz erfordern. [Weitere Informationen zu Beispielworkloads.](../blobs/storage-blob-performance-tiers.md#premium-performance) |
| Premium-Dateifreigaben<sup>3</sup> | Azure Files | LRS<br /><br />ZRS<sup>2</sup> | Kontotyp „Premium Storage“ nur für Dateifreigaben. Empfohlen für Unternehmens- oder Hochleistungsanwendungen. Verwenden Sie diesen Kontotyp, wenn Ihr Speicherkonto sowohl SMB- als auch NFS-Dateifreigaben unterstützen soll. |
| Premium-Seitenblobs<sup>3</sup> | Nur Seiten-BLOBs | LRS | Storage Premium-Kontotyp nur für Seitenblobs. [Informieren Sie sich ausführlicher über Seitenblobs und Beispiel-Anwendungsfälle.](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Data Lake Storage basiert auf Azure Blob Storage und bietet eine Reihe von Funktionen für die Big Data-Analyse. Weitere Informationen finden Sie unter [Einführung in Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) und [Erstellen eines Speicherkontos für die Verwendung mit Data Lake Storage Gen2](../blobs/create-data-lake-storage-account.md).

<sup>2</sup> Zonenredundanter Speicher (ZRS) und geozonenredundanter Speicher (GZRS/RA-GZRS) stehen in bestimmten Regionen nur für Standardkonten des Typs „Universell V2“ sowie für Konten des Typs „Premium-Blockblobs“ und „Premium-Dateifreigaben“ zur Verfügung. Weitere Informationen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

<sup>3</sup> Speicherkonten in einer Premium-Leistungsstufe verwenden SSD-Datenträger (Solid State Drives, SSDs) für niedrige Latenz und hohen Durchsatz.

Legacy-Speicherkonten werden ebenfalls unterstützt. Weitere Informationen finden Sie unter [Legacy-Speicherkontotypen](#legacy-storage-account-types).

## <a name="storage-account-endpoints"></a>Speicherkontoendpunkte

Ein Speicherkonto bietet einen eindeutigen Namespace für Ihre Daten in Azure. Jedes Objekt, das Sie in Azure speichern, besitzt eine Adresse, die den eindeutigen Namen Ihres Speicherkontos enthält. Die Kombination aus Kontoname und Azure Storage-Dienstendpunkt bildet die Endpunkte für Ihr Speicherkonto.

Beachten Sie bei der Benennung Ihres Speicherkontos folgende Regeln:

- Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.
- Der Name Ihres Speicherkontos muss innerhalb von Azure eindeutig sein. Zwei Speicherkonten können nicht denselben Namen haben.

In der folgenden Tabelle sind die Formate den Endpunkt für die einzelnen Azure Storage-Dienste aufgeführt.

| Speicherdienst | Endpunkt |
|--|--|
| Blobspeicher | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Tabellenspeicher | `https://<storage-account>.table.core.windows.net` |

Erstellen Sie die URL für den Zugriff auf ein Objekt in einem Speicherkonto, indem Sie den Objektstandort im Speicherkonto an den Endpunkt anhängen. Beispielsweise ist die URL für einen Blob ähnlich wie folgendes:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Sie können Ihr Speicherkonto auch für die Verwendung einer benutzerdefinierten Domäne konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihr Azure Storage-Konto](../blobs/storage-custom-domain-name.md).

## <a name="migrate-a-storage-account"></a>Migrieren eines Speicherkontos

Die folgende Tabelle fasst zusammen und verweist auf Anleitungen zum Verschieben, Aktualisieren oder Migrieren eines Speicherkontos:

| Migrationsszenario | Details |
|--|--|
| Verschieben eines Speicherkontos in ein anderes Abonnement | Der Azure Resource Manager bietet Optionen zum Verschieben einer Ressource in ein anderes Abonnement. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Verschieben Sie ein Speicherkonto in eine andere Ressourcengruppe | Der Azure Ressourcenmanager bietet Optionen zum Verschieben einer Ressource in eine andere Ressourcengruppe. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Verschieben eines Speicherkontos in eine andere Region | Erstellen Sie eine Kopie Ihres Speicherkontos in einer anderen Region, um ein Speicherkonto zu verschieben. Verschieben Sie dann Ihre Daten in dieses Konto, indem Sie AzCopy oder ein anderes Tool Ihrer Wahl verwenden. Weitere Informationen finden Sie unter [Verschieben eines Azure Speicherkontos in eine andere Region](storage-account-move.md). |
| Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“ | Sie können ein Allzweck v1-Speicherkonto oder ein Blob-Speicherkonto auf ein Allzweck v2-Konto aktualisieren. Diese Aktion kann nicht mehr rückgängig gemacht werden. Weitere Informationen finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md). |
| Migrieren eines klassischen Speicherkontos zu Azure Ressourcenmanager | Das Azure Ressourcenmanager-Bereitstellungsmodell ist dem klassischen Bereitstellungsmodell im Hinblick auf Funktionalität, Skalierbarkeit und Sicherheit überlegen. Weitere Informationen zum Migrieren eines klassischen Speicherkontos zu Azure Resource Manager finden Sie unter [Plattformgestützte Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) im Abschnitt „Migration von Speicherkonten“. |

## <a name="transfer-data-into-a-storage-account"></a>Übertragen von Daten in ein Speicherkonto

Microsoft bietet Dienste und Hilfsprogramme für den Import Ihrer Daten von lokalen Speichergeräten oder Drittanbietern von Cloudspeichern. Welche Lösung Sie nutzen, richtet sich nach der Menge an Daten, die Sie übertragen müssen. Weitere Informationen finden Sie unter [Azure Storage-Migration – Übersicht](storage-migration-overview.md).

## <a name="storage-account-encryption"></a>Speicherkontoverschlüsselung

Alle Daten in Ihrem Speicherkonto werden automatisch auf Dienstseite verschlüsselt. Weitere Informationen zur Verschlüsselung und Schlüsselverwaltung finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Speicherkontoabrechnung

Die Abrechnung für Azure Storage basiert auf der Nutzung Ihres Speicherkontos. Alle Objekte in einem Speicherkonto werden zusammen als Gruppe abgerechnet. Die Speicherkosten werden nach den folgenden Faktoren berechnet:

- **Region** bezieht sich auf die geografische Region, in der sich Ihr Konto befindet.
- **Kontotyp** verweist auf den Typ des Speicherkontos, das Sie verwenden.
- **Zugriffsebene** bezieht sich auf das Datenverwendungsmuster, das Sie für Ihr Konto vom Typ „Allgemein v2“ oder Ihr Blobspeicherkonto angegeben haben.
- **Kapazität** bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird.
- Die **Redundanz** bestimmt, wie viele Kopien Ihrer Daten jeweils an welchen Standorten unterhalten werden.
- **Transaktionen** beziehen sich auf alle Lese- und Schreibvorgänge in Azure Storage.
- **Datenausgang** bezieht sich alle Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. Weitere Informationen zur Verwendung von Ressourcengruppen zum Gruppieren von Daten und Diensten in der gleichen Region zur Beschränkung vom Gebühren für den Datenausgang, finden Sie unter [Was ist eine Azure-Ressourcengruppe?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Die Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/) bietet detaillierte Preisinformationen basierend auf Kontotyp, Speicherkapazität, Replikation und Transaktionen. In der [Preisübersicht für die Datenübertragung](https://azure.microsoft.com/pricing/details/data-transfers/) finden Sie detaillierte Preisinformationen für den Datenausgang. Sie können den [Azure Storage-Preisrechner](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) verwenden, um Ihre Kosten zu bestimmen.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Legacy-Speicherkontotypen

In der folgenden Tabelle werden die Legacy-Speicherkontotypen beschrieben. Diese Kontotypen werden von Microsoft nicht empfohlen, können aber in bestimmten Szenarien verwendet werden:

| Legacy-Speicherkontotyp | Unterstützte Speicherdienste | Redundanzoptionen | Bereitstellungsmodell | Verbrauch |
|--|--|--|--|--|
| Standard „Allgemein v1“ | Blob, Warteschlange und Tabellenspeicher, Azure Files | LRS/GRS/RA-GRS | Resource Manager, klassisch | Konten vom Typ „Allgemein v1“ bieten möglicherweise nicht die neuesten Features oder die niedrigsten Preise pro Gigabyte. Erwägen Sie die Verwendung für diese folgenden Szenarien:<br /><ul><li>Für Ihre Anwendungen ist das [klassische Bereitstellungsmodell](../../azure-portal/supportability/classic-deployment-model-quota-increase-requests.md) von Azure erforderlich.</li><li>Ihre Anwendungen verursachen eine hohe Transaktionslast oder nutzen eine erhebliche Bandbreite für die Georeplikation, erfordern aber keine großen Kapazitäten. In diesem Fall sind Allgemein v1-Konten möglicherweise die wirtschaftlich sinnvollste Wahl.</li><li>Sie verwenden eine ältere Version der REST-API für Azure Storage (vor 2014-02-14) oder eine Clientbibliothek mit einer niedrigeren Version als 4.x und können kein Upgrade für Ihre Anwendung durchführen.</li></ul> |
| Standard-Blobspeicher | Blobspeicher (nur Block- und Anfügeblobs) | LRS/GRS/RA-GRS | Ressourcen-Manager | Microsoft empfiehlt stattdessen nach Möglichkeit die Verwendung von Standardkonten vom Typ „Standard-Allgemein v2“. |

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](storage-account-create.md)
- [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md)
- [Wiederherstellen eines gelöschten Speicherkontos](storage-account-recover.md)
