---
title: Nützliche Ressourcen für das Arbeiten mit Microsoft Sentinel
description: Dieses Dokument enthält eine Liste von Ressourcen, die nützlich sind, wenn mit Microsoft Sentinel gearbeitet wird.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6ad57bdde0ce86f69747e3d29258bc128888b212
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522477"
---
# <a name="useful-resources-for-working-with-microsoft-sentinel"></a>Nützliche Ressourcen für das Arbeiten mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel listet Ressourcen auf, aus denen Sie weitere Informationen zum Arbeiten mit Microsoft Sentinel abrufen können.

## <a name="learn-more-about-creating-queries"></a>Weitere Informationen zum Erstellen von Abfragen

In Microsoft Sentinel werden Abfragen mit Abfragesprache KQL (Kusto Query Language) von Azure Monitor Log Analytics erstellt. Weitere Informationen finden Sie unter:

- [Erste Schritte mit Kusto](/azure/data-explorer/kusto/concepts/)
- [Übersicht über KQL-Abfragen](/azure/data-explorer/kusto/query/)
- [KQL-Kurzübersicht](/azure/data-explorer/kql-quick-reference).
- [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>Weitere Informationen zum Erstellen von automatisierten Vorgängen

Erstellen Sie in Microsoft Sentinel automatisierte Vorgänge mithilfe von Azure Logic Apps, mit einem wachsenden Katalog integrierter Playbooks. 

Weitere Informationen finden Sie unter [Connectordokumentation](/connectors/).

## <a name="compare-playbooks-workbooks-and-notebooks"></a>Vergleich von Playbooks, Arbeitsmappen und Notebooks

In der folgenden Tabelle werden die Unterschiede zwischen Playbooks, Arbeitsmappen und Notebooks in Microsoft Sentinel beschrieben:

| Category |Playbooks  |Arbeitsmappen  |Notebooks  |
|---------|---------|---------|---------|
|**Personas**     |   <ul><li>SOC-Ingenieure</li><li>Analysten aller Ebenen</li></ul>      | <ul><li> SOC-Ingenieure</li><li>Analysten aller Ebenen</li></ul>       | <ul><li>Bedrohungssucher und Ebene-2/Ebene-3-Analysten</li><li>Zwischenfallermittler</li><li>Datenanalysten</li><li>Sicherheitsforscher</li></ul>       |
|**Verwendung**     | Automatisierung von einfachen, wiederholbaren Aufgaben:<ul><li>Erfassung externer Daten </li><li>Datenanreicherung mit Tl, GeoIP-Lookups und mehr </li><li> Untersuchung </li><li>Behebung </li></ul>       | <ul><li>Visualisierung</li></ul>        |   <ul><li>Abfrage von Microsoft Sentinel- und externen Daten </li><li>Datenanreicherung mit Tl, GeoIP-Lookups und Whois-Lookups und mehr </li><li> Untersuchung </li><li> Visualisierung </li><li> Suche </li><li>Maschinelles Lernen und Big-Data-Analyse </li></ul>      |
|**Vorteile**     |<ul><li> Am besten geeignet für einzelne, wiederholbare Aufgaben </li><li>Keine Kodierungskenntnisse erforderlich  </li></ul>      |<ul><li>Am besten geeignet für einen allgemeinen Überblick über Microsoft Sentinel-Daten </li><li>Keine Kodierungskenntnisse erforderlich</li></ul>       | <ul><li>Am besten für komplexe Ketten von wiederholbaren Aufgaben </li><li>Ad-hoc, mehr prozedurale Kontrolle</li><li>Leichteres Pivotieren mit interaktiven Funktionen </li><li>Umfangreiche Python-Bibliotheken für die Datenmanipulation und -visualisierung </li><li>Maschinelles Lernen und benutzerdefinierte Analysen </li><li>Einfaches Dokumentieren und Freigeben von Analysebeweisen </li></ul>       |
|**Herausforderungen**     | <ul><li>Nicht geeignet für ad-hoc und komplexe Aufgabenketten </li><li>Nicht ideal zum Dokumentieren und Freigeben von Beweisen</li></ul>        |   <ul><li>Eine Integration mit externen Daten ist nicht möglich </li></ul>     |    <ul><li> Hohe Lernkurve und erfordert Programmierkenntnisse </li></ul>   |
|  **Weitere Informationen**   | [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md)        | [Visualisieren gesammelter Daten](get-visibility.md)        | [Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>Kommentieren Sie unsere Blogs und Foren

Wir freuen uns, von unseren Benutzern zu hören.

Im TechCommunity-Bereich für Microsoft Sentinel:

- [Lesen Sie aktuelle Blogbeiträge, und kommentieren Sie sie](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Posten Sie Ihre Fragen zu Microsoft Sentinel](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

Sie können auch über unser [User Voice](https://feedback.azure.com/d365community/forum/37638d17-0625-ec11-b6e6-000d3a4f07b8)-Programm Verbesserungsvorschläge senden.

## <a name="join-the-microsoft-sentinel-github-community"></a>Treten Sie der GitHub-Community für Microsoft Sentinel bei.

Das [GitHub-Repository für Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel) ist eine hervorragende Ressource für Bedrohungserkennung und Automatisierung. 

Die Sicherheitsanalysten von Microsoft erstellen laufend neue Arbeitsmappen, Playbooks, Suchabfragen usw., die in der Community veröffentlicht werden und Ihnen zur Verwendung in Ihrer Umgebung zur Verfügung stehen. 

Laden Beispielinhalte aus dem privaten GitHub-Repository der Community herunter, um benutzerdefinierte Arbeitsmappen, Suchabfragen, Notebooks und Playbooks für Microsoft Sentinel zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lassen Sie sich zertifizieren!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Informieren Sie sich über Anwendungsfälle unserer Kunden](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)
