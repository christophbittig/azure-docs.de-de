---
title: Installieren von Updates auf einem Azure Stack Edge Pro-GPU-Gerät | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Updates über das Azure-Portal und die lokale Webbenutzeroberfläche für Azure Stack Edge Pro-GPU-Geräte und den Kubernetes-Cluster auf dem Gerät anwenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 13fc1ad892509d04750e51b593cc7aaf07f0c91a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082928"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Aktualisieren Ihrer Azure Stack Edge Pro-GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel werden die Schritte beschrieben, die ausgeführt werden müssen, um Updates über die lokale Webbenutzeroberfläche und über das Azure-Portal auf Ihrem Azure Stack Edge Pro-Gerät mit GPU zu installieren. Softwareupdates oder Hotfixes werden angewandt, um Ihr Azure Stack Edge Pro-Gerät und den zugehörigen Kubernetes-Cluster auf dem Gerät auf dem neuesten Stand zu halten.

Das in diesem Artikel beschriebene Verfahren wurde mit einer anderen Version der Software ausgeführt, die Vorgehensweise bleibt bei der aktuellen Softwareversion aber dieselbe.

## <a name="about-latest-update"></a>Neuestes Update

Das aktuelle Update ist das Update 2110. Dieses Update installiert zwei Updates: das Geräteupdate gefolgt von Kubernetes-Updates. Die zugeordneten Versionen für dieses Update sind:

- Gerätesoftwareversion: **2.2.1758.4034**
- Kubernetes-Serverversion: **v1.20.9**
- IoT Edge-Version: **0.1.0-beta15**
- GPU-Treiberversion: **460.32.03**
- CUDA-Version: **11.2**

Informationen zu Neuerungen in diesem Update finden Sie in den [Versionshinweisen](azure-stack-edge-gpu-2110-release-notes.md).

**Damit Sie das Update 2110 anwenden können, muss auf Ihrem Gerät Release 2106 ausgeführt werden.** 

- Wenn Sie nicht die minimal unterstützte Version nutzen, wird die Fehlermeldung *Das Updatepaket kann nicht installiert werden, da seine Abhängigkeiten nicht erfüllt sind.* angezeigt. 
- Sie können ein Update von einer älteren Version auf 2106 durchführen und dann 2110 installieren.

Beachten Sie, dass das Gerät bei der Installation des Updates oder Hotfixes neu gestartet wird. Da es sich bei Azure Stack Edge Pro GPU um ein Gerät mit einem einzelnen Knoten handelt, werden beim Aktualisieren alle ausgeführten E/A-Vorgänge unterbrochen, und es kommt zu einer Gerätedowntime von bis zu 1,5 Stunden.

Führen Sie die folgenden Schritte aus, um Updates auf Ihrem Gerät zu installieren:

1. Konfigurieren Sie den Speicherort des Updateservers. 
1. Wenden Sie die Updates über die Benutzeroberfläche des Azure-Portals oder die lokale Benutzeroberfläche an.

Die einzelnen Schritte werden in den folgenden Abschnitten beschrieben.

