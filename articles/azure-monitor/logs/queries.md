---
title: Verwenden von Abfragen in Azure Monitor Log Analytics
description: Dies ist eine Übersicht über Protokollabfragen in Azure Monitor Log Analytics, einschließlich verschiedener Arten von Abfragen und Beispielabfragen, die Sie verwenden können.
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 004196117512282ee960ed275dd970b472cde918
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662317"
---
# <a name="using-queries-in-azure-monitor-log-analytics"></a>Verwenden von Abfragen in Azure Monitor Log Analytics
Wenn Sie Log Analytics öffnen, haben Sie Zugriff auf vorhandene Protokollabfragen. Sie können diese Abfragen entweder ohne Änderung ausführen oder als Startpunkt für Ihre eigenen Abfragen verwenden. Die verfügbaren Abfragen enthalten von Azure Monitor bereitgestellte Beispiele und von Ihrer Organisation gespeicherte Abfragen. In diesem Artikel wird beschrieben, welche Abfragen verfügbar sind und wie Sie diese suchen und verwenden können.


## <a name="queries-interface"></a>Abfrageschnittstelle
Wählen Sie Abfragen über die Abfrageschnittstelle aus, auf die Sie über zwei verschiedene Punkte in Log Analytics zugreifen können.

### <a name="queries-dialog"></a>Dialogfeld „Abfragen“

Wenn Sie Log Analytics öffnen, wird automatisch das Dialogfeld *Abfragen* angezeigt. Wenn dieses Dialogfeld nicht automatisch angezeigt werden soll, deaktivieren Sie den Schalter **Abfragen immer anzeigen**.

![Bildschirm „Abfragen“](media/queries/query-start.png)


Jede Abfrage wird durch eine Karte dargestellt. Sie können die Abfragen schnell durchsuchen, um die gewünschte Abfrage zu finden. Sie können die Abfrage direkt über das Dialogfeld ausführen oder in den Abfrage-Editor laden, um Anpassungen vorzunehmen.

Auf dieses Dialogfeld können Sie auch zugreifen, indem Sie oben rechts auf dem Bildschirm auf **Abfragen** klicken.

