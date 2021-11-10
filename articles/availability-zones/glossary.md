---
title: Terminologie zur Azure-Resilienz
description: Verstehen von Begriffen
author: mamccrea
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: mamccrea
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: ade8db49435064c31ec3c1262f359069641a11dd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057003"
---
# <a name="terminology"></a>Begriff

Für ein besseres Verständnis von Regionen und Verfügbarkeitszonen in Azure ist ein Verständnis der zentralen Begriffe und Konzepte erforderlich.

| Begriff oder Konzept | BESCHREIBUNG |
| --- | --- |
| region | Mehrere Rechenzentren werden innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und sind über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden. |
| geography | Hierbei handelt es sich um einen Bereich auf der Welt, in dem mindestens eine Azure-Region enthalten ist. Geografien definieren einen separaten Markt, der Grenzen für Datenresidenz und Konformität einhält. In Geografien können Kunden mit spezifischen Anforderungen an Datenresidenz und Compliance ihre Daten und Anwendungen eng zusammenhalten. Geografien sind fehlertolerant und sorgen dank ihrer Verbindung mit unserer dedizierten Netzwerkinfrastruktur mit sehr hohen Kapazitäten auch beim Ausfall einer ganzen Region für Verfügbarkeit. |
| Verfügbarkeitszone | Hierbei handelt es sich um physische Orte innerhalb einer Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. |
| Empfohlene Region | Hierbei handelt es sich um eine Region, in der die größte Auswahl an Dienstfunktionen zur Verfügung gestellt werden kann. Sie ist darauf ausgelegt, Verfügbarkeitszonen jetzt oder in Zukunft zu unterstützen. Diese Regionen werden im Azure-Portal als **Empfohlen** markiert. |
| Alternative (andere) Region | Hierbei handelt es sich um eine Region, die die Präsenz von Azure innerhalb einer Datenresidenzgrenze ausweitet, in der auch eine empfohlene Region vorhanden ist. Alternative Regionen unterstützen die Optimierung von Wartezeit und stellen eine zweite Region für den Fall einer Notfallwiederherstellung bereit. Sie sind nicht darauf ausgelegt, Verfügbarkeitszonen zu unterstützen. Azure nimmt jedoch regelmäßig Bewertungen dieser Regionen vor, um festzustellen, ob sie als empfohlene Regionen deklariert werden können. Diese Regionen werden im Azure-Portal als **Sonstige** markiert. |
| Regionsübergreifende Replikation (ehemals gekoppelte Region) | Eine Zuverlässigkeitsstrategie und -implementierung, die Hochverfügbarkeit von Verfügbarkeitszonen mit Schutz vor regionsweiten Vorfällen kombiniert, um Anforderungen sowohl an die Notfallwiederherstellung als auch an die Geschäftskontinuität zu erfüllen. |
| Grundlegender Dienst | Hierbei handelt es sich um einen Azure-Kerndienst, der in allen Regionen zur Verfügung steht, wenn die Region allgemein verfügbar ist. |
| Hauptdienst | Hierbei handelt es sich um einen Azure-Dienst, der in allen empfohlenen Regionen innerhalb von 90 Tagen nach allgemeiner Verfügbarkeit einer Region verfügbar ist. In alternativen Regionen ist die Verfügbarkeit von der Nachfrage abhängig. |
| Strategischer Dienst | Hierbei handelt es sich um einen Dienst, dessen Verfügbarkeit von der Nachfrage in Regionen abhängt und der von angepasster/spezieller Hardware unterstützt wird. |
| Regionaler Dienst | Hierbei handelt es sich um einen Dienst, der regional bereitgestellt wird und es Kunden ermöglicht, die Region anzugeben, in der der Dienst bereitgestellt werden soll. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Nicht-regionaler Dienst | Hierbei handelt es sich um einen Azure-Dienst, der keine Abhängigkeit von einer bestimmten Azure-Region aufweist. Nicht regionale Dienste werden in mindestens zwei Regionen bereitgestellt. Bei einem regionalen Ausfall fährt die Instanz des Diensts in einer anderen Region mit der Versorgung von Kunden fort. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Zonendienst | Ein Azure-Dienst, der Verfügbarkeitszonen unterstützt und die Bereitstellung einer Ressource in einer bestimmten, selbst ausgewählten Verfügbarkeitszone ermöglicht, um strengere Latenz- oder Leistungsanforderungen zu erfüllen. |
| Zonenredundanter Dienst | Ein Azure-Dienst, der Verfügbarkeitszonen unterstützt und die automatische zonenübergreifende Replikation oder Verteilung von Ressourcen ermöglicht. |
| Immer verfügbarer Dienst | Ein Azure-Dienst, der Verfügbarkeitszonen unterstützt und die ständige Verfügbarkeit von Ressourcen in allen Azure-Geografien sowie Resilienz bei zonenweiten und regionsweiten Ausfällen ermöglicht. |
