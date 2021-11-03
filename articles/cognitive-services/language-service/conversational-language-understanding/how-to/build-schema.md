---
title: Erstellen eines Projektschemas für Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Verwenden Sie diesen Artikel, um mit dem Erstellen eines Projektschemas für Conversational Language Understanding zu beginnen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 0a140457e67c24cc363d7c660f641a2c98a25939
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101796"
---
# <a name="how-to-build-your-project-schema"></a>Erstellen Ihres Projektschemas
 
In Conversational Language Understanding ist das *Schema* als die Kombination der Absichten und Entitäten definiert, die in Ihrem Projekt enthalten sind. Der Schemaentwurf ist ein wichtiger Baustein für den Erfolg Ihres Projekts. Beim Erstellen eines Schemas sollten Sie überlegen, welche Absichten und Entitäten in Ihr Projekt aufgenommen werden sollen.

## <a name="guidelines-and-recommendations"></a>Richtlinien und Empfehlungen

Beachten Sie bei der Auswahl von Absichten für Ihr Projekt die folgenden Richtlinien:

  1. Erstellen Sie distinkte, deutlich unterscheidbare Absichten. Eine Absicht wird am besten als die gewünschte Gesamtaktion eines Benutzers beschrieben. Stellen Sie sich das Projekt vor, das Sie erstellen, und bestimmen Sie alle verschiedenen Aktionen, die Benutzer bei der Interaktion mit Ihrem Projekt ausführen können. Senden, Aufrufen und Abbrechen sind alles Aktionen, die am besten als unterschiedliche Absichten dargestellt werden. „Stornieren einer Bestellung“ und „Stornieren eines Termins“ sind sehr ähnlich, der Unterschied besteht darin, *was* storniert wird. Diese beiden Aktionen sollten unter der gleichen Absicht dargestellt werden.
  
  2. Erstellen Sie Entitäten, um relevante Informationen in Ihrem Text zu extrahieren. Die Entitäten sollten verwendet werden, um die relevanten Informationen zu erfassen, die zum Erfüllen der Aktion Ihres Benutzers erforderlich sind. Beispielsweise können *Bestellung* oder *Termin* verschiedene Dinge sein, die ein Benutzer zu stornieren versucht, und Sie sollten eine Entität erstellen, um diese Information zu erfassen.

Sie können eine *Nachricht* *„senden“* , eine *E-Mail* *„senden“* oder ein Paket *„senden“* . Das Erfassen dieser Anforderungen durch Erstellen von Absichten schließt eine Skalierung im Lauf der Zeit aus, daher sollten Sie Entitäten verwenden, um zu identifizieren, *was* der Benutzer sendet. Die Kombination aus Absichten und Entitäten sollte Ihren Unterhaltungsfluss bestimmen. 

Stellen Sie sich beispielsweise ein Unternehmen vor, in dem die Botentwickler die drei häufigsten Aktionen identifiziert haben, die Benutzer bei der Verwendung eines Kalenders ausführen: 

* Einrichten neuer Besprechungen 
* Antworten auf Besprechungsanfragen 
* Stornieren von Besprechungen 

Sie können eine Absicht erstellen, um jede dieser Aktionen darzustellen. Sie können außerdem Entitäten einschließen, um die Vervollständigung dieser Aktionen zu unterstützen, beispielsweise:

* Besprechungsteilnehmer
* Datum
* Besprechungsdauer


Für **Orchestrierungsworkflow**-Projekte können nur Absichten erstellt werden. Das Orchestrierungsworkflow-Projekt dient zum Weiterleiten an andere Zieldienste, die möglicherweise über Entitätsextraktion verfügen, um den Unterhaltungsfluss zu vervollständigen. Sie können neue Absichten hinzufügen, die mit anderen Diensten verbunden sind, _oder_ Absichten erstellen, die nicht mit einem Dienst verbunden sind (eine nicht verbundene Absicht). 

Indem Sie eine nicht verbundene Absicht hinzufügen, ermöglichen Sie dem Orchestrator die Weiterleitung an diese Absicht und die Rückgabe, ohne einen zusätzlichen Dienst aufrufen zu müssen. Sie müssen Trainingsbeispiele für nicht verbundene Absichten zur Verfügung stellen. Sie können nur Verbindungen mit Projekten herstellen, die sich im Besitz der gleichen Azure-Ressource befinden. 

