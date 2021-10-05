---
title: Erstellen von Auslösern für benutzerdefinierte Ereignisse in Azure Data Factory
description: Hier erfahren Sie, wie in Azure Data Factory ein Auslöser erstellt wird, der eine Pipeline als Antwort auf ein in Event Grid veröffentlichtes Ereignis ausführt.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: dcad59c6434f6751cba8633868a9dc9969ffc0ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610534"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory"></a>Erstellen eines benutzerdefinierten Ereignisauslösers zum Ausführen einer Pipeline in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die ereignisgesteuerte Architektur (EDA) ist ein allgemeines Datenintegrationsmuster, das die Produktion, Erkennung, Verbrauch und Reaktion auf Ereignisse beinhaltet. In Datenintegrationsszenarien müssen Azure Data Factory-Kunden häufig Pipelines bei Eintritt bestimmter Ereignisse auslösen. Data Factory native Integration mit [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) umfasst jetzt [benutzerdefinierte Themen](../event-grid/custom-topics.md). Sie senden Ereignisse an ein Event Grid-Thema. Data Factory abonniert das Thema, überwacht es und löst dann die Pipelines entsprechend aus.

> [!NOTE]
> Die in diesem Artikel beschriebene Integration basiert auf [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Stellen Sie sicher, dass Ihr Abonnement für den Event Grid-Ressourcenanbieter registriert ist. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Sie müssen in der Lage sein, die Aktion `Microsoft.EventGrid/eventSubscriptions/` auszuführen. Diese Aktion ist Teil der integrierten [EventGrid-EventSubscription-Mitwirkender](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) Rolle.

Wenn Sie Pipelineparameter und einen benutzerdefinierten Ereignisauslöser kombinieren, können Sie benutzerdefinierte `data` Nutzlasten in Pipelineausführungen analysieren und darauf verweisen. Da das `data` Feld in einer benutzerdefinierten Ereignisnutzlast eine Freiform-JSON-Schlüssel-Wert-Struktur ist, können Sie ereignisgesteuerte Pipelineausführungen steuern.

> [!IMPORTANT]
> Wenn ein Schlüssel, auf den in der Parametrisierung verwiesen wird, in der benutzerdefinierten Ereignisnutzlast fehlt, `trigger run` tritt ein Fehler auf. Sie erhalten einen Fehler, der besagt, dass der Ausdruck nicht ausgewertet werden kann, da die Eigenschaft `keyName` nicht vorhanden ist. In diesem Fall wird **nein** `pipeline run` durch das Ereignis ausgelöst.

## <a name="set-up-a-custom-topic-in-event-grid"></a>Einrichten eines benutzerdefinierten Themas in Event Grid

Zum Verwenden eines Auslösers für ein benutzerdefiniertes Ereignis in Data Factory müssen Sie *zunächst* ein [benutzerdefiniertes Thema in Event Grid](../event-grid/custom-topics.md) einrichten.

Wechseln Sie zu Azure Event Grid und erstellen Sie das Thema selbst. Weitere Informationen zum Erstellen des benutzerdefinierten Themas finden Sie in den [Portal-Tutorials](../event-grid/custom-topics.md#azure-portal-tutorials) und den [CLI-Tutorials](../event-grid/custom-topics.md#azure-cli-tutorials) für Azure Event Grid.

> [!NOTE]
> Der Workflow unterscheidet sich vom Speicher-Ereignisauslöser. Hier richtet Data Factory das Thema nicht für Sie ein.

Data Factory erwartet, dass die Ereignisse dem [Event Grid-Ereignisschema](../event-grid/event-schema.md) folgen. Stellen Sie sicher, dass die Ereignisnutzlasten über folgende Felder verfügen:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>Verwenden von Data Factory zum Erstellen eines benutzerdefinierten Ereignisauslösers

1. Wechseln Sie zu Azure Data Factory und melden Sie sich an.

1. Wechseln Sie zur Registerkarte **Bearbeiten**. Suchen Sie nach dem Stiftsymbol.

1. Klicken Sie im Menü auf **Auslöser** und dann auf **Neu/Bearbeiten**.

1. Klicken Sie auf der Seite **Auslöser hinzufügen** auf **Auslöser auswählen** und dann auf **+Neu**.

1. Wählen Sie **Benutzerdefinierte Ereignisse** für **Typ** aus.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Screenshot der Seite „Autor“ zum Erstellen eines neuen Auslösers für benutzerdefinierte Ereignisse über die Data Factory-Benutzeroberfläche" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Wählen Sie in der Dropdownliste „Azure-Abonnement“ das benutzerdefinierte Thema aus, oder geben Sie den Bereich für das Ereignisthema manuell ein.

   > [!NOTE]
   > Zum Erstellen oder Ändern eines benutzerdefinierten Ereignisauslösers in Data Factory müssen Sie ein Azure-Konto mit der entsprechenden rollenbasierten Zugriffssteuerung (Azure RBAC) verwenden. Zusätzliche Berechtigungen sind nicht erforderlich. Das Prinzip des Data Factory Diensts erfordert *keine* besondere Berechtigung für Ihr Event Grid. Weitere Informationen zur Zugriffssteuerung finden Sie im Abschnitt [Rollenbasierte Zugriffssteuerung](#role-based-access-control).

1. Mit den Eigenschaften **Betreff beginnt mit** und **Betreff endet mit** können Sie Auslöseereignisse herausfiltern. Beide Eigenschaften sind optional.

1. Verwenden Sie **+ Neu**, um **Ereignistypen** hinzuzufügen, die Sie herausfiltern möchten. Die Liste der benutzerdefinierten Ereignisauslöser verwendet eine OR-Beziehung. Wenn ein benutzerdefiniertes Ereignis mit einer `eventType` Eigenschaft vorhanden ist, die einem in der Liste entspricht, wird eine Pipelineausführung ausgelöst. Beim Ereignistyp wird die Groß-/Kleinschreibung nicht beachtet. Im folgenden Screenshot entspricht der Auslöser beispielsweise allen `copycompleted` oder `copysucceeded` Ereignissen, die über ein Betreff verfügen, das mit *Factories* beginnt.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Screenshot der Seite „Trigger bearbeiten“ zum Erläutern des Filterns nach Ereignistypen und Themen auf der Data Factory Benutzeroberfläche":::

1. Der Auslöser für benutzerdefinierte Ereignisse kann die benutzerdefinierte `data` Nutzlast analysieren und an ihre Pipeline senden. Erstellen Sie die Pipelineparameter und geben Sie die Werte auf der Seite **Parameter** ein. Verwenden Sie das Format `@triggerBody().event.data._keyName_` zum Analysieren der Datennutzlast, und übergeben Sie die Werte an die Pipelineparameter.
 
   Eine ausführliche Erläuterung finden Sie in den folgenden Artikeln:
   - [Verweisen auf Auslösermetadaten in Pipelines](how-to-use-trigger-parameterization.md)
   - [Systemvariablen im benutzerdefinierten Ereignisauslöser](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Screenshot der Einstellungen „Pipelineparameter“.":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Screenshot der Seite „Parameter“ zum Verweisen auf die Datennutzlast in einem benutzerdefinierten Ereignis.":::

1. Wählen Sie nach Eingabe der Parameter **OK** aus.

## <a name="advanced-filtering"></a>Erweiterte Filterung

Benutzerdefinierte Ereignisauslöser unterstützen erweiterte Filterfunktionen, ähnlich wie [Event Grid: Erweiterte Filterung](../event-grid/event-filtering.md#advanced-filtering). Mit diesen bedingten Filtern können Pipelines basierend auf den _Werten_ der Ereignisnutzdaten ausgelöst werden. Sie können z. B. ein Feld namens _Department_ in den Ereignisnutzdaten verwenden, und die Pipeline sollte nur ausgelöst werden, wenn _Department_ (Abteilung) gleich _Finance_ (Finanzen) ist. Sie können auch eine komplexe Logik angeben. Beispiel: _date_ field in list [1, 2, 3, 4, 5] (Feld „Datum“ in Liste [1, 2, 3, 4, 5]), _month_ field __not__ in list [11, 12] (Feld „Monat“ nicht in Liste [11, 12]), _tag_ field contains any of ['Fiscal Year 2021', 'FiscalYear2021', 'FY2021'] (Feld „Tag“ enthält eine der folgenden Angaben ['Geschäftsjahr 2021', 'GeschäftsJahr2021', 'GJ2021']).

 :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-5-advanced-filters.png" alt-text="Screenshot: Festlegen erweiterter Filter für benutzerdefinierte Ereignisauslöser":::

Ab heute unterstützt der benutzerdefinierte Ereignisauslöser eine __Teilmenge__ der [erweiterten Filteroperatoren](../event-grid/event-filtering.md#advanced-filtering) in Event Grid. Die folgenden Filterbedingungen werden unterstützt:

* NumberIn
* NumberNotIn
* NumberLessThan
* NumberGreaterThan
* NumberLessThanOrEquals
* NumberGreaterThanOrEquals
* BoolEquals
* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Klicken Sie auf **+Neu**, um neue Filterbedingungen hinzuzufügen. 

Darüber hinaus gelten für benutzerdefinierte Ereignisauslöser [dieselben Einschränkungen wie für Event Grid](../event-grid/event-filtering.md#limitations), einschließlich:

* 5 erweiterte Filter und 25 Filterwerte für alle Filter pro benutzerdefiniertem Ereignisauslöser
* 512 Zeichen pro Zeichenfolgenwert
* Fünf Werte für die Operatoren „in“ und „not in“
* Schlüssel dürfen keine `.`-Zeichen (Punkt) enthalten, z. B. `john.doe@contoso.com`. Derzeit gibt es keine Unterstützung für Escapezeichen in Schlüsseln.
* Der gleiche Schlüssel kann in mehr als einem Filter verwendet werden.

Data Factory basiert auf der neuesten _GA_-Version (allgemeine Verfügbarkeit) der Event Grid-[API](../event-grid/whats-new.md). Sobald neue API-Versionen die GA-Phase erreicht haben, wird Data Factory seine Unterstützung für fortgeschrittene Filteroperatoren erweitern.

## <a name="json-schema"></a>JSON-Schema

Die folgende Tabelle bietet eine Übersicht über die Schemaelemente im Zusammenhang mit Auslösern für benutzerdefinierte Ereignisse:

| JSON-Element | BESCHREIBUNG | type | Zulässige Werte | Erforderlich |
|---|----------------------------|---|---|---|
| `scope` | Die Azure Resource Manager-Ressourcen-ID des Event Grid-Themas. | String | Azure Resource Manager-ID | Ja |
| `events` | Der Ereignistyp, die diesen Trigger auslöst. | Array von Zeichenfolgen    |  | Ja, mindestens ein Wert wird erwartet. |
| `subjectBeginsWith` | Das `subject` Feld muss mit dem angegebenen Muster beginnen, damit der Trigger ausgelöst wird. Beispielsweise löst _factories_ nur den Trigger für Ereignisbetreffe aus, die mit *factories* beginnen. | String   | | Nein |
| `subjectEndsWith` | Das `subject` Feld muss mit dem angegebenen Muster beginnen, damit der Trigger ausgelöst wird. | String   | | Nein |
| `advancedFilters` | Liste der JSON-Blobs, die jeweils eine Filterbedingung angeben. Jedes Blob gibt `key`, `operatorType` und `values` an. | Liste der JSON-Blobs | | Nein |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Azure Data Factory verwendet die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure, um nicht autorisierten Zugriff zu verhindern. Um ordnungsgemäß zu funktionieren, erfordert Data Factory Zugriff auf:
- Ereignisse überwachen
- Abonnieren Sie Updates von Ereignissen.
- Lösen Sie Pipelines aus, die mit benutzerdefinierten Ereignissen verknüpft sind.

Um einen benutzerdefinierten Ereignisauslöser erfolgreich zu erstellen oder zu aktualisieren, müssen Sie sich bei Data Factory mit einem Azure-Konto anmelden, das über den entsprechenden Zugriff verfügt. Andernfalls meldet die Datensammlung den Fehler, dass der _Zugriff verweigert_ wurde.

Data Factory erfordert keine besondere Berechtigung für Ihr Event Grid. Sie müssen dem Data Factory-Dienstprinzipal für den Vorgang auch *keine* spezielle Azure RBAC-Berechtigung zuweisen.

Insbesondere benötigen Sie die `Microsoft.EventGrid/EventSubscriptions/Write` Berechtigung für `/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics`.

## <a name="next-steps"></a>Nächste Schritte

* Hier erhalten Sie ausführliche Informationen zur [Auslöserausführung.](concepts-pipeline-execution-triggers.md#trigger-execution)
* Hier erfahren Sie, wie Sie auf [Auslösermetadaten in Pipelineausführungen verweisen](how-to-use-trigger-parameterization.md).
