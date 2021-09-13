---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339769"
---
## <a name="local-settings"></a>Lokale Einstellungen

Bei der Ausführung in einer Funktions-App in Azure werden die von Ihren Funktionen benötigten Einstellungen [sicher in den App-Einstellungen gespeichert](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings). Bei der lokalen Entwicklung werden diese Einstellungen hingegen zum `Values`-Objekt in der „local.settings.json“-Datei hinzugefügt. Die Datei „local.settings.json“ speichert auch Einstellungen, die von lokalen Entwicklungstools verwendet werden. 

Da die Datei „local.settings.json“ möglicherweise Geheimnisse wie Verbindungszeichenfolgen enthält, sollten Sie sie niemals in einem Remoterepository speichern. Weitere Informationen zu lokalen Einstellungen finden Sie unter [Datei für lokale Einstellungen](../articles/azure-functions/functions-develop-local.md#local-settings-file).