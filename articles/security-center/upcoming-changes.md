---
title: Wichtige bevorstehende Änderungen an Azure Security Center
description: Bevorstehende Änderungen an Azure Security Center, die Sie beachten sollten und für die Sie ggf. planen müssen
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/05/2021
ms.author: memildin
ms.openlocfilehash: e6d1c8cf55687a8e4d7612ca432a314ae38256bc
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534410"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Wichtige bevorstehende Änderungen an Azure Security Center

> [!IMPORTANT]
> Die Informationen auf dieser Seite beziehen sich auf Vorabversionen von Produkten oder Features, die vor einer möglichen Veröffentlichung für den Handel wesentlich verändert werden können. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.

Auf dieser Seite erfahren Sie, welche Änderungen für Security Center geplant sind. Es werden geplante Änderungen am Produkt beschrieben, die sich auf Dinge wie Ihre Sicherheitsbewertung oder Workflows auswirken könnten.

Die neuesten Versionshinweise finden Sie unter [Neuerungen in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplante Änderungen

| Geplante Änderung       | Voraussichtliches Datum der Änderung |
|----------------------|---------------------------|
| [Veraltete Vorschauwarnung: ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | Oktober 2021|
| [Die Legacy-Implementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| Oktober 2021|
| [Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Oktober 2021    |
| [Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | Q1 2022    |
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Veraltete Vorschauwarnung: ARM.MCAS_ActivityFromAnonymousIPAddresses

**Geschätztes Datum für die Änderung:** Oktober 2021

Die folgende Vorschauwarnung wird eingestellt:

|Warnungsname| BESCHREIBUNG|
|----------------------|---------------------------|
|**VORSCHAU: Aktivität von einer riskanten IP-Adresse**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Es wurde eine Benutzeraktivität über eine IP-Adresse erkannt, die als anonyme Proxy-IP-Adresse identifiziert wurde.<br>Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden. Die Erkennung nutzt einen Algorithmus für maschinelles Lernen, um falsch positive Ergebnisse wie etwa falsch gekennzeichnete IP-Adressen zu reduzieren, die regelmäßig von anderen Benutzern in der Organisation verwendet werden.<br>Erfordert eine aktive Microsoft Cloud App Security-Lizenz|
|||

Wir haben neue Warnungen erstellt, die diese Informationen in erweiterter Form bereitstellen. Darüber hinaus ist für die neuere Warnung (ARM_OperationFromSuspiciousIP, ARM_OperationFromSuspiciousProxyIP) keine Lizenz für Microsoft Cloud App Security erforderlich.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Die Legacyimplementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.

**Geschätztes Datum für die Änderung:** Oktober 2021

Die Legacyimplementierung von ISO 27001 wird vom Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen entfernt. Wenn Sie Ihre ISO 27001-Konformität mit Security Center nachverfolgen, integrieren Sie den neuen Standard „ISO 27001:2013“ für alle relevanten Verwaltungsgruppen oder Abonnements. Die derzeitige Legacynorm „ISO 27001“ wird in Kürze vom Dashboard entfernt.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center-Dashboard zur Einhaltung gesetzlicher Bestimmungen mit der Meldung, dass die Legacyimplementierung von ISO 27001 entfernt wird" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen

**Geschätztes Datum für die Änderung:** Oktober 2021

Im August 2021 wurden zwei neue **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Ausführliche Informationen finden Sie in den [Versionshinweisen](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Wenn die Empfehlungen für die allgemeine Verfügbarkeit freigegeben werden, ersetzen sie die folgenden vorhandenen Empfehlungen:

- **Endpoint Protection sollte auf Ihren Computern installiert sein** ersetzt Folgendes:
    - [Endpoint Protection-Lösung auf virtuellen Computern installieren (Schlüssel: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Endpoint Protection-Lösung auf Ihren Computern installieren (Schlüssel: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **Endpoint Protection-Integritätsprobleme sollten auf Ihren Computern gelöst werden** ersetzt die vorhandene Empfehlung mit derselben Bezeichnung. Die beiden Empfehlungen verfügen über unterschiedliche Bewertungsschlüssel:
    - Bewertungsschlüssel für die **Vorschau**-Empfehlung: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Bewertungsschlüssel für die **GA**-Empfehlung (allgemeine Verfügbarkeit): 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

Weitere Informationen:
- [Von Azure Security Center unterstützte Endpoint Protection-Lösungen](security-center-services.md#endpoint-supported)
- [Bewerten des Status Ihrer bereitgestellten Lösungen durch diese Empfehlungen](security-center-endpoint-protection.md)

### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken

**Geschätztes Datum für die Änderung:** 1. Quartal 2022

Die Empfehlung **Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden** in der Sicherheitskontrolle **Datenklassifizierung anwenden** wird durch eine neue Version ersetzt, die stärker an der Microsoft-Strategie zur Datenklassifizierung ausgerichtet ist. Infolgedessen ändert sich auch die ID der Empfehlung (derzeit b0df6f56-862d-4730-8597-38c0fd4ebd59).


## <a name="next-steps"></a>Nächste Schritte

Alle aktuellen Änderungen an Security Center finden Sie unter [Neuerungen in Azure Security Center](release-notes.md).