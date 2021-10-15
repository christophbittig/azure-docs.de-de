---
title: Überwachen Ihres Azure Stack Edge Pro-Geräts über das Kubernetes-Dashboard | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie auf das Kubernetes-Dashboard zugreifen und es zum Überwachen Ihres Azure Stack Edge Pro-Geräts verwenden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2021
ms.author: alkohli
ms.openlocfilehash: 30e46f9425f4015893c08b94382e87cfa93c8be8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363115"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Überwachen Ihres Azure Stack Edge Pro-GPU-Geräts über das Kubernetes-Dashboard

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie auf das Kubernetes-Dashboard zugreifen und es zum Überwachen Ihres Azure Stack Edge Pro-GPU-Geräts verwenden. Zum Überwachen des Geräts können Sie Diagramme im Azure-Portal verwenden, das Kubernetes-Dashboard anzeigen oder `kubectl`-Befehle über die PowerShell-Schnittstelle des Geräts ausführen. 

Dieser Artikel bezieht sich ausschließlich auf die Überwachungsaufgaben, die im Kubernetes-Dashboard ausgeführt werden können.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Zugreifen auf das Kubernetes-Dashboard auf Ihrem Gerät
> * Anzeigen von auf dem Gerät bereitgestellten Modulen
> * Abrufen der IP-Adresse für auf dem Gerät bereitgestellte Anwendungen
> * Anzeigen von Containerprotokollen für auf dem Gerät bereitgestellte Module


## <a name="about-kubernetes-dashboard"></a>Informationen zum Kubernetes-Dashboard

Das Kubernetes-Dashboard ist eine webbasierte Benutzeroberfläche, die Sie zur Problembehandlung Ihrer Containeranwendungen verwenden können. Das Kubernetes-Dashboard ist eine benutzeroberflächenbasierte Alternative zur Befehlszeile `kubectl` für Kubernetes. Weitere Informationen finden Sie unter [Kubernetes-Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Auf Ihrem Azure Stack Edge Pro-Gerät können Sie das Kubernetes-Dashboard im *schreibgeschützten* Modus verwenden, um eine Übersicht über die auf dem Azure Stack Edge Pro-Gerät ausgeführten Anwendungen, den Status von Kubernetes-Clusterressourcen und eventuelle auf dem Gerät aufgetretene Fehler anzuzeigen.

## <a name="access-dashboard"></a>Zugreifen auf das Dashboard

Das Kubernetes-Dashboard ist *schreibgeschützt* und wird auf dem Kubernetes-Masterknoten an Port 31000 ausgeführt. Gehen Sie wie folgt vor, um auf das Dashboard zuzugreifen: 

1. Wechseln Sie in der lokalen Benutzeroberfläche des Geräts zu **Gerät** und dann zu **Device endpoints** (Geräteendpunkte). 
1. Kopieren Sie den Endpunkt **Kubernetes-Dashboard**. Erstellen Sie einen DNS-Eintrag in der Datei `C:\Windows\System32\Drivers\etc\hosts` Ihres Clients, um eine Verbindung mit dem Kubernetes-Dashboard herzustellen. 

    `<IP address of the Kubernetes dashboard>    <Kubernetes dashboard endpoint suffix>` 
        
    ![Hinzufügen eines DNS-Eintrags für den Endpunkt „Kubernetes-Dashboard“](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/add-domain-name-service-entry-hosts-1.png) 

1. Wählen Sie in der Zeile für den Endpunkt **Kubernetes-Dashboard** die Option **Konfiguration herunterladen** aus. Durch diese Aktion wird eine `kubeconfig` heruntergeladen, mit der Sie auf das Dashboard zugreifen können. Speichern Sie die Datei `config.json` auf Ihrem lokalen System.   

1. Laden Sie das Kubernetes-Dashboardzertifikat von der lokalen Benutzeroberfläche herunter. 
    1. Wechseln Sie auf der lokalen Benutzeroberfläche des Geräts zu **Zertifikate**.
    1. Suchen Sie den Eintrag für das **Zertifikat des Endpunkts „Kubernetes-Dashboard“** . Wählen Sie rechts neben diesem Eintrag den Link **Herunterladen** aus, um das Zertifikat auf Ihr Clientsystem herunterzuladen, das Sie für den Zugriff auf das Dashboard verwenden möchten. 

    ![Herunterladen des Zertifikats für den Endpunkt „Kubernetes-Dashboard“](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/download-kubernetes-dashboard-endpoint-certificate-1.png)  

1. Installieren Sie das heruntergeladene Zertifikat auf dem Client. Wenn Sie einen Windows-Client verwenden, führen Sie die folgenden Schritte aus: 
    1. Wählen Sie das Zertifikat und dann im **Zertifikatimport-Assistenten** als Speicherort **Lokaler Computer** aus. 

        ![Installieren des Zertifikats auf Client 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Installieren Sie das Zertifikat auf dem lokalen Computer im vertrauenswürdigen Stammspeicher. 

        ![Installieren des Zertifikats auf Client 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 
1. Kopieren und verwenden Sie die Kubernetes-Dashboard-URL, um das Dashboard in einem Browser zu öffnen. Auf der Anmeldeseite des **Kubernetes-Dashboards**:
    
    1. Wählen Sie **kubeconfig** aus. 
    1. Wählen Sie die Auslassungspunkte ( **...** ) aus. Navigieren Sie zu der Datei `kubeconfig`, die Sie zuvor auf das lokale System heruntergeladen haben, und zeigen Sie auf sie. Wählen Sie **Anmelden** aus.
        ![Navigieren zur Datei „kubeconfig“](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Sie können jetzt das Kubernetes-Dashboard für Ihr Azure Stack Edge Pro-Gerät im schreibgeschützten Modus anzeigen.

    ![Hauptseite des Kubernetes-Dashboards](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Anzeigen des Modulstatus

Computingmodule sind Container mit implementierter Geschäftslogik. Sie können das Dashboard verwenden, um zu überprüfen, ob ein Computemodul auf dem Azure Stack Edge Pro-Gerät erfolgreich bereitgestellt wurde.

Gehen Sie auf dem Dashboard wie folgt vor, um den Modulstatus anzuzeigen:

1. Navigieren Sie im linken Bereich des Dashboards zu **Namespace**. Filtern Sie nach dem Namespace, in dem die IoT Edge-Module angezeigt werden, in diesem Fall **iotedge**.
1. Navigieren Sie im linken Bereich zu **Workloads > Bereitstellungen**.
1. Im rechten Bereich werden alle auf dem Gerät bereitgestellten Module angezeigt. In diesem Fall wurde das Modul „GettingStartedWithGPU“ auf dem Azure Stack Edge Pro-Gerät bereitgestellt. Sie können sehen, dass das Modul bereitgestellt wurde.

    ![Anzeigen der Modulbereitstellung](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Abrufen der IP-Adresse für Dienste oder Module

Sie können das Dashboard verwenden, um die IP-Adressen der Dienste oder Module abzurufen, die Sie außerhalb des Kubernetes-Clusters verfügbar machen möchten. 

Der IP-Adressbereich für diese externen Dienste wird über die lokale Webbenutzeroberfläche des Geräts auf der Seite **Compute network settings** (Computenetzwerkeinstellungen) zugewiesen. Nach dem Bereitstellen der IoT Edge-Module können Sie die einem bestimmten Modul oder Dienst zugewiesene IP-Adresse abrufen. 

Gehen Sie im Dashboard wie folgt vor, um die IP-Adresse abzurufen:

1. Navigieren Sie im linken Bereich des Dashboards zu **Namespace**. Filtern Sie nach dem Namespace, in dem ein externer Dienst bereitgestellt wird, in diesem Fall **iotedge**.
1. Navigieren Sie im linken Bereich zu **Discovery and Load balancing (Ermittlung und Lastenausgleich) > Dienste**.
1. Im rechten Bereich werden alle Dienste angezeigt, die im Namespace `iotedge` auf dem Azure Stack Edge Pro-Gerät ausgeführt werden.

    ![Abrufen der IP-Adresse für externe Dienste](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Containerprotokolle anzeigen

In bestimmten Fällen möchten Sie die Containerprotokolle anzeigen. Sie können das Dashboard verwenden, um Protokolle für einen bestimmten Container abzurufen, den Sie im Kubernetes-Cluster bereitgestellt haben.

Gehen Sie wie folgt vor, um die Containerprotokolle anzuzeigen:

1. Navigieren Sie im linken Bereich des Dashboards zu **Namespace**. Filtern Sie nach dem Namespace, in dem die IoT Edge-Module bereitgestellt werden, in diesem Fall **iotedge**.
1. Navigieren Sie im linken Bereich zu **Workloads > Pods**.
1. Im rechten Bereich werden alle auf dem Gerät ausgeführten Pods angezeigt. Suchen Sie den Pod, auf dem das Modul ausgeführt wird, dessen Protokolle Sie anzeigen möchten. Wählen Sie die vertikalen Auslassungspunkte für den entsprechenden Pod und dann im Kontextmenü die Option **Protokolle** aus.

    ![Anzeigen von Containerprotokollen 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Die Protokolle werden in einer im Dashboard integrierten Protokollanzeige angezeigt. Sie können die Protokolle auch herunterladen.

    ![Anzeigen von Containerprotokollen 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Anzeigen der CPU- und Speicherauslastung

Das Kubernetes-Dashboard für das Azure Stack Edge Pro-Gerät umfasst auch das [Metrics Server-Add-On](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/), mit dem die CPU- und Speicherauslastung der Kubernetes-Ressourcen aggregiert wird.
 
Beispielsweise können Sie die CPU- und Speicherauslastung der Bereitstellungen in allen Namespaces anzeigen. 

![Anzeigen der CPU- und Speicherauslastung aller Bereitstellungen](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Sie können auch nach einem bestimmten Namespace filtern. Im folgenden Beispiel können Sie die CPU- und Speicherauslastung nur für Azure Arc-Bereitstellungen anzeigen.  

![Anzeigen der CPU- und Speicherauslastung für Azure Arc-Bereitstellungen](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Der Kubernetes-Metrikserver ermöglicht die automatische Skalierung von Pipelines, etwa die [horizontale automatische Podskalierung](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr über die [Überwachung mit Azure Monitor](azure-stack-edge-gpu-enable-azure-monitor.md).
Hier erfahren Sie, wie [Sie Diagnosen ausführen und Protokolle sammeln](azure-stack-edge-gpu-troubleshoot.md).
