---
title: Wichtige Änderungen an Microsoft Defender für Cloud
description: Bevorstehende Änderungen an Microsoft Defender für Cloud, die Sie beachten sollten und für die Sie möglicherweise planen müssen
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/10/2021
ms.author: memildin
ms.openlocfilehash: fca622c8c49a16d9926a5fc1e8e0f0064ee758d7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157456"
---
# <a name="important-upcoming-changes-to-microsoft-defender-for-cloud"></a>Wichtige bevorstehende Änderungen an Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Die Informationen auf dieser Seite beziehen sich auf Vorabversionen von Produkten oder Features, die vor einer möglichen Veröffentlichung für den Handel wesentlich verändert werden können. Microsoft gewährt in Bezug auf die hier angegebenen Informationen keine Zusicherungen oder Garantien, weder ausdrücklich noch konkludent.

Auf dieser Seite erfahren Sie, welche Änderungen für Defender für Cloud geplant sind. Es werden geplante Änderungen am Produkt beschrieben, die sich auf Dinge wie Ihre Sicherheitsbewertung oder Workflows auswirken könnten.

Die neuesten Versionshinweise finden Sie unter [Neuerungen in Microsoft Defender für Cloud](release-notes.md).


## <a name="planned-changes"></a>Geplante Änderungen

