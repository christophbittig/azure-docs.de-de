---
title: 'Schnellstart: Erstellen einer Demo-App im Azure-Portal'
titleSuffix: Azure Cognitive Search
description: Führen Sie den Assistenten „Demo-App erstellen“ aus, um HTML-Seiten und ein Skript für eine funktionierende Web-App zu generieren. Die Seite umfasst eine Suchleiste, einen Ergebnisbereich, eine Randleiste und Typeahead-Unterstützung.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 08/24/2021
ms.openlocfilehash: f9ce6f3c7898a492400f6a8cec62e65ab8777e15
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770863"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Schnellstart: Erstellen einer Demo-App im Portal (Azure Cognitive Search)

Verwenden Sie den Assistenten **Demo-App erstellen** im Azure-Portal, um eine herunterladbare, localhost-ähnliche Web-App zu generieren, die in einem Browser ausgeführt wird. Die generierte App ist abhängig von ihrer Konfiguration sofort einsatzbereit und verfügt über eine schreibgeschützte Liveverbindung mit einem Remoteindex. Eine Standard-App kann eine Suchleiste, einen Ergebnisbereich, Randleistenfilter und Typeahead-Unterstützung beinhalten.

Mit der Demo-App können Sie visualisieren, wie ein Index in einer Client-App funktioniert, aber sie ist nicht für Produktionsszenarien bestimmt. Client-Apps sollten Logik für Sicherheit, Fehlerbehandlung und Hosting enthalten, die von der generierten HTML-Seite nicht bereitgestellt wird. Wenn Sie zum Erstellen einer Client-App bereit sind, können Sie die nächsten Schritte unter [Erstellen Ihrer ersten Such-App mit dem .NET SDK](tutorial-csharp-create-first-app.md) ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

