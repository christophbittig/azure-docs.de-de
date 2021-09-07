---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Repräsentativer Anwendungsfall'
description: Der folgende Anwendungsfall basiert auf einem realen Kundenszenario eines Unternehmens, das seine MySQL-Workload zu Azure Database for MySQL migriert hat.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 508e27006c96003bd4c2825c9987761f83358f0d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085072"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-representative-use-case"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Repräsentativer Anwendungsfall

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Introduction (Einführung)](01-mysql-migration-guide-intro.md)
## <a name="overview"></a>Übersicht

Der folgende Anwendungsfall basiert auf einem realen Kundenszenario eines Unternehmens, das seine MySQL-Workload zu Azure Database for MySQL migriert hat.

Das Unternehmen World-Wide Importers (WWI) ist ein Hersteller und Großhändler von neuartigen Waren mit Sitz in San Francisco, Kalifornien. Das Unternehmen hat den Betrieb im Jahr 2002 aufgenommen und ein effektives Business-to-Business-Modell (B2B) für den direkten Vertrieb der selbst erzeugten Artikel an Einzelhandelskunden in den ganzen USA entwickelt. Zu den Kunden gehören Fachgeschäfte, Supermärkte, Computergeschäfte, touristische Geschäfte und einige Einzelpersonen. Dieses B2B-Modell ermöglicht eine Optimierung des Vertriebssystems für die Produkte des Unternehmens, damit die Kosten gesenkt und konkurrenzfähigere Preise für die hergestellten Produkte angeboten werden können. Das Unternehmen verkauft seine Produkte über Zwischenhändler, die die Produkte im Auftrag von WWI bewerben, auch an andere Großhändler.

Vor dem Einstieg in neue Bereiche möchte WWI sicherstellen, dass seine IT-Infrastruktur das erwartete Wachstum bewältigen kann. WWI hostet derzeit die gesamte IT-Infrastruktur lokal am Hauptsitz des Unternehmens und ist der Meinung, dass die Verlagerung dieser Ressourcen in die Cloud ein zukünftiges Wachstum möglich macht. Daher wurde der CIO beauftragt, die Migration des Kundenportals und der zugehörigen Datenworkloads zur Cloud zu leiten.

WWI möchte weiterhin die vielen erweiterten Funktionen nutzen, die in der Cloud verfügbar sind, und ist daran interessiert, die Datenbanken und zugehörigen Workloads des Unternehmens zu Azure zu migrieren. Dies soll in kurzer Zeit geschehen, ohne dass Änderungen an den Anwendungen oder Datenbanken vorgenommen werden müssen. Zunächst sollen die Java-basierte Webanwendung für das Kundenportal sowie die zugehörigen MySQL-Datenbanken und -Workloads zur Cloud migriert werden.

### <a name="migration-goals"></a>Migrationsziele

Die wichtigsten Ziele für die Migration der Datenbanken und zugehörigen SQL-Workloads zur Cloud sind:

  - Verbessern des allgemeinen Sicherheitsstatus von ruhenden und übertragenen Daten

  - Verbessern der Hochverfügbarkeits- und Notfallwiederherstellungsfunktionen

  - Positionieren der Organisation für die Verwendung von cloudnativen Funktionen und Technologien wie der Zeitpunktwiederherstellung

  - Nutzen der Verwaltungs- und Leistungsoptimierungsfeatures von Azure Database for MySQL

  - Erstellen einer skalierbaren Plattform, mit der das Unternehmen seine Geschäftstätigkeit in weitere geografische Regionen erweitern kann

  - Ermöglichen einer besseren Einhaltung verschiedener rechtlicher Anforderungen, wenn personenbezogene Informationen gespeichert werden

WWI hat das [Cloud Adoption Framework (CAF)](/azure/cloud-adoption-framework/) verwendet, um sein Team dahingehend zu schulen, dass die Leitlinien zu bewährten Methoden für die Cloudmigration befolgt werden. Anschließend hat WWI das CAF als allgemeinen Migrationsleitfaden genutzt, um seinen Migrationsprozess in drei Hauptphasen zu unterteilen. Abschließend wurden die Aktivitäten definiert, die in den einzelnen Phasen durchgeführt werden mussten, um eine erfolgreiche Lift & Shift-Cloudmigration sicherzustellen.

Diese Phasen umfassen:

| Phase | Name | activities |
|-------|------|------------|
| 1 | Vor der Migration | Bewertung, Planung, Auswertung der Migrationsmethode, Auswirkungen auf Anwendungen, Testpläne, Leistungsbaselines |
| 2 | Migration     | Ausführung der Migration, Ausführung der Testpläne                                                                          |
| 3 | Nach der Migration| Geschäftskontinuität, Notfallwiederherstellung, Verwaltung, Sicherheit, Leistungsoptimierung, Plattformmodernisierung |

WWI verfügt über mehrere Instanzen von MySQL mit unterschiedlichen Versionen von 5.5 bis 5.7. Die Instanzen sollen so schnell wie möglich auf die neueste Version umgestellt werden. Hierbei soll aber sichergestellt sein, dass die Anwendungen bei der Umstellung auf die neueren Versionen weiterhin funktionieren. Das Unternehmen hätte kein Problem damit, in der Cloud zunächst die gleiche Version zu verwenden und das Upgrade später durchzuführen. Die bevorzugte Vorgehensweise besteht aber darin, zwei Aufgaben gleichzeitig zu erledigen.

Darüber hinaus möchte das Unternehmen sicherstellen, dass die Datenworkloads bei einem Ausfall sicher und in mehreren geografischen Regionen verfügbar sind, und sich die verfügbaren Konfigurationsoptionen ansehen.

WWI möchte für die erste Migration mit einer einfachen Anwendung beginnen und dann in einer späteren Phase zu unternehmenskritischen Anwendungen übergehen. Auf diese Weise kann das Team die Kenntnisse und Erfahrungswerte erlangen, die es benötigt, um zukünftige Migrationen vorbereiten und planen zu können.  

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewertung](./03-assessment.md)
