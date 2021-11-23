---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 11/15/2021
ms.author: alkohli
ms.openlocfilehash: 6e18baf15b63a37f9c7746ac1273b481f1ea77cc
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556565"
---
Gehen Sie wie folgt vor, wenn Sie Data Box in den Regionen „US Government“, „Japan“, „Singapur“, „Südkorea“, „Indien“, „Südafrika“, „Vereinigtes Königreich“, „Deutschland“, „Schweiz“, „Europa, Westen“, „Australien“ oder „Brasilien“ verwenden und bei der Auftragserstellung die Option für selbst verwalteten Versand ausgewählt haben: Ausführliche Schritte finden Sie unter [Verwenden des selbst verwalteten Versands für Azure Data Box im Azure-Portal](../articles/databox/data-box-portal-customer-managed-shipping.md).

1. Notieren Sie sich den Autorisierungscode, der auf der Seite **Versandvorbereitung** der lokalen Webbenutzeroberfläche von Data Box angezeigt wird, nachdem dieser Schritt erfolgreich abgeschlossen wurde.
2. Schalten Sie das Gerät aus, und entfernen Sie die Kabel. Wickeln Sie das mitgelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Wenn Sie zum Rückversand des Geräts bereit sind, senden Sie eine E-Mail an das Azure Data Box Operations-Team. Verwenden Sie dafür die Vorlage unten.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a government-approved ID during the drop off.
    ```

   > [!NOTE]
   > - Die für eine Retoure erforderlichen Informationen können je nach Region variieren. 
   > - Ausführliche Anweisungen für eine Data Box-Retoure in Brasilien finden Sie unter [Verwenden des selbst verwalteten Versands für Azure Data Box](..\articles\databox\data-box-portal-customer-managed-shipping.md). 
