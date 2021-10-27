---
title: Informationen zu Open-Source-Tools und Hilfsprogrammen für Azure Purview
description: Dieses Tutorial enthält eine Liste mit verschiedenen Tools und Hilfsprogrammen, die in Azure Purview verfügbar sind, sowie Informationen zu deren Verwendung.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/10/2021
ms.openlocfilehash: 8c87adc48a9a717a6ca9796b6d3bb2ec780220f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007577"
---
# <a name="azure-purview-open-source-tools-and-utilities"></a>Open-Source-Tools und Hilfsprogramme für Azure Purview

In diesem Artikel werden mehrere Open-Source-Tools und Hilfsprogramme (Befehlszeilen-, Python- und PowerShell-Schnittstelle) aufgeführt, die einen schnellen Einstieg in den Azure Purview-Dienst ermöglichen. Diese Tools wurden gemeinsam von der Azure Purview-Produktgruppe und der Open-Source-Community erstellt und entwickelt. Das Ziel dieser Tools besteht darin, das Kennenlernen, den Einstieg, die reguläre Nutzung und die langfristige Einführung von Purview zu vereinfachen und zu beschleunigen.

### <a name="intended-audience"></a>Zielpublikum

- Azure Purview-Community einschließlich Kunden, Entwicklern, ISVs, Partnern, Evangelists und Enthusiasten. 

