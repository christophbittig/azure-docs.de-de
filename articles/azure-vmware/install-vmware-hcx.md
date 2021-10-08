---
title: Installieren von VMware HCX in Azure VMware Solution
description: Installieren Sie VMware HCX in Ihrer privaten Azure VMware Solution-Cloud.
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: e3db000661ea310c35d32d988db5cd2001290004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600815"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>Installieren und Aktivieren von VMware HCX in Azure VMware Solution

VMware HCX Advanced und der zugehörige Cloud-Manager werden nicht mehr vorab in Azure VMware Solution bereitgestellt. Stattdessen installieren Sie diese Komponenten über das Azure-Portal als Add-On. Sie laden dennoch die OVA-Datei des HCX-Connectors herunter und stellen das virtuelle Gerät auf Ihrem lokalen vCenter bereit. 

Jede Edition von VMware HCX unterstützt 25 Standortpaare (lokal/Cloud oder Cloud/Cloud).  Die Standardedition ist HCX Advanced. Sie können jedoch eine [Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support) eröffnen, damit HCX Enterprise Edition aktiviert ist, die sich derzeit in der öffentlichen Vorschau befindet. Sobald der Dienst allgemein verfügbar ist, haben Sie 30 Tage Zeit, um über Ihre nächsten Schritte zu entscheiden. Sie können auch den HCX Enterprise Edition-Dienst deaktivieren oder abbestellen, aber HCX Advanced behalten, da es Teil der Knotenkosten ist.

Ein Downgrade von HCX Enterprise Edition auf HCX Advanced ist ohne erneute Bereitstellung möglich. Stellen Sie zunächst sicher, dass Sie Ihre Konfiguration auf HCX Advanced zurückgesetzt haben und keine Enterprise-Features verwenden. Wenn Sie ein Downgrade planen, stellen Sie sicher, dass keine Migrationen geplant sind, Features wie RAV und [HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) nicht verwendet werden und maximal drei Standortpaare vorhanden sind.

