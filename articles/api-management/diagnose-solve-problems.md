---
title: 'Azure API Management: Diagnostizieren und Lösen von Problemen'
description: Erfahren Sie, wie Sie mit dem Tool „Diagnose und Problembehandlung“ im Azure-Portal Probleme bei der API in Azure API Management behandeln.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: 83fbb17baf63d51b9f7ef29d369924294aa7c262
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447472"
---
# <a name="azure-api-management-diagnostics-overview"></a>Übersicht über Azure API Management Diagnostics

Wenn Sie eine API in Azure API Management erstellen und verwalten, sollten Sie auf mögliche Probleme vorbereitet sein, vom Fehler „404: Nicht gefunden“ bis zum Fehler „502: Ungültiges Gateway“. API Management Diagnostics ist eine intelligente und interaktive Komponente, mit der Sie Probleme bei der in APIM (API Management) veröffentlichten API beheben können, ohne dass eine Konfiguration erforderlich ist. Wenn bei den veröffentlichten APIs Probleme auftreten, zeigt API Management Diagnostics den Fehler auf und führt Sie zu den richtigen Informationen, damit Sie das Problem schnell lösen können.

Diese Komponente ist besonders hilfreich, wenn innerhalb der letzten 24 Stunden Probleme bei der API aufgetreten sind, jedoch stehen Ihnen alle Diagnosediagramme jederzeit für die Analyse zur Verfügung.

[!NOTE] Das Diagnostizieren und Lösen von Problemen wird für Verbrauchsebenen derzeit nicht unterstützt.

## <a name="open-api-management-diagnostics"></a>Öffnen von API Management Diagnostics

Um auf API Management Diagnostics zuzugreifen, navigieren Sie im [Azure-Portal](https://portal.azure.com) zum API Management-Dienst. Wählen Sie im linken Navigationsbereich **Diagnose und Problembehandlung** aus.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Screenshot: Navigieren zu API Management Diagnostics":::



## <a name="intelligent-search"></a>Intelligente Suche

Sie können in der Suchleiste oben auf der Seite nach Problemen suchen. Mit der Suchfunktion können Sie auch die Tools zum Behandeln oder Lösen der Probleme suchen. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="Screenshot der intelligenten Suche":::


## <a name="troubleshooting-categories"></a>Kategorien der Problembehandlung

Sie können Probleme nach Kategorien behandeln. Häufige Probleme im Zusammenhang mit der API-Leistung, dem Gateway, mit API-Richtlinien und der Dienstebene können innerhalb der jeweiligen Kategorie analysiert werden. Jede Kategorie bietet zudem genauere Diagnosemöglichkeiten. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="Screenshot der Kategorienübersicht":::


### <a name="availability-and-performance"></a>Verfügbarkeit und Leistung

Unter dieser Kategorie können Sie Probleme im Hinblick auf die API-Verfügbarkeit und -Leistung untersuchen. Nach dem Auswählen dieser Kategoriekachel werden auf einer interaktiven Benutzeroberfläche einige allgemeine Überprüfungen angezeigt, die empfohlen werden. Klicken Sie auf jede Überprüfung, um das jeweilige Problem genauer zu untersuchen. Die Überprüfung bietet Ihnen außerdem ein Diagramm der API-Leistung und eine Zusammenfassung der Leistungsprobleme. Beispielsweise sind im API-Dienst möglicherweise innerhalb der letzten Stunden am Back-End ein 5xx-Fehler und ein Timeout aufgetreten. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Screenshot 1 der Überprüfung auf der interaktiven Benutzeroberfläche":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Screenshot 2 der Überprüfung auf der interaktiven Benutzeroberfläche":::

### <a name="api-policies"></a>API-Richtlinien

In dieser Kategorie werden Fehler erkannt, und Sie werden über die Richtlinienprobleme benachrichtigt. 

Eine ähnliche interaktive Benutzeroberfläche führt Sie zu den Datenmetriken, die Sie bei der Behandlung von Problemen bei der API-Richtlinienkonfiguration unterstützen.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="Screenshot der Kategoriekachel für API-Richtlinien":::

### <a name="gateway-performance"></a>Gatewayleistung 

Verwenden Sie für Gatewayanforderungen oder -antworten oder für 4xx- oder 5xx-Fehler im Gateway diese Kategorie zur Überwachung und Problembehandlung. Nutzen Sie die interaktive Benutzeroberfläche, um den Bereich genau zu untersuchen, den Sie im Hinblick auf die Leistung des API-Gateways überprüfen möchten. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="Screenshot der Kategoriekachel für die Gatewayleistung":::

### <a name="service-upgrade"></a>Dienstupgrade

In dieser Kategorie wird überprüft, welche Dienstebene (SKU) Sie zurzeit verwenden, und Sie werden zu einem Upgrade aufgefordert, um Probleme zu vermeiden, die möglicherweise mit dieser Ebene in Zusammenhang stehen. Die gleiche interaktive Benutzeroberfläche bietet Ihnen weitere Grafiken und eine Zusammenfassung der Überprüfungsergebnisse. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="Screenshot der Kategoriekachel für das Dienstupgrade":::

## <a name="search-documentation"></a>Dokumentation durchsuchen

Zusätzlich zu den Tools für die Diagnose und Problembehandlung können Sie nach Dokumenten für die Behandlung des Problems suchen. Nachdem Sie die Diagnose für den Dienst ausgeführt haben, wählen Sie auf der interaktiven Benutzeroberfläche die Option **Dokumentation durchsuchen** aus. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="Screenshot 1: Verwenden der Funktion „Dokumentation durchsuchen“":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="Screenshot 2: Verwenden der Funktion „Dokumentation durchsuchen“":::


## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie außerdem [API-Analysen](howto-use-analytics.md), um die Nutzung und Leistung der APIs zu analysieren. 
* Möchten Sie Probleme bei Web-Apps durch die App Service-Diagnose beheben? Das entsprechende Dokument finden Sie [hier](../app-service/overview-diagnostics.md).
* Nutzen Sie das Diagnosetool, um Probleme bei Azure Kubernetes Services zu überprüfen. Weitere Informationen finden Sie unter [Übersicht über die Azure Kubernetes Service-Diagnose (Vorschau)](../aks/concepts-diagnostics.md).
* Posten Sie Ihre Fragen oder Ihr Feedback auf [UserVoice](https://feedback.azure.com/d365community/forum/e808a70c-ff24-ec11-b6e6-000d3a4f0858), indem Sie dem Titel „[Diag]“ hinzufügen.
