---
title: Fehler vom Typ „Reservierter Ressourcenname“
description: Hier wird beschrieben, wie Sie Fehler beheben, die auftreten, wenn der angegebene Ressourcenname ein reserviertes Wort enthält.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095584"
---
# <a name="resolve-reserved-resource-name-errors"></a>Fehler vom Typ „Reservierter Ressourcenname“

In diesem Artikel wird der Fehler beschrieben, der auftritt, wenn Sie eine Ressource bereitstellen, deren Name ein reserviertes Wort enthält.

## <a name="symptom"></a>Symptom

Wenn Sie eine Ressource bereitstellen, die über einen öffentlichen Endpunkt verfügbar ist, kann der folgende Fehler auftreten:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Ursache

Die Namen von Ressourcen mit einem öffentlichen Endpunkt dürfen keine reservierten Wörter oder Marken enthalten.

Die folgenden Wörter sind reserviert:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Die folgenden Wörter dürfen weder als ganzes Wort noch als Teilzeichenfolge im Namen verwendet werden:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Lösung

Geben Sie einen Namen an, der keines der reservierten Wörter enthält.