---
title: Ausschließen von Elementen von der dynamischen Mitgliedschaft in Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie, wie Sie Elemente von der dynamischen Mitgliedschaft in Azure Virtual Network Manager ausschließen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 318a2eba29ee7641219e8c970c3dd3a51407ddf3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095388"
---
# <a name="excluding-elements-from-dynamic-membership-in-azure-virtual-network-manager-preview"></a>Ausschließen von Elementen von der dynamischen Mitgliedschaft in Azure Virtual Network Manager (Vorschau)

In diesem Artikel erfahren Sie, wie Sie mit bedingten Anweisungen virtuelle Netzwerke von der dynamischen Mitgliedschaft ausschließen. Sie erstellen diese bedingten Anweisungen mit dem Basiseditor, indem Sie Parameter und Operatoren aus einem Dropdown-Menü auswählen. Außerdem erfahren Sie, wie Sie mit dem erweiterten Editor bedingte Anweisungen einer vorhandenen Netzwerkgruppe aktualisieren.

> [!IMPORTANT]
> Der Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="parameters-and-operators"></a><a name="parameters"></a> Parameter und Operatoren

Virtuelle Netzwerke mit dynamischen Mitgliedschaften werden über bedingte Anweisungen ausgewählt. Sie können mehr als eine bedingte Anweisung definieren, indem Sie *logische Operatoren* wie **AND** und **OR** für Szenarien verwenden, in denen Sie die ausgewählten virtuellen Netzwerke weiter eingrenzen müssen. 

Liste der unterstützten Parameter:

| Parameter | Erweitertes Editorfeld |
| ---------- | ------------------------- |
| Name | `Name` |
| id | `Id` |
| `Tags`| `tag['tagName']` |
| Subscription Name | `[subscription().Name]` |
| Abonnement-ID | `[subscription().Id]` |
| Abo-Tags | `[subscription().tags['tagName']]` |
| Ressourcengruppenname | `[resourceGroup().Name]` |
| Ressourcengruppen-ID | `[resourceGroup().Id]` |
| Ressourcengruppen-Tags | `[resourceGroup().tags['tagName']]` |

Liste der unterstützten Operatoren:

| Operatoren | Erweiterter Editor |
| --------- | --------------- |
| Enthält | `"contains": <>` |
| Enthält nicht | `"notcontains": <>` |
| In | `"in": <>` |
| Not In | `"notin": <>` |
| Equals | `"equals": <>` |
| Ungleich | `"notequals": <>` |
| Enthält eines von | `"contains": <>` |
| Enthält alle | `"contains": <>` |
| Enthält keine von | `"notcontains": <>` |
| Exists | `"exists": true` |
| Existiert nicht | `"exists": false` |

> [!NOTE]
> Die Operatoren *Existiert* und *Existiert nicht* werden nur mit dem Parameter **Tags** verwendet.

## <a name="basic-editor"></a>Basiseditor

Angenommen, Sie haben die folgenden virtuellen Netzwerke in Ihrem Abonnement. Jedem virtuellen Netzwerk ist entweder ein Tag *Produktion* oder *Test* zugeordnet. Sie möchten nur virtuelle Netzwerke mit dem Tag Produktion auswählen und im Namen **VNet-A** enthalten.

* VNet-A-EastUS – *Produktion*
* VNet-A-WestUS - *Produktion*
* VNet-B-WestUS - *Test*
* VNet-C-WestUS - *Test*
* VNetA - *Produktion*
* VNetB - *Test*

Um Ihre bedingte Anweisung mit dem Basiseditor zu erstellen, müssen Sie eine neue Netzwerkgruppe erstellen.

1. Gehen Sie zu Ihrer Azure Virtual Network Manager-Instanz und wählen Sie **Netzwerkgruppen** unter *Einstellungen* aus. Wählen Sie dann **+ Hinzufügen**, um eine neue Netzwerkgruppe zu erstellen.

1. Geben Sie einen **Namen** für die Netzwerkgruppe ein. Wählen Sie dann die Registerkarte **Bedingte Anweisungen** aus.

1. Wählen Sie **Tags** aus dem Drop-down-Menü unter *Parameter* und dann **Existiert** aus dem Drop-down-Menü unter *Operator* aus.

1. Geben Sie dann  **Prod** als *Bedingung* ein und wählen Sie dann **Evaluieren** aus. In der Liste sollten nur VNet-A-EastUS, VNet-B-WestUS und VNetA angezeigt werden.