Um das Beispiel von vorhin fortzusetzen: Die Entwickler eines Bots stellen möglicherweise fest, dass sie für jede Fähigkeit ihres Bots (dazu zählen: Kalenderaktionen, E-Mail-Aktionen und häufig gestellte Fragen in einem Unternehmen) eine Absicht benötigen, die eine Verbindung mit jeder dieser Fähigkeiten herstellt.  

## <a name="build-project-schema-for-conversation-projects"></a>Erstellen eines Projektschemas für Unterhaltungsprojekte

So erstellen Sie ein Projektschema für Unterhaltungsprojekte:

1. Wählen Sie auf der Seite zum Erstellen des Schemas die Registerkarte **Absichten** oder **Entitäten** und dann **Hinzufügen** aus. Sie werden zur Eingabe eines Namens aufgefordert, bevor Sie die Erstellung der Absicht oder Entität abschließen können. 

2. Wenn Sie auf eine Absicht klicken, werden Sie zur Seite [Tag-Äußerungen](tag-utterances.md) weitergeleitet, auf der Sie Beispiele für Absichten hinzufügen und Beispielentitäten mit Bezeichnungen versehen können.

    :::image type="content" source="../media/build-schema-page.png" alt-text="Screenshot: Seite zur Schemaerstellung für Unterhaltungsprojekte in Language Studio." lightbox="../media/build-schema-page.png":::
    
3. Nach dem Erstellen einer Entität werden Sie zur Seite mit den Entitätsdetails weitergeleitet. Jede Komponente wird durch mehrere Komponenten definiert. Sie können Beispiele auf der Seite „Tag-Äußerungen“ bezeichnen, um eine gelernte Komponente zu trainieren, eine Liste von Werten hinzufügen, die mit der Listenkomponente abgeglichen werden soll, oder einen Satz vorgefertigter Komponenten aus der verfügbaren Bibliothek hinzufügen. Mehr zu Komponenten erfahren Sie [hier](../concepts/entity-components.md)

    :::image type="content" source="../media/entity-details.png" alt-text="Screenshot: Seite mit Entitätsdetails für Unterhaltungsprojekte in Language Studio." lightbox="../media/entity-details.png":::

## <a name="build-project-schema-for-orchestration-workflow-projects"></a>Erstellen eines Projektschemas für Orchestrierungsworkflow-Projekte

So erstellen Sie ein Projektschema für Orchestrierungsworkflow-Projekte: 

1. Wählen Sie auf der Seite **Schema erstellen** die Option **Hinzufügen** aus. Sie werden aufgefordert, einen Namen einzugeben und gegebenenfalls eine Verbindung für die Absicht zu definieren. Wenn Sie eine Verbindung für eine Absicht herstellen möchten, müssen Sie diese Angaben machen:
    1. **Diensttyp**: LUIS, Antworten auf benutzerdefinierte Fragen (Custom Question Answering, QnA) oder Conversational Language Understanding.
    2. **Projektname**: Das Projekt, mit dem die Absicht eine Verbindung herstellen soll.
    3. **Version für Äußerungen** (nur für LUIS): Welche LUIS-Version soll zum Trainieren des Orchestratorklassifizierungs-Modells verwendet werden?

    :::image type="content" source="../media/orchestration-intent.png" alt-text="Screenshot: Modales Dialogfeld zur Absichtserstellung für Orchestrierungsprojekte in Language Studio." lightbox="../media/orchestration-intent.png":::

> [!IMPORTANT]
> * Verbundene Absichten können nicht ausgewählt werden, da Sie einer verbundenen Absicht keine Trainingsbeispiele hinzufügen können, denn diese verwendet bereits die Daten des Zielprojekts, um ihre Absichtsklassifizierung zu trainieren.
> * Sie können nur Verbindungen mit Zieldiensten herstellen, die sich im Besitz derselben Ressource befinden.

## <a name="next-steps"></a>Nächste Schritte
* [Tag-Äußerungen](tag-utterances.md)