- Der Azure Purview-Katalog basiert auf [Apache Atlas](https://atlas.apache.org/) und erweitert die vollständige Unterstützung für Apache Atlas-APIs. Wir laden die Apache Atlas-Community sowie Apache Atlas-Enthusiasten und -Entwickler dazu ein, voll und ganz auf Azure Purview aufzubauen und es weiterzuempfehlen.

### <a name="purview-customer-journey-stages"></a>Phasen der Customer Journey für Purview

- *Purview-Einsteiger:* Benutzer, die noch keine Erfahrung mit dem Azure Purview-Dienst haben und sich mit der Funktionsweise einer einheitlichen Datengovernancelösung mit mehreren Clouds vertraut machen möchten. Dazu gehören auch Benutzer, die Purview mit anderen konkurrierenden Lösungen auf dem Datengovernancemarkt vergleichen und Purview testen möchten, bevor sie sich für eine langfristige Nutzung entscheiden.

- *Purview-Innovatoren:* Innovatoren, die sich mit bereits vorhandenen und den neuesten Features auseinandersetzen, Ideen entwickeln und zukünftige Purview-Features konzipieren möchten. Sie haben sowohl Erfahrung mit der Erstellung und Entwicklung von Lösungen für Kunden als auch zukunftsorientierte Ideen für innovative Datengovernanceprodukte der nächsten Generation.

- *Purview-Enthusiasten/-Evangelists:* Eine Kombination aus Einsteigern und Innovatoren. Sie verfügen über solide Purview-Kenntnisse und sind von der Einführung von Purview begeistert. Sie können dazu beitragen, Purview als Dienst zu bewerben und verschiedene andere Purview-Benutzer und potenzielle Kunden auf der ganzen Welt mit Purview vertraut zu machen.

- *Purview-Anwender:* Anwender, die Purview bereits eingeführt und erkundet haben und seit mehreren Monaten problemlos damit arbeiten.

- *Purview-Langzeitbenutzer:* Langzeitbenutzer, die Purview seit mehr als einem Jahr verwenden und inzwischen auch mit besonders komplexen Purview-Anwendungsfällen im Azure-Portal und in Purview Studio vertraut sind. Darüber hinaus kennen sie sich nahezu perfekt mit den Purview-REST-APIs sowie mit den zusätzlichen Anwendungsfällen aus, die über Purview-APIs unterstützt werden.


## <a name="azure-purview-open-source-tools-and-utilities-list"></a>Liste der Open-Source-Tools und Hilfsprogramme für Azure Purview

1. [Purview-API-via-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/README.md) 

    - **Empfohlene Customer Journey-Phasen:** *Einsteiger, Innovatoren, Enthusiasten, Anwender, Langzeitbenutzer*
    - **Beschreibung:** Dieses Hilfsprogramm basiert auf der Microsoft-Dokumentation der [Azure Purview-REST-API-Referenz](/rest/api/purview/) und deckt diese vollständig ab. Sie können es [hier](https://aka.ms/purview-api-ps) aus dem PowerShell-Katalog herunterladen und installieren. Es hilft Ihnen beim Ausführen aller dokumentierten Purview-REST-APIs über eine schnelle und einfach zu verwendende PowerShell-Schnittstelle. Verwenden und automatisieren Sie Purview-APIs für die regelmäßige und langfristige Verwendung über Befehlszeilen- und Skriptmethoden. Dies ist eine Alternative für Kunden, die Massenaufgaben automatisiert, im Batchmodus oder in geplanten Cronjobs ausführen möchten (im Gegensatz zur GUI-Methode mit Azure-Portal und Purview Studio). Eine ausführliche Dokumentation, einen Leitfaden für die Nutzung von Beispielen, Selbsthilfe und Beispiele finden Sie unter auf GitHub unter [Azure-Purview-API-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell).

1. [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)

    - **Empfohlene Customer Journey-Phasen:** *Einsteiger, Innovatoren, Enthusiasten*
    - **Beschreibung:** PowerShell-Skript für die erstmalige Einrichtung des Purview-Kontos. Für alle, die mehrere neue Purview-Konten in weniger als fünf Minuten einrichten möchten.

1. [Purview-Lab](https://aka.ms/purviewlab)

    - **Empfohlene Customer Journey-Phasen:** *Einsteiger, Innovatoren, Enthusiasten*
    - **Beschreibung:** Ein praktisches Lab mit einer Einführung in die unzähligen Features von Purview. Die Konzepte werden mithilfe eines praxisorientierten Ansatzes vermittelt, bei dem Sie jeden Schritt selbst ausführen, um sich bestmöglich mit Purview vertraut zu machen.

1. [Purview-CLI](https://aka.ms/purviewcli)

    - **Empfohlene Customer Journey-Phasen:** *Innovatoren, Enthusiasten, Anwender, Langzeitbenutzer*
    - **Beschreibung:** Python-basiertes Tool zum Ausführen der Purview-APIs (ähnlich wie [Purview-API-via-PowerShell](https://aka.ms/purview-api-ps), aber mit eingeschränkten/weniger Funktionen als beim PowerShell-basierten Framework).

1. [Purview-Demo](https://aka.ms/pvdemo)

    - **Empfohlene Customer Journey-Phasen:** *Einsteiger, Innovatoren, Enthusiasten*
    - **Beschreibung:** Ein auf einer ARM-Vorlage (Azure Resource Manager) basierendes Tool zum schnellen und sicheren automatischen Einrichten und Bereitstellen eines neuen Azure Purview-Kontos mit nur einem einzelnen Befehl. Es ähnelt [Purview-Starter-Kit](https://aka.ms/PurviewKickstart), stellt aber einige weitere vorkonfigurierte Datenquellen bereit: Azure SQL-Datenbank, Azure Data Lake Storage Gen2-Konto, Azure Data Factory, Azure Synapse Analytics-Arbeitsbereich

1. [PyApacheAtlas: Schnittstelle zwischen Azure Purview und Apache Atlas](https://github.com/wjohnson/pyapacheatlas) mit Atlas-APIs

    - **Empfohlene Customer Journey-Phasen:** *Innovatoren, Enthusiasten, Anwender, Langzeitbenutzer*
    - **Beschreibung:** Ein Python-Paket für die Verwendung von Azure Purview und der Apache Atlas-API. Unterstützt Massenladen, benutzerdefinierte Datenherkunft und Ähnliches aus Python-bezogenen Klassen und Excel-Vorlagen. Das Paket unterstützt die programmgesteuerte Interaktion und eine Excel-Vorlage für Uploads mit wenig Code.

1. [Purview-Leser für Event Hub-Benachrichtigungen](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/purview_atlas_eventhub_sample.py)

    - **Empfohlene Customer Journey-Phasen:** *Innovatoren, Enthusiasten, Anwender, Langzeitbenutzer*
    - **Beschreibung:** Dieses Tool veranschaulicht das Lesen des Event Hubs von Purview sowie das Erfassen von Kafka-Echtzeitbenachrichtigungen aus dem Event Hub im Atlas-Benachrichtigungsformat (https://atlas.apache.org/2.0.0/Notifications.html). Während der Ausführung wird außerdem eine Excel-Tabelle (CSV-Datei) mit den Entitäten und Ressourcen generiert, die während einer Überprüfung gefunden wurden, sowie mit ggf. weiteren interessanten Benachrichtigungen, die von Purview generiert werden.


## <a name="feedback-and-disclaimer"></a>Feedback und Haftungsausschluss

Microsoft gibt für keines der Tools eine Garantie hinsichtlich ihrer Leistungsfähigkeit oder Funktionalität. Sie sind jedoch frei von schädlichen Aktivitäten und Viren, und es wird garantiert, dass keinerlei private oder vertrauliche Daten gesammelt werden.

Wenden Sie sich bei Feedback oder Fragen zur Leistungsfähigkeit und Funktionalität während der Nutzung an die jeweiligen Toolbesitzer und -ersteller. Die Kontaktdetails finden Sie im jeweiligen GitHub Repository.


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
