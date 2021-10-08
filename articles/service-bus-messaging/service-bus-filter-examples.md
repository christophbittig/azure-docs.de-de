---
title: Festlegen von Abonnementfiltern in Azure Service Bus | Microsoft-Dokumentation
description: Dieser Artikel enthält Beispiele zum Definieren von Filtern und Aktionen für Abonnements von Azure Service Bus-Themen.
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: d73a8cdade8600a793911acfefc54a8e1e1530b6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754978"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Festlegen von Abonnementfiltern (Azure Service Bus)
Dieser Artikel enthält einige Beispiele zum Festlegen von Filtern für Abonnements von Azure Service Bus-Themen. Konzeptionelle Informationen zu Filtern finden Sie unter [Filter](topic-filters.md).

## <a name="filter-on-system-properties"></a>Filter für Systemeigenschaften
Verwenden Sie das folgende Format, um in einem Filter auf eine Systemeigenschaft zu verweisen: `sys.<system-property-name>`. 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filter für Nachrichteneigenschaften
Hier sind die Beispiele für die Verwendung von Nachrichteneigenschaften in einem Filter angegeben. Sie können auf Nachrichteneigenschaften zugreifen, indem Sie `user.property-name` oder einfach `property-name` verwenden.

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filter für Nachrichteneigenschaften mit Sonderzeichen
Wenn der Name der Nachrichteneigenschaft Sonderzeichen enthält, sollten Sie den Namen in doppelte Anführungszeichen (`"`) setzen. Verwenden Sie im Filter beispielsweise die folgende Syntax, wenn der Eigenschaftsname `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` lautet. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filter für Nachrichteneigenschaften mit numerischen Werten
In den folgenden Beispielen wird veranschaulicht, wie Sie Eigenschaften mit numerischen Werten in Filtern verwenden können. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Parameterbasierte Filter
Hier sind einige Beispiele für die Verwendung von parameterbasierten Filtern angegeben. In diesen Beispielen ist `DataTimeMp` eine Nachrichteneigenschaft vom Typ `DateTime` und `@dtParam` ein Parameter, der als `DateTime`-Objekt an den Filter übergeben wird.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Verwenden von IN und NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ein C# Beispiel finden Sie im [Beispiel für Themenfilter auf GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filters"></a>Korrelationsfilter

### <a name="correlation-filter-using-correlationid"></a>Korrelationsfilter mithilfe der CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Filtert Nachrichten, bei denen `CorrelationID` auf `Contoso` festgelegt ist 

> [!NOTE]
> Die [CorrelationRuleFilter](/dotnet/api/azure.messaging.servicebus.administration.correlationrulefilter)-Klasse in .NET befindet sich im Namespace [Azure.Messaging.ServiceBus.Administration](/dotnet/api/azure.messaging.servicebus.administration). Beispielcode, der zeigt, wie Filter im Allgemeinen mithilfe von .NET erstellt werden, finden Sie in [hier auf GitHub](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters/Program.cs#L179).


### <a name="correlation-filter-using-system-and-user-properties"></a>Korrelationsfilter mithilfe von System- und Benutzereigenschaften

```csharp
var filter = new CorrelationRuleFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Entspricht `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`





## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die folgenden Beispiele an: 

- [.NET – Grundlegendes Tutorial zum Senden und Empfangen mit Filtern](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET – Themenfilter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-Vorlage](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)
