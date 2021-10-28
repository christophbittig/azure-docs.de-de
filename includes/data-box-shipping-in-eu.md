---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/21/2021
ms.author: alkohli
ms.openlocfilehash: bd845eef3e2f41bc9f14303430e3e44ab68c1b68
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247216"
---
Wenn Sie das Gerät in Europa zurückgeben möchten, gehen Sie wie folgt vor:

1. Vergewissern Sie sich, dass das Gerät ausgeschaltet ist und die Kabel entfernt wurden.
2. Wickeln Sie das mit dem Gerät gelieferte Netzkabel auf, und befestigen Sie es sicher an der Rückseite des Geräts.
3. Stellen Sie sicher, dass das Adressetikett auf dem Freihanddisplay angezeigt wird, und vereinbaren Sie einen Abholtermin mit Ihrem Kurierdienst. Falls das Adressetikett beschädigt oder nicht mehr vorhanden ist oder nicht in der E-Ink-Anzeige angezeigt wird, wenden Sie sich an den Microsoft-Support. Sofern dies vom Support empfohlen wird, können Sie im Azure-Portal zu **Übersicht > Versandetikett herunterladen** navigieren. Laden Sie das Versandetikett herunter, und bringen Sie es am Gerät an.
1. **Beim Versand aus Deutschland oder der Schweiz** muss das Azure-Rechenzentrum vorab über alle Geräterückgaben informiert werden:
    1. Senden Sie eine E-Mail an das Azure Data Box Operations-Team, um eine Eingangs-ID zu erhalten. Verwenden Sie dazu die folgenden Vorlage. Senden Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com).

       ```
       To: adbops@microsoft.com
       Subject: Request for Azure Data Box Inbound ID: <orderName> 
       Body: 
        
       I am ready to return an Azure Data Box and would like to request an Inbound ID for the following order:
       
       Order Name: <orderName>
       Return Tracking Number: <returnTracking#>
       ```

    1. Notieren Sie sich die vom Azure Data Box Operations-Team bereitgestellte Eingangs-ID, und kleben Sie sie gut sichtbar in der Nähe des Rücksendeetiketts auf das Paket.
1. Planen Sie die Abholung durch UPS, falls Sie das Gerät zurücksenden. So planen Sie die Abholung:

    * Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
    * Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie in der E-Ink-Anzeige oder auf dem gedruckten Etikett finden. Wenn Sie die Nachverfolgungsnummer nicht angeben, wird bei der Abholung eine zusätzliche Gebühr erhoben.
    * Falls während der Planung einer Abholung Probleme auftreten oder wenn Sie aufgefordert werden, zusätzliche Gebühren zu bezahlen, wenden Sie sich an Azure Data Box Operations. Senden Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Wenn Sie keine Abholung planen können oder möchten, können Sie die Data Box auch an der nächstgelegenen Versandstelle abgeben.

    <!--FOLLOW-UP: Move this to after the Include in data-box-deploy-picked-up. This has been done already in data-box-export-deploy-picked-up. - **If you're shipping from Germany or Switzerland,** you can also [use self-managed shipping](data-box-deploy-picked-up.md#self-managed-shipping).-->

4. Nachdem die Data Box vom Kurierdienst abgeholt und eingescannt wurde, wird der Status der Bestellung im Portal in **Abgeholt** geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