>[!TIP]
>Sie können [HCX Advanced](#uninstall-hcx-advanced) auch über das Portal deinstallieren. Wenn Sie HCX Advanced deinstallieren, vergewissern Sie sich, dass keine aktiven Migrationen ausgeführt werden. Durch das Entfernen von HCX Advanced werden die von den virtuellen HCX-Geräten belegten Ressourcen in Ihre private Cloud zurückgeführt.

In dieser Schrittanleitung führen Sie die folgenden Schritte aus:

* Installieren von HCX Advanced über das Azure-Portal
* Herunterladen und Bereitstellen der OVA-Datei für den VMware HCX-Connector
* Aktivieren von HCX Advanced mit einem Lizenzschlüssel


Wenn Sie fertig sind, folgen Sie den empfohlenen nächsten Schritten am Ende, um mit den Schritten dieses Leitfadens für erste Schritte fortzufahren.

## <a name="prerequisites"></a>Voraussetzungen

- [Vorbereiten von HCX-Installationen](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- Wenn Sie VMware HCX Enterprise verwenden möchten, stellen Sie sicher, dass Sie das [ VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)-Add-On über eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support) aktiviert haben. Es handelt sich um eine kostenlose Testversion für zwölf Monate in Azure VMware Solution.

- [VMware-Blogreihe: Cloudmigration](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>Installieren von VMware HCX Advanced

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud die Option **Manage** > **Add-ons** (Verwalten > Add-Ons) aus.

1. Wählen Sie **Erste Schritte** für **HCX Workload Mobility** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="Screenshot: Schaltfläche „Erste Schritte“ für HCX Workload Mobility":::

1. Aktivieren Sie das Kontrollkästchen **I agree with terms and conditions** (Ich stimme den Geschäftsbedingungen zu), und wählen Sie dann **Install** (Installieren) aus.

   Die Installation von HCX Advanced und die Konfiguration von Cloud-Manager dauert etwa 35 Minuten. Nach der Installation werden auf der Registerkarte **Migration using HCX** (Migration mithilfe von HCX) die URL des HCX-Managers und die HCX-Schlüssel angezeigt, die für die Kopplung mit dem lokalen HCX-Connector benötigt werden.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="Screenshot: Registerkarte „Migration using HCX“ (Migration mithilfe von HCX) unter „Connectivity“ (Konnektivität)":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>Herunterladen und Bereitstellen der OVA-Datei für den VMware HCX-Connector 

In diesem Schritt laden Sie die OVA-Datei des VMware HCX-Connectors herunter und stellen dann den VMware HCX-Connector in Ihrem lokalen vCenter bereit.

1. Öffnen Sie ein Browserfenster, und melden Sie sich unter `https://x.x.x.9` (Port 443) mit den Benutzeranmeldeinformationen **cloudadmin\@vsphere.local** beim Azure VMware Solution HCX-Manager an.

1. Wählen Sie unter **Administration** > **System Updates** („Verwaltung“ > „Systemupdates“) die Option **Request Download Link** (Downloadlink anfordern) aus. Wenn das Feld abgeblendet ist, warten Sie einige Sekunden, bis ein Link generiert wurde.

1. Laden Sie die OVA-Datei für den VMware HCX-Connector, die Sie in Ihrem lokalen vCenter bereitstellen, entweder herunter oder rufen Sie einen entsprechenden Link dazu ab.

1. Wählen Sie in Ihrer lokalen vCenter-Instanz eine [OVF-Vorlage](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) aus, um den VMware HCX-Connector in Ihrer lokalen vCenter-Instanz bereitzustellen.

1. Navigieren Sie zu der heruntergeladenen OVA-Datei, wählen Sie sie aus, und wählen Sie dann **Öffnen** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot: Navigieren zu einer OVF-Vorlage." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. Wählen Sie einen Namen und einen Speicherort sowie eine Ressource oder einen Cluster aus, in der bzw. in dem der VMware HCX-Connector bereitgestellt werden soll. Überprüfen Sie anschließend die Details und die erforderlichen Ressourcen, und wählen Sie **Next** (Weiter) aus.

1. Lesen Sie die Lizenzbedingungen, wählen Sie den erforderlichen Speicher sowie das erforderliche Netzwerk und dann **Next** (Weiter) aus.

1. Wählen Sie das [VMware HCX-Verwaltungsnetzwerksegment](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) aus, das Sie während des Planungszustands definiert haben. Wählen Sie **Weiter** aus.  

1. Geben Sie unter **Customize template** (Vorlage anpassen) alle erforderlichen Informationen ein, und wählen Sie anschließend **Next** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot: Felder zum Anpassen einer Vorlage." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Führen Sie die Überprüfung durch, und wählen Sie anschließend **Finish** (Fertig stellen) aus, um die OVA-Datei für den VMware HCX-Connector bereitzustellen.

   >[!IMPORTANT]
   >Das virtuelle Gerät muss manuell eingeschaltet werden.  Warten Sie nach dem Einschalten zehn bis 15 Minuten, bevor Sie mit dem nächsten Schritt fortfahren.


## <a name="activate-vmware-hcx"></a>Aktivieren von VMware HCX

Nachdem Sie die OVA-Datei für den VMware HCX-Connector lokal bereitgestellt und die Gerät gestartet haben, sind Sie bereit für die Aktivierung. Zunächst müssen Sie einen Lizenzschlüssel aus dem Azure VMware Solution-Portal abrufen und ihn dann in VMware HCX Manager aktivieren. Schließlich benötigen Sie einen Schlüssel für jeden bereitgestellten lokalen HCX-Connector.

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud die Option **Manage** > **Add-ons** > **Migration using HCX** (Verwalten > Add-Ons > Migration mithilfe von HCX) aus. Kopieren Sie dann den **Aktivierungsschlüssel**.

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="Screenshot: Aktivierungsschlüssel":::   

1. Melden Sie sich mit den **Administratoranmeldeinformationen** beim lokalen VMware HCX Manager unter `https://HCXManagerIP:9443` an.  Geben Sie die Portnummer `9443` mit der IP-Adresse des VMware HCX Managers an.

   >[!TIP]
   >Das Benutzerkennwort für den **Administrator** wurde im Rahmen der Bereitstellung der OVA-Datei für den VMware-HCX-Manager definiert.

1. Geben Sie unter **Licensing** (Lizenzierung) Ihren Schlüssel für **HCX Advanced Key** (Erweiterter HCX-Schlüssel) ein, und wählen Sie **Activate** (Aktivieren) aus.

    >[!IMPORTANT]
    >Für den VMware HCX Manager muss offener Internetzugriff möglich oder ein Proxy konfiguriert sein.

1. Geben Sie unter **Datacenter Location** (Standort des Rechenzentrums) den nächstgelegenen Standort für die lokale Installation des VMware-HCX-Managers an. Klicken Sie anschließend auf **Weiter**.

1. Ändern Sie unter **System Name** (Systemname) den Namen, oder übernehmen Sie die Standardeinstellung, und wählen Sie anschließend **Continue** (Weiter) aus.

1. Wählen Sie **Yes, Continue** (Ja, fortsetzen) aus, um den Vorgang fortzusetzen.

1. Geben Sie in **Connect your vCenter** (Mit vCenter verbinden) den FQDN oder die IP-Adresse Ihres vCenter-Servers und die entsprechenden Anmeldeinformationen an, und wählen Sie dann **Continue** aus.

   >[!TIP]
   >Der vCenter-Server ist der Ort, an dem Sie den VMware HCX-Connector in Ihrem Rechenzentrum bereitgestellt haben.

1. Geben Sie in **Configure SSO/PSC** (SSO/PSC konfigurieren) den FQDN oder die IP-Adresse Ihres Platform Services Controllers an, und wählen Sie dann **Continue** (Weiter) aus.

   >[!NOTE]
   >In der Regel ist dieser Wert mit Ihrem vCenter-FQDN oder Ihrer IP-Adresse identisch.

1. Vergewissern Sie sich, dass die eingegebenen Informationen korrekt sind, und wählen Sie anschließend **Restart** (Neu starten) aus.

   >[!NOTE]
   >Nach dem Neustart tritt eine Verzögerung auf, bevor Sie zum Wechsel zum nächsten Schritt aufgefordert werden.

Nachdem der Dienst neu gestartet wurde, wird vCenter auf dem angezeigten Bildschirm mit einem grünen Punkt dargestellt. Sowohl vCenter als auch SSO müssen die geeigneten Konfigurationsparameter aufweisen, die mit denen im vorherigen Bildschirm identisch sein sollten.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot: Dashboard mit grünem vCenter-Status." lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>Deinstallieren von HCX Advanced

Sie können HCX Advanced über das Portal deinstallieren, wodurch die vorhandene Kopplung und Software entfernt wird. 

>[!NOTE]
>Es kann etwa 30 Minuten dauern, bis die von den virtuellen HCX-Geräten belegten Ressourcen in Ihre private Cloud zurückkehren. 

1. Vergewissern Sie sich, dass keine aktiven Migrationen ausgeführt werden.

1. Vergewissern Sie sich, dass L2-Erweiterungen nicht mehr benötigt werden oder dass die Netzwerke bis zum Zielort „entlastet“ worden sind. 

1. Stellen Sie bei Workloads, die MON verwenden, sicher, dass Sie die Standardgateways entfernt haben. Andernfalls kann dies dazu führen, dass Workloads nicht kommunizieren oder funktionieren können.  

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud die Option **Manage** > **Add-ons** (Verwalten > Add-Ons) aus.

1. Wählen Sie **Get started** (Erste Schritte) für **HCX Workload Mobility** und dann **Uninstall** (Deinstallieren) aus.
   
1. Geben Sie **yes** (Ja) ein, um die Deinstallation zu bestätigen.

Zu diesem Zeitpunkt verfügt HCX Advanced nicht mehr über das vCenter-Plug-In, das Sie bei Bedarf jederzeit neu installieren können.


## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Tutorial fort, um den VMware HCX-Connector zu konfigurieren.  Nachdem Sie den VMware HCX-Connector konfiguriert haben, verfügen Sie über eine produktionsbereite Umgebung zum Erstellen von virtuellen Computern (VMs) und zur Migration. 


>[!div class="nextstepaction"]
>[Konfigurieren von VMware HCX in Azure VMware Solution](configure-vmware-hcx.md)
