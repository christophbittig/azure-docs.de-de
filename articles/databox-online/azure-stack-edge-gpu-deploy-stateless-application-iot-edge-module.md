---
title: Bereitstellen einer zustandslosen Kubernetes-App auf einem Azure Stack Edge Pro-GPU-Gerät über ein IoT Edge-Modul | Microsoft-Dokumentation
description: Beschreibt die Bereitstellung einer zustandslosen Kubernetes-Anwendung auf Ihrem Azure Stack Edge Pro-GPU-Gerät unter Verwendung eines IoT Edge-Moduls, auf das über eine externe IP-Adresse zugegriffen wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 233afd9b103a250633c5e49776fc0dfda55a0389
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112247"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Verwenden des IoT Edge-Moduls zum Ausführen einer zustandslosen Kubernetes-Anwendung auf Ihrem Azure Stack Edge Pro-GPU-Gerät

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie mit einem IoT Edge-Modul eine zustandslose Anwendung auf Ihrem Azure Stack Edge Pro-Gerät bereitstellen.

Führen Sie die folgenden Schritte aus, um die zustandslose Anwendung bereitzustellen:

- Stellen Sie sicher, dass die Voraussetzungen erfüllt sind, bevor Sie ein IoT Edge-Modul bereitstellen.
- Fügen Sie für den Zugriff auf das Computenetzwerk auf Ihrem Azure Stack Edge Pro-Gerät ein IoT Edge-Modul hinzu.
- Überprüfen Sie den Zugriff auf die aktivierte Netzwerkschnittstelle durch das Modul.

In diesem Anleitungsartikel verwenden Sie ein Webserver-App-Modul zur Veranschaulichung des Szenarios.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

- Ein Azure Stack Edge Pro-Gerät muss vorhanden sein. Stellen Sie Folgendes sicher:

    - Die Einstellungen des Computingnetzwerks sind auf dem Gerät konfiguriert.
    - Das Gerät ist gemäß den Schritten im [Tutorial: Aktivieren Ihres Geräts](azure-stack-edge-gpu-deploy-activate.md) aktiviert.
- Sie haben den Schritt **Konfigurieren der Computeumgebung** unter [Tutorial: Konfigurieren von Compute auf einem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-configure-compute.md) auf Ihrem Gerät abgeschlossen. Ihrem Gerät sollte eine IoT Hub-Ressource, ein IoT-Gerät und ein IoT Edge-Gerät zugeordnet sein.


## <a name="add-webserver-app-module"></a>Hinzufügen eines Webserver-App-Moduls

Führen Sie die folgenden Schritte aus, um ein Webserver-App-Modul zu Ihrem Azure Stack Edge Pro-Gerät hinzuzufügen.

1. In der Ihrem Gerät zugeordneten IoT Hub-Ressource wechseln Sie zu **Automatische Geräteverwaltung > IoT Edge**.
1. Wählen Sie das IoT Edge-Gerät aus, das Ihrem Azure Stack Edge Pro-Gerät zugeordnet ist, und klicken Sie darauf. 

    ![Auswählen des IoT Edge-Geräts](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Wählen Sie **Module festlegen** aus. Wählen Sie unter **Module für Gerät festlegen** die Option **+ Hinzufügen** und dann **IoT Edge-Modul** aus.

    ![Auswählen des IoT Edge-Moduls](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. Unter **IoT Edge-Modul hinzufügen**:

    1. Legen Sie einen **Namen** für Ihr Webserver-App-Modul fest, das Sie bereitstellen möchten.
    2. Stellen Sie unter der Registerkarte **Moduleinstellungen** einen **Image-URI** für Ihr Modulimage bereit. Ein Modul, das dem angegebenen Namen und den Tags entspricht, wird abgerufen. In diesem Fall wird `mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` ein nginx-Image (als 1.15.5-alpine markiert) aus der öffentlichen Registrierung `mcr.microsoft.com` pullen.

        ![Hinzufügen eines IoT Edge-Moduls](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Fügen Sie den folgenden Beispielcode auf der Registerkarte **Optionen für Containererstellung** ein:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Mit dieser Konfiguration können Sie mithilfe der IP-Adresse des Computingnetzwerks per *HTTP* über den TCP-Port 8080 auf das Modul zugreifen (der Webserver-Standardport ist Port 80). Wählen Sie **Hinzufügen**.

        ![Festlegen der Portinformationen auf dem Blatt „Benutzerdefiniertes IoT Edge-Modul“](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Moduldetails, und wählen Sie **Erstellen** aus.

## <a name="verify-module-access"></a>Überprüfen des Modulzugriffs

1. Überprüfen Sie, ob das Modul erfolgreich bereitgestellt wurde und ausgeführt wird. Der Laufzeitstatus des Moduls auf der Registerkarte **Module** sollte **Wird ausgeführt** lauten.  

    ![Überprüfen der Aktivität des Modulstatus](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. [Greifen Sie auf das Kubernetes-Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard) zu, um den externen Endpunkt der Webserver-App abzurufen. 
1. Filtern Sie im linken Bereich des Dashboards nach dem Namespace **iotedge**. Wechseln Sie zu **Ermittlung und Lastenausgleich > Dienste**. Suchen Sie in der Liste der aufgeführten Dienste den externen Endpunkt für das Webserver-App-Modul. 

    ![Herstellen einer Verbindung mit einer Webserver-App am externen Endpunkt](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Wählen Sie den externen Endpunkt aus, um ein neues Browserfenster zu öffnen.

    Es sollte angezeigt werden, dass die Webserver-App ausgeführt wird.

    ![Überprüfen der Verbindung mit dem Modul über den festgelegten Port](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine zustandsbehaftete Anwendung über ein IoT Edge-Modul verfügbar machen.<!--insert link-->.
