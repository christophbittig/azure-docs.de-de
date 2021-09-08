---
title: Gemeinsame Verantwortung in der Cloud – Microsoft Azure
description: Grundlegendes zum Modell gemeinsamer Verantwortung sowie welche Sicherheitsaufgaben vom Cloudanbieter und welche Aufgaben von Ihnen verarbeitet werden.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2021
ms.author: terrylan
ms.openlocfilehash: 14d53d3f58c1bbdb03a5ac510f6a2e44157906f9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272107"
---
# <a name="shared-responsibility-in-the-cloud"></a>Gemeinsame Verantwortung in der Cloud

Wenn Sie öffentliche Clouddienste erwägen und beurteilen, ist es wichtig, das Modell gemeinsamer Verantwortung zu verstehen sowie welche Sicherheitsaufgaben vom Cloudanbieter und welche Aufgaben von Ihnen verarbeitet werden. Die Workloadverantwortlichkeiten variieren in Abhängigkeit davon, ob die Workload auf SaaS (Software-as-a-Service), auf PaaS (Platform-as-a-Service), auf IaaS (Infrastructure-as-a-Service) oder in einem lokalen Rechenzentrum gehostet wird.

## <a name="division-of-responsibility"></a>Geteilte Zuständigkeit
In einem lokalen Rechenzentrum sind Sie für den gesamten Stapel zuständig. Bei einem Wechsel in die Cloud wird dagegen ein Teil der Zuständigkeit auf Microsoft übertragen. Im folgenden Diagramm werden die Zuständigkeitsbereiche zwischen Ihnen und Microsoft entsprechend der Art der Bereitstellung Ihres Stapels veranschaulicht.

:::image type="content" source="media/shared-responsibility/shared-responsibility.svg" alt-text="Das Diagramm, das die Zuständigkeitszonen zeigt" border="false":::.

Ihre Daten und Identitäten gehören bei jeder Art von Cloudbereitstellung Ihnen. Sie sind für den Schutz der Sicherheit Ihrer Daten und Identitäten, lokalen Ressourcen und der von Ihnen gesteuerten Cloudkomponenten zuständig (abhängig von der Art des Diensts).

Für folgende Bereiche sind immer Sie selbst zuständig (unabhängig von der Art der Bereitstellung):

- Daten
- Endpunkte
- Konto
- Zugriffsverwaltung

## <a name="cloud-security-advantages"></a>Sicherheitsvorteile der Cloud
Die Cloud bietet bedeutende Vorteile für die Lösung lang etablierter Herausforderungen bei der Sicherheit von Daten. In einer lokalen Umgebung werden in Organisationen häufig nicht alle Zuständigkeitsbereiche abgedeckt, und für die Sicherheit stehen nur begrenzte Ressourcen zur Verfügung. So entsteht eine Umgebung, in der Angreifer Sicherheitslücken auf allen Ebenen ausnutzen können.

Das folgende Diagramm zeigt einen herkömmlichen Ansatz, bei dem viele Sicherheitsverantwortlichkeiten aufgrund begrenzter Ressourcen nicht erfüllt sind. Beim cloudbasierten Ansatz sind Sie in der Lage, die täglichen Sicherheitsaufgaben an Ihren Cloudanbieter zu übergeben und ihre Ressourcen neu zuzuordnen.

:::image type="content" source="media/shared-responsibility/cloud-enabled-security.svg" alt-text="Das Diagramm, das die Sicherheitsvorteile von Cloudera zeigt" border="false":::.

Beim cloudbasierten Ansatz sind Sie außerdem in der Lage, cloudbasierte Sicherheitsfunktionen für eine höhere Effektivität zu nutzen und Cloud Intelligence zur Verbesserung der Bedrohungserkennung und von Reaktionszeiten. Durch die Übertragung von Aufgaben an den Cloudanbieter profitieren Organisationen von einer höheren Sicherheit und können Sicherheitsressourcen und entsprechende Finanzmittel für andere geschäftliche Prioritäten nutzen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Aufteilung der Zuständigkeit zwischen Ihnen und Microsoft in einer SaaS-, PaaS- und IaaS-Bereitstellung finden Sie unter [Gemeinsame Verantwortung für das Cloud Computing](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/).
