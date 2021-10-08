---
title: Untergeordnete Ressourcen in Bicep
description: Beschreibt, wie Sie den Namen und Typ für untergeordnete Ressourcen in Bicep festlegen.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 2d928ec83559a1bd57adde3cbae98c589bb1cd15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621997"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Festlegen von Name und Typ für untergeordnete Ressourcen in Bicep

Untergeordnete Ressourcen sind Ressourcen, die nur im Kontext einer anderen Ressource verfügbar sind. Beispielsweise kann eine [Erweiterung des virtuellen Computers](/azure/templates/microsoft.compute/virtualmachines/extensions) nicht ohne einen [virtuellen Computer](/azure/templates/microsoft.compute/virtualmachines) vorhanden sein. Die Erweiterungsressource ist ein untergeordnetes Element des virtuellen Computers.

Jede übergeordnete Ressource akzeptiert nur bestimmte Ressourcentypen als untergeordnete Ressourcen. Die Hierarchie der Ressourcentypen ist in der [Bicep Ressourcenreferenz](/azure/templates/) verfügbar.

In diesem Artikel werden verschiedene Möglichkeiten zum Deklarieren einer untergeordneten Ressource beschrieben.

### <a name="microsoft-learn"></a>Microsoft Learn

Weitere Informationen zu untergeordneten Ressourcen und praktische Anleitungen finden Sie unter [Bereitstellen von untergeordneten und Erweiterungsressourcen mithilfe von Bicep](/learn/modules/child-extension-bicep-templates) auf **Microsoft Learn**.

## <a name="name-and-type-pattern"></a>Name und Typmuster

In Bicep können Sie die untergeordnete Ressource entweder innerhalb oder außerhalb der übergeordneten Ressource angeben. Die Werte, die Sie für Ressourcennamen und Ressourcentyp angeben, ändern sich, je nachdem, wie Sie die untergeordnete Ressource deklarieren. Der vollständige Name und Typ werden jedoch immer in das gleiche Muster auflösen. 

Der **vollständige Name** der untergeordneten Ressource verwendet das Muster:

```bicep
{parent-resource-name}/{child-resource-name}
```

Wenn sie über mehr als zwei Ebenen in der Hierarchie verfügen, wiederholen Sie die übergeordneten Namen:

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

Der **vollständige Typ** der untergeordneten Ressource verwendet das Muster:

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

Wenn sie über mehr als zwei Ebenen in der Hierarchie verfügen, wiederholen Sie die übergeordneten Namen:

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

Wenn Sie die Segmente zwischen `/` Zeichen zählen, ist die Anzahl der Segmente im Typ immer eines mehr als die Anzahl der Segmente im Namen. 

## <a name="within-parent-resource"></a>Innerhalb der übergeordneten Ressource

Im folgenden Beispiel ist die untergeordnete Ressource in der Eigenschaft „resources“ der übergeordneten Ressource enthalten.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

Eine verschachtelte Ressourcendeklaration muss auf der obersten Ebene der Syntax der übergeordneten Ressource erscheinen. Deklarationen können beliebig tief verschachtelt werden, solange jede Ebene ein untergeordneter Typ seiner übergeordneten Ressource ist.

Wenn sie innerhalb des übergeordneten Ressourcentyps definiert sind, formatieren Sie die Werte für Typ und Name als ein einziges Segment ohne Schrägstriche. Das folgende Beispiel zeigt ein Speicherkonto mit einer untergeordneten Ressource für den Dateidienst, und der Dateidienst verfügt über eine untergeordnete Ressource für die Dateifreigabe. Der Name des Dateidienstes ist auf `default` festgelegt, und sein Typ ist auf `fileServices` festgelegt. Der Name der Dateifreigabe ist auf `exampleshare` festgelegt, und ihr Typ ist auf `shares` festgelegt.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

