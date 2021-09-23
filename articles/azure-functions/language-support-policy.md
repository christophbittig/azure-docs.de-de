---
title: Unterstützungsrichtlinie für die Azure Functions-Sprachlaufzeit
description: Informationen zur Unterstützungsrichtlinie für die Azure Functions-Sprachlaufzeit
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: b3b5f7cf108fd18ed450a6837a5dd35ceb83dc60
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303965"
---
# <a name="language-runtime-support-policy"></a>Unterstützungsrichtlinie für die Sprachlaufzeit

In diesem Artikel wird die Unterstützungsrichtlinie für die Azure Functions-Sprachlaufzeit erläutert. 

## <a name="retirement-process"></a>Deaktivierungsprozess

Die Azure Functions-Laufzeit basiert auf verschiedenen Komponenten, dazu zählen Betriebssysteme, der Azure Functions-Host und sprachspezifische Worker. Um vollständige Supportabdeckungen für Funktions-Apps zu gewährleisten, verwendet Azure Functions eine stufenweise Reduzierung der Unterstützung, da die Versionen der Programmiersprache das Ende ihrer Lebensdauer erreichen. Bei den meisten Sprachversionen stimmt das Deaktivierungsdatum mit dem Ende der Lebensdauer der Community überein. 

### <a name="notification-phase"></a>Benachrichtigungsphase

Wir senden Benachrichtigungs-E-Mails an Funktions-App-Benutzer zu bevorstehenden Deaktivierungen von Sprachversionen. Die Benachrichtigungen erfolgen mindestens ein Jahr vor dem Deaktivierungsdatum. Nach der Benachrichtigung sollten Sie für die Sprachversion, die Ihre Funktions-Apps verwenden, ein Upgrade auf eine unterstützte Version durchführen.

### <a name="retirement-phase"></a>Deaktivierungsphase

Nach dem Ende der Lebensdauer einer Sprachversion können Sie keine neuen Funktions-Apps mehr erstellen, die diese Sprachversion verwenden sollen.

Nach dem Ende der Lebensdauer der Sprache sind Funktions-Apps, die deaktivierte Sprachversionen verwenden, nicht mehr zu neuen Features, Sicherheitspatches und Leistungsoptimierungen berechtigt. Diese Funktions-Apps werden jedoch weiterhin auf der Plattform ausgeführt. 

> [!IMPORTANT]
>Es wird dringend empfohlen, für die Sprachversion Ihrer betroffenen Funktions-Apps ein Upgrade auf eine unterstützte Version durchzuführen.   
>Wenn Sie Funktions-Apps mit einer nicht unterstützten Sprachversion ausführen, müssen Sie ein Upgrade durchführen, bevor Sie Unterstützung für die Funktions-Apps erhalten.


## <a name="retirement-policy-exceptions"></a>Ausnahmen bei der Deaktivierungsrichtlinie

Bei der oben beschriebenen Deaktivierungsrichtlinie gibt es einige Ausnahmen. Im Folgenden finden Sie eine Liste der Sprachen, die sich dem Ende ihrer Lebensdauer nähern oder dieses erreicht haben, jedoch weiterhin bis auf weiteres auf der Plattform unterstützt werden. Wenn diese Sprachversionen das Ende ihrer Lebensdauer erreichen, werden sie nicht mehr aktualisiert oder gepatcht. Aus diesem Grund raten wir davon ab, Ihre Funktions-Apps in diesen Sprachversionen zu entwickeln und auszuführen.

|Sprachversionen                        |Datum des Lebensdauerendes         |Deaktivierungsdatum|
|-----------------------------------------|-----------------|----------------|
|.NET 5|Februar 2022|Wird noch angekündigt|
|Node 6|30. April 2019|28. Februar 2022| 
|Node 8|31. Dezember 2019|28. Februar 2022| 
|Node 10|30. April 2021|30. September 2022| 
|PowerShell Core 6| 4\. September 2020|30. September 2022|
|Python 3.6 |23. Dezember 2021|30. September 2022| 
 

## <a name="language-version-support-timeline"></a>Zeitachse für die Sprachversionsunterstützung

Weitere Informationen zur Zeitachse einer Unterstützungsrichtlinie für eine bestimmte Sprachversion finden Sie in den folgenden externen Ressourcen:
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>Konfigurieren von Sprachversionen

|Sprache                         | Konfigurationsanleitungen         |
|-----------------------------------------|-----------------|
|C# (Klassenbibliothek) |[Link](./functions-dotnet-class-library.md#supported-versions).|
|Node |[Link](./functions-reference-node.md#setting-the-node-version).|
|PowerShell |[Link](./functions-reference-powershell.md#changing-the-powershell-version).|
|Python |[Link](./functions-reference-python.md#python-version).|
 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Durchführen von Upgrades für die Sprachversionen Ihrer Funktions-Apps finden Sie in den folgenden Ressourcen:


+ [Derzeit unterstützte Sprachversionen](./supported-languages.md#languages-by-runtime-version)
