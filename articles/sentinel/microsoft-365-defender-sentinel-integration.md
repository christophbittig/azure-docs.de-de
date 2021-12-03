---
title: Microsoft 365 Defender-Integration in Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft 365 Defender zusammen mit Microsoft Sentinel verwenden können, um Microsoft Sentinel als universelle Queue für Vorfälle zu nutzen und gleichzeitig die Stärken von Microsoft 365 Defender nahtlos für die Untersuchung von Microsoft 365 Sicherheitsvorfällen einzusetzen. Außerdem erfahren Sie, wie Sie erweiterte Hunting-Daten von Defender-Komponenten in Microsoft Sentinel erfassen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5a969397ce63568c95891206d95275af5caf6a8
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520482"
---
# <a name="microsoft-365-defender-integration-with-microsoft-sentinel"></a>Microsoft 365 Defender-Integration in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Der Microsoft 365 Defender-Connector ist derzeit als **VORSCHAU** verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** wurde bisher als **Microsoft Threat Protection** oder **MTP** bezeichnet.
>
> **Microsoft Defender for Endpoint** wurde bisher als **Microsoft Defender Advanced Threat Protection** oder **MDATP** bezeichnet.
>
> **Microsoft Defender für Office 365** war früher bekannt als **Office 365 Advanced Threat Protection**.
>
> Die alten Namen werden möglicherweise eine Zeit lang weiterhin verwendet.

## <a name="incident-integration"></a>Vorfallsintegration

Die [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection)-Vorfallintegration von Microsoft Sentinel ermöglicht es Ihnen, alle Microsoft 365 Defender-Vorfälle in Microsoft Sentinel zu streamen und sie zwischen beiden Portalen synchron zu bewahren. Vorfälle aus Microsoft 365 Defender (vormals bekannt als Microsoft Threat Protection oder MTP) enthalten alle zugehörigen Warnungen, Entitäten und relevanten Informationen, die Ihnen genügend Kontext bieten, um Triagen und vorläufige Untersuchungen in Microsoft Sentinel durchzuführen. Sobald sie in Sentinel enthalten sind, bleiben Vorfälle bidirektional mit Microsoft 365 Defender synchronisiert, sodass Sie die Vorteile beider Portale bei Ihrer Vorfallsuntersuchung nutzen können.

Durch diese Integration können Microsoft 365-Sicherheitsvorfälle innerhalb von Microsoft Sentinel als Teil der primären Warteschlange für Vorfälle im gesamten Unternehmen verwaltet werden, sodass Sie Microsoft 365-Vorfälle zusammen mit denen aus allen anderen Cloud- und lokalen Systemen einsehen und korrelieren können. Gleichzeitig können Sie die einzigartigen Stärken und Fähigkeiten von Microsoft 365 Defender für eingehende Untersuchungen und eine Microsoft 365-spezifische Erfahrung im gesamten Microsoft 365-Ökosystem nutzen. Microsoft 365 Defender bereichert und gruppiert Warnmeldungen aus mehreren Microsoft 365-Produkten, wodurch sowohl die Größe der Queue für Vorfälle des SOC reduziert als auch die Zeit zur Behebung verkürzt wird. Die folgenden Komponentendienste sind Teil des Microsoft 365 Defender-Stapels:

- **Microsoft Defender for Endpoint** (vormals Microsoft Defender ATP)
- **Microsoft Defender for Identity** (vormals Azure ATP)
- **Microsoft Defender for Office 365** (vormals Office 365 ATP)
- **Microsoft Defender für Cloud-Apps**

Zusätzlich zur Erfassung von Warnungen von diesen Komponenten erstellt Microsoft 365 Defender eigene Warnungen. Die Anwendung erstellt Vorfälle aus allen diesen Warnungen und sendet sie an Microsoft Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Gängige Anwendungsfälle und -szenarien

- Die Ein-Klick-Verbindung von Microsoft 365 Defender-Vorfällen, einschließlich aller Warnungen und Entitäten von Microsoft 365 Defender-Komponenten, in Microsoft Sentinel.

- Bidirektionale Synchronisierung zwischen Sentinel- und Microsoft 365 Defender-Vorfällen bezüglich Status, Besitzer und Schließungsgrund.