## <a name="configure-update-server"></a>Konfigurieren des Updateservers

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Konfiguration** > **Updateserver**.
   
    ![Konfigurieren von Updates 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. Wählen Sie in der Dropdownliste unter **Typ des Updateservers auswählen** zwischen Microsoft Update-Server (Standard) und Windows Server Update Services.  
   
    Geben Sie bei Verwendung der Option „Windows Server Update Services“ den Server-URI an. Der Server unter diesem URI stellt die Updates auf allen Geräten bereit, die mit diesem Server verbunden sind.

    ![Konfigurieren von Updates 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Der WSUS-Server wird verwendet, um Updates über eine Verwaltungskonsole zu verwalten und zu verteilen. Ein WSUS-Server kann auch als Updatequelle für andere WSUS-Server in der Organisation dienen. Der als Updatequelle eingesetzte WSUS-Server wird als Upstreamserver bezeichnet. In einer WSUS-Implementierung muss mindestens ein WSUS-Server im Netzwerk eine Verbindung mit Microsoft Update herstellen können, um verfügbare Updateinformationen herunterzuladen. Als Administrator kannst du – basierend auf der Netzwerksicherheit und -konfiguration – festlegen, wie viele weitere WSUS-Server eine direkte Verbindung mit Microsoft Update herstellen.
    
    Weitere Informationen finden Sie unter [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus).

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Die Installation von Updates über das Azure-Portal wird empfohlen. Das Gerät sucht einmal täglich automatisch nach Updates. Sobald Updates verfügbar sind, wird im Portal eine Benachrichtigung angezeigt. Sie können die Updates dann herunterladen und installieren.

> [!NOTE]
> Vergewissern Sie sich, dass das Gerät in Ordnung ist und der Status **Ihr Gerät funktioniert einwandfrei** angezeigt wird! bevor Sie mit der Installation der Aktualisierungen fortfahren.

Je nach ausgeführter Softwareversion kann sich der Installationsvorgang geringfügig unterscheiden. 

- Wenn Sie von 2106 auf 2110 aktualisieren, wird eine 1-Klick-Installation durchgeführt. Anweisungen finden Sie in der Registerkarte **Version 2106**.
- Wenn Sie auf Versionen vor 2110 aktualisieren, ist eine Installation mit zwei Klicks erforderlich. Anweisungen finden Sie in der Registerkarte **Version 2105 und früher**.

### <a name="version-2106"></a>[Version 2106](#tab/version-2106)

[!INCLUDE [azure-stack-edge-install-2110-updates](../../includes/azure-stack-edge-install-2110-updates.md)]

### <a name="version-2105-and-earlier"></a>[Version 2105 und früher](#tab/version-2105-and-earlier)

1. Wenn die Updates für Ihr Gerät verfügbar sind, wird auf der Seite **Übersicht** Ihrer Azure Stack Edge-Ressource eine Benachrichtigung angezeigt. Wählen Sie die Benachrichtigung aus, oder klicken Sie auf der oberen Befehlsleiste auf **Gerät aktualisieren**. Auf diese Weise können Sie Updates der Gerätesoftware anwenden.

    ![Softwareversion nach dem Update](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Bestätigen Sie auf dem Blatt **Geräteupdates**, dass Sie die Lizenzbedingungen für neue Features in den Versionshinweisen gelesen haben.

    Sie können auswählen, ob Sie die Updates **herunterladen und installieren** oder nur **herunterladen** möchten. Sie können diese Updates dann später installieren.

    ![Softwareversion nach dem Update 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Wenn Sie die Updates herunterladen und installieren möchten, aktivieren Sie die Option zum automatischen Installieren der Updates nach Abschluss des Downloads.

    ![Softwareversion nach dem Update 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Der Download der Updates wird gestartet. Es wird eine Benachrichtigung angezeigt, dass der Download läuft.

    ![Softwareversion nach dem Update 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Außerdem wird im Azure-Portal ein Benachrichtigungsbanner angezeigt. Dies zeigt den Downloadstatus an.

    ![Softwareversion nach dem Update 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Sie können diese Benachrichtigung oder **Gerät aktualisieren** auswählen, um den ausführlichen Status des Updates anzuzeigen.

    ![Softwareversion nach dem Update 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Nachdem der Download abgeschlossen ist, wird das Benachrichtigungsbanner aktualisiert, um den Abschluss anzuzeigen. Wenn Sie ausgewählt haben, dass die Updates heruntergeladen und installiert werden sollen, wird die Installation automatisch gestartet.

    Wenn Sie Updates nur herunterladen möchten, wählen Sie die Benachrichtigung aus, um das Blatt **Geräteupdates** zu öffnen. Wählen Sie **Installieren** aus.
  
5. Es wird eine Benachrichtigung angezeigt, dass die Installation läuft. Im Portal wird außerdem eine Informationsmeldung angezeigt, um darauf hinzuweisen, dass die Installation ausgeführt wird. Das Gerät wird offline geschaltet und in den Wartungsmodus versetzt.
   
    ![Softwareversion nach dem Update 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Da es sich um ein Gerät mit Einzelknoten handelt, wird das Gerät nach der Installation der Updates neu gestartet. Die kritische Warnung während des Neustarts weist darauf hin, dass das Gerät keinen Heartbeat mehr ausgibt.

    ![Softwareversion nach dem Update 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Wählen Sie die Warnung aus, um das entsprechende Geräteereignis anzuzeigen.
    
    ![Softwareversion nach dem Update 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Nach dem Neustart wird die Aktualisierung der Gerätesoftware abgeschlossen. Nachdem die Aktualisierung abgeschlossen ist, können Sie über die lokale Web-Benutzeroberfläche überprüfen, ob die Gerätesoftware aktualisiert wurde. Die Kubernetes-Softwareversion wurde nicht aktualisiert.

    ![Softwareversion nach dem Update 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. Sie sehen ein Benachrichtigungsbanner, das anzeigt, dass Geräte-Aktualisierungen verfügbar sind. Wählen Sie dieses Banner aus, um die Kubernetes-Software auf Ihrem Gerät zu aktualisieren. 

    ![Software-Version nach der Aktualisierung 13a](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![Softwareversion nach dem Update 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    Wenn Sie auf der oberen Befehlsleiste die Option **Gerät aktualisieren** auswählen, wird der Fortschritt der Updates angezeigt.  

    ![Softwareversion nach dem Update 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. Die Gerätestatus-Aktualisierungen von **Ihr Gerät läuft einwandfrei** nachdem die Aktualisierungen installiert wurden. 

    ![Softwareversion nach dem Update 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    Rufen Sie die lokale Web-Benutzeroberfläche auf und gehen Sie dann zur Seite **Softwareaktualisierung**. Vergewissern Sie sich, dass die Aktualisierung erfolgreich installiert wurde und dass die entsprechende Version der Gerätesoftwareversion angezeigt wird.

    ![Softwareversion nach dem Update 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


Nach erfolgreicher Installation der Updates für die Gerätesoftware und Kubernetes wird die Bannerbenachrichtigung ausgeblendet. 

---

Ihr Gerät verfügt nun über die aktuelle Version der Gerätesoftware sowie über die aktuelle Version von Kubernetes.


## <a name="use-the-local-web-ui"></a>Verwenden der lokalen Webbenutzeroberfläche

Die Verwendung der lokalen Webbenutzeroberfläche umfasst zwei Schritte:

* Herunterladen des Updates oder Hotfixes
* Installieren des Updates oder Hotfixes

Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.


### <a name="download-the-update-or-the-hotfix"></a>Herunterladen des Updates oder Hotfixes

Führen Sie die folgenden Schritte aus, um das Update herunterzuladen. Sie können das Update von einem von Microsoft bereitgestellten Speicherort oder aus dem Microsoft Update-Katalog herunterladen.


Führen Sie die folgenden Schritte aus, um das Softwareupdate aus dem Microsoft Update-Katalog herunterzuladen.

1. Starten Sie einen Browser, und navigieren Sie zu [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

    ![Katalog durchsuchen](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Geben Sie im Suchfeld des Microsoft Update-Katalogs die KB-Nummer (Knowledge Base) des Hotfixes oder der Nutzungsbedingungen des Updates ein, das Sie herunterladen möchten. Geben Sie beispielsweise **Azure Stack Edge** ein, und klicken Sie auf **Suchen**.
   
    Der Updateeintrag lautet **Azure Stack Edge-Update 2110**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Wählen Sie **Herunterladen** aus. Es müssen zwei Pakete heruntergeladen werden: <!--KB 4616970 and KB 4616971--> eines für die Gerätesoftwareupdates (*SoftwareUpdatePackage.exe*) und ein weiteres für die Kubernetes-Updates (*Kubernetes_Package.exe*). Laden Sie die Pakete in einen Ordner im lokalen System herunter. Sie können den Ordner auch in eine Netzwerkfreigabe kopieren, auf die vom Gerät aus zugegriffen werden kann.

### <a name="install-the-update-or-the-hotfix"></a>Installieren des Updates oder Hotfixes

Vergewissern Sie sich vor dem Installieren des Updates oder Hotfixes, dass Folgendes erfüllt ist:

 - Das Update oder der Hotfix wurde lokal auf Ihren Host heruntergeladen, oder auf das Update oder den Hotfix kann über eine Netzwerkfreigabe zugegriffen werden.
 - Der Gerätestatus ist fehlerfrei, wie auf der Seite **Übersicht** der lokalen Webbenutzeroberfläche angezeigt wird.

   ![Gerät aktualisieren](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Dieser Vorgang dauert etwa 20 Minuten. Führen Sie die folgenden Schritte aus, um Updates oder Hotfixes zu installieren.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** > **Softwareupdate**. Notieren Sie sich die Softwareversion, die Sie ausführen. 
   
   ![Aktualisieren des Geräts 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Geben Sie den Pfad zur Updatedatei an. Sie können auch zur Installationsdatei des Updates navigieren, sofern sie auf einer Netzwerkfreigabe platziert wurde. Wählen Sie die Softwareupdatedatei mit dem Suffix *SoftwareUpdatePackage.exe* aus.

   ![Aktualisieren des Geräts 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Wählen Sie **Update anwenden** aus.

   <!--![update device 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)-->

4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren. Da es sich um ein Gerät mit einem Einzelknoten handelt, wird das Gerät neu gestartet, und es kommt zu einer Downtime. 
   
   ![Aktualisieren des Geräts 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Das Update wird gestartet. Nachdem das Gerät erfolgreich aktualisiert wurde, wird es neu gestartet. Auf die lokale Benutzeroberfläche kann währenddessen nicht zugegriffen werden.
   
6. Nach dem Neustart wird die Seite **Anmelden** angezeigt. Um sicherzustellen, dass die Gerätesoftware aktualisiert wurde, wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung** >  **Softwareupdate**. Für das aktuelle Release sollte als Softwareversion **Azure Stack Edge 2110** angezeigt werden. 


7. Als Nächstes wird die Kubernetes-Softwareversion aktualisiert. Wiederholen Sie die obigen Schritte. Geben Sie einen Pfad zur Kubernetes-Updatedatei mit dem Suffix *Kubernetes_Package.exe* an.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Wählen Sie **Update anwenden** aus.

   <!--![update device 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)-->

9. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren.

10. Nach erfolgreicher Installation des Kubernetes-Updates bleibt die unter **Wartung** > **Softwareupdate** angezeigte Software unverändert.

    ![Aktualisieren des Geräts 6](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Verwaltung Ihrer Azure Stack Edge Pro-Instanz](azure-stack-edge-manage-access-power-connectivity-mode.md).