[![Schaltfläche „Queries“ (Abfragen)](media/queries/queries-button.png)](media/queries/queries-button.png#lightbox)

### <a name="query-sidebar"></a>Randleiste „Abfragen“

Sie können auch über den Bereich „Abfragen“ auf der linken Randleiste von Log Analytics auf die Funktionen des Dialogfelds zugreifen. Sie können mit dem Mauszeiger auf den Namen einer Abfrage zeigen, um die Beschreibung und zusätzliche Funktionen der Abfrage anzuzeigen.

[![Randleiste „Abfragen“](media/queries/query-sidebar.png)](media/queries/query-sidebar.png#lightbox)

## <a name="finding-and-filtering-queries"></a>Suchen und Filtern von Abfragen

Die Optionen in diesem Abschnitt sind im Dialogfeld sowie im Bereich „Abfragen“ der Seitenleiste verfügbar, jedoch mit einer etwas anderen Benutzeroberfläche.  


### <a name="group-by"></a>Gruppieren nach

Ändern Sie die Gruppierung der Abfragen, indem Sie auf die Dropdownliste *Gruppieren nach* klicken. Die Gruppierungswerte dienen außerdem als aktives Inhaltsverzeichnis. Durch Klicken auf einen der Werte links auf dem Bildschirm wird in der Ansicht der Abfragen direkt das entsprechende Element angezeigt. Wenn Ihre Organisation Abfragepakete mit Tags erstellt hat, werden die benutzerdefinierten Tags in diese Liste aufgenommen.

[![Bildschirm „Beispielabfragen“, „Gruppieren nach“](media/queries/example-query-groupby.png)](media/queries/example-query-groupby.png#lightbox)



### <a name="filter"></a>Filtern

Sie können die Abfragen auch nach den oben erwähnten Werten für **Gruppieren nach** filtern. Die Filter werden oben im Dialogfeld „Beispielabfragen“ angezeigt.

[![Bildschirm „Beispielabfragen“, Filter](media/queries/example-query-filter.png)](media/queries/example-query-filter.png#lightbox)

### <a name="combining-group-by-and-filter"></a>Kombinieren von Gruppierung und Filtern

Die Filter- und Gruppierungsfunktionen können kombiniert werden. Dies bietet Flexibilität bei der Anordnung der Abfragen. Bei einer Ressourcengruppe mit mehreren Ressourcen müssen Sie die Abfragen beispielsweise nach einem bestimmten Ressourcentyp filtern und dann nach Thema anordnen.

## <a name="query-properties"></a>Abfrageeigenschaften
Jede Abfrage verfügt über mehrere Eigenschaften, die Ihnen helfen, sie zu gruppieren und zu finden. Diese Eigenschaften stehen zum Sortieren und Filtern zur Verfügung, und Sie können mehrere davon definieren, [wenn Sie Ihre eigene Abfrage speichern](save-query.md). Dies sind die Eigenschaftentypen:

- **Ressourcentyp:** eine Ressource entsprechend der Definition in Azure, z. B. ein virtueller Computer. Eine vollständige Zuordnung der Azure Monitor-Protokolle und Log Analytics-Tabellen zum Ressourcentyp finden Sie in der [Referenz zu Azure Monitor-Tabellen](/azure/azure-monitor/reference/tables/tables-resourcetype).  
- **Kategorie:** ein Informationstyp, z. B. *Sicherheit* oder *Überprüfung*. Die Kategorien sind identisch mit den Kategorien, die im Bereich „Tabellen“ der Seitenleiste definiert sind. In der [Referenz zu Azure Monitor-Tabellen](/azure/azure-monitor/reference/tables/tables-category) finden Sie eine vollständige Liste der Kategorien.  
- **Lösung:** eine Azure Monitor-Lösung, die mit den Abfragen verknüpft ist.
- **Thema:** das Thema der Beispielabfrage, z. B. *Aktivitätsprotokolle* oder *App-Protokolle*. Die Themeneigenschaft ist spezifisch für Beispielabfragen und kann sich je nach Ressourcentyp unterscheiden.
- - **Abfragetyp:** definiert den Typ der Abfrage. Der Abfragetyp kann Beispielabfragen, Abfragepaketabfragen oder Legacyabfragen lauten.
- **Bezeichnungen** – Benutzerdefinierte Bezeichnungen, die Sie definieren und zuweisen können, [wenn Sie Ihre eigene Abfrage speichern](save-query.md).
- **Tags** – Benutzerdefinierte Eigenschaften, die beim [Erstellen eines Abfragepakets](query-packs.md) definiert werden können. Tags ermöglichen es Ihrer Organisation, eigene Taxonomien zum Organisieren von Abfragen zu erstellen.

## <a name="favorites"></a>Favoriten
Für einen schnelleren Zugriff können Sie häufig verwendete Abfragen als Favoriten festlegen. Klicken Sie auf den Stern neben der Abfrage, um Sie zu Ihren **Favoriten** hinzuzufügen. Zeigen Sie Ihre bevorzugten Abfragen über die Option **Favoriten** in der Abfrageschnittstelle an.

## <a name="types-of-queries"></a>Abfragetypen
Die Abfrageschnittstelle wird mit den folgenden Abfragetypen aufgefüllt:

**Beispielabfragen:** Beispielabfragen bieten einen direkten Einblick in eine Ressource und stellen eine gute Ausgangsbasis für den Einstieg in die Verwendung von KQL dar, sodass Sie schneller mit der Verwendung von Log Analytics beginnen können. Es wurden mehr als 500 Beispielabfragen gesammelt und zusammengestellt, die Ihnen einen unmittelbaren Nutzen bieten sollen. Die Zahl der Beispielabfragen steigt darüber hinaus kontinuierlich.

**Abfragepakete:** Ein [Abfragepaket](query-packs.md) enthält mehrere Protokollabfragen, einschließlich Abfragen, die Sie selbst speichern. Dies schließt das [Standardabfragepaket](query-packs.md#default-query-pack) und alle anderen Abfragepakete ein, die Ihre Organisation möglicherweise im Abonnement erstellt hat.

**Legacyabfragen:** Dies sind Protokollabfragen, die zuvor im Abfrage-Explorer gespeichert wurden und Azure-Lösungen abfragen, die im Arbeitsbereich installiert sind. Diese sind im Abfragedialogfeld unter **Legacy queries** (Legacyabfragen) aufgeführt.
>[!TIP]
> Legacyabfragen sind nur in einem Log Analytics-Arbeitsbereich verfügbar.

## <a name="effect-of-query-scope"></a>Auswirkungen des Abfragebereichs
Die Abfragen, die beim Öffnen von Log Analytics verfügbar sind, werden durch den aktuellen [Abfragebereich](scope.md) bestimmt.

- Für einen **Arbeitsbereich:** Alle Beispielabfragen und Abfragen aus Abfragepaketen. Legacyabfragen im Arbeitsbereich.
- Für eine **einzelne Ressource:** Beispielabfragen und Abfragen aus Abfragepaketen für den Ressourcentyp. 
- Für eine **Ressourcengruppe:** Beispielabfragen und Abfragen aus Abfragepaketen für Ressourcentypen in der Ressourcengruppe. 

> [!TIP]
> Je mehr Ressourcen in einem Bereich enthalten sind, desto länger dauert es, bis im Portal die Abfragen gefiltert und im entsprechenden Dialogfeld angezeigt werden.


## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit KQL-Abfragen](get-started-queries.md)

