---
title: 'Merkmalsauswertung: Personalisierung'
titleSuffix: Azure Cognitive Services
description: Wenn Sie über das Azure-Portal eine Auswertung in Ihrer Personalisierungsressource ausführen, liefert die Personalisierung Informationen dazu, welche Kontext- und Aktionsmerkmale das Modell beeinflussen.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3d504293da96ea6f0c22c2a54563b39b92e4485e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831017"
---
# <a name="feature-evaluation"></a>Featureauswertung

Wenn Sie über das [Azure-Portal](https://portal.azure.com) eine Auswertung in Ihrer Personalisierungsressource ausführen, liefert die Personalisierung Informationen dazu, welche Kontext- und Aktionsmerkmale das Modell beeinflussen. 

Dies ermöglicht Folgendes:

* Ermittlung weiterer möglicher Merkmale auf der Grundlage der relevanteren Merkmale im Modell
* Ermittlung und ggf. Entfernung nicht relevanter Merkmale oder ausführlichere Analyse, wodurch die Nutzung beeinflusst wird
* Bereitstellung hilfreicher Informationen für Redaktions- oder Kuratorenteams zu neuen Inhalten oder Produkten, die es wert sind, in den Katalog aufgenommen zu werden
* Behandlung allgemeiner Probleme und Fehler beim Senden von Merkmalen an die Personalisierung

Relevantere Merkmale werden im Modell stärker gewichtet. Aufgrund der stärkeren Gewichtung dieser Merkmale sind sie üblicherweise vorhanden, wenn die Personalisierung höhere Belohnungen erhält.

## <a name="getting-feature-importance-evaluation"></a>Auswerten der Merkmalsrelevanz

Um die Ergebnisse für die Merkmalsrelevanz anzeigen zu können, müssen Sie eine Auswertung ausführen. Die Auswertung erstellt lesbare Merkmalsbezeichnungen auf der Grundlage der Merkmalsnamen aus dem Evaluierungszeitraum.

Die resultierenden Informationen zur Merkmalsrelevanz stellen das aktuelle Onlinemodell der Personalisierung dar. Bei der Auswertung wird die Merkmalsrelevanz des Modells analysiert, die am Enddatum des Evaluierungszeitraums gespeichert wurde, nachdem das gesamte Training während der Evaluierung mit der aktuellen Online-Lernrichtlinie durchgeführt wurde. 

Die Ergebnisse der Merkmalsrelevanz stellen keine anderen Richtlinien und Modelle dar, die während der Auswertung getestet oder erstellt wurden.  Die Auswertung enthält keine Merkmale, die nach Ablauf des Evaluierungszeitraums an die Personalisierung gesendet wurden.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Interpretieren der Auswertung der Merkmalsrelevanz

Zur Auswertung der Merkmale erstellt die Personalisierung Gruppen von Merkmalen mit ähnlicher Relevanz. Eine Gruppe kann im Vergleich zu anderen Gruppen als insgesamt relevanter eingestuft werden. Innerhalb der Gruppe werden die Merkmale jedoch alphabetisch sortiert.

Die Informationen zu den einzelnen Merkmalen umfassen Folgendes:

* Angabe, ob es sich um ein kontextbezogenes oder um ein aktionsbezogenes Merkmal handelt
* Schlüssel und Wert des Merkmals

Für die Bestell-App eines Eiscafés kann beispielsweise „Context.Weather:Hot“ ein Merkmal mit sehr hoher Relevanz sein.

Die Personalisierung zeigt Korrelationen von Merkmalen an, die bei gemeinsamer Berücksichtigung zu höheren Belohnungen führen.

So können beispielsweise „Context.Weather:Hot *with* Action.MenuItem:IceCream“ und „Context.Weather:Cold *with* Action.MenuItem:WarmTea“ angezeigt werden.

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Mögliche Aktionen auf der Grundlage der Merkmalsauswertung

### <a name="imagine-additional-features-you-could-use"></a>Ermitteln weiterer möglicher Merkmale

Lassen Sie sich von den relevanteren Merkmalen im Modell inspirieren. Wird also beispielweise bei einer mobilen Video-App „Context.MobileBattery:Low“ angezeigt, hat unter Umständen der Verbindungstyp Auswirkungen darauf, warum Kunden einen bestimmten Videoclip einem anderen vorziehen, und Sie können Ihrer App Merkmale für Verbindungstyp und Bandbreite hinzufügen.

### <a name="see-what-features-are-not-important"></a>Ermitteln nicht relevanter Merkmale

Entfernen Sie ggf. nicht relevante Merkmale, oder analysieren Sie ausführlicher, wodurch die Nutzung beeinträchtigt wird. Merkmale können aus unterschiedlichen Gründen eine geringe Relevanz haben. Es kann beispielsweise sein, dass das Merkmal keine Auswirkungen auf das Benutzerverhalten hat. Es kann aber auch bedeuten, dass das Merkmal für den Benutzer nicht offensichtlich ist. 

Bei einer Videowebsite könnte beispielsweise der Eindruck entstehen, dass „Action.VideoResolution=4k“ ein Merkmal mit geringer Relevanz ist, was jedoch der Benutzerforschung widerspricht. Eine mögliche Ursache dafür könnte sein, dass die Videoauflösung in der Anwendung gar nicht erwähnt oder angezeigt wird, sodass dieser Aspekt keine Auswirkungen auf das Benutzerverhalten hat.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Bereitstellen hilfreicher Informationen für Redaktions- oder Kuratorenteams

Stellen Sie hilfreiche Informationen zu neuen Inhalten oder Produkten bereit, die es wert sind, in den Katalog aufgenommen zu werden. Das Personalisierungstool dient zur Generierung von Erkenntnissen sowie zur Unterstützung von Teams. Zu diesem Zweck stellt es unter anderem Informationen für Redaktionsgruppen bereit, die Aufschluss darüber geben, welche Aspekte von Produkten, Artikeln oder Inhalten das Verhalten beeinflussen. In dem Szenario mit der Videoanwendung könnte beispielsweise festgestellt werden, dass es ein relevantes Merkmal namens „Action.VideoEntities.Cat:true“ gibt, woraufhin das Redaktionsteam die Anzahl von Katzenvideos erhöht.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Behandeln allgemeiner Probleme und Fehler

Zur Behebung allgemeiner Probleme und Fehler können Sie Ihren Anwendungscode ändern, sodass keine unpassenden oder falsch formatierten Merkmale an die Personalisierung gesendet werden. 

Im Anschluss finden Sie einige allgemeine Fehler beim Senden von Merkmalen:

* Senden personenbezogener Informationen (Personally Identifiable Information, PII): Personenbezogene Informationen wie Name, Telefonnummer, Kreditkartennummern oder IP-Adressen dürfen nicht mit der Personalisierung verwendet werden. Wenn Ihre Anwendung Benutzer nachverfolgen muss, verwenden Sie eine nicht identifizierende UUID oder eine andere Art von Benutzer-ID. In den meisten Szenarien ist dies ebenfalls problematisch.
* Bei einer großen Anzahl von Benutzern ist es unwahrscheinlich, dass die Interaktion der einzelnen Benutzer stärker ins Gewicht fällt als die Interaktion der Gesamtheit. Die Übermittlung von Benutzer-IDs ist daher für das Modell wahrscheinlich eher hinderlich (selbst wenn es sich um nicht personenbezogene Informationen handelt).
* Senden von Datums-/Uhrzeitfeldern als präzise Zeitstempel anstelle von Merkmalszeitwerten: Die Verwendung von Merkmalen wie „Context.TimeStamp.Day=Monday“ oder „"Context.TimeStamp.Hour"="13"“ ist hilfreicher. In diesem Fall gibt es bis zu sieben bzw. 24 Merkmalswerte. „"Context.TimeStamp":"1985-04-12T23:20:50.52Z"“ ist dagegen so präzise, dass damit keine Erkenntnisse gewonnen werden können, da es sich hierbei um ein einmaliges Ereignis handelt.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Skalierbarkeit und Leistung](concepts-scalability-performance.md) mit der Personalisierung.

