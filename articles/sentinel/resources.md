---
title: Nützliche Ressourcen für die Arbeit mit Azure Sentinel
description: Dieses Dokument enthält eine Liste von Ressourcen, die nützlich sind, wenn mit Azure Sentinel gearbeitet wird.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f8f4dc553bcb9750199d38c640c70012b73bf4bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055101"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nützliche Ressourcen für das Arbeiten mit Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel listet Ressourcen auf, aus denen Sie weitere Informationen zum Arbeiten mit Azure Sentinel abrufen können.

## <a name="learn-more-about-creating-queries"></a>Weitere Informationen zum Erstellen von Abfragen

In Azure Sentinel werden Abfragen mit der Kusto-Abfragesprache (Kusto Query Language, KQL) von Azure Monitor Log Analytics erstellt. Weitere Informationen finden Sie unter:

- [Erste Schritte mit Kusto](/azure/data-explorer/kusto/concepts/)
- [Übersicht über KQL-Abfragen](/azure/data-explorer/kusto/query/)
- [KQL-Kurzübersicht](/azure/data-explorer/kql-quick-reference).
- [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>Weitere Informationen zum Erstellen von automatisierten Vorgängen

Erstellen Sie in Azure Sentinel automatisierte Vorgänge mithilfe von Azure Logic Apps, mit einem wachsenden Katalog integrierter Playbooks. 

Weitere Informationen finden Sie unter [Connectordokumentation](/connectors/).

## <a name="compare-playbooks-workbooks-and-notebooks"></a>Vergleich von Playbooks, Arbeitsmappen und Notebooks

In der folgenden Tabelle werden die Unterschiede zwischen Playbooks, Arbeitsmappen und Notebooks in Azure Sentinel beschrieben:

| Category |Playbooks  |Arbeitsmappen  |Notebooks  |
|---------|---------|---------|---------|
|**Personas**     |   <ul><li>SOC-Ingenieure</li><li>Analysten aller Ebenen</li></ul>      | <ul><li> SOC-Ingenieure</li><li>Analysten aller Ebenen</li></ul>       | <ul><li>Bedrohungssucher und Ebene-2/Ebene-3-Analysten</li><li>Zwischenfallermittler</li><li>Datenanalysten</li><li>Sicherheitsforscher</li></ul>       |
|**Verwendung**     | Automatisierung von einfachen, wiederholbaren Aufgaben:<ul><li>Erfassung externer Daten </li><li>Datenanreicherung mit Tl, GeoIP-Lookups und mehr </li><li> Untersuchung </li><li>Behebung </li></ul>       | <ul><li>Visualisierung</li></ul>        |   <ul><li>Abfrage von Azure Sentinel-Daten und externen Daten </li><li>Datenanreicherung mit Tl, GeoIP-Lookups und Whois-Lookups und mehr </li><li> Untersuchung </li><li> Visualisierung </li><li> Suche </li><li>Maschinelles Lernen und Big-Data-Analyse </li></ul>      |
|**Vorteile**     |<ul><li> Am besten geeignet für einzelne, wiederholbare Aufgaben </li><li>Keine Kodierungskenntnisse erforderlich  </li></ul>      |<ul><li>Am besten geeignet für einen Überblick über Azure Sentinel-Daten auf hoher Ebene </li><li>Keine Kodierungskenntnisse erforderlich</li></ul>       | <ul><li>Am besten für komplexe Ketten von wiederholbaren Aufgaben </li><li>Ad-hoc, mehr prozedurale Kontrolle</li><li>Leichteres Pivotieren mit interaktiven Funktionen </li><li>Umfangreiche Python-Bibliotheken für die Datenmanipulation und -visualisierung </li><li>Maschinelles Lernen und benutzerdefinierte Analysen </li><li>Einfaches Dokumentieren und Freigeben von Analysebeweisen </li></ul>       |
|**Herausforderungen**     | <ul><li>Nicht geeignet für ad-hoc und komplexe Aufgabenketten </li><li>Nicht ideal zum Dokumentieren und Freigeben von Beweisen</li></ul>        |   <ul><li>Eine Integration mit externen Daten ist nicht möglich </li></ul>     |    <ul><li> Hohe Lernkurve und erfordert Programmierkenntnisse </li></ul>   |
|  **Weitere Informationen**   | [Automatisieren der Bedrohungsabwehr mit Playbooks in Azure Sentinel](automate-responses-with-playbooks.md)        | [Visualisieren gesammelter Daten](get-visibility.md)        | [Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>Kommentieren Sie unsere Blogs und Foren

Wir freuen uns, von unseren Benutzern zu hören.

Im TechCommunity-Bereich für Azure Sentinel:

- [Lesen Sie aktuelle Blogbeiträge, und kommentieren Sie sie](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Posten Sie Ihre Fragen zu Azure Sentinel](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

Sie können auch über unser [User Voice](https://feedback.azure.com/forums/920458-azure-sentinel)-Programm Verbesserungsvorschläge senden.

## <a name="join-the-azure-sentinel-github-community"></a>Werden Sie Mitglied der Azure Sentinel-GitHub-Community

Das [Azure Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel) ist eine hervorragende Ressource für die Bedrohungserkennung und Automatisierung. 

Die Sicherheitsanalysten von Microsoft erstellen laufend neue Arbeitsmappen, Playbooks, Suchabfragen usw., die in der Community veröffentlicht werden und Ihnen zur Verwendung in Ihrer Umgebung zur Verfügung stehen. 

Laden Sie Beispielinhalte aus dem privaten GitHub-Repository der Community herunter, um benutzerdefinierte Arbeitsmappen, Suchabfragen, Notebooks und Playbooks für Azure Sentinel zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lassen Sie sich zertifizieren!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Informieren Sie sich über Anwendungsfälle unserer Kunden](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)
