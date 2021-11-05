---
title: 'Bereitstellen von Azure Video Analyzer auf einem IoT Edge-Gerät: Azure'
description: In diesem Artikel werden Schritte aufgelistet, die Sie bei der Bereitstellung von Azure Video Analyzer auf Ihrem IoT Edge-Gerät unterstützen. Dies können Sie beispielsweise tun, wenn Sie Zugriff auf einen lokalen Linux-Computer besitzen.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bd045a6cbc1d85790119564a00783b74f759b97
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556595"
---
# <a name="deploy-azure-video-analyzer-to-an-iot-edge-device"></a>Bereitstellen von Azure Video Analyzer auf einem IoT Edge-Gerät

[!INCLUDE [header](includes/edge-env.md)]

In diesem Artikel wird beschrieben, wie Sie das Azure Video Analyzer-Edgemodul auf einem IoT Edge-Gerät bereitstellen können, auf dem noch keine anderen Module installiert wurden. Wenn Sie die Schritte in diesem Artikel ausgeführt haben, haben Sie ein Video Analyzer-Konto erstellt und das Video Analyzer-Modul auf Ihrem IoT Edge Gerät zusammen mit einem Modul, das eine RTSP-fähige IP-Kamera simuliert, bereitgestellt. Der Prozess ist für die Verwendung mit den Schnellstarts und Tutorials für Video Analyzer vorgesehen. Wenn Sie beabsichtigen, das Video Analyzer-Modul für die Verwendung in der Produktion bereitzustellen, lesen Sie den Artikel zur [Produktionsbereitschaft und zu den bewährten Methoden](production-readiness.md).

> [!NOTE]
> Mit dem in diesem Artikel beschriebenen Prozess werden Edgemodule, die auf Ihrem IoT Edge-Gerät installiert sind, ggf. deinstalliert.

## <a name="prerequisites"></a>Voraussetzungen

* Ein x86-64- oder ARM64-Gerät, auf dem eines der [unterstützten Linux-Betriebssysteme ausgeführt wird](../../../iot-edge/support.md#operating-systems)
* Ein Azure-Konto mit einem aktiven Abonnement
* [Ein erstellter und eingerichteter IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md)
* [Registrierung bei einem IoT Edge-Gerät](../../../iot-edge/how-to-register-device.md)
* [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../../../iot-edge/how-to-install-iot-edge.md)


## <a name="create-resources-on-iot-edge-device"></a>Erstellen von Ressourcen auf dem IoT Edge-Gerät

Das Azure Video Analyzer-Modul sollte für die Ausführung auf dem IoT Edge-Gerät mit einem nicht privilegierten lokalen Benutzerkonto konfiguriert werden. Für das Modul werden bestimmte lokale Ordner benötigt, um die Daten für die Anwendungskonfiguration zu speichern. Für diese Schrittanleitung wird ein [RTSP-Simulator](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) verwendet, mit dem ein Videofeed in Echtzeit zur Analyse an ein AVA-Modul weitergeleitet wird. Für diesen Simulator werden als Eingabe vorab aufgezeichnete Videodateien aus einem Eingabeverzeichnis verwendet. Das folgende Skript bereitet Ihr Gerät auf die Verwendung mit unseren Schnellstarts und Tutorials vor.

https://aka.ms/ava/prepare-device

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

Das oben verwendete Skript „prep-device“ automatisiert die Erstellung von Eingabe- und Konfigurationsordnern, das Herunterladen von Videoeingabedateien und das Erstellen von Benutzerkonten mit den richtigen Berechtigungen. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollten auf Ihrem Edgegerät die folgenden Ordner erstellt worden sein. 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    Beachten Sie die Videodateien („*.mkv“) im Ordner „/home/localedgeuser/samples/input“, die zum Simulieren des Livevideos verwendet werden. 

## <a name="creating-azure-resources-and-deploying-edge-modules"></a>Erstellen von Azure-Ressourcen und Bereitstellen von Edgemodulen
Im nächsten Schritt erstellen Sie die erforderlichen Azure-Ressourcen (Video Analyzer-Konto, Speicherkonto, benutzerseitig zugewiesene verwaltete Identität), registrieren ein Video Analyzer-Edgemodul mit dem Video Analyzer-Konto und stellen das Video Analyzer-Edgemodul und das RTSP-Simulatormodul auf dem IoT Edge-Gerät bereit.

Klicken Sie auf die Schaltfläche **In Azure bereitstellen**.

> [!WARNING]
> Verwenden Sie dies nicht mit IoT Edge-Geräten, auf denen bereits Edgemodule installiert sind, z. B. Percept DK. Wird außerdem nicht mit Azure Stack Edge unterstützt.

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava/click-to-deploy/form)

