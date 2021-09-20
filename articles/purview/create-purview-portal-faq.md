---
title: Erstellen einer Azure-Richtlinie für Azure Purview
description: In diesem Artikel wird beschrieben, wie Sie eine Azure-Richtlinie für Purview erstellen und gleichzeitig vorhandene Richtlinien zur Aufrechterhaltung der Sicherheit behalten.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 10ac52f62da8f8c20ca79ad4d5bb3073eb2bfe69
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111910"
---
# <a name="create-an-azure-policy-exception-for-purview"></a>Erstellen einer Azure-Richtlinie für Purview

Viele Abonnements verfügen über [Azure-Richtlinien](../governance/policy/overview.md), die die Erstellung einiger Ressourcen einschränken. Dies soll die Abonnementsicherheit und -sauberkeit gewährleisten. Purview-Konten stellen jedoch zwei weitere Azure-Ressourcen bei ihrer Erstellung zur Verfügung: ein Azure Storage-Konto und einen Event Hub-Namespace. Wenn Sie das [Purview-Konto erstellen](create-catalog-portal.md), werden diese Ressourcen bereitgestellt. Die Ressourcen werden von Azure verwaltet. Sie müssen sie nicht verwalten, aber Sie müssen sie bereitstellen.

Sie können eine Richtlinienausnahme erstellen, um Ihre Richtlinien in Ihrem Abonnement zu verwalten, aber dennoch die Erstellung dieser verwalteten Ressourcen zu ermöglichen.

## <a name="create-a-policy-exception-for-purview"></a>Erstellen einer Richtlinienausnahme für Purview

1. Suchen Sie im **Azure-Portal** nach [Richtlinie](https://portal.azure.com).

    :::image type="content" source="media/create-purview-portal-faq/search-for-policy.png" alt-text="Screenshot, der die Azure-Portal Suchleiste mit dem Schlüsselwort „Richtlinie“ zeigt.":::

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
