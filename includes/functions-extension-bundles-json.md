---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: feabf1cfde0facc49694554094064ee7b54e816b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852332"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"
    }
}
```

In `extensionBundle` sind die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG |
| -------- | ----------- |
| id | Der Namespace für Microsoft Azure Functions-Erweiterungsbündel. |
| version | Die Version des zu installierenden Pakets. Die Functions Runtime wählt immer die zulässige Höchstversion aus, die durch den Versionsbereich oder das Intervall definiert ist. Mit dem obigen Versionswert sind alle Bündelversionen von 2.0.0 bis kleiner als 3.0.0 zulässig. Weitere Informationen finden Sie im Abschnitt zur [Intervallnotation zum Angeben von Versionsbereichen](/nuget/reference/package-versioning#version-ranges). |