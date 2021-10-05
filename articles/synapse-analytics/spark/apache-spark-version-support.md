---
title: Versionsunterstützung für Apache Spark
description: Unterstützte Versionen von Spark, Scala, Python, .NET
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/22/2021
ms.author: midesa
ms.openlocfilehash: 9f13a0797caa13f0d4367357308809c97bde0b17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577768"
---
# <a name="azure-synapse-runtimes"></a>Azure Synapse-Runtimes
Apache Spark-Pools in Azure Synapse verwenden Runtimes, um wichtige Komponentenversionen, Azure Synapse-Optimierungen, Pakete und Connectors mit einer bestimmten Apache Spark-Version zu verknüpfen. Diese Runtimes werden regelmäßig aktualisiert, um neue Verbesserungen, Features und Patches einzuschließen. 

Wenn Sie einen serverlosen Apache Spark-Pool erstellen, haben Sie die Möglichkeit, die entsprechende Apache Spark-Version auszuwählen. Auf dieser Grundlage wird der Pool mit den zugehörigen Runtime-Komponenten und -Paketen vorinstalliert. Diese Runtimes haben die folgenden Vorteile:

- Schnellere Startzeiten von Sitzungen
- Getestete Kompatibilität mit bestimmten Apache Spark-Versionen
- Zugriff auf beliebte, kompatible Connectors und Open-Source-Pakete

> [!NOTE]
> - Wartungsupdates werden automatisch auf neue Sitzungen für einen bestimmten serverlosen Apache Spark-Pool angewendet. 
> - Sie sollten testen und überprüfen, ob Anwendungen bei Verwendung neuer Runtime-Versionen ordnungsgemäß ausgeführt werden.

## <a name="supported-azure-synapse-runtime-releases"></a>Unterstützte Azure Synapse-Runtime-Releases 
In der folgenden Tabelle sind der Runtime-Name, die Apache Spark-Version und das Releasedatum für unterstützte Azure Synapse-Runtime-Releases aufgeführt.

|  Runtime-Name  | Veröffentlichungsdatum |  Releasestufe |
| ----- | ----- | ----- |
| [Azure Synapse Runtime für Apache Spark 2.4](./apache-spark-24-runtime.md) | 15. Dezember 2020 | Allgemein verfügbar|
| [Azure Synapse Runtime für Apache Spark 3.1](./apache-spark-3-runtime.md) | 26. Mai 2021 | Allgemein verfügbar |

## <a name="runtime-release-stages"></a>Runtime-Releasestufen

## <a name="preview-runtimes"></a>Preview-Runtimes
Azure Synapse Analytics bietet Previews, über die Sie Features bewerten und Feedback zu diesen teilen können, bevor sie allgemein verfügbar (GA) werden. Während eine Runtime in der Vorschau verfügbar ist, können neue Abhängigkeiten und Komponentenversionen eingeführt werden. Support-SLAs gelten nicht für Preview-Runtimes. 

## <a name="generally-available-runtimes"></a>Allgemein verfügbare Runtimes
Allgemein verfügbare (GA) Runtimes stehen allen Kunden zur Verfügung und sind für die Produktion bereit. Sobald eine Runtime allgemein verfügbar ist, können Sicherheitskorrekturen und Stabilitätsverbesserungen zurückportiert werden. Darüber hinaus werden neue Komponenten nur eingeführt, wenn sie keine zugrunde liegenden Abhängigkeiten oder Komponentenversionen ändern. 
