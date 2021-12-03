---
titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
ms.openlocfilehash: 2d26c560cd05aad4962d318e8c2c798c576db811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "131011648"
---
Wenn Sie Probleme beim Ausführen eines Cognitive Services-Containers haben, können Sie versuchen, den Microsoft-Diagnosecontainer zu verwenden. Verwenden Sie diesen Container zum Diagnostizieren häufiger Fehler in Ihrer Bereitstellungsumgebung, die ggf. verhindern, dass Cognitive Services-Container wie erwartet funktionieren.

Verwenden Sie den folgenden Docker-Befehl vom Typ `pull`, um den Container abzurufen:

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

Führen Sie dann den Container aus, ersetzen Sie `{ENDPOINT_URI}` durch Ihren Endpunkt, und ersetzen Sie `{API_KEY}` durch den Schlüssel für Ihre Ressource:

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Der Container überprüft die Netzwerkkonnektivität mit dem Abrechnungsendpunkt.