---
title: Wichtige bevorstehende Änderungen an Azure Security Center
description: Bevorstehende Änderungen an Azure Security Center, die Sie beachten sollten und für die Sie ggf. planen müssen
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 08/19/2021
ms.author: memildin
ms.openlocfilehash: 0e9988aa7779d85714d6e1562af78c3a5bf0e1ff
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444690"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Wichtige bevorstehende Änderungen an Azure Security Center

> [!IMPORTANT]
> Die Informationen auf dieser Seite beziehen sich auf Vorabversionen von Produkten oder Features, die vor einer möglichen Veröffentlichung für den Handel wesentlich verändert werden können. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.

Auf dieser Seite erfahren Sie, welche Änderungen für Security Center geplant sind. Es werden geplante Änderungen am Produkt beschrieben, die sich auf Dinge wie Ihre Sicherheitsbewertung oder Workflows auswirken könnten.

Die neuesten Versionshinweise finden Sie unter [Neuerungen in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplante Änderungen

| Geplante Änderung       | Voraussichtliches Datum der Änderung |
|----------------------|---------------------------|
| [Die Legacy-Implementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| August 2021|
| [Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“](#changing-prefix-of-some-alert-types-from-arm_-to-vm_)                                          | Oktober 2021|
| [Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Q4 2021    |
| [Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | Q1 2022    |


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Die Legacyimplementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.

**Geschätztes Datum der Änderung:** August 2021

Die Legacyimplementierung von ISO 27001 wird vom Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen entfernt. Wenn Sie Ihre ISO 27001-Konformität mit Security Center nachverfolgen, integrieren Sie den neuen Standard „ISO 27001:2013“ für alle relevanten Verwaltungsgruppen oder Abonnements. Die derzeitige Legacynorm „ISO 27001“ wird in Kürze vom Dashboard entfernt.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center-Dashboard zur Einhaltung gesetzlicher Bestimmungen mit der Meldung, dass die Legacyimplementierung von ISO 27001 entfernt wird" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="changing-prefix-of-some-alert-types-from-arm_-to-vm_"></a>Ändern des Präfix einiger Warnungstypen von „ARM_“ in „VM_“ 

**Geschätztes Datum für die Änderung:** Oktober 2021

Im Juli 2021 haben wir eine [logische Neuorganisation des Azure Defender für Resource Manager-Warnungen](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) angekündigt. 

Im Rahmen einer logischen Neuorganisation für einige der Azure Defender-Pläne wurden 21 Warnungen vom [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) in den [Azure Defender für Server](defender-for-servers-introduction.md) verschoben.

Wir planen nun, die Präfixe dieser Warnungen entsprechend dieser Neuzuweisung zu aktualisieren. Wir ersetzen „ARM_“ durch „VM_“, wie in der folgenden Tabelle dargestellt.

| Aktueller Name                                   | Nach dieser Änderung                             |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |

Hier finden Sie weitere Informationen zu den Plänen [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md) und [Azure Defender für Server](defender-for-servers-introduction.md).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen

**Geschätztes Datum für die Änderung:** Q4 2021

Im August 2021 wurden zwei neue **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Ausführliche Informationen finden Sie in den [Versionshinweisen](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Wenn die Empfehlungen für die allgemeine Verfügbarkeit freigegeben werden, ersetzen sie die folgenden vorhandenen Empfehlungen:

- **Endpoint Protection sollte auf Ihren Computern installiert sein** ersetzt Folgendes:
    - [Endpoint Protection-Lösung auf virtuellen Computern installieren](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Endpoint Protection-Lösung auf Ihren Computern installieren](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee) 

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