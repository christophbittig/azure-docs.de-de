---
title: Erstellen eines Azure Purview-Kontos im Portal – Häufig gestellte Fragen (FAQ)
description: In diesem Artikel werden häufig gestellte Fragen zum Erstellen von Purview-Konten über das Portal beantwortet.
author: nayenama
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: bf148408d733dffad862eecf51cc06455846b19a
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398417"
---
# <a name="faq-about-creating-purview-accounts-via-portal"></a>Häufig gestellte Fragen zum Erstellen von Purview-Konten über das Portal

In diesem Artikel werden gängige Fragen beantwortet, die Kunden und Außendienstteams häufig zum Erstellen eines Purview-Kontos über das Azure-Portal stellen ([Erstellen eines Purview-Kontos stellen](create-catalog-portal.md)).

## <a name="common-questions"></a>Häufig gestellte Fragen

Sehen Sie sich die Antworten auf die folgenden häufig gestellten Fragen an.

### <a name="azure-policy-blocking-from-creating-storage-and-event-hub-namespace"></a>Azure Policy blockiert das Erstellen des Speichers und des Event Hub-Namespace 

* Wenn durch **Azure Policy** alle Anwendungen an der Erstellung eines **Speicherkontos** und eines **EventHub-Namespace** gehindert werden, benötigen Sie eine Richtlinienausnahme mit einem Tag, das im Rahmen der Erstellung eines Purview-Kontos eingegeben werden kann. Das liegt in erster Linie daran, dass für ein Purview-Konto jeweils eine verwaltete Ressourcengruppe mit einem darin enthaltenen Speicherkonto und einem EventHub-Namespace erstellt werden muss.

   >[!IMPORTANT]
   >Sie müssen diesen Schritt nicht ausführen, wenn Sie nicht über Azure Policy verfügen oder eine vorhandene Azure Policy-Instanz die Erstellung eines **Speicherkontos** und **EventHub-Namespaces** nicht blockiert.

    1. Suchen Sie im Azure-Portal nach **Policy**.
    1. Gehen Sie wie unter [Tutorial: Erstellen einer benutzerdefinierten Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md) beschrieben vor, oder ändern Sie die vorhandene Richtlinie, um zwei Ausnahmen mit dem Operator `not` und dem Tag `resourceBypass` hinzuzufügen:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > Das Tag kann einen beliebigen Wert (mit Ausnahme von `resourceBypass`) haben und von Ihnen später im Rahmen der Purview-Erstellung definiert werden. Es muss allerdings von der Richtlinie erkannt werden können.

        :::image type="content" source="media/create-catalog-portal/policy-definition.png" alt-text="Screenshot: Erstellen der Richtliniendefinition":::

    1. [Erstellen Sie eine Richtlinienzuweisung](../governance/policy/assign-policy-portal.md) mit der erstellten benutzerdefinierten Richtlinie.

       :::image type="content" source="media/create-catalog-portal/policy-assignment.png" alt-text="Screenshot: Erstellen der Richtlinienzuweisung" lightbox="./media/create-catalog-portal/policy-assignment.png":::

> [!Note] 
> Wenn Sie über **Azure Policy** verfügen und wie in den **Voraussetzungen** erwähnt eine Ausnahme hinzufügen müssen, müssen Sie das richtige Tag hinzufügen. Sie können beispielsweise das Tag `resourceBypass` hinzufügen: :::image type="content" source="media/create-catalog-portal/add-purview-tag.png" alt-text="Hinzufügen eines Tags zum Purview-Konto":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten von Azure Purview mit Private Link finden Sie unter [Verwenden privater Endpunkte für Ihr Azure Purview-Konto](./catalog-private-link.md).