1. Wählen Sie Ihr **Abonnement** aus.
2. Wählen Sie Ihre bevorzugte **Region** aus.
3. Wählen Sie die **Ressourcengruppe** aus, zu der IoT Hub und IoT Edge gehören.
4. Wählen Sie im Dropdownmenü für **Benötigen Sie ein Edgegerät?** die Option **_Vorhandenes Edgegerät verwenden_** aus.
5. Klicken Sie auf **Weiter**.
![Screenshot des Ausgangsbereitstellungsformulars](./media/deploy-iot-edge-device/project-details.png)

1. Wählen Sie den **Namen des vorhandenen IoT Hub** aus, mit dem Ihr IoT Edge-Gerät verbunden ist.
1. Klicken Sie auf **Weiter**.
![Screenshot des zweiten Bereitstellungsformulars](./media/deploy-iot-edge-device/iot-hub-name.png)

1. Klicken Sie auf der letzten Seite auf **Erstellen**

Es kann einen Moment dauern, bis die Azure-Ressourcen erstellt und die Edgemodule bereitgestellt wurden.

### <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

Navigieren Sie nach dem Erstellen der Bereitstellung im Azure-Portal zur Seite des IoT Edge-Geräts Ihres IoT Hub.

1. Wählen Sie das IoT Edge-Zielgerät für die Bereitstellung aus, um dessen Details zu öffnen.
2. Vergewissern Sie sich anhand der Gerätedetails, dass für die Module **In Bereitstellung angegeben und Vom Gerät gemeldet** aufgeführt ist.

Unter Umständen dauert es einen Moment, bis die Module auf dem Gerät gestartet und an IoT Hub gemeldet wurden. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen.
Statuscode: 200: OK bedeutet, dass [die IoT Edge-Runtime](../../../iot-edge/iot-edge-runtime.md) fehlerfrei ist und ordnungsgemäß funktioniert.

![Im Screenshot wird ein Statuswert für eine IoT Edge-Runtime angezeigt.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Aufrufen einer direkten Methode

Als nächstes testen Sie das Beispiel, indem Sie eine direkte Methode aufrufen. Ausführliche Informationen zu allen von Ihrem avaEdge-Modul bereitgestellten direkten Methoden finden Sie unter [Direkte Methoden für Azure Video Analyzer](direct-methods.md).

1. Wenn Sie auf das von Ihnen erstellte Edge-Modul klicken, gelangen Sie zu seiner Konfigurationsseite.  

    ![Im Screenshot wird die Konfigurationsseite eines Edge-Moduls angezeigt.](./media/deploy-iot-edge-device/modules.png)
1. Klicken Sie auf die Menüoption **Direkte Methode**.

    > [!NOTE] 
    > Sie müssen in den Abschnitten für die Verbindungszeichenfolge einen Wert hinzufügen, wie Sie auf der aktuellen Seite sehen können. Sie brauchen im Abschnitt **Einstellungsname** nichts auszublenden oder zu ändern. Es ist in Ordnung, diese Informationen öffentlich zu belassen.

    ![Direkte Methode](./media/deploy-iot-edge-device/module-details.png)
1. Geben Sie als Nächstes „pipelineTopologyList“ in die `Method Name`-Box ein.
1. Kopieren Sie anschließend die folgende JSON-Nutzlast, und fügen Sie sie in das Nutzlastfeld ein:
    
   ```
   {
       "@apiVersion": "1.1"
   }
   ```
1. Klicken Sie oben auf der Seite auf die Option **Methode aufrufen**.
1. Im `Result`-Feld sollte die Statusmeldung „200“ angezeigt werden.

    ![Die 200-Statusmeldung](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Erste Schritte – Azure Video Analyzer](get-started-detect-motion-emit-events.md)

> [!TIP]
> Wenn Sie mit der obigen Schnellstartanleitung fortfahren und die direkten Methoden mithilfe von Visual Studio Code aufrufen, verwenden Sie statt des standardmäßigen `avasample-iot-edge-device` das Gerät, das in diesem Artikel dem IoT Hub hinzugefügt wurde.