- Nutzen der Funktionen zur Gruppierung und Anreicherung von Microsoft 365 Defender-Warnungen in Microsoft Sentinel, wodurch sich die Zeit der Problemlösung verkürzt.

- Kontextbezogener Deep Link zwischen einem Microsoft Sentinel-Vorfall und seinem parallelen Microsoft 365 Defender-Vorfall, um die Untersuchung über beide Portale hinweg zu erleichtern.

### <a name="connecting-to-microsoft-365-defender"></a>Herstellen einer Verbindung mit Microsoft 365 Defender

Sobald Sie den Microsoft 365 Defender-Datenverbinder zum [Erfassen von Vorfällen und Warnungen](connect-microsoft-365-defender.md) aktiviert haben, werden Microsoft 365 Defender-Vorfälle in der Microsoft Sentinel-Vorfallwarteschlange mit **Microsoft 365 Defender** im Feld **Produktname** angezeigt, kurz nachdem sie in Microsoft 365 Defender erstellt wurden.
- Es kann bis zu 10 Minuten ab dem Zeitpunkt dauern, an dem ein Vorfall, der in Microsoft 365 Defender erstellt wird, in Microsoft Sentinel angezeigt wird.

- Vorfälle werden ohne zusätzliche Kosten erfasst und synchronisiert.

Sobald die Microsoft 365 Defender-Integration verbunden ist, werden alle Komponenten-Warnungsverbinder (Defender für Endpunkt, Defender für Identität, Defender für Office 365, Defender for Cloud-Apps) automatisch im Hintergrund verbunden, sofern sie es vorher noch nicht waren. Wenn nach der Verbindung von Microsoft 365 Defender Komponenten-Lizenzen erworben wurden, werden die Warnungen und Vorfälle aus dem neuen Produkt weiterhin ohne zusätzliche Konfiguration oder Gebühren an Microsoft Sentinel weitergeleitet.

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Microsoft 365 Defender-Vorfälle und Microsoft-Regeln zur Erstellung von Vorfällen

- Von Microsoft 365 Defender generierte Incidents, die auf Warnungen von Microsoft 365 Sicherheitsprodukten basieren, werden mit benutzerdefinierter Microsoft 365 Defender-Logik erstellt.

- Die Microsoft-Regeln für die Erstellung von Vorfällen in Microsoft Sentinel erstellen ebenfalls Vorfälle aus denselben Warnungen, wobei eine (andere) benutzerdefinierte Microsoft Sentinel-Logik verwendet wird.

- Die gemeinsame Verwendung beider Mechanismen wird vollständig unterstützt, und diese Konfiguration kann verwendet werden, um den Übergang zur neuen Microsoft 365 Defender-Logik für die Erstellung von Vorfällen zu erleichtern. Dies führt jedoch zu **duplizierten Vorfällen** für dieselben Warnungen.

- Um die Erstellung von duplizierten Vorfällen für dieselben Alarme zu vermeiden, empfehlen wir Kunden, alle **Regeln zur Erstellung von Microsoft-Vorfällen** für Microsoft 365-Produkte (Defender für Endpoint, Defender für Identity und Defender für Office 365 und Defender for Cloud-Apps) zu deaktivieren, wenn sie den Microsoft 365 Defender verbinden. Dies kann durch das Deaktivieren der Vorfall-Erstellung auf der Verbinder-Seite erfolgen. Beachten Sie bitte, dass in diesem Fall alle Filter, die durch die Regeln zur Vorfallerstellung angewendet wurden, nicht auf die Microsoft 365 Defender-Vorfallintegration angewendet werden.

    > [!NOTE]
    > Alle Warnmeldungstypen von Microsoft Defender for Cloud-Apps werden gerade in Microsoft 365 Defender integriert.

### <a name="working-with-microsoft-365-defender-incidents-in-microsoft-sentinel-and-bi-directional-sync"></a>Mit Microsoft 365 Defender-Vorfällen in Microsoft Sentinel und der bidirektionalen Synchronisierung arbeiten

Microsoft 365 Defender-Vorfälle werden in der Microsoft Sentinel-Vorfallwarteschlange mit dem Produktnamen **Microsoft 365 Defender** und mit ähnlichen Details und Funktionen wie alle anderen Sentinel-Vorfälle angezeigt. Jeder Vorfall enthält einen Link, der zurück zum parallelen Vorfall im Microsoft 365 Defender-Portal führt.

