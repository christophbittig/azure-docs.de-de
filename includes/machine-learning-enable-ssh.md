---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346511"
---
Nach Auswahl von **Weiter: Erweiterte Einstellungen**:

1. Aktivieren Sie die Option **SSH-Zugriff aktivieren**.
1. Wählen Sie als **Quelle für öffentlichen SSH-Schlüssel** eine der folgenden Optionen in der Dropdownliste aus:
    * Bei Verwendung von **Neues Schlüsselpaar generieren**:
        1. Geben Sie in **Name des Schlüsselpaars** einen Namen für den Schlüssel ein.
        1. Klicken Sie auf **Erstellen**.
        1. Wählen Sie **Privaten Schlüssel herunterladen und Computeressource erstellen** aus.  Der Schlüssel wird üblicherweise in den Ordner **Downloads** heruntergeladen.  
    * Wenn Sie **In Azure vorhandenen öffentlichen Schlüssel verwenden** auswählen, suchen Sie unter **Gespeicherter Schlüssel** nach dem Schlüssel, und wählen Sie ihn aus.
    * Wenn Sie **Vorhandenen öffentlichen Schlüssel verwenden** auswählen, geben Sie einen öffentlichen RSA-Schlüssel im einzeiligen Format (beginnend mit „ssh-rsa“) oder im mehrzeiligen PEM-Format an. Sie können die SSH-Schlüssel mithilfe von „ssh-keygen“ unter Linux und OS X oder mit PuTTYGen unter Windows generieren.