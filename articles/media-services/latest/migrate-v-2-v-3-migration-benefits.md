---
title: Vorteile der Migration zur Media Services-API V3
description: In diesem Artikel werden die Vorteile der Migration von Media Services V2 zu V3 aufgeführt.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 1a733492a46b51d471a0efb0c5f316b0a47c8598
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353165"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Schritt 1: Informieren Sie sich über die Vorteile der Migration zur Media Services-API V3

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-1.svg)

## <a name="use-the-latest-api"></a>Verwenden der aktuellen API

Es wird empfohlen, jetzt die Version 2020-05-01 (oder höher) der Azure Media Services-API V3 zu verwenden, um von den Vorteilen zu profitieren, da neue Features, Funktionen und Leistungsoptimierungen nur in der aktuellen V3-API verfügbar sind.

Sie können die API-Version im Portal, in den neuesten SDKs, der neuesten Befehlszeilenschnittstelle und der REST-API mit der richtigen Versionszeichenfolge ändern.

Mit V3 wurde Media Services erheblich verbessert.  

## <a name="benefits-of-media-services-v3"></a>Vorteile von Media Services v3

| **V3-Feature** | **Vorteil** |
| --- | --- |
| **Azure portal** | |
| Azure-Portalupdates | Das Azure-Portal wurde aktualisiert, um die Verwaltung von V3-API-Entitäten einzubeziehen. Kunden können über das Portal das Livestreaming starten, V3-Transformationsaufträge übermitteln, Richtlinien zum Schutz von Inhalten und Streamingendpunkte verwalten, API-Zugriff erhalten, verknüpfte Speicherkonten verwalten und Überwachungsaufgaben durchführen. |
| **Konten und Speicher** | |
| Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen? | Kunden können jetzt ihre eigenen Rollen definieren und den Zugriff auf die einzelnen Entitäten in der Media Services ARM-API steuern. Dies hilft, den Zugriff auf Ressourcen durch AAD-Konten zu steuern. |
| Verwaltete Identitäten | Mit verwalteten Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten, da eine Identität für die Azure-Ressource in Azure AD bereitgestellt wird. Weitere Informationen zu verwalteten Identitäten finden Sie [hier](../../active-directory/managed-identities-azure-resources/overview.md). |
| Unterstützung für private Verbindungen | Kunden greifen über einen PrivateEndpoint in ihrem VNet auf Media Services-Endpunkte für Schlüsselübermittlung, LiveEvents und StreamingEndpoints zu. |
| [Kundenseitig verwaltete Schlüssel](concept-use-customer-managed-keys-byok.md) oder BYOK-Unterstützung (Bring Your Own Key) | Kunden können die Daten in ihrem Media Services-Konto mithilfe eines Schlüssels in ihrem Azure Key Vault verschlüsseln. |
| **Medienobjekte** | |
| Ein Medienobjekt kann mehrere [Streaminglocators](stream-streaming-locators-concept.md) mit verschiedenen Einstellungen für die [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und dynamische Verschlüsselung aufweisen. | Für jedes Medienobjekt sind maximal 100 Streaminglocators zulässig. Kunden können eine einzelne Kopie des Medieninhalts im Medienobjekt speichern, aber verschiedene Streaming-URLs mit unterschiedlichen Streamingrichtlinien oder Richtlinien zum Schutz von Inhalten freigeben, die auf einer bestimmten Zielgruppe basieren.
| **Auftragsverarbeitung** ||
| V3 führt das Konzept von  [Transformationen](transform-jobs-concept.md)  für dateibasierte Auftragsverarbeitung ein. | Eine Transformation kann verwendet werden, um wiederverwendbare Konfigurationen und Azure Resource Manager-Vorlagen zu erstellen, und um Verarbeitungseinstellungen zwischen mehreren Kunden oder Mandanten zu isolieren. |
| Für dateibasierte Auftragsverarbeitung können Sie als Eingabe eine HTTP(S)-URL verwenden. | Sie müssen noch keine Inhalte in Azure gespeichert haben und müssen auch keine Eingabemedienobjekte erstellen. |
| **Liveereignisse** ||
| Premium-1080p-Liveereignisse | Mit der neuen Liveereignis-SKU können Kunden eine Cloudcodierung mit einer Ausgabe in einer Auflösung von bis zu 1080p erhalten. |
| Neue Unterstützung für Livestreaming mit [niedriger Latenz](live-event-latency-reference.md) für Liveereignisse. | Dadurch können Benutzer Liveereignisse in nahezu Echtzeit verfolgen, was nicht der Fall wäre, wenn sie diese Einstellung nicht aktiviert hätten. |
| Die Vorschau von Liveereignissen unterstützt die  [dynamische Paketerstellung](encode-dynamic-packaging-concept.md) und dynamische Verschlüsselung. | Dadurch wird in der Vorschau der Inhaltsschutz sowie auch die Paketerstellung für DASH und HLS ermöglicht. |
| Liveausgaben ersetzen Programme | Die Liveausgabe ist einfacher zu verwenden als die Programmentität in den V2-APIs. |
| Die RTMP-Erfassung für Liveereignisse wurde verbessert, mit Unterstützung für weitere Encoder | Erhöht die Stabilität und bietet Flexibilität hinsichtlich des Quellencoders |
| Liveereignisse können rund um die Uhr gestreamt werden | Sie können ein Liveereignis hosten und Ihre Zielgruppe über einen längeren Zeitraum binden. |
| Livetranskription für Liveereignisse | Die Livetranskription ermöglicht Kunden die automatische Transkription von gesprochener Sprache in Text in Echtzeit während der Liveübertragung eines Ereignisses. Dies verbessert die Barrierefreiheit von Liveereignissen erheblich. |
| [Standbymodus](live-event-outputs-concept.md#standby-mode) für Liveereignisse | Liveereignisse im Standbyzustand sind kostengünstiger als aktive Liveereignisse. Dadurch können Kunden einen Satz von Liveereignissen verwalten, die innerhalb von Sekunden startbereit sind, und das zu geringeren Kosten als die Verwaltung eines Satzes von aktiven Liveereignissen. Die reduzierten Preise für Liveereignisse im Standbyzustand werden im Februar 2021 für die meisten Regionen in Kraft treten, der Rest folgt im April 2021.
|**Inhaltsschutz** ||
| Der [Inhaltsschutz](drm-content-key-policy-concept.md) unterstützt Features mit mehreren Schlüsseln. | Kunden können jetzt mehrere Verschlüsselungsschlüssel für Inhalte auf ihren Streaminglocators verwenden. |
| **Überwachung** | |
| Unterstützung von [Azure EventGrid](monitoring/reacting-to-media-services-events.md)-Benachrichtigungen | EventGrid-Benachrichtigungen weisen umfangreichere Features auf. Es gibt mehr Arten von Benachrichtigungen, eine umfassendere SDK-Unterstützung für den Empfang der Benachrichtigungen in Ihrer eigenen Anwendung und mehr bestehende Azure-Dienste, die als Ereignishandler fungieren können. |
| [Azure Monitor-Unterstützung und -Integration im Azure-Portal](monitoring/monitor-events-portal-how-to.md) | Dies ermöglicht Kunden die Visualisierung der Kontingentnutzung von Media Services, Echtzeitstatistiken von Streamingendpunkten sowie Erfassungs- und Archivstatistiken für Liveereignisse. Kunden sind jetzt in der Lage, Warnungen festzulegen und erforderliche Aktionen auf der Grundlage von Echtzeitmetrikdaten durchzuführen. |
