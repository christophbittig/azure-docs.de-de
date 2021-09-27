---
title: Auswählen einer Azure-Lösung für regelmäßige Datenübertragung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure-Lösung für die Datenübertragung auswählen, wenn Sie regelmäßig Daten übertragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: alkohli
ms.openlocfilehash: 00ff58c2d11ba76889e1293bdb386c199cb3de00
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589281"
---
# <a name="solutions-for-periodic-data-transfer"></a>Lösungen für eine regelmäßige Datenübertragung

Dieser Artikel gibt einen Überblick über die Datenübertragungslösungen für regelmäßige Datenübertragungen. Die regelmäßige Datenübertragung über das Netzwerk kann als in regelmäßigen Abständen wiederkehrend oder als kontinuierliche Datenverschiebung kategorisiert werden. Der Artikel beschreibt auch die empfohlenen Datenübertragungsoptionen sowie die jeweilige Schlüsselfunktionsmatrix für dieses Szenario.

Eine Übersicht über alle verfügbaren Datenübertragungsoptionen finden Sie unter [Auswählen einer Azure-Datenübertragungslösung](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Empfohlene Optionen

Die empfohlenen Optionen für die regelmäßige Datenübertragung lassen sich in zwei Kategorien einteilen, je nachdem, ob es sich um eine wiederkehrende oder kontinuierliche Übertragung handelt.

- **Skripts/programmgesteuerte Tools**: Für eine Datenübertragung, die in regelmäßigen Abständen stattfindet, verwenden Sie Skripts und programmgesteuerte Tools wie AzCopy und die Azure Storage REST-APIs. Diese Tools sind für IT-Profis und Entwickler geeignet.

  - **AzCopy**: Verwenden Sie dieses Befehlszeilentool, um Daten einfach und schnell in und aus Azure Blobs, Files und Table Storage mit optimaler Leistung zu kopieren. AzCopy unterstützt Nebenläufigkeit und Parallelität sowie die Fortsetzung unterbrochener Kopiervorgänge.
  - **Azure Storage REST-APIs/SDKs**: Beim Erstellen einer Anwendung können Sie die Anwendung mithilfe von Azure Storage REST-APIs entwickeln und die in mehreren Sprachen angebotenen Azure SDKs verwenden. Die REST-APIs können auch die Azure Storage-Datenverschiebungsbibliothek nutzen, die speziell für das leistungsstarke Kopieren von Daten aus und in Azure entwickelt wurde.

- **Tools für kontinuierliche Datenerfassung**: Für kontinuierliche, fortlaufende Datenerfassung können Sie eine der folgenden Optionen auswählen.

  - **Objektreplikation**: Die Objektreplikation kopiert Blockblobs asynchron zwischen Containern in einem Quell- und einem Zielspeicherkonto. Verwenden Sie die Objektreplikation als Lösung, um Container in zwei verschiedenen Speicherkonten synchron zu halten.
  - **Azure Data Factory**: Data Factory sollte verwendet werden, um einen Übertragungsvorgang aufzuskalieren, und wenn es Bedarf für Orchestrierung und Überwachungsfunktionen auf Unternehmensebene gibt. Verwenden Sie Azure Data Factory, um eine Cloudpipeline einzurichten, die regelmäßig Dateien zwischen mehreren Azure-Diensten, lokal oder einer Kombination aus beidem überträgt. Mit Azure Data Factory können Sie datengesteuerte Workflows orchestrieren, die Daten aus unterschiedlichen Datenspeichern erfassen, und die Datenverschiebung und Datentransformation automatisieren.
  - **Azure Data Box-Produktfamilie für Onlineübertragungen**: Data Box Edge und Data Box Gateway sind Onlinenetzwerkgeräte, die Daten in und aus Azure übertragen können. Data Box Edge verwendet KI-fähiges (künstliche Intelligenz) Edge-Compute, um Daten vor dem Hochladen zu vorzuverarbeiten. Data Box Gateway ist eine virtuelle Version des Geräts mit den gleichen Datenübertragungsfunktionen.

Data Box-Onlineübertragungsgeräte oder Azure Data Factory werden von IT-Profis eingerichtet und können die Datenübertragung transparent automatisieren.

## <a name="comparison-of-key-capabilities"></a>Vergleich der Schlüsselfunktionen

In der folgenden Tabelle werden die Unterschiede der Schlüsselfunktionen zusammengefasst.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Datenübertragung mit Skripts/programmgesteuerte Netzwerkdatenübertragung

| Funktion                  | AzCopy                                 | Table Storage REST-APIs       |
|-----------------------------|----------------------------------------|-------------------------------|
| Formfaktor                 | Befehlszeilentool von Microsoft       | Kunden entwickeln für Storage <br> REST-APIs mit Azure-Clientbibliotheken |
| Anfängliche einmalige Konfiguration     | Minimal                                | Mittel, variabler Entwicklungsaufwand    |
| Datenformat                 | Azure-Blobs, Azure Files, Azure Tables | Azure-Blobs, Azure Files, Azure Tables   |
| Leistung                 | Bereits optimiert                      | Optimierung bei der Entwicklung                  |
| Preise                     | Kostenlos, Gebühren für Datenerfassung fallen an      | Kostenlos, Gebühren für Datenerfassung fallen an        |

### <a name="continuous-data-ingestion-over-network"></a>Kontinuierliche Datenerfassung über Netzwerk

| Funktion                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Formfaktor                                   | Virtuelles Gerät             | Physisches Gerät          | Dienst im Azure-Portal, Agent lokal                                                            |
| Hardware                                      | Ihr Hypervisor            | Bereitgestellt von Microsoft    | Nicht verfügbar                                                            |
| Für Anfangskonfiguration erforderlicher Aufwand                          | Niedrig (< 30 Minuten)            | Mittel (mehrere Stunden) | Groß (mehrere Tage)                                                 |
| Datenformat                                   | Azure-Blobs, Azure Files   | Azure-Blobs, Azure Files | [Unterstützt mehr als 70 Datenconnectors für Datenspeicher und Formate](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Datenvorverarbeitung                           | Nein                         | Ja, über Edge-Compute    | Ja                                                           |
| Lokaler Cache<br>(zum Speichern lokaler Daten)    | Ja                        | Ja                      | Nein                                                            |
| Übertragung aus anderen Clouds                    | Nein                         | Nein                       | Ja                                                           |
| Preise                                       | [Preise](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Preise](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Preise](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Nächste Schritte

- [Übertragen von Daten mit AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Weitere Informationen zu Datenübertragungen mit Storage REST-APIs](/dotnet/api/overview/azure/storage).
- Informationen zum
  - [Übertragen von Daten mit Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md)
  - [Transformieren von Daten mit Data Box Edge vor dem Senden an Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Informationen zum Übertragen von Daten mit Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).
