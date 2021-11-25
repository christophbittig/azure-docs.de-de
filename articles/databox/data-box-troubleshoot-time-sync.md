---
title: Beheben von Zeitsynchronisierungsproblemen für Azure Data Box- und Azure Data Box Heavy-Geräte
description: Beschreibt die Behebung von Zeitsynchronisierungsproblemen für ein Azure Data Box- oder Azure Data Box Heavy-Gerät über die PowerShell-Schnittstelle.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 8999c2873528dd5dd0fefc0b730f4856ac995649
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557293"
---
# <a name="sync-device-time-for-azure-data-box-and-azure-data-box-heavy"></a>Synchronisieren der Gerätezeit für Azure Data Box und Azure Data Box Heavy

In diesem Artikel wird beschrieben, wie Sie diagnostizieren, dass Ihr Data Box nicht synchron ist, und dann die Zeit auf Ihrem Data Box-Gerät ändern. Die Informationen in diesem Artikel gelten für Import- und Exportaufträge auf Data Box- und Data Box Heavy-Geräten.


## <a name="about-device-time-sync"></a>Informationen zur Gerätezeitsynchronisierung

Data Box synchronisiert die Zeit automatisch, wenn es mit dem Internet verbunden ist, indem der Standardzeitserver Windows verwendet wird `time.windows.com`. Wenn Data Box jedoch nicht mit dem Internet verbunden ist, ist die Gerätezeit möglicherweise nicht synchron. Diese Situation kann sich auf das Kopieren von Daten aus den Quelldaten in Data Box insbesondere dann auswirken, wenn die Kopie über die REST-API oder bestimmte Tools mit Zeiteinschränkungen erfolgt. 

Wenn Sie einen Zeitunterschied zwischen der Zeit auf Data Box und anderen lokalen Geräten an Ihrem Standort feststellen, können Sie die Zeit auf Ihrem Data Box synchronisieren, indem Sie auf die zugehörige PowerShell-Schnittstelle zugreifen. `Set-Date API` wird verwendet, um die Gerätezeit zu ändern. Weitere Informationen finden Sie unter [Datums-API festlegen](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).


## <a name="connect-to-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

Um Zeitsynchronisierungsprobleme zu beheben, müssen Sie zunächst eine Verbindung mit der PowerShell-Schnittstelle Ihres Geräts herstellen.

[!INCLUDE [Connect to Data Box PowerShell interface](../../includes/data-box-connect-powershell-interface.md)]


## <a name="change-device-time"></a>Ändern der Gerätezeit

Führen Sie zum Ändern der Gerätezeit die folgenden Schritte aus.

1. Um zu diagnostizieren, ob die Gerätezeit nicht synchron ist, müssen Sie zunächst die Gerätezeit ermitteln. Verwenden Sie das `Get-Date` Cmdlet, um das Datum und die Uhrzeit Ihres Data Box anzuzeigen.

    `Get-Date`

1. Wenn die Gerätezeit nicht synchron ist, verwenden Sie das `Set-Date` Cmdlet, um die Zeit auf Ihrem Data Box zu ändern.

    - Stellen Sie die Zeit um 2 Minuten vor.
    
        ```powershell
        Set-Date -Adjust <time change in hours:mins:secs format> -DisplayHint Time
        ```
    - Stellen Sie die Zeit um 2 Minuten zurück.

        ```powershell
        Set-Date -Adjust -<time change in hours:mins:secs format> -DisplayHint Time
        ```    

        Beispielausgabe:
        
        ```powershell
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:22:50 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust 00:02:00 -DisplayHint Time
        2:25:18 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Set-Date -Adjust -00:02:00 -DisplayHint Time
        2:23:28 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>Get-date
        Friday, September 3, 2021 2:23:42 PM
        [by506b4b5d0790.microsoftdatabox.com]: PS>
        ```
        Weitere Informationen finden Sie unter [Datums-API festlegen](/powershell/module/microsoft.powershell.utility/set-date?view=powershell-7.1&preserve-view=true).
    
## <a name="next-steps"></a>Nächste Schritte

Informationen zur Behebung anderer Data Box-Probleme finden Sie in einem der folgenden Artikel:

- [Behandeln von Problemen im Zusammenhang mit Data Box-Blobspeicherfehlern](data-box-troubleshoot-rest.md).
- [Behandeln von Problemen im Zusammenhang mit Fehlern beim Kopieren von Data Box-Fehlern](data-box-troubleshoot.md).
- [Behandeln von Problemen im Zusammenhang mit Fehlern beim Hochladen von Daten](data-box-troubleshoot-data-upload.md).
