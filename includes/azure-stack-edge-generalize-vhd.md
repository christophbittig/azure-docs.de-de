---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968390"
---
1. Öffnen Sie in der VM eine Eingabeaufforderung.

1. Führen Sie den folgenden Befehl aus, um die VM zu generalisieren. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Weitere Informationen finden Sie unter [Übersicht über Sysprep (Systemvorbereitung)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Nachdem der Befehl ausgeführt wurde, wird die VM heruntergefahren. **Starten Sie den virtuellen Computer nicht neu**.
