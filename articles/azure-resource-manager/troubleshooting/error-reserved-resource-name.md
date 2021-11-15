---
title: Fehler vom Typ „Reservierter Ressourcenname“
description: Hier wird beschrieben, wie Sie Fehler beheben, die auftreten, wenn der angegebene Ressourcenname ein reserviertes Wort enthält.
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 28376ed34e2007c3f6cfb7f57f08199e5a1ade8a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458834"
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
* APP_CODE
* APP_THEMES
* APP_DATA
* APP_GLOBALRESOURCES
* APP_LOCALRESOURCES
* APP_WEBREFERENCES
* APP_BROWSERS
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

* MICROSOFT
* WINDOWS

## <a name="solution"></a>Lösung

Geben Sie einen Namen an, der keines der reservierten Wörter enthält.
