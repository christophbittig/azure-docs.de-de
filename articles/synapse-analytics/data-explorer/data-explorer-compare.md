---
title: Unterschiede zwischen Azure Data Explorer und Azure Synapse Data Explorer (Vorschau)
description: In diesem Artikel werden die Unterschiede zwischen Azure Data Explorer und Azure Synapse Data Explorer beschrieben.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: a6368d0431d2ce388f2d5bed37d125a34b4d5094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029604"
---
# <a name="what-is-the-difference-between-azure-synapse-data-explorer-and-azure-data-explorer-preview"></a>Worin besteht der Unterschied zwischen Azure Synapse Data Explorer und Azure Data Explorer? (Vorschau)

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) ist ein eigenständiger, schneller und hochgradig skalierbarer Dienst zur Untersuchung von Protokoll- und Telemetriedaten. Die dem Dienst zugrunde liegende Technologie ist auch in Azure Synapse als integrierter Analysedienst verfügbar, um die vorhandenen SQL- und Spark-Dienste für Szenarien mit maschinellem Lernen für Data Warehouses und Datentechnik zu ergänzen.

Durch die native Integration von Data Explorer-Funktionen stehen in Synapse Workloads für Analyseprotokolle und Zeitreihen in Form von Synapse Data Explorer-Pools zur Verfügung. Diese Pools bieten eine enge Integration und eine einfachere Verwaltung der Konnektivität mit anderen Synapse-Funktionen. Wenn Sie alle Ihre Computer- und Benutzerdaten an einem Ort verfügbar und zugänglich machen und die Untersuchungsfunktionen von Data Explorer nahezu in Echtzeit nutzen, können Sie Lösungen erstellen, die den Geschäftswert steigern.

Es wird empfohlen, mit Synapse Data Explorer zu beginnen, wenn Sie nach einer einheitlichen Lösung suchen, die Big Data- und Analysefunktionen kombiniert.

## <a name="capability-support"></a>Funktionsunterstützung

| Category | Funktion | Azure-Daten-Explorer | Synapse Data Explorer |
|--|--|--|--|
| **Security** | VNET | Unterstützt VNet-Einschleusung und Azure Private Link | Unterstützung für Azure Private Link als Teil des verwalteten Synapse-VNet automatisch integriert |
|  | CMK | ✓ | Automatisch von der Konfiguration des Synapse-Arbeitsbereichs geerbt |
|  | Firewall | ✗ | Automatisch von der Konfiguration des Synapse-Arbeitsbereichs geerbt |
| **Geschäftskontinuität** | Verfügbarkeitszonen | Optional | Standardmäßig aktiviert, wenn Verfügbarkeitszonen verfügbar sind |
| **SKU** | Computeoptionen | Mehr als 22 Azure-VM-SKUs zur Auswahl | Vereinfacht in SKUs für Synapse-Workloadtypen |
| **Integrationen** | Integrierte Erfassungspipelines | Event Hub, Event Grid, IoT Hub | Event Hub, Event Grid und IoT Hub über das Azure-Portal für nicht verwaltete VNets unterstützt |
|  | Spark-Integration | Verknüpfter Azure Data Explorer-Dienst: integrierte Kusto Spark-Integration mit Unterstützung für Passthrough-Authentifizierung von Azure Active Directory, MSI des Synapse-Arbeitsbereichs und Dienstprinzipal | Integrierte Unterstützung für Kusto Spark-Connector mit Unterstützung für Passthrough-Authentifizierung von Azure Active Directory, MSI des Synapse-Arbeitsbereichs und Dienstprinzipal |
|  | Verwaltung von KQL-Artefakten | ✗ | Speichern von KQL-Abfragen und Integrieren in Git |
|  | Synchronisierung von Metadaten | ✗ | Externe Tabellen werden mit dem zentralen Synapse-Metaspeicher für die Nutzung durch andere Synapse-Dienste synchronisiert. |
| **Funktionen** | Übersicht über KQL-Abfragen | ✓ | ✓ |
|  | API und SDKs | ✓ | ✓ |
|  | Connectors | ✓ | ✓ |
|  | Abfragetools | ✓ | ✓ |
| **Preise** | Geschäftsmodell | Kosten- und Abrechnungsmodell mit mehreren Verbrauchseinheiten: Azure Data Explorer-IP-Markup, Compute, Speicher und Netzwerk | V-Kern-Abrechnungsmodell mit zwei Verbrauchseinheiten: V-Kern und Speicher |
|  | Reservierte Instanzen | ✓ | ✗ |

## <a name="next-steps"></a>Nächste Schritte

[Data Explorer in Azure Synapse Analytics](data-explorer-overview.md)
