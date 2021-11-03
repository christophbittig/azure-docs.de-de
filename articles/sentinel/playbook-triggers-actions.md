---
title: Verwenden von Triggern und Aktionen in Azure Sentinel-Playbooks | Microsoft-Dokumentation
description: Erfahren Sie genauer, wie Sie Ihren Playbooks Zugriff auf die Informationen in Ihren Azure Sentinel-Warnungen und -Incidents geben und diese Informationen nutzen können, um Abhilfemaßnahmen zu ergreifen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 093db947b30444b4d7c3614126c83977fd45e3de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083952"
---
# <a name="use-triggers-and-actions-in-azure-sentinel-playbooks"></a>Verwenden von Triggern und Aktionen in Azure Sentinel-Playbooks

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Dokument werden die Arten von Triggern und Aktionen im [Azure Sentinel-Connector für Logic Apps](/connectors/azuresentinel/) erläutert, die Playbooks zur Interaktion mit Azure Sentinel und den Informationen in den Tabellen Ihres Arbeitsbereichs verwenden können. Außerdem wird Ihnen gezeigt, wie Sie zu bestimmten Arten von Azure Sentinel-Informationen gelangen, die Sie voraussichtlich benötigen werden.

Dieses Dokument ist zusammen mit unserem Leitfaden zum [Authentifizieren von Playbooks in Azure Sentinel](authenticate-playbooks-to-sentinel.md) eine Ergänzung zu unserer anderen Playbookdokumentation: [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Azure Sentinel](tutorial-respond-threats-playbook.md). In diesen drei Dokumenten wird immer wieder aufeinander verwiesen.

Eine Einführung in Playbooks finden Sie unter [Automatisieren der Bedrohungsabwehr mit Playbooks in Azure Sentinel](automate-responses-with-playbooks.md).

Die vollständige Spezifikation des Azure Sentinel-Connectors finden Sie in der [Dokumentation zu Logic Apps-Connectors](/connectors/azuresentinel/).

## <a name="permissions-required"></a>Erforderliche Berechtigungen

