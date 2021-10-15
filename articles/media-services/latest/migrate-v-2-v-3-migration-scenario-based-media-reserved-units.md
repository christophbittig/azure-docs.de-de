---
title: Migrationsleitfaden für reservierte Einheiten für Medien (Media Reserved Units, MRUs)
description: In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für reservierte Einheiten für Medien (MRUs), der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: 98fd879cb8d960f2f688b3aa002fa86832981744
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353071"
---
# <a name="media-reserved-units-migration-guidance"></a>Migrationsleitfaden für reservierte Einheiten für Medien

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-4.svg)

In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für reservierte Einheiten für Medien (MRUs), der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.

> [!Important]
> Für Media Services-Konten sind keine reservierten Einheiten für Medien mehr erforderlich, da das System je nach Auslastung automatisch hoch- und herunterskaliert wird. 

## <a name="scenario-guidance"></a>Leitfaden für das Szenario

Migrieren Sie Ihre MRUs basierend auf den folgenden Szenarien:

* Bei Media Services-Konten müssen Sie keine reservierten Einheiten für Medien (Media Reserved Units, MRUs) mehr festlegen. Das System skaliert jetzt basierend auf der Last automatisch hoch oder herunter.
* Wenn Sie über ein Konto verfügen, das vor der API-Version 2020-05-01 erstellt wurde, haben Sie möglicherweise weiterhin Zugriff auf APIs zum Verwalten von MRUs. Allerdings wird keine der von Ihnen festgelegten MRU-Konfigurationen angewandt, um die Parallelität oder Leistung bei der Codierung zu steuern. Weitere Informationen finden Sie unter [Übersicht über das Skalieren der Medienverarbeitung](../previous/media-services-scale-media-processing-overview.md). Die reservierten Einheiten für Medien können mit der Befehlszeilenschnittstelle 2.0 für Media Services V3 oder über das Azure-Portal verwaltet werden.
* Wenn die Option zum Verwalten von reservierten Einheiten für Medien im Azure-Portal nicht angezeigt wird, nutzen Sie ein Konto, das mit einer API ab Version 2020-05-01 erstellt wurde.
* Wenn Sie bereits wissen, wie Sie Ihren MRU-Typ auf „S3“ festlegen, können Sie durch das Entfernen von MRUs die Leistung verbessern oder unverändert halten.
* Wenn Sie V2-Kunde sind, müssen Sie vor dem Abschluss der Migration ein neues V3-Konto erstellen, damit Ihre vorhandene Anwendung weiterhin unterstützt wird. 
* Indexer V1 oder andere Medienprozessoren, die noch nicht vollständig veraltet sind, müssen jedoch möglicherweise erneut aktiviert werden. 

Weitere Informationen zu reservierten Einheiten für Medien finden Sie unter [Reservierte Einheiten für Medien](concept-media-reserved-units.md) und [Skalieren reservierter Einheiten für Medien](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Konzepte, Tutorials und Schrittanleitungen für reservierte Einheiten für Medien

### <a name="concepts"></a>Konzepte

[Reservierte Einheiten für Medien](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Anleitungen

[Skalieren reservierter Einheiten für Medien](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Beispiele

Sehen Sie sich auch den [Vergleich von V2- und V3-Code in den Codebeispielen](migrate-v-2-v-3-migration-samples.md) an.