1. Fügen Sie eine weitere bedingte Anweisung hinzu, indem Sie den logischen Operator **AND** auswählen. Wählen Sie **Name** für den *Parameter* und **Enthält** für den *Operator*. Geben Sie **VNet-A** in das Feld *Bedingung* ein. Wählen Sie **Evaluieren** aus, um zu sehen, welches virtuelle Netzwerk in der Liste angezeigt wird. Sie sollten nur VNet-A-EastUS und VNet-A-WestUS sehen.

1. Wählen Sie **Überprüfen + Erstellen** und dann **Erstellen** aus, sobald die Validierung bestanden ist.

> [!NOTE] 
> Der **Basiseditor** ist nur während der Erstellung einer Netzwerkgruppe verfügbar. 

## <a name="advanced-editor"></a>Erweiterter Editor

Der erweiterte Editor kann verwendet werden, um beim Erstellen einer Netzwerkgruppe oder beim Aktualisieren einer bestehenden Netzwerkgruppe ein virtuelles Netzwerk auszuwählen. 

1. Wählen Sie die im vorherigen Abschnitt erstellte Netzwerkgruppe aus. Wählen Sie dann die Registerkarte **Bedingte Anweisungen** aus.

1. Sie sehen die bedingten Anweisungen für die Netzwerkgruppe in der erweiterten Editoransicht wie folgt:

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          }
       ]
    }
    ```

    Der Parameter `"allOf"` enthält beide bedingten Anweisungen, die durch den logischen Operator **AND** getrennt sind.

1. Um eine weitere bedingte Anweisung für ein *Name*-Feld hinzuzufügen, das *nicht*  **WestUS** enthält, geben Sie Folgendes in den erweiterten Editor ein:

    ```json
    {
       "allOf": [
          {
             "field": "tags['Environment']",
             "exists": true
          },
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "notcontains": "WestUS"
          }
       ]
    }
    ```

1. Wählen Sie dann **Evaluieren** aus. In der Liste sollte nur das virtuelle Netzwerk VNet-A-EastUS angezeigt werden.

1. Wählen Sie **Überprüfen + speichern** und dann **Speichern** aus, sobald die Validierung bestanden ist.

Eine vollständige Liste der Parameter und Operatoren, die Sie mit dem erweiterten Editor verwenden können, finden Sie unter [Parameter und Operatoren](#parameters). Siehe unten für weitere Beispiele:

## <a name="more-examples"></a>Weitere Beispiele

### <a name="example-1-or-operator-only"></a>Beispiel 1: Nur OR-Operator

In diesem Beispiel wird der logische Operator **OR** verwendet, um zwei bedingte Anweisungen zu trennen.

* Basiseditor:

    :::image type="content" source="./media/how-to-exclude-elements/or-operator.png" alt-text="Screenshot der bedingten Anweisung der Netzwerkgruppe mit dem logischen Operator OR.":::

* Fortgeschrittener Operator:

    ```json
    {
       "anyOf": [
          {
             "field": "Name",
             "contains": "VNet-A"
          },
          {
             "field": "Name",
             "contains": "VNetA"
          }
       ]
    }
    ```

Der Parameter `"anyOf"` enthält beide bedingten Anweisungen, die durch den logischen Operator **OR** getrennt sind.

### <a name="example-2-and-and-or-operator-at-the-same-time"></a>Beispiel 2: AND- und OR-Operator gleichzeitig

* Basiseditor:

    :::image type="content" source="./media/how-to-exclude-elements/both-operator.png" alt-text="Screenshot der bedingten Anweisung der Netzwerkgruppe mit dem logischen Operator OR und AND.":::

* Fortgeschrittener Editor:

```json
{
   "allOf": [
      {
         "anyOf": [
            {
               "field": "Name",
               "contains": "VNet-A"
            },
            {
               "field": "Name",
               "contains": "VNetA"
            }
         ]
      },
      {
         "field": "Name",
         "notcontains": "West"
      }
   ]
}
```

Sowohl `"allOf"` and `"anyOf"` als auch or werden im Code verwendet. Da der **AND**-Operator an letzter Stelle in der Liste steht, befindet er sich im äußeren Teil des Codes, der die beiden bedingten Anweisungen mit dem **OR**-Operator enthält.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Netzwerkgruppen](concept-network-groups.md).
- Eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz erstellen.