+ Ein Azure Cognitive Search-Dienst [Erstellen Sie einen Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Für diesen Schnellstart können Sie einen kostenlosen Dienst verwenden. 

+ [Microsoft Edge (neueste Version)](https://www.microsoft.com/edge) oder Google Chrome

+ Ein [Suchindex](search-what-is-an-index.md) als Grundlage für Ihre generierte Anwendung 

  In dieser Schnellstartanleitung werden die integrierten Immobilienbeispieldaten und der zugehörige Index verwendet, da sie Miniaturbilder enthalten. (Vom Assistenten wird das Hinzufügen von Bildern zur Ergebnisseite unterstützt.) Führen Sie zum Erstellen des in dieser Übung verwendeten Index den Assistenten **Daten importieren** aus, und verwenden Sie die Datenquelle *realestate-us-sample*.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="Datenquellenseite für Beispieldaten" border="false":::

Wenn der Index bereit ist, fahren Sie mit dem nächsten Schritt fort.

## <a name="start-the-wizard"></a>Starten des Assistenten

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

1. [Suchen Sie Ihren Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/), und wählen Sie auf der Übersichtsseite über die Links in der Mitte der Seite **Indizes** aus. 

1. Wählen Sie in der Liste mit den bereits vorhandenen Indizes den Index *realestate-us-sample-index* aus.

1. Wählen Sie oben auf der Indexseite die Option **Demo-App erstellen (Vorschau)** aus, um den Assistenten zu starten.

1. Wählen Sie auf der ersten Seite des Assistenten **CORS aktivieren** (Cross-Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) aus, um Ihrer Indexdefinition CORS-Unterstützung hinzuzufügen. Dieser Schritt ist zwar optional, ohne ihn stellt Ihre lokale Web-App jedoch keine Verbindung mit dem Remoteindex her.

## <a name="configure-search-results"></a>Konfigurieren von Suchergebnissen

Der Assistent bietet ein einfaches Layout für gerenderte Suchergebnisse mit Platz für ein Miniaturbild, einen Titel und eine Beschreibung. Diesen Elementen liegt jeweils ein Feld in Ihrem Index zugrunde, das die Daten liefert. 

1. Wählen Sie unter „Miniaturbild“ das Feld *thumbnail* des Index *realestate-us-sample* aus. Dieses Beispiel enthält Miniaturbilder in Form von Bildern mit einer URL-Adresse, die in einem Feld namens *thumbnail* gespeichert sind. Sollte Ihr Index keine Bilder enthalten, lassen Sie dieses Feld leer.

1. Wählen Sie unter „Titel“ ein Feld aus, das die Eindeutigkeit des jeweiligen Dokuments vermittelt. In diesem Beispiel ist die Angebots-ID eine gute Wahl.

1. Wählen Sie unter „Beschreibung“ ein Feld mit Details aus, auf deren Grundlage Benutzer ggf. besser entscheiden können, ob sie zu diesem speziellen Dokument navigieren möchten.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="Konfigurieren von Ergebnissen für Beispieldaten" border="false":::

## <a name="add-a-sidebar"></a>Hinzufügen einer Randleiste

Der Suchdienst unterstützt die Facettennavigation, und diese wird häufig als Randleiste gerendert. Facetten basieren auf filter- und facettierbaren Feldern, die im Indexschema ausgedrückt werden.

Die Facettennavigation in Azure Cognitive Search ist eine kumulative Filterfunktion. Durch Auswählen mehrerer Filter innerhalb einer einzelnen Kategorie – beispielsweise „Seattle“ und „Bellevue“ unter „City“ (Ort) – werden die Ergebnisse ausgeweitet. Wenn Sie mehrere kategorieübergreifende Filter auswählen, werden die Ergebnisse eingegrenzt.

> [!TIP]
> Das vollständige Indexschema kann im Portal angezeigt werden. Suchen Sie auf der Übersichtsseite des jeweiligen Index nach dem Link **Indexdefinition (JSON)** . Für die Facettennavigation geeignete Felder verfügen über die Attribute „filterable: true“ und „facetable: true“.

1. Wählen Sie im Assistenten oben auf der Seite die Registerkarte **Seitenleiste** aus. Eine Liste aller Felder wird angezeigt, die im Index durch Attribute als filterbar und facettenreich gekennzeichnet sind.

1. Akzeptieren Sie die aktuelle Auswahl von Facettenfeldern, und wechseln Sie zur nächsten Seite.

## <a name="add-typeahead"></a>Hinzufügen von Typeahead

Die Typeahead-Funktion steht in Form von AutoVervollständigen und Abfragevorschlägen zur Verfügung. Der Assistent unterstützt Abfragevorschläge. Der Suchdienst gibt auf der Grundlage der Tastatureingaben des Benutzers eine Liste vervollständigter Abfragezeichenfolgen zurück, die als Eingabe ausgewählt werden können.

Vorschläge werden für bestimmte Felddefinitionen aktiviert. Der Assistent bietet Optionen, mit denen Sie den Informationsumfang in einem Vorschlag konfigurieren können. 

Im folgenden Screenshot werden die Optionen im Assistenten einer gerenderten Seite in der App gegenübergestellt. Sie sehen, wie die Feldauswahl verwendet und wie mithilfe von „Feldname anzeigen“ gesteuert wird, ob die Beschriftung in den Vorschlag einbezogen wird.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Konfiguration von Abfragevorschlägen":::

## <a name="add-suggestions"></a>Hinzufügen von Vorschlägen

Vorschläge beziehen sich auf automatisierte Abfrageaufforderungen, die an das Suchfeld angefügt sind. Cognitive Search unterstützt zwei davon: *automatische Vervollständigung* eines teilweise eingegebenen Suchbegriffs und *Vorschläge* für eine Dropdownliste möglicherweise übereinstimmender Dokumente.

Der Assistent unterstützt Vorschläge, und die Felder, die vorgeschlagene Ergebnisse bereitstellen können, werden von einem [`Suggesters`](index-add-suggesters.md)-Konstrukt im Index abgeleitet:

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. Wählen Sie im Assistenten oben auf der Seite die Registerkarte **Vorschläge** aus. Eine Liste aller Felder wird angezeigt, die im Indexschema als Vorschlagsanbieter festgelegt sind.

1. Akzeptieren Sie die aktuelle Auswahl, und wechseln Sie zur nächsten Seite.

## <a name="create-download-and-execute"></a>Erstellen, Herunterladen und Ausführen

1. Wählen Sie unten auf der Seite **Demo-App erstellen** aus, um die HTML-Datei zu generieren.

1. Wählen Sie nach entsprechender Aufforderung die Option **Download your app** (App herunterladen) aus, um die Datei herunterzuladen.

1. Öffnen Sie die Datei, und klicken Sie auf die Schaltfläche „Suchen“. Mit dieser Aktion wird eine Abfrage ausgeführt. Dabei kann es sich um eine leere Abfrage (`*`) handeln, die ein beliebiges Resultset zurückgibt. Die Seite sollte in etwa dem folgenden Screenshot entsprechen. Geben Sie einen Begriff ein, und grenzen Sie die Ergebnisse mithilfe von Filtern ein. 

Der zugrunde liegende Index besteht aus fiktiven, generierten, dokumentübergreifend duplizierten Daten, und die Beschreibungen passen manchmal nicht zum Bild. Wenn Sie eine App auf der Grundlage Ihrer eigenen Indizes erstellen, können Sie ein stimmigeres Ergebnis erwarten.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Ausführen der App":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, sollten Sie sich am Ende eines Projekts überlegen, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link **Alle Ressourcen** oder **Ressourcengruppen** im linken Navigationsbereich gesucht und verwaltet werden.

Denken Sie bei Verwendung eines kostenlosen Diensts an die Beschränkung auf maximal drei Indizes, Indexer und Datenquellen. Sie können einzelne Elemente über das Portal löschen, um unter dem Limit zu bleiben. 

## <a name="next-steps"></a>Nächste Schritte

Die Demo-App eignet sich für das Erstellen von Prototypen, da Sie eine Endbenutzerumgebung simulieren können, ohne JavaScript- oder Front-End-Code schreiben zu müssen. Wenn Sie weitere Informationen zu Front-End-Funktionen benötigen, beginnen Sie mit der Facettennavigation:

> [!div class="nextstepaction"]
> [Erstellen eines Facettenfilters in der kognitiven Azure-Suche](search-filters-facets.md)