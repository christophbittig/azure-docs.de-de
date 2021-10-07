---
title: Bereitstellen von virtuellen Bitnami-Geräten
description: Erfahren Sie mehr über die virtuellen Bitnami-Geräte für die Bereitstellung in Ihrer privaten Azure VMware Solution-Cloud.
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700182"
---
# <a name="bitnami-appliance-deployment"></a>Bereitstellung der Bitnami-Appliance

Bitnami by VMware bietet einen umfangreichen Katalog von schlüsselfertigen virtuellen Geräten. Sie können jedes vSphere-kompatible Gerät von Bitnami bereitstellen, die im [VMware Marketplace](https://marketplace.cloud.vmware.com/) verfügbar ist, einschließlich vieler der gängigsten Open-Source-Softwareprojekte.

In diesem Artikel erfahren Sie, wie Sie die folgenden virtuellen Geräte installieren und konfigurieren, die von Bitnami für Ihre private Azure VMware Solution-Cloud gepackt werden:

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>Voraussetzungen

- Eine private Cloud einer Azure VMware Solution, die mit [mindestens drei Knoten bereitgestellt wird](deploy-azure-vmware-solution.md).

- Ein Netzwerk, das gemäß der Beschreibung unter [Checkliste für die Netzwerkplanung](tutorial-network-checklist.md) konfiguriert ist.



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>Schritt 1: Laden Sie die OVA/OVF-Datei des virtuellen Bitnami-Geräts herunter.


1. Wechseln Sie zum [VMware Marketplace](https://marketplace.cloud.vmware.com/), und laden Sie das virtuelle Gerät herunter, das Sie in Ihrer privaten Azure VMware Solution-Cloud installieren möchten:

   - [LAMP: Von Bitnami gepacktes virtuelles Gerät](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. Wählen Sie die Version und dann **Herunterladen** aus, und akzeptieren Sie anschließend die EULA-Lizenz (Lizenzvertrag). 

   >[!NOTE]
   >Stellen Sie sicher, dass vom virtuellen Computer aus auf die Datei zugegriffen werden kann.



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>Schritt 2: Greifen Sie auf die lokale vCenter-Instanz Ihrer privaten Cloud zu.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, wählen Sie Ihre private Cloud und dann **Verwalten** > **Identität** aus.

1. Kopieren Sie die vCenter-URL, den Benutzernamen und das Kennwort. Sie verwenden sie, um auf Ihren virtuellen Computer (VM) zuzugreifen. 

1. Wählen Sie **Übersicht** und dann die VM aus, und stellen Sie anschließend über RDP eine Verbindung her. Wenn Sie Hilfe beim Herstellen einer Verbindung benötigen, finden Sie ausführliche Informationen unter [Verbinden mit dem virtuellen Computer](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine).

1. Öffnen Sie in der VM einen Browser, und navigieren Sie zur vCenter-URL. 

1. Melden Sie sich mit den zuvor kopierten Benutzeranmeldeinformationen `cloudadmin@vsphere.local` an.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Screenshot: Anmeldeseite für VMware vSphere" border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>Schritt 3: Installieren der Bitnami OVA/OVF-Datei in vCenter

1. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie das virtuelle LAMP-Gerät installieren möchten, und wählen Sie **OVF-Vorlage bereitstellen** aus.

1. Wählen Sie **Lokale Datei** aus, und navigieren Sie zu der OVF-Datei, die Sie zuvor heruntergeladen haben. Wählen Sie **Weiter** aus.

1. Wählen Sie Ihr Rechenzentrum aus, und geben Sie einen Namen für Ihre VM des virtuellen Geräts an, z. B. **bitnami-lampstack**. Wählen Sie **Weiter** aus.

1. Wählen Sie den ESXi-Host als Computeressource aus, um Ihren virtuellen Computer auszuführen, und wählen Sie dann **Weiter** aus.

1. Überprüfen Sie die Angaben, und wählen Sie **Weiter** aus.

1. Akzeptieren Sie die Lizenzbedingungen, und wählen Sie **Weiter** aus.

1. Wählen Sie den Speicher für Ihren virtuellen Computer und dann **Weiter** aus.

1. Wählen Sie das Zielnetzwerk für Ihren virtuellen Computer und dann **Weiter** aus.

1. Geben Sie die erforderlichen Informationen an, um die Vorlage anzupassen, z. B. die VM und die Netzwerkeigenschaften. Wählen Sie **Weiter** aus.  

1. Überprüfen Sie die Konfigurationseinstellungen, und wählen Sie dann **Fertig stellen** aus.

1. Überprüfen Sie in der **Taskkonsole**, ob der Status der Bereitstellung der OVF-Vorlage erfolgreich abgeschlossen wurde.

1. Wenn die Installation abgeschlossen ist, wählen Sie unter **Aktionen** die Option **Einschalten** aus, um das Gerät einzuschalten. 

1. Wählen Sie in der vCenter-Konsole die Option **Webkonsole starten** aus, und melden Sie sich beim virtuellen Bitnami-Gerät an. Informationen zum Standardbenutzernamen und -kennwort finden Sie in der [Dokumentation zur Unterstützung virtueller Bitnami-Geräte](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/).

   >[!NOTE]
   >Sie können das Standardkennwort in ein sichereres Kennwort ändern. Weitere Informationen finden Sie unter:



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>Schritt 4. Weisen Sie eine statische IP-Adresse zum virtuellen Gerät zu.

In diesem Schritt ändern Sie die Parameter *bootproto* und *onboot* und weisen dem virtuellen Bitnami-Gerät eine statische IP-Adresse zu. 

1. Suchen Sie die Netzwerkkonfigurationsdatei. 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. Bearbeiten Sie die Datei *\/etc\/sysconfig\/network-scripts\/ifcfg-ens160*, und ändern Sie die Startparameter. Fügen Sie dann die statische IP-Adresse, die Netzmaske und die Gatewayadressen hinzu.

   - bootproto=static

   - onboot=yes


1. Prüfen und bestätigen Sie die Änderungen an der Datei **ifcfg-ens160**.

   ```bash
   cat ifcfg-ens160  
   ```

1. Starten Sie den Netzwerkdienst neu. Dadurch werden die Netzwerkdienste zuerst beendet und dann die IP-Konfiguration angewendet. 

   ```bash
   sudo systemctl restart network
   ```

1. Pingen Sie die Gateway-IP-Adresse, um die Konfiguration und VM-Konnektivität mit dem Netzwerk zu überprüfen.

1. Vergewissern Sie sich, dass die Standardroute 0.0.0.0 aufgeführt ist.

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>Schritt 5: Aktivieren Sie den SSH-Zugriff auf das virtuelle Gerät.

In diesem Schritt aktivieren Sie SSH auf Ihrem virtuellen Gerät für die Remotezugriffssteuerung. Der SSH-Dienst ist standardmäßig deaktiviert. Sie verwenden PuTTy auch für die Verbindung mit der Hostkonsole.

1. Aktivieren und starten Sie den SSH-Dienst.

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. Bearbeiten Sie die Datei *\/etc\/ssh\/sshd_config*, um das Kennwort für die Authentifizierung zu ändern.

   ```bash
   PasswordAuthentication yes
   ```

1. Prüfen und bestätigen Sie die Änderungen an der Datei **sshd_config**.

   ```bash
   sudo cat sshd_config
   ```

1. Laden Sie die an der Datei vorgenommenen Änderungen erneut. 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. Öffnen Sie PuTTY, wählen Sie die Option **SSH** aus, und geben Sie den Hostnamen und **22* für den Port an. Wählen Sie anschließend **Öffnen** aus. 

1. Geben Sie an der Eingabeaufforderung der Konsole des virtuellen Geräts den Bitnami-Benutzernamen und das Kennwort ein, um eine Verbindung mit dem Host herzustellen. 