In dem Maß, in dem sich der Vorfall in Microsoft 365 Defender weiterentwickelt und weitere Alarme oder Entitäten hinzugefügt werden, wird der Microsoft Sentinel-Vorfall entsprechend aktualisiert.

Änderungen am Status, am Schließungsgrund oder an der Zuweisung eines Microsoft 365-Vorfalls, die entweder in Microsoft 365 Defender oder in Microsoft Sentinel vorgenommen werden, werden auch in der Warteschlange des jeweils anderen Vorfalls entsprechend aktualisiert. Die Synchronisierung erfolgt in beiden Portalen ohne Verzögerung sofort nach der Änderung des Vorfalls. Möglicherweise ist eine Aktualisierung erforderlich, um die neuesten Änderungen anzuzeigen.

Im Microsoft 365 Defender können alle Warnungen aus einem Vorfall in einen anderen übertragen werden, was zu einer Zusammenführung der Vorfälle führt. Wenn diese Zusammenführung erfolgt, spiegeln die Microsoft Sentinel-Vorfälle die Änderungen wider. Ein Vorfall enthält alle Warnungen der beiden ursprünglichen Vorfälle, und der andere Vorfall wird automatisch geschlossen und mit dem Tag „umgeleitet“ versehen.

> [!NOTE]
> Vorfälle in Microsoft Sentinel können maximal 150 Warnungen enthalten. Microsoft 365 Defender-Vorfälle können aus mehr als dies bestehen. Wenn ein Microsoft 365 Defender-Vorfall mit mehr als 150 Alarmen mit Microsoft Sentinel synchronisiert wird, wird der Sentinel-Vorfall als mit "150+" Alarmen angezeigt und bietet einen Link zu dem parallelen Vorfall in Microsoft 365 Defender, wo Sie den vollständigen Alarmsatz sehen können.

## <a name="advanced-hunting-event-collection"></a>Erfassung erweiterter Huntingereignisse

Mit dem Microsoft 365 Defender-Connector können Sie auch **erweiterte Huntingereignisse** (eine Art von Ereignisrohdaten) aus Microsoft 365 Defender und den zugehörigen Komponentendiensten in Microsoft Sentinel streamen. Sie können derzeit [erweiterte Huntingereignisse](/microsoft-365/security/defender/advanced-hunting-overview) aus Microsoft Defender für Endpunkt und *(seit Oktober 2021)* aus Microsoft Defender für Office 365 erfassen und diese direkt in eigens erstellte Tabellen in Ihrem Microsoft Sentinel-Arbeitsbereich streamen. Diese Tabellen bauen auf dem gleichen Schema auf, das im Microsoft 365 Defender-Portal verwendet wird, wodurch Sie vollständigen Zugriff auf die gesamte Reihe erweiterter Huntingereignisse erhalten und Folgendes erreichen können:

- Kopieren Sie Ihre vorhandenen erweiterten Huntingabfragen für Microsoft Defender für Endpunkt/Office 365 einfach in Microsoft Sentinel.

- Verwenden Sie die rohen Ereignisprotokolle, um weitere Erkenntnisse für Ihre Warnungen, die Suche und die Untersuchung zu gewinnen, und diese Ereignisse mit Ereignissen aus anderen Datenquellen in Microsoft Sentinel korrelieren zu können.

- Speichern Sie die Protokolle mit verlängerter Aufbewahrungsdauer, jenseits der Standardaufbewahrungsdauer von 30 Tagen in Microsoft Defender für Endpunkt//Office 365 oder Microsoft 365 Defender. Dies erreichen Sie durch Konfigurieren der Aufbewahrungsdauer Ihres Arbeitsbereichs oder der tabellenweisen Aufbewahrung in Log Analytics.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie von der Verwendung von Microsoft 365 Defender zusammen mit Microsoft Sentinel profitieren können, indem Sie den Microsoft 365 Defender-Connector verwenden.

- Rufen Sie Anweisungen zum [Aktivieren des Microsoft 365 Defender-Connectors](connect-microsoft-365-defender.md) ab.
- Erstellen Sie [benutzerdefinierte Warnungen](detect-threats-custom.md), und [untersuchen Sie Vorfälle](investigate-cases.md).
