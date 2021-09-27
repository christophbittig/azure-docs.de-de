---
title: Verwalten mehrerer Mandanten mit Azure Video Analyzer for Media (früher Video Indexer) – Azure
description: In diesem Artikel werden verschiedene Integrationsoptionen für die Verwaltung mehrerer Mandanten mit Azure Video Analyzer for Media (früher Video Indexer) vorgeschlagen.
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: d771c6add9b9b9b3484a15ee630b20f94cba7e4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673918"
---
# <a name="manage-multiple-tenants"></a>Verwalten mehrerer Mandanten

In diesem Artikel werden verschiedene Optionen für die Verwaltung mehrerer Mandanten mit Azure Video Analyzer for Media (früher Video Indexer) erläutert. Wählen Sie eine Methode, die sich für Ihr Szenario am besten eignet:

* Video Analyzer for Media-Konto pro Mandant
* Einzelnes Video Analyzer for Media-Konto für alle Mandanten
* Azure-Abonnement pro Mandant

## <a name="video-analyzer-for-media-account-per-tenant"></a>Video Analyzer for Media-Konto pro Mandant

Wenn Sie diese Architektur verwenden, wird für jeden Mandanten ein Video Analyzer for Media-Konto erstellt. Die Mandanten zeichnen sich durch vollständige Isolation auf der persistenten und Compute-Ebene aus.  

![Video Analyzer for Media-Konto pro Mandant](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Überlegungen

* Kunden nutzen Speicherkonten nicht gemeinsam (es sei denn, der Kunde hat dies manuell konfiguriert).
* Kunden nutzen die Compute-Leistung (reservierten Einheiten) nicht gemeinsam und beeinflussen die Verarbeitungszeiten der Aufträge der jeweils anderen nicht.
* Sie können einen Mandanten ganz einfach aus dem System entfernen, indem Sie das Video Analyzer for Media-Konto löschen.
* Es gibt keine Möglichkeit, dass Mandanten benutzerdefinierte Modelle gemeinsam nutzen.

    Stellen Sie sicher, dass keine geschäftliche Notwendigkeit zur gemeinsamen Nutzung benutzerdefinierter Modelle besteht.
* Schwierigere Verwaltung aufgrund mehrerer Video Analyzer for Media-Konten (und zugehöriger Media Services-Konten) pro Mandant.

> [!TIP]
> Erstellen Sie im [Video Indexer-Entwicklerportal](https://api-portal.videoindexer.ai/) einen Administratorbenutzer für Ihr System, und stellen Sie Ihren Mandanten mithilfe der Autorisierungs-API das entsprechende [Kontozugriffstoken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token) zur Verfügung.

## <a name="single-video-analyzer-for-media-account-for-all-users"></a>Einzelnes Video Analyzer for Media-Konto für alle Benutzer

Bei Verwenden dieser Architektur ist der Kunde für die Isolation von Mandanten zuständig. Alle Mandanten müssen ein einzelnes Video Analyzer for Media-Konto mit einem einzelnen Azure Media Service-Konto verwenden. Beim Hochladen, Durchsuchen oder Löschen von Inhalten muss der Kunde die richtigen Ergebnisse für diesen Mandanten filtern.

![Einzelnes Video Analyzer for Media-Konto für alle Benutzer](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Mit dieser Option können Anpassungsmodelle (Person, Sprache und Marken) gemeinsam genutzt oder zwischen Mandanten durch Filtern der Modelle nach Mandant isoliert werden.

Wenn Sie [Videos hochladen](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video), können Sie pro Mandant ein anderes Partitionsattribut angeben. Dies ermöglicht die Isolation in der [Such-API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos). Durch Angabe des Partitionsattributs in der Such-API erhalten Sie nur die Ergebnisse der angegebenen Partition. 

### <a name="considerations"></a>Überlegungen

* Möglichkeit, dass Mandanten Inhalte und benutzerdefinierte Modelle gemeinsam nutzen.
* Ein Mandant wirkt sich auf die Leistung anderer Mandanten aus.
* Der Kunde muss basierend auf Video Analyzer for Media eine komplexe Verwaltungsebene aufbauen.

> [!TIP]
> Sie können mit dem Attribut [Priorität](upload-index-videos.md) Aufträge von Mandanten priorisieren.

## <a name="azure-subscription-per-tenant"></a>Azure-Abonnement pro Mandant 

Wenn Sie diese Architektur verwenden, verfügt jeder Mandant über ein eigenes Azure-Abonnement. Für jeden Benutzer erstellen Sie im Abonnement des Mandanten ein neues Video Analyzer for Media-Konto.

![Azure-Abonnement pro Mandant](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Überlegungen

* Dies ist die einzige Möglichkeit, eine Trennung für die Abrechnung zu ermöglichen.
* Diese Integration weist mehr Verwaltungsaufwand als ein Video Analyzer for Media-Konto pro Mandant auf. Wenn die Abrechnung nicht erforderlich ist, wird empfohlen, eine der anderen in diesem Artikel beschriebenen Optionen zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