Die vollständigen Ressourcentypen sind immer noch `Microsoft.Storage/storageAccounts/fileServices` und `Microsoft.Storage/storageAccounts/fileServices/shares`. Sie geben `Microsoft.Storage/storageAccounts/` nicht an, weil es vom übergeordneten Ressourcentyp und der Version angenommen wird. Die geschachtelte Ressource kann fakultativ eine API-Version mit der Syntax `<segment>@<version>` deklarieren. Wenn die geschachtelte Ressource die API-Version auslässt, wird die API-Version der übergeordneten Ressource verwendet. Wenn die geschachtelte Ressource eine API-Version angibt, wird die angegebene API-Version verwendet.

Die Namen der untergeordneten Ressourcen sind auf `default` und `exampleshare` festgelegt, die vollständigen Namen enthalten jedoch die übergeordneten Namen. `examplestorage` oder `default` wird nicht angegeben, da dieser Teil von der übergeordneten Ressource übernommen wird.

Eine geschachtelte Ressource kann auf Eigenschaften ihrer übergeordneten Ressource zugreifen. Andere Ressourcen, die innerhalb des Textkörpers derselben übergeordneten Ressource deklariert sind, können aufeinander verweisen, indem die symbolischen Namen verwendet werden. Eine übergeordnete Ressource greift möglicherweise nicht auf die Eigenschaften der darin enthaltenen Ressourcen zu. Dieser Versuch würde zu einer zyklischen Abhängigkeit führen.

Um auf eine geschachtelte Ressource außerhalb der übergeordneten Ressource zu verweisen, muss sie mit dem enthaltenden Ressourcennamen und dem `::`-Operator qualifiziert werden. So können Sie beispielsweise eine Eigenschaft von einer untergeordneten Ressource ausgeben:

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>Außerhalb der übergeordneten Ressource

Das folgende Beispiel zeigt die untergeordnete Ressource außerhalb der übergeordneten Ressource an. So können Sie vorgehen, wenn die übergeordnete Ressource nicht in derselben Vorlage bereitgestellt wird oder wenn Sie [eine Schleife](loop-resources.md) verwenden möchten, um mehrere untergeordnete Ressourcen zu erstellen. Geben Sie die übergeordnete Eigenschaft für das untergeordnete Element an, wobei der Wert auf den symbolischen Namen des übergeordneten Elements festgelegt ist. Mit dieser Syntax müssen Sie weiterhin den vollständigen Ressourcentyp deklarieren, aber der Name der untergeordneten Ressource ist nur der Name des untergeordneten Elements.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

Bei Definition außerhalb der übergeordneten Ressource formatieren Sie den Typ und Namen mit Schrägstrichen, um den übergeordneten Typ und Namen einzuschließen.

Das folgende Beispiel zeigt ein Speicherkonto, einen Dateidienst und eine Dateifreigabe, die alle auf Stammebene definiert sind.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

Das Verweisen auf den symbolischen Namen der untergeordneten Ressource funktioniert genauso wie das Verweisen auf das übergeordnete Element.

## <a name="full-resource-name-outside-parent"></a>Vollständiger Ressourcenname außerhalb des übergeordneten Elements

Sie können auch den vollständigen Ressourcennamen und -typ verwenden, wenn Sie die untergeordnete Ressource außerhalb des übergeordneten Elements deklarieren. Sie legen die Eigenschaft für das übergeordnete Element nicht an der untergeordneten Ressource fest. Da die Abhängigkeit nicht abgeleitet werden kann, müssen Sie diese explizit festlegen.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> Das Festlegen des vollständigen Ressourcennamens und -typs ist nicht die empfohlene Vorgehensweise. Sie ist nicht so typsicher wie einer der anderen Ansätze.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen von Bicep-Dateien finden Sie unter [Verstehen der Struktur und Syntax von Bicep-Dateien](./file.md).
* Weitere Informationen zum Format des Ressourcennamens beim Verweisen auf die Ressource finden Sie unter der [Referenzfunktion](./bicep-functions-resource.md#reference).