| Rollen/Connectorkomponenten | Auslöser | „Get“-Aktionen | Incident aktualisieren,<br>Kommentar hinzufügen |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Azure Sentinel-Leser](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Azure Sentinel [Antwortdienst](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[Mitwirkender](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Erfahren Sie mehr zu Berechtigungen in Azure Sentinel](./roles.md).

## <a name="azure-sentinel-triggers-summary"></a>Übersicht über Azure Sentinel-Trigger

Obwohl der Azure Sentinel-Connector auf vielfältige Weise verwendet werden kann, lassen sich die Komponenten des Connectors in zwei Flows unterteilen, die jeweils durch ein anderes Azure Sentinel-Ereignis ausgelöst werden:

| Trigger | Vollständiger Triggername in<br>Designer für Logik-Apps | Einsatzgebiete | Bekannte Einschränkungen 
| --------- | ------------ | -------------- | -------------- | 
| **Incidenttrigger** | „Wenn die Azure Sentinel-Incidenterstellungsregel ausgelöst wird (Vorschau)“ | Wird für die meisten Szenarien zur Incidentautomatisierung empfohlen.<br><br>Das Playbook empfängt Incidentobjekte, einschließlich Entitäten und Warnungen. Mit diesem Trigger kann das Playbook an eine **Automatisierungsregel** angefügt werden, damit es ausgelöst werden kann, wenn ein Incident in Azure Sentinel erstellt wird. So können alle [Vorteile von Automatisierungsregeln](./automate-incident-handling-with-automation-rules.md) auf den Incident angewendet werden. | Playbooks mit diesem Trigger können nicht manuell in Azure Sentinel ausgeführt werden.<br><br>Playbooks mit diesem Trigger unterstützen keine Gruppierung von Warnungen, d. h., sie empfangen nur die erste Warnung, die bei jedem Incident gesendet wird.
| **Warnungstrigger** | „Wenn eine Reaktion auf eine Azure Sentinel-Warnung ausgelöst wird“ | Empfehlenswert für Playbooks, die manuell bei Warnungen aus dem Azure Sentinel-Portal ausgeführt werden müssen, oder für **geplante** Analyseregeln, die keine Incidents für ihre Warnungen generieren. | Dieser Trigger kann nicht verwendet werden, um Reaktionen auf Warnungen zu automatisieren, die von **Microsoft-Sicherheitsanalyseregeln** generiert werden.<br><br>Playbooks, die diesen Trigger verwenden, können nicht von **Automatisierungsregeln aufgerufen werden**. |
|

Die von diesen beiden Flows verwendeten Schemas sind nicht identisch.
Es wird empfohlen, den Flow **Azure Sentinel-Incidenttrigger** zu verwenden, der für die meisten Szenarien geeignet ist.

### <a name="incident-dynamic-fields"></a>Dynamische Felder für Incidents

Das Objekt **Incident**, das von **Wenn die Azure Sentinel-Incidenterstellungsregel ausgelöst wird** empfangen wurde, enthält die folgenden dynamischen Felder:

- Incidenteigenschaften (als „Incident: Feldname“ angezeigt)

- Warnungen (Array)

  - Warnungseigenschaften (als „Warnung: Feldname“ angezeigt)

    Wenn Sie eine Warnungseigenschaft wie **Warnung: \<property name>** auswählen, wird automatisch eine *for each*-Schleife generiert, da ein Incident mehrere Warnungen enthalten kann.

- Entitäten (Array aller Entitäten einer Warnung)

- Felder für Arbeitsbereichsinformationen (gilt für den Sentinel-Arbeitsbereich, in dem der Incident erstellt wurde)
  - Abonnement-ID
  - Arbeitsbereichname
  - Arbeitsbereichs-ID
  - Ressourcengruppenname

## <a name="azure-sentinel-actions-summary"></a>Übersicht über Azure Sentinel-Aktionen

| Komponente | Einsatzgebiete |
| --------- | -------------- |
| **Warnung: Incident abrufen** | In Playbooks, die mit dem Warnungstrigger beginnen. Nützlich für das Abrufen der Incidenteigenschaften oder der **Incident-ARM-ID** zur Verwendung mit den Aktionen **Incident aktualisieren** oder **Kommentar zu Incident hinzufügen**. |
| **Incident abrufen** | Wenn ein Playbook über eine externe Quelle oder mit einem Nicht-Sentinel-Trigger ausgelöst wird. Identifizieren Sie es anhand einer **Incident-ARM-ID**. Ruft die Incidenteigenschaften und Kommentare ab. |
| **Incident aktualisieren** | Um den **Status** eines Incidents zu ändern (z. B. beim Schließen des Incidents), einen **Besitzer** zuzuweisen, ein Tag hinzuzufügen oder zu entfernen oder **Schweregrad**, **Titel** oder **Beschreibung** zu ändern.
| **Kommentare zu Incidents hinzufügen** | Zum Anreichern des Incidents mit aus externen Quellen gesammelten Informationen; zum Überprüfen der vom Playbook auf die Entitäten angewendeten Maßnahmen; zur Bereitstellung zusätzlicher Informationen, die für die Untersuchung des Incidents nützlich sind. |
| **Entitäten – Abrufen\<*entity type*\>** | In Playbooks, die mit einem bestimmten Entitätstyp (**IP**, **Konto**, **Host**, **URL** oder **FileHash**) arbeiten, der zum Zeitpunkt der Erstellung des Playbooks bekannt ist, müssen Sie in der Lage sein, ihn zu analysieren und mit seinen eindeutigen Feldern zu arbeiten. |
|

## <a name="work-with-incidents---usage-examples"></a>Arbeiten mit Incidents: Verwendungsbeispiele

> [!TIP] 
> Die Aktionen **Incident aktualisieren** und **Kommentar zu Incident hinzufügen** erfordern die **Incident-ARM-ID**. <br>
Verwenden Sie zuvor die Aktion **Warnung: Incident abrufen**, um die **Incident-ARM-ID** zu erhalten.

### <a name="update-an-incident"></a>Aktualisieren eines Incidents
-  Playbook wird ausgelöst, **wenn ein Incident erstellt wird**

    ![Incidenttrigger: einfaches Beispiel für „Flow aktualisieren“](media/playbook-triggers-actions/incident-simple-flow.png)

-  Playbook wird ausgelöst, **wenn eine Warnung generiert wird**

    ![Warnungstrigger: einfaches Beispiel für „Incidentflow aktualisieren“](media/playbook-triggers-actions/alert-update-flow.png)
      
### <a name="use-incident-information"></a>Verwenden von Incidentinformationen

Einfaches Playbook zum Senden von Incidentdetails per E-Mail:
-  Playbook wird ausgelöst, **wenn ein Incident erstellt wird**

    ![Incidenttrigger: einfaches Beispiel für „Flow abrufen“](media/playbook-triggers-actions/incident-simple-mail-flow.png)

-  Playbook wird ausgelöst, **wenn eine Warnung generiert wird**

    ![Warnungstrigger: einfaches Beispiel für „Incidentflow abrufen“](media/playbook-triggers-actions/alert-simple-mail-flow.png)

### <a name="add-a-comment-to-the-incident"></a>Hinzufügen eines Kommentars zum Incident

-  Playbook wird ausgelöst, **wenn ein Incident erstellt wird**

    ![Incidenttrigger: einfaches Beispiel für „Kommentar hinzufügen“](media/playbook-triggers-actions/incident-comment.png)

-  Playbook wird ausgelöst, **wenn eine Warnung generiert wird**

    ![Warnungstrigger: einfaches Beispiel für „Kommentar hinzufügen“](media/playbook-triggers-actions/alert-comment.png)

## <a name="work-with-specific-entity-types"></a>Arbeiten mit bestimmten Entitätstypen

Das dynamische Feld **Entitäten** ist ein Array von JSON-Objekten, von denen jedes eine Entität darstellt. Jede Entität hat abhängig von ihren eindeutigen Eigenschaften ihr eigenes Schema.

Die Aktion **Entitäten – Abrufen\<entity name>** können ermöglicht Ihnen Folgendes:

- Filtern des Arrays von Entitäten nach dem angeforderten Typ.
- Analysieren der spezifischen Felder dieses Typs, damit sie in weiteren Aktionen als dynamische Felder verwendet werden können.

Die Eingabe erfolgt in das dynamische Feld **Entitäten**.

Die Antwort ist ein Array von Entitäten, in dem die speziellen Eigenschaften analysiert werden und direkt in einer *For each*-Schleife verwendet werden können.

Die folgenden Entitätstypen werden derzeit unterstützt:

- [IP](/connectors/azuresentinel/#entities---get-ips)
- [Host](/connectors/azuresentinel/#entities---get-hosts)
- [Konto](/connectors/azuresentinel/#entities---get-accounts)
- [URL](/connectors/azuresentinel/#entities---get-urls)
- [Dateihash](/connectors/azuresentinel/#entities---get-filehashes)

    :::image type="content" source="media/playbook-triggers-actions/entities-actions.png" alt-text="Entitätsaktionsliste":::

Für andere Entitätstypen kann eine ähnliche Funktionalität mit den integrierten Aktionen von Logic Apps erreicht werden:

- Filtern des Arrays von Entitäten nach dem angeforderten Typ mithilfe von [**Array filtern**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action).

- Analysieren der spezifischen Felder dieses Typs, damit sie in weiteren Aktionen als dynamische Felder verwendet werden können, mithilfe von [**JSON analysieren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="work-with-custom-details"></a>Arbeiten mit benutzerdefinierten Details

Das dynamische Feld **Benutzerdefinierte Warnungsdetails**, das im **Incidenttrigger** verfügbar ist, ist ein Array von JSON-Objekten, von denen jedes ein benutzerdefiniertes Detail einer Warnung darstellt. [Bei benutzerdefinierten Details](surface-custom-details-in-alerts.md) handelt es sich um Schlüssel-Wert-Paare, die es Ihnen ermöglichen, Informationen aus Ereignissen in der Warnung sichtbar zu machen, damit sie als Teil des Incidents dargestellt, nachverfolgt und analysiert werden können.

Da dieses Feld in der Warnung anpassbar ist, hängt das Schema vom Typ des sichtbar gemachten Ereignisses ab. Sie müssen Daten aus einer Instanz dieses Ereignisses bereitstellen, um das Schema zu generieren, das bestimmt, wie das benutzerdefinierte Detailfeld analysiert werden soll.

Sehen Sie sich folgendes Beispiel an:

![In der Analyseregel definierte benutzerdefinierte Details.](./media/playbook-triggers-actions/custom-details-values.png)

Bei diesen Schlüssel-Wert-Paaren steht der Schlüssel (die linke Spalte) für die von Ihnen erstellten benutzerdefinierten Felder und der Wert (die rechte Spalte) für die Felder aus den Ereignisdaten, die die benutzerdefinierten Felder auffüllen.

Sie können den folgenden JSON-Code zum Generieren des Schemas bereitstellen. Der Code zeigt die Schlüsselnamen als Arrays und die Werte (die tatsächlichen Werte, nicht die Spalte, die die Werte enthält) als Elemente in den Arrays.

```json
{ "FirstCustomField": [ "1", "2" ], "SecondCustomField": [ "a", "b" ] }
```

1. Fügen Sie einen neuen Schritt mit der integrierten Aktion **JSON analysieren** hinzu. Sie können „JSON analysieren“ in das Suchfeld eingeben, um sie zu finden.

1. Suchen Sie **Benutzerdefinierte Warnungsdetails** in der Liste **Dynamischer Inhalt** unter dem Incidenttrigger, und wählen Sie sie aus.

    ![Wählen Sie unter „Dynamischer Inhalt“ die Option „Benutzerdefinierte Warnungsdetails“ aus.](./media/playbook-triggers-actions/custom-details-dynamic-field.png)

    Dadurch wird eine **For each-Schleife** erstellt, da ein Incident ein Array mit Warnungen enthält.

1. Klicken Sie auf den Link **Beispielnutzdaten zum Generieren eines Schemas verwenden**.

    ![Wählen Sie den Link „Beispielnutzdaten zum Generieren eines Schemas verwenden“ aus](./media/playbook-triggers-actions/generate-schema-link.png)

1. Geben Sie Beispielnutzdaten an. Ein Beispiel für die Nutzdaten finden Sie, wenn Sie in Log Analytics (Blatt **Protokolle**) nach einer anderen Instanz dieser Warnung suchen und das benutzerdefinierte Detailobjekt (unter **Erweiterte Eigenschaften**) kopieren. Im folgenden Screenshot haben wir den oben gezeigten JSON-Code verwendet.

    ![Geben Sie JSON-Beispielnutzdaten ein.](./media/playbook-triggers-actions/sample-payload.png)

1. Die benutzerdefinierten Felder sind bereit, dynamische Felder des Typs **Array** zu verwenden. Sie sehen hier das Array und seine Elemente, sowohl im Schema als auch in der Liste, die unter **Dynamischer Inhalt** angezeigt wird, die wir oben beschrieben haben.

    ![Felder aus dem Schema, die verwendet werden können.](./media/playbook-triggers-actions/fields-ready-to-use.png)
    
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über die Verwendung von Triggern und Aktionen in Azure Sentinel-Playbooks erfahren, um auf Bedrohungen zu reagieren. 
- Im [nächsten Artikel](hunting.md) erfahren Sie, wie Sie mithilfe von Azure Sentinel proaktiv nach Bedrohungen suchen.
