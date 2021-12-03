---
author: alkohli
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 24cc360da36e49726cc23b407ada5a3d88c3a2a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082966"
---
1. Wenn die Updates für Ihr Gerät verfügbar sind, wird auf der Seite **Übersicht** Ihrer Azure Stack Edge-Ressource eine Benachrichtigung angezeigt. Wählen Sie die Benachrichtigung aus, oder klicken Sie auf der oberen Befehlsleiste auf **Gerät aktualisieren**. Auf diese Weise können Sie Updates der Gerätesoftware anwenden.

    ![Auswählen des zu aktualisierenden Geräts](media/azure-stack-edge-install-2110-updates/install-updates-portal-1.png)

2. Bestätigen Sie auf dem Blatt **Geräteupdates**, dass Sie die Lizenzbedingungen für neue Features in den Versionshinweisen gelesen haben.

    Nachdem die Updates auf das Gerät heruntergeladen wurden, können Sie die Option **Automatisch installieren** für die Updates auswählen. 

    ![Auswählen der Option „Updates automatisch installieren“](media/azure-stack-edge-install-2110-updates/install-updates-portal-2.png)    

    Sie können die Updates auch einfach herunterladen und später **manuell installieren**.

    ![Auswählen der Option „Updates später manuell installieren“](media/azure-stack-edge-install-2110-updates/install-updates-portal-3.png)

3. Der Download der Updates wird gestartet. Es wird eine Benachrichtigung angezeigt, dass der Download läuft.

    ![Benachrichtigung, die angibt, dass der Download von Updates ausgeführt wird](media/azure-stack-edge-install-2110-updates/install-updates-portal-4.png)

    Außerdem wird im Azure-Portal ein Benachrichtigungsbanner angezeigt. Dies zeigt den Downloadstatus an. Sie können diese Benachrichtigung oder **Gerät aktualisieren** auswählen, um den ausführlichen Status des Updates anzuzeigen.

    ![Anzeigen des detaillierten Updatestatus auf dem Blatt „Geräteupdates“](media/azure-stack-edge-install-2110-updates/install-updates-portal-5.png)


4. Nachdem der Download abgeschlossen ist, wird das Benachrichtigungsbanner aktualisiert, um den Abschluss anzuzeigen. Wenn Sie sich für automatische Installation der Updates entschieden haben, beginnt die Installation automatisch.

    Wenn Sie Updates später manuell installieren möchten, wählen Sie die Benachrichtigung aus, um das Blatt **Geräteupdates** zu öffnen. Wählen Sie **Update installieren** aus.
 
    ![Auswählen von „Update installieren, nachdem Updates heruntergeladen wurden“](media/azure-stack-edge-install-2110-updates/install-updates-portal-6.png)
 
5. Es wird eine Benachrichtigung angezeigt, dass die Installation läuft. Im Portal wird außerdem eine Informationsmeldung angezeigt, um darauf hinzuweisen, dass die Installation ausgeführt wird. Das Gerät wird offline geschaltet und in den Wartungsmodus versetzt.
   
    ![Bannerbenachrichtigung, die angibt, dass sich das Gerät in der Wartung befindet ](media/azure-stack-edge-install-2110-updates/install-updates-portal-7.png)

6. Da es sich um ein Gerät mit Einzelknoten handelt, wird das Gerät nach der Installation der Updates neu gestartet. 

    ![Bannerbenachrichtigung, die angibt, dass das Gerät neu gestartet wird](media/azure-stack-edge-install-2110-updates/install-updates-portal-8.png)

7. Nach dem Neustart wird die Aktualisierung der Gerätesoftware abgeschlossen. Das Kubernetes-Softwareupdate wird automatisch gestartet. Das Gerät wird erneut offline geschaltet und in den Wartungsmodus versetzt.

    ![Bannerbenachrichtigung, die angibt, dass sich das Gerät im Wartungsmodus befindet](media/azure-stack-edge-install-2110-updates/install-updates-portal-9.png)   


8. Nach erfolgreicher Installation der Updates für die Gerätesoftware und Kubernetes wird die Bannerbenachrichtigung ausgeblendet. Der Gerätestatus wird in **Ihr Gerät ist online** aktualisiert. 

    ![Das Update ist abgeschlossen, und das Gerät ist online](media/azure-stack-edge-install-2110-updates/install-updates-portal-10.png)

    Rufen Sie die lokale Web-Benutzeroberfläche auf und gehen Sie dann zur Seite **Softwareaktualisierung**. Bestätigen Sie, dass die Gerätesoftware und Kubernetes erfolgreich aktualisiert wurden und die Softwareversion dies widerspiegelt.

    ![Anzeigen der aktualisierten Softwareversion in der lokalen Benutzeroberfläche](media/azure-stack-edge-install-2110-updates/install-updates-portal-11.png)


