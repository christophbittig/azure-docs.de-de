---
title: Referenztabelle für alle Microsoft Defender für Cloud-Empfehlungen
description: In diesem Artikel sind die Microsoft Defender für Cloud-Sicherheitsempfehlungen aufgeführt, die Ihnen beim Härten und Schützen Ihrer Ressourcen helfen.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 09/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 7f00f874cb1611e86867f19859ffcaa4800f323a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457200"
---
# <a name="security-recommendations---a-reference-guide"></a>Sicherheitsempfehlungen: Referenzhandbuch

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel sind die Empfehlungen aufgeführt, die möglicherweise in Microsoft Defender für Cloud angezeigt werden. Welche Empfehlungen in Ihrer Umgebung angezeigt werden, hängt von den geschützten Ressourcen und Ihrer angepassten Konfiguration ab.

Defender für Cloud-Empfehlungen beruhen auf dem [Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md). Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz Azure-spezifischer Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Microsoft Defender für Cloud](implement-security-recommendations.md).

Ihre Sicherheitsbewertung basiert auf der Anzahl der Defender für Cloud-Empfehlungen, die Sie umgesetzt haben. Sehen Sie sich den Schweregrad jeder einzelnen Empfehlung und deren mögliche Auswirkung auf Ihre Sicherheitsbewertung an, um zu entscheiden, welche Empfehlungen zuerst umgesetzt werden sollten.

> [!TIP]
> Wenn es in der Beschreibung einer Empfehlung heißt: „Keine zugehörige Richtlinie“, so liegt das normalerweise daran, dass diese Empfehlung von einer anderen Empfehlung und _deren_ Richtlinie abhängig ist. Die Empfehlung „Endpoint Protection-Integritätsfehler sollten behoben werden...“ stützt sich beispielsweise auf die Empfehlung, die prüft, ob _überhaupt_ eine Endpoint Protection-Lösung installiert ist. („Endpoint Protection-Lösung sollte installiert werden...“). Die zugrunde liegende Empfehlung _verfügt_ über eine Richtlinie.
> Die Richtlinien nur auf die grundlegende Empfehlung zu beschränken, vereinfacht die Richtlinienverwaltung.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices-Empfehlungen

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Computeempfehlungen

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Containerempfehlungen

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Datenempfehlungen

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess-Empfehlungen

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT-Empfehlungen

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Netzwerkempfehlungen

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Veraltete Empfehlungen

|Empfehlung|Beschreibung und zugehörige Richtlinie|severity|
|----|----|----|
|Der Zugriff auf App Services sollte eingeschränkt werden.|Schränken Sie den Zugriff auf Ihre App Services ein, indem Sie die Netzwerkkonfiguration ändern, um eingehenden Datenverkehr aus Bereichen abzulehnen, die zu weit gefasst sind.<br>(Zugehörige Richtlinie: [Vorschau]: Zugriff auf App Services muss eingeschränkt sein)|High|
|Die Regeln für Webanwendungen in IaaS-Netzwerksicherheitsgruppen sollten verstärkt werden.|Härten Sie die Netzwerksicherheitsgruppe (NSG) Ihrer virtuellen Computer, auf denen Webanwendungen ausgeführt werden, mit NSG-Regeln, die in Bezug auf Webanwendungsports zu tolerant sind.<br>(Zugehörige Richtlinie: Für Webanwendungen in IaaS müssen die NSG-Regeln verstärkt werden)|High|
|Podsicherheitsrichtlinien sollten definiert werden, um den Angriffsvektor zu reduzieren, indem nicht erforderliche Anwendungsberechtigungen entfernt werden (Vorschau)|Definieren Sie Podsicherheitsrichtlinien, um den Angriffsvektor zu reduzieren, indem nicht erforderliche Anwendungsberechtigungen entfernt werden. Wir empfehlen Ihnen die Konfiguration von Podsicherheitsrichtlinien durchzuführen, damit Pods nur auf Ressourcen zugreifen können, für die sie über eine Zugriffsberechtigung verfügen.<br>(Zugehörige Richtlinie: [Vorschau]: Podsicherheitsrichtlinien für Kubernetes Service definieren)|Medium|
|Für bessere Einblicke in Ihre IoT-Geräte das Sicherheitsmodul „Azure Security Center für IoT“ installieren|Installieren Sie für bessere Einblicke in Ihre IoT-Geräte das Sicherheitsmodul „Azure Security Center für IoT“.|Niedrig|
|Ihre Computer sollten zur Anwendung von Systemupdates neu gestartet werden|Starten Sie Ihre Computer neu, um die Systemupdates anzuwenden und Ihre Computer vor Sicherheitsrisiken zu schützen. (Zugehörige Richtlinie: Systemupdates müssen auf Ihren Computern installiert werden)|Medium|
| Der Überwachungs-Agent sollte auf Ihren Computern installiert werden.|Durch diese Aktion wird ein Überwachungs-Agent auf den ausgewählten virtuellen Computern installiert. Wählen Sie einen Arbeitsbereich aus, an den der Agent Berichte übermitteln soll. (Keine zugehörige Richtlinie)|High|
||||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Empfehlungen finden Sie in den folgenden Artikeln:

- [Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?](security-policy-concept.md)
- [Überprüfen Ihrer Sicherheitsempfehlungen](review-security-recommendations.md)
