---
title: Rückgabe des Azure Stack Edge-Geräts
description: Erfahren Sie, wie Sie die Daten löschen und Ihr Azure Stack Edge-Gerät zurückgeben und dann die dem Gerät zugeordnete Ressource löschen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/28/2021
ms.author: alkohli
ms.openlocfilehash: b17b613c07430ce7d4a86e251a86a96d250f0255
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469295"
---
# <a name="return-your-azure-stack-edge-device"></a>Zurückgeben Ihres Azure Stack Edge-Geräts

[!INCLUDE [applies-to-pro-fpga](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

In diesem Artikel wird beschrieben, wie Sie die Daten löschen und Ihr Azure Stack Edge-Gerät zurückgeben. Nachdem Sie das Gerät zurückgegeben haben, können Sie auch die mit dem Gerät verknüpfte Ressource löschen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Löschen der Daten von den Datenträgern des Geräts
> * Initiieren der Geräterückgabe im Azure-Portal
> * Packen des Geräts und Planen einer Abholung
> * Löschen der Ressourcen im Azure-Portal

## <a name="erase-data-from-the-device"></a>Löschen der Daten vom Gerät

Um die Daten von den Datenträgern Ihres Geräts zu löschen, müssen Sie Ihr Gerät zurücksetzen.

Erstellen Sie bei Bedarf vor dem Zurücksetzen eine Kopie der lokalen Daten auf dem Gerät. Sie können die Daten vom Gerät in einen Azure Storage-Container kopieren. 

Die Geräterückgabe kann bereits vor dem Zurücksetzen des Geräts initiiert werden.

Sie können Ihr Gerät über die lokale Webbenutzeroberfläche oder über PowerShell zurücksetzen. PowerShell-Anweisungen finden Sie unter [Zurücksetzen Ihres Geräts](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Bei einem Geräteaustausch oder Upgrade auf ein neues Gerät sollten Sie Ihr Gerät erst zurücksetzen, nachdem Sie das neue Gerät erhalten haben.
> - Beim Zurücksetzen werden nur alle lokalen Daten vom Gerät gelöscht. Die Daten in der Cloud werden nicht gelöscht, und für sie fallen [Kosten](https://azure.microsoft.com/pricing/details/storage/) an. Diese Daten müssen mithilfe eines Cloudspeicher-Verwaltungstools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) separat gelöscht werden.

## <a name="initiate-device-return"></a>Initiieren der Geräterückgabe

Beginnen Sie den Rückgabeprozess mit den folgenden Schritten.

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (Vorschau)](#tab/azure-edge-hardware-center) 

Wenn Sie das Azure Edge Hardware Center für die Bestellung Ihres Geräts verwendet haben, müssen Sie bei der Rückgabe des Geräts die folgenden Schritte befolgen:

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Edge Hardware Center-Bestellartikelressource. Wählen Sie in der **Übersicht** über die obere Befehlsleiste im rechten Bereich die Option **Rückgabe** aus. Die Rückgabeoption ist erst aktiviert, nachdem Sie ein Gerät erhalten haben.

    ![Zurückgeben des Geräts 1](media/azure-stack-edge-return-device/hardware-center-return-device-1.png)  

1. Geben Sie auf dem Blatt **Hardware zurückgeben** die folgenden Informationen ein:

    ![Zurückgeben des Geräts 2](media/azure-stack-edge-return-device/hardware-center-return-device-2.png) 

    1. Wählen Sie in der Dropdownliste einen **Grund für die Rückgabe** aus.

    1. Geben Sie die Seriennummer des Geräts an. Die Seriennummer des Geräts ist auf der lokalen Webbenutzeroberfläche des Geräts in der **Übersicht** angegeben.  
    
       ![Seriennummer des Geräts 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    1. Geben Sie (optional) die Nummer des **Serviceetiketts** ein. Die Nummer des Serviceetiketts ist ein Bezeichner mit fünf oder mehr Zeichen, der für Ihr Gerät eindeutig ist. Das Serviceetikett befindet sich in der rechten unteren Ecke des Geräts (wenn Sie auf das Gerät blicken). Ziehen Sie das Informationsetikett heraus. Der Auszug enthält Systeminformationen wie Serviceetikett, NIC, MAC-Adresse und Ähnliches. 
    
       ![Serviceetikettnummer 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    1. Um einen Rücksendekarton anzufordern, können Sie **Versandkarton erforderlich, um Hardwareeinheit zurückzugeben** auswählen. Antworten Sie mit **Ja** auf die Frage **Benötigen Sie einen leeren Karton für die Rückgabe?** .
    
    1. Lesen Sie die **Datenschutzbestimmungen**, und aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie die Datenschutzbestimmungen gelesen haben und ihnen zustimmen.

    1. Überprüfen Sie die **Abholdetails**. Standardmäßig wird die Lieferadresse als Abholadresse festgelegt. Sie können entweder eine neue Adresse hinzufügen oder aus den gespeicherten Adressen eine andere für die Abholung auswählen.

        ![Zurückgeben des Geräts 3](media/azure-stack-edge-return-device/hardware-center-return-device-3.png) 

    1. Wählen Sie **Rückgabe initiieren** aus.

1. Nach der Übermittlung des Rückgabeanforderung wird der Status Ihrer Rückgabe von der Bestellartikelressource widerspiegelt. Der Status verändert sich von **Rückgabe initiiert** zu **Abgeholt** zu **Rückgabe abgeschlossen**. Verwenden Sie das Portal, um den Rückgabestatus Ihrer Ressource jederzeit zu überprüfen.

    ![Zurückgeben des Geräts 5](media/azure-stack-edge-return-device/hardware-center-return-device-4.png) 

1. Nachdem die Rückgabeanforderung initiiert wurde, wird sich das Betriebsteam von Azure Stack Edge an Sie wenden, um die Abholung des Geräts zu planen.

Im nächsten Schritt muss das Gerät verpackt werden.


### <a name="portal-classic"></a>[Portal (klassisch)](#tab/azure-portal)

Wenn Sie Ihr Gerät klassisch über das Portal bestellt haben, müssen Sie für die Rückgabe des Geräts die folgenden Schritte befolgen:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource. Wählen Sie in der **Übersicht** über die Befehlsleiste im rechten Bereich die Option **Gerät zurückgeben** aus. 

    ![Zurückgeben des Geräts 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Gehen Sie auf dem Blatt **Gerät zurückgeben** unter **Grundlegende Details** wie folgt vor:

    1. Geben Sie die Seriennummer des Geräts an. Die Seriennummer des Geräts ist auf der lokalen Webbenutzeroberfläche des Geräts in der **Übersicht** angegeben.  
    
       ![Seriennummer des Geräts 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Geben Sie die Nummer des Serviceetiketts ein. Die Nummer des Serviceetiketts ist ein Bezeichner mit fünf oder mehr Zeichen, der für Ihr Gerät eindeutig ist. Das Serviceetikett befindet sich in der rechten unteren Ecke des Geräts (wenn Sie auf das Gerät blicken). Ziehen Sie das Informationsetikett heraus. Der Auszug enthält Systeminformationen wie Serviceetikett, NIC, MAC-Adresse und Ähnliches. 
    
       ![Serviceetikettnummer 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Wählen Sie in der Dropdownliste einen Grund für die Rückgabe aus.

       ![Zurückgeben des Geräts 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Gehen Sie unter **Versandinformationen** wie folgt vor:

    1. Geben Sie Ihren Namen, den Namen des Unternehmens sowie die vollständige Unternehmensadresse an. Geben Sie eine geschäftliche Telefonnummer einschließlich Vorwahl sowie eine E-Mail-ID für Benachrichtigungen ein.
    2. Wenn Sie einen Karton für die Rückgabe benötigen, können Sie ihn anfordern. Antworten Sie mit **Ja** auf die Frage **Benötigen Sie einen leeren Karton für die Rückgabe?** .

    ![Zurückgeben des Geräts 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Lesen Sie die **Datenschutzbestimmungen**, und aktivieren Sie das Kontrollkästchen, um zu bestätigen, dass Sie die Datenschutzbestimmungen gelesen haben und ihnen zustimmen.

5. Wählen Sie **Rückgabe initiieren** aus.

    ![Zurückgeben des Geräts 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Nach der Erfassung der Angaben für die Geräterückgabe können Sie das Azure Stack Edge-Betriebsteam per E-Mail benachrichtigen. Sie können Ihre E-Mail-Anwendung verwenden, sofern diese installiert und konfiguriert ist. Sie können die Daten auch kopieren, um eine E-Mail zu erstellen und zu senden.

    ![Zurückgeben des Geräts 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Nachdem das Azure Stack Edge-Betriebsteam die E-Mail erhalten hat, sendet es Ihnen ein Etikett für die Rücksendung. Nach Erhalt dieses Etiketts können Sie die Geräteabholung mit dem Versandunternehmen planen. 

---

## <a name="pack-the-device"></a>Verpacken des Geräts

Führen Sie die folgenden Schritte aus, um das Gerät zu verpacken.

1. Fahren Sie das Gerät herunter. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung > Power settings** (Energieeinstellungen).
2. Wählen Sie **Herunterfahren** aus. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren. Weitere Informationen finden Sie unter [Verwalten der Energieeinstellungen](../databox-online/azure-stack-edge-manage-access-power-connectivity-mode.md#manage-power).
3. Trennen Sie die Netzkabel, und entfernen Sie alle Netzwerkkabel vom Gerät.
4. Bereiten Sie das Versandpaket den folgenden Anweisungen entsprechend und wie im folgenden Diagramm dargestellt sorgfältig vor:

    ![Verpackung des Geräts](media/azure-stack-edge-return-device/device-packaging-1.svg) 

    1. Verwenden Sie die Versandschachtel, die Sie von Azure angefordert haben, oder die Originalversandschachtel mit der zugehörigen Schaumstoffverpackung. 
    1. Legen Sie das untere Schaumstoffteil in die Schachtel.
    1. Legen Sie das Gerät auf den Schaumstoff, und achten Sie darauf, dass es passend im Schaumstoff sitzt.
    1. Legen Sie das obere Schaumstoffteil in das Paket.
    1. Bringen Sie die Netzkabel im Zubehörfach unter, und legen Sie die Schienen auf das obere Schaumstoffteil.
    1. Verschließen Sie die Schachtel, und bringen Sie das Adressetikett, das Sie von Azure erhalten haben, auf dem Paket an.
   
    > [!IMPORTANT]
    > Wenn die Richtlinien für die Vorbereitung der Rücklieferung nicht ordnungsgemäß beachtet werden, kann das Gerät u. U. beschädigt werden, sodass eine Gebühr für beschädigte Geräte anfallen kann. Weitere Informationen finden Sie in den [Produktbestimmungen](https://www.microsoft.com/licensing/product-licensing/products) und den [häufig gestellten Fragen zu verloren gegangenen oder beschädigten Geräten](https://azure.microsoft.com/pricing/details/databox/edge/).
 


## <a name="schedule-a-pickup"></a>Planen der Abholung

Führen Sie die folgenden Schritte aus, um eine Abholung zu planen:

1. Planen Sie eine Abholung mit Ihrem regionalen Paketdienst. Bei Rückgabe des Geräts in den USA ist dies UPS oder FedEx. So planen Sie eine Abholung durch UPS

    1. Rufen Sie Ihre lokale UPS-Versandstelle an (landes-/regionsspezifische gebührenfreie Telefonnummer).
    2. Geben Sie bei dem Telefonat die Nachverfolgungsnummer für die Rücksendung an, die Sie auf dem gedruckten Etikett finden.
    3. Wenn Sie keine Nachverfolgungsnummer angeben, fordert UPS eine Zusatzgebühr, die Sie bei der Abholung entrichten müssen.

    Wenn Sie keine Abholung planen können oder möchten, können Sie das Azure Stack Edge-Gerät auch an der nächstgelegenen Versandstelle abgeben.

## <a name="complete-return"></a>Vollständige Rückgabe

In diesem Abschnitt können Sie überprüfen, wann die Rückgabe abgeschlossen ist, und die Bestellung anschließend löschen. 

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (Vorschau)](#tab/azure-edge-hardware-center)

Nachdem das Gerät im Azure-Rechenzentrum eingegangen ist, wird das Gerät auf Beschädigungen oder Anzeichen von Manipulation untersucht.

- Wenn das Gerät intakt und in gutem Zustand eintrifft, endet die Abrechnung für diese Ressource. Das Azure Stack Edge-Betriebsteam setzt sich mit Ihnen in Verbindung, um die Rückgabe des Geräts zu bestätigen. Die abgeschlossene Rückgabe wird im Bestellartikelstatus angezeigt.  Sie können jetzt die Bestellartikelressource im Azure-Portal löschen.
- Wenn das Gerät erheblich beschädigt eintrifft, fallen möglicherweise Gebühren an. Weitere Informationen finden Sie unter [häufig gestellten Fragen zu verloren gegangenen oder beschädigten Geräten](https://azure.microsoft.com/pricing/details/databox/edge/) und [Produktbestimmungen](https://www.microsoft.com/licensing/product-licensing/products). 

### <a name="portal-classic"></a>[Portal (klassisch)](#tab/azure-portal) 

Nachdem das Gerät im Azure-Rechenzentrum eingegangen ist, wird das Gerät auf Beschädigungen oder Anzeichen von Manipulation untersucht.

- Wenn das Gerät intakt und in gutem Zustand eintrifft, endet die Abrechnung für diese Ressource. Das Azure Stack Edge-Betriebsteam setzt sich mit Ihnen in Verbindung, um die Rückgabe des Geräts zu bestätigen. Wahlweise können Sie die Ressource löschen, die im Azure-Portal mit dem Gerät verknüpft ist.
- Wenn das Gerät erheblich beschädigt eintrifft, fallen möglicherweise Gebühren an. Weitere Informationen finden Sie unter [häufig gestellten Fragen zu verloren gegangenen oder beschädigten Geräten](https://azure.microsoft.com/pricing/details/databox/edge/) und [Produktbestimmungen](https://www.microsoft.com/licensing/product-licensing/products).  


Sie können das Gerät im Azure-Portal löschen:

- Nachdem Sie eine Bestellung aufgegeben haben und bevor das Gerät von Microsoft vorbereitet wird.
- Nachdem Sie ein Gerät an Microsoft zurückgegeben haben und das Azure Stack Edge-Betriebsteam Ihnen die Rückgabe des Geräts telefonisch bestätigt hat. Das Betriebsteam meldet sich erst, wenn das zurückgegebene Gerät die physische Überprüfung im Azure-Rechenzentrum bestanden hat.

Wenn Sie das Gerät mit einem anderen Abonnement oder Standort aktiviert haben, verschiebt Microsoft Ihre Bestellung innerhalb eines Geschäftstags an das neue Abonnement oder den neuen Standort. Nach dem Verschieben der Bestellung können Sie diese Ressource löschen.


Löschen Sie mit folgenden Schritten das Gerät und die Ressource im Azure-Portal.

1. Navigieren Sie im Azure-Portal zu Ihrer Ressource und anschließend zu **Übersicht**. Wählen Sie in der Befehlsleiste **Löschen** aus.

    ![Auswählen von „Löschen“](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Geben Sie auf dem Blatt **Gerät löschen** den Namen des zu löschenden Geräts ein, und wählen Sie **Löschen** aus.

    ![Bestätigen des Löschens](media/azure-stack-edge-return-device/delete-resource-2.png)

Sie werden nach erfolgreichem Löschen von Gerät und zugeordneter Ressource benachrichtigt.

---

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [ein Azure Stack Edge-Ersatzgerät erhalten](azure-stack-edge-replace-device.md).