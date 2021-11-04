---
title: Azure Stack Edge Pro FPGA-Compute-Netzwerkverwaltung für den Zugriff auf Module
description: In diesem Artikel wird beschrieben, wie das Compute-Netzwerk in Azure Stack Edge Pro FPGA erweitert wird, um über eine externe IP-Adresse auf Module zuzugreifen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 335b1429cceb60f99fada1b7eaf207fb4a64ce2d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088215"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Aktivieren des Computingnetzwerks auf Ihrem Azure Stack Edge Pro-Gerät

In diesem Artikel wird beschrieben, wie die in Azure Stack Edge Pro ausgeführten Module auf das auf dem Gerät aktivierte Computingnetzwerk zugreifen können.

Zum Konfigurieren des Netzwerks müssen Sie die folgenden Schritte vornehmen:

- Aktivieren einer Netzwerkschnittstelle auf Ihrem Azure Stack Edge Pro-Gerät für das Computing
- Hinzufügen eines Moduls für den Zugriff auf das Computingnetzwerk auf Ihrem Azure Stack Edge Pro-Gerät
- Überprüfen des Zugriffs auf die aktivierte Netzwerkschnittstelle durch das Modul

In diesem Tutorial verwenden Sie ein Webserver-App-Modul zur Veranschaulichung des Szenarios.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

- Sie besitzen ein Azure Stack Edge Pro-Gerät mit abgeschlossenem Gerätesetup.
- Sie haben den Schritt **Konfigurieren der Computeumgebung** unter [Tutorial: Transformieren von Daten mit Azure Stack Edge Pro für erweiterten Bereitstellungsablauf](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) auf Ihrem Gerät abgeschlossen. Ihrem Gerät sollte eine IoT Hub-Ressource, ein IoT-Gerät und ein IoT Edge-Gerät zugeordnet sein.

## <a name="enable-network-interface-for-compute"></a>Aktivieren einer Netzwerkschnittstelle für Computing

Sie müssen auf Ihrem Gerät eine IP-Adresse einer Netzwerkschnittstelle zuweisen, um über ein externes Netzwerk auf die Module zuzugreifen, die auf Ihrem Gerät ausgeführt werden. Sie können diese Computeeinstellungen über Ihre lokale Webbenutzeroberfläche verwalten.

Führen Sie zum Konfigurieren von Computeeinstellungen die folgenden Schritte auf der lokalen Webbenutzeroberfläche aus.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Konfiguration > Computeeinstellungen**.  

2. **Aktivieren** Sie die Netzwerkschnittstelle, über die Sie eine Verbindung mit einem Computemodul herstellen möchten, das Sie auf Ihrem Gerät ausführen.

    - Geben Sie bei Verwendung von statischen IP-Adressen eine IP-Adresse für die Netzwerkschnittstelle ein.
    - Bei Verwendung von DHCP werden die IP-Adressen automatisch zugewiesen. In diesem Beispiel wird DHCP verwendet.

    ![Aktivieren von Computeeinstellungen 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Wählen Sie zum Anwenden der Einstellungen die Option **Übernehmen** aus. Notieren Sie sich die IP-Adresse, die der Netzwerkschnittstelle zugewiesen ist, wenn Sie DHCP verwenden.

    ![Aktivieren von Computeeinstellungen](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Hinzufügen eines Webserver-App-Moduls

Führen Sie die folgenden Schritte aus, um ein Webserver-App-Modul zu Ihrem Azure Stack Edge Pro-Gerät hinzuzufügen.

1. Navigieren Sie zur IoT Hub-Ressource, die Ihrem Azure Stack Edge Pro-Gerät zugeordnet ist, und klicken Sie dann auf **IoT Edge-Gerät**.
2. Wählen Sie das IoT Edge Pro-Gerät aus, das Ihrem Azure Stack Edge-Gerät zugeordnet ist. Klicken Sie in den **Gerätedetails** auf **Module festlegen**. Klicken Sie unter **Module hinzufügen** auf **+ Hinzufügen**, und wählen Sie dann **IoT Edge-Modul** aus.
3. Gehen Sie auf dem Blatt **Benutzerdefinierte IoT Edge-Module** wie folgt vor:

    1. Legen Sie einen **Namen** für Ihr Webserver-App-Modul fest, das Sie bereitstellen möchten.
    2. Stellen Sie einen **Image-URI** für Ihr Modulimage bereit. Ein Modul, das dem angegebenen Namen und den Tags entspricht, wird abgerufen. In diesem Fall wird `mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` ein nginx-Image (als `1.15.5-alpine` markiert) aus der öffentlichen Registrierung `mcr.microsoft.com` pullen.
    3. Fügen Sie den folgenden Beispielcode in die **Optionen für Containererstellung** ein:  

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

        Mit dieser Konfiguration können Sie mithilfe der IP-Adresse des Computingnetzwerks per *HTTP* über den TCP-Port 8080 auf das Modul zugreifen (der Webserver-Standardport ist Port 80).

        ![Festlegen der Portinformationen auf dem Blatt „Benutzerdefiniertes IoT Edge-Modul“](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Wählen Sie **Speichern** aus.

## <a name="verify-module-access"></a>Überprüfen des Modulzugriffs

1. Überprüfen Sie, ob das Modul erfolgreich bereitgestellt wurde und ausgeführt wird. Der Laufzeitstatus des Moduls auf der Registerkarte **Module** der Seite **Gerätedetails** sollte **Wird ausgeführt** lauten.  
2. Stellen Sie eine Verbindung mit dem Webserver-App-Modul her. Öffnen Sie ein Browserfenster, und geben Sie Folgendes ein:

    `http://<compute-network-IP-address>:8080`

    Es sollte angezeigt werden, dass die Webserver-App ausgeführt wird.

    ![Überprüfen der Verbindung mit dem Modul über den festgelegten Port](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Benutzer über das Azure-Portal verwalten](azure-stack-edge-manage-users.md).