| Geplante Änderung       | Voraussichtliches Datum der Änderung |
|----------------------|---------------------------|
| [Veraltete Vorschauwarnung: ARM.MCAS_ActivityFromAnonymousIPAddresses](#deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses)             | November 2021|
| [Die Legacy-Implementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)| November 2021|
| [Für die Bestandsanzeige von lokalen Computern wird eine andere Vorlage für den Ressourcennamen verwendet.](#inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name)    | November 2021    |
| [Mehrere Änderungen an Identitätsempfehlungen](#multiple-changes-to-identity-recommendations)                                                                                          | Dezember 2021    |
| [Änderungen an einer Sicherheitswarnung von Microsoft Defender für Storage](#changes-to-a-security-alert-from-microsoft-defender-for-storage)  | November 2021    |
| [Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken](#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)   | Q1 2022    |
| [Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen](#changes-to-recommendations-for-managing-endpoint-protection-solutions)             | Februar 2022| 
|||

### <a name="deprecating-a-preview-alert-armmcas_activityfromanonymousipaddresses"></a>Veraltete Vorschauwarnung: ARM.MCAS_ActivityFromAnonymousIPAddresses

**Geschätztes Datum für die Änderung:** November 2021

Die folgende Vorschauwarnung wird eingestellt:

|Warnungsname| BESCHREIBUNG|
|----------------------|---------------------------|
|**VORSCHAU: Aktivität von einer riskanten IP-Adresse**<br>(ARM.MCAS_ActivityFromAnonymousIPAddresses)|Es wurde eine Benutzeraktivität über eine IP-Adresse erkannt, die als anonyme Proxy-IP-Adresse identifiziert wurde.<br>Diese Proxys werden von Personen verwendet, die die IP-Adresse ihres Geräts verbergen möchten, und können in böswilliger Absicht eingesetzt werden. Die Erkennung nutzt einen Algorithmus für maschinelles Lernen, um falsch positive Ergebnisse wie etwa falsch gekennzeichnete IP-Adressen zu reduzieren, die regelmäßig von anderen Benutzern in der Organisation verwendet werden.<br>Erfordert eine aktive Microsoft Cloud App Security-Lizenz|
|||

Wir haben neue Warnungen erstellt, die diese Informationen in erweiterter Form bereitstellen. Darüber hinaus ist bei den neueren Warnungen („ARM_OperationFromSuspiciousIP“, „ARM_OperationFromSuspiciousProxyIP“) keine Lizenz für Microsoft Defender für Cloud-Apps (früher als „Microsoft Cloud App Security“ bezeichnet) erforderlich.

### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Die Legacyimplementierung von ISO 27001 wird durch die neue ISO-Norm 27001:2013 ersetzt.

**Geschätztes Datum für die Änderung:** November 2021

Die Legacyimplementierung von ISO 27001 wird vom Defender für Cloud-Dashboard für die Einhaltung gesetzlicher Bestimmungen entfernt. Wenn Sie Ihre ISO 27001-Konformität mit Defender für Cloud nachverfolgen, integrieren Sie den neuen Standard „ISO 27001:2013“ für alle relevanten Verwaltungsgruppen oder Abonnements. Die derzeitige Legacynorm „ISO 27001“ wird bald vom Dashboard entfernt.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Defender für Cloud-Dashboard zur Einhaltung gesetzlicher Bestimmungen mit der Meldung, dass die Legacyimplementierung von ISO 27001 entfernt wird" lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::


### <a name="inventory-display-of-on-premises-machines-will-use-different-template-for-resource-name"></a>Für die Bestandsanzeige von lokalen Computern wird eine andere Vorlage für den Ressourcennamen verwendet.

**Geschätztes Datum für die Änderung:** November 2021

Um die Darstellung von Ressourcen unter [Ressourcenbestand](asset-inventory.md) zu verbessern, wird das Element „source-computer-IP“ aus der Vorlage für die Benennung von lokalen Computern entfernt.

- **Aktuelles Format:** ``machine-name_source-computer-id_VMUUID``
- **Nach dem Update:** ``machine-name_VMUUID``


### <a name="multiple-changes-to-identity-recommendations"></a>Mehrere Änderungen an Identitätsempfehlungen

**Geschätztes Datum für die Änderung:** Dezember 2021

Defender für Cloud enthält mehrere Empfehlungen zur Verbesserung der Verwaltung von Benutzern und Konten. Im Dezember werden die nachfolgend beschriebenen Änderungen vorgenommen:

- **Verbessertes Aktualisierungsintervall:** Derzeit gilt für die Identitätsempfehlungen ein Aktualisierungsintervall von 24 Stunden. Durch dieses Update wird dieses Intervall auf zwölf Stunden reduziert.

- **Kontoausnahmefunktion:** Defender für Cloud enthält viele Features zum Anpassen der Informationen und um sicherzustellen, dass Ihre Sicherheitsbewertung die Sicherheitsprioritäten Ihrer Organisation widerspiegelt. Die Option „Ausnahme“ für Sicherheitsempfehlungen ist ein solches Feature. Eine vollständige Übersicht und Anweisungen finden Sie unter [Ausschließen von Ressourcen und Empfehlungen aus der Sicherheitsbewertung](exempt-resource.md). Mit diesem Update können bestimmte Konten von der Auswertung durch die acht Empfehlungen aus der folgenden Tabelle ausgenommen werden.

    In der Regel würden Sie Notfallkonten aus MFA-Empfehlungen ausschließen, da solche Konten häufig absichtlich von den MFA-Anforderungen der Organisation ausgenommen werden. Sie verfügen unter Umständen über externe Konten, für die Sie den Zugriff zulassen möchten, für die MFA jedoch nicht aktiviert ist.

    > [!TIP]
    > Wenn Sie ein Konto ausgenommen haben, wird es nicht als fehlerhaft angezeigt. Es führt auch nicht dazu, dass ein Abonnement als fehlerhaft angezeigt wird.

    |Empfehlung| Bewertungsschlüssel|
    |-|-|
    |[MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/94290b00-4d0c-d7b4-7cea-064a9554e681)|94290b00-4d0c-d7b4-7cea-064a9554e681|
    |[MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/151e82c5-5341-a74b-1eb0-bc38d2c84bb5)|151e82c5-5341-a74b-1eb0-bc38d2c84bb5|
    |[MFA sollte für Konten mit Schreibberechtigungen für Ihr Abonnement aktiviert werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/57e98606-6b1e-6193-0e3d-fe621387c16b)|57e98606-6b1e-6193-0e3d-fe621387c16b|
    |[Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/c3b6ae71-f1f0-31b4-e6c1-d5951285d03d)|c3b6ae71-f1f0-31b4-e6c1-d5951285d03d|
    |[Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b)|a8c6a4ad-d51e-88fe-2979-d3ee3c864f8b|
    |[Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/04e7147b-0deb-9796-2e5c-0336343ceb3d)|04e7147b-0deb-9796-2e5c-0336343ceb3d|
    |[Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)|e52064aa-6853-e252-a11e-dffc675689c2|
    |[Veraltete Konten sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|00c6d40b-e990-6acf-d4f3-471e747a27c4|
    |||
 
- **Umbenannte Empfehlungen:** In diesem Update werden zwei Empfehlungen umbenannt. Außerdem werden ihre Beschreibungen überarbeitet. Die Bewertungsschlüssel bleiben unverändert. 


    |Eigenschaft  |Aktueller Wert  | Nach dem Update|
    |---------|---------|---------|
    |Bewertungsschlüssel     | e52064aa-6853-e252-a11e-dffc675689c2        | Unverändert|
    |Name     |[Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/e52064aa-6853-e252-a11e-dffc675689c2)         |Abonnements sollten aus Konten gelöscht werden, die in Active Directory blockiert sind und über Besitzerberechtigungen verfügen.        |
    |BESCHREIBUNG     |Benutzerkonten, für die die Anmeldung blockiert wurde, sollten aus Ihren Abonnements entfernt werden.<br>Diese Konten können Ziele für Angreifer sein, die versuchen, unbemerkt auf Ihre Daten zuzugreifen.|Benutzerkonten, für die die Anmeldung bei Active Directory blockiert wurde, sollten aus Ihren Abonnements entfernt werden. Diese Konten können Ziele für Angreifer sein, die versuchen, unbemerkt auf Ihre Daten zuzugreifen.<br>Weitere Informationen zum Schützen des Identitätsumkreises finden Sie unter [Azure-Identitätsverwaltung und Sicherheit bei der Zugriffssteuerung: Best Practices](../security/fundamentals/identity-management-best-practices.md).|
    |Zugehörige Richtlinie     |[Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)         |Abonnements sollten aus Konten gelöscht werden, die in Active Directory blockiert sind und über Besitzerberechtigungen verfügen. |
    |||

    |Eigenschaft  |Aktueller Wert  | Nach dem Update|
    |---------|---------|---------|
    |Bewertungsschlüssel     | 00c6d40b-e990-6acf-d4f3-471e747a27c4        | Unverändert|
    |Name     |[Veraltete Konten sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/00c6d40b-e990-6acf-d4f3-471e747a27c4)|Abonnements sollten aus Konten gelöscht werden, die in Active Directory blockiert sind und über Lese- und Schreibberechtigungen verfügen.|
    |BESCHREIBUNG     |Benutzerkonten, für die die Anmeldung blockiert wurde, sollten aus Ihren Abonnements entfernt werden.<br>Diese Konten können Ziele für Angreifer sein, die versuchen, unbemerkt auf Ihre Daten zuzugreifen.|Benutzerkonten, für die die Anmeldung bei Active Directory blockiert wurde, sollten aus Ihren Abonnements entfernt werden. Diese Konten können Ziele für Angreifer sein, die versuchen, unbemerkt auf Ihre Daten zuzugreifen.<br>Weitere Informationen zum Schützen des Identitätsumkreises finden Sie unter [Azure-Identitätsverwaltung und Sicherheit bei der Zugriffssteuerung: Best Practices](../security/fundamentals/identity-management-best-practices.md).|
    |Zugehörige Richtlinie     |[Veraltete Konten sollten aus Ihrem Abonnement entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)|Abonnements sollten aus Konten gelöscht werden, die in Active Directory blockiert sind und über Lese- und Schreibberechtigungen verfügen.|
    |||
 

### <a name="changes-to-a-security-alert-from-microsoft-defender-for-storage"></a>Änderungen an einer Sicherheitswarnung von Microsoft Defender für Storage

**Geschätztes Datum für die Änderung:** November 2021

Eine der Vorschauwarnungen von Microsoft Defender für Storage wird in zwei neue Empfehlungen unterteilt, um mehr Informationen zu den erkannten verdächtigen Ereignissen bereitzustellen. Diese Warnung ist nur für Azure Blob Storage relevant.

**Der Warnungstyp ändert sich ebenfalls.**

- Vor der Änderung lautete die Warnung:<br>
    „Vorschau: Anonyme Überprüfung von öffentlichen Speichercontainern“<br>(Storage.Blob_ContainerAnonymousScan)

- Nach dieser Änderung gibt es zwei Empfehlungen:

    - „Open storage containers discovered by external scanning tool or script“ (Offene Speichercontainer durch externes Überprüfungstool oder -skript erkannt)<br>(Storage.Blob_OpenContainersScanning.FailedAttempt)
    - „Successful discovery of open storage containers by external scanning script or tool“ (Erfolgreiche Ermittlung offener Speichercontainer durch externes Überprüfungsskript oder -tool erkannt)<br>(Storage.Blob_OpenContainersScanning.SuccessfulDiscovery)

Weitere Details zu diesen Warnungen werden bei Freigabe der Änderung veröffentlicht.


### <a name="enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases"></a>Verbesserungen bei der Empfehlung zur Klassifizierung vertraulicher Daten in den SQL Datenbanken

**Geschätztes Datum für die Änderung:** 1. Quartal 2022

Die Empfehlung **Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden** in der Sicherheitskontrolle **Datenklassifizierung anwenden** wird durch eine neue Version ersetzt, die stärker an der Microsoft-Strategie zur Datenklassifizierung ausgerichtet ist. Infolgedessen ändert sich auch die ID der Empfehlung (derzeit b0df6f56-862d-4730-8597-38c0fd4ebd59).


### <a name="changes-to-recommendations-for-managing-endpoint-protection-solutions"></a>Änderungen an Empfehlungen für die Verwaltung von Endpoint Protection-Lösungen

**Geschätztes Datum für die Änderung:** Februar 2022

Im August 2021 wurden zwei neue **Vorschau**-Empfehlungen für die Bereitstellung und Wartung der Endpoint Protection-Lösungen auf Ihren Computern hinzugefügt. Ausführliche Informationen finden Sie in den [Versionshinweisen](release-notes.md#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview).

Wenn die Empfehlungen für die allgemeine Verfügbarkeit freigegeben werden, ersetzen sie die folgenden vorhandenen Empfehlungen:

- **Endpoint Protection sollte auf Ihren Computern installiert sein** ersetzt Folgendes:
    - [Endpoint Protection-Lösung auf virtuellen Computern installieren (Schlüssel: 83f577bd-a1b6-b7e1-0891-12ca19d1e6df)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/83f577bd-a1b6-b7e1-0891-12ca19d1e6df)
    - [Endpoint Protection-Lösung auf Ihren Computern installieren (Schlüssel: 383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/383cf3bc-fdf9-4a02-120a-3e7e36c6bfee)

- **Endpoint Protection-Integritätsprobleme sollten auf Ihren Computern gelöst werden** ersetzt die vorhandene Empfehlung mit derselben Bezeichnung. Die beiden Empfehlungen verfügen über unterschiedliche Bewertungsschlüssel:
    - Bewertungsschlüssel für die **Vorschau**-Empfehlung: 37a3689a-818e-4a0e-82ac-b1392b9bb000
    - Bewertungsschlüssel für die **GA**-Empfehlung (allgemeine Verfügbarkeit): 3bcd234d-c9c7-c2a2-89e0-c01f419c1a8a

Weitere Informationen:
- [Von Defender für Cloud unterstützte Endpoint Protection-Lösungen](supported-machines-endpoint-solutions-clouds.md#endpoint-supported)
- [Bewerten des Status Ihrer bereitgestellten Lösungen durch diese Empfehlungen](endpoint-protection-recommendations-technical.md)



## <a name="next-steps"></a>Nächste Schritte

Alle aktuellen Änderungen an Defender für Cloud finden Sie unter [Neuerungen in Microsoft Defender für Cloud](release-notes.md).
