---
title: Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution
description: Erstellen Sie eine Inhaltsbibliothek zum Bereitstellen einer VM in einer privaten Azure VMware Solution-Cloud.
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356639"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Erstellen einer Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution

In einer Inhaltsbibliothek werden Inhalte in Form von Bibliothekselementen gespeichert und verwaltet. Ein einzelnes Bibliothekselement besteht aus Dateien, die Sie zum Bereitstellen virtueller Computer (VMs) verwenden.

In diesem Artikel erstellen Sie eine Inhaltsbibliothek im vSphere-Client und stellen dann einen virtuellen Computer mithilfe eines ISO-Images aus der Inhaltsbibliothek bereit.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial sind ein NSX-T-Segment und ein verwalteter DHCP-Dienst erforderlich.  Weitere Informationen finden Sie unter [Konfigurieren von DHCP für Azure VMware Solution](configure-dhcp-azure-vmware-solution.md).  

## <a name="create-a-content-library"></a>Erstellen einer Inhaltsbibliothek

1. Wählen Sie auf dem lokalen vSphere-Client **Menü** > **Inhaltsbibliotheken** aus.

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="Screenshot: Menüoption „Inhaltsbibliotheken“ im vSphere-Client":::

1. Wählen Sie zum Erstellen einer neuen Inhaltsbibliothek **Hinzufügen** aus.

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="Screenshot: Erstellen einer neuen Inhaltsbibliothek in vSphere.":::

1. Geben Sie einen Namen an, und bestätigen Sie die IP-Adresse des vCenter-Servers. Wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="Screenshot: Name und vCenter-Server-IP für die neue Inhaltsbibliothek.":::

1. Wählen Sie **Lokale Inhaltsbibliothek** und dann **Weiter** aus.

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="Screenshot: Option „Lokale Inhaltsbibliothek“ für die neue Inhaltsbibliothek ausgewählt.":::

1. Wählen Sie den Datenspeicher zum Speichern der Inhaltsbibliothek und dann **Weiter** aus.

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="Screenshot des ausgewählten vsanDatastore-Speicherorts.":::

1. Überprüfen Sie die Einstellungen der Inhaltsbibliothek, und wählen Sie dann **Fertig stellen** aus.

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="Screenshot, der die Einstellungen für die neue Inhaltsbibliothek zeigt":::.

## <a name="upload-an-iso-image-to-the-content-library"></a>Hochladen eines ISO-Images in die Inhaltsbibliothek

Nachdem Sie die Inhaltsbibliothek erstellt haben, können Sie ein ISO-Image hinzufügen, um eine VM in einem privaten Cloudcluster bereitzustellen. 

1. Wählen Sie auf dem vSphere-Client **Menü** > **Inhaltsbibliotheken** aus.

1. Klicken Sie mit der rechten Maustaste auf die Inhaltsbibliothek, die Sie für das neue ISO-Image verwenden möchten, und wählen Sie **Element importieren** aus.

1. Importieren Sie ein Bibliothekselement für die Quelle mithilfe einer der folgenden Aktionen, und wählen Sie dann **Importieren** aus:
   1. Wählen Sie „URL“ aus, und geben Sie eine URL zum Herunterladen eines ISO-Images an.

   1. Wählen Sie die **Lokale Datei** aus, die vom lokalen System hochgeladen werden soll.

   > [!TIP]
   > Optional können Sie einen benutzerdefinierten Elementnamen und Hinweise für das Ziel definieren.

1. Öffnen Sie die Bibliothek, und wählen Sie die Registerkarte **Andere Typen** aus, um zu überprüfen, ob Ihr ISO-Image erfolgreich hochgeladen wurde.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Bereitstellen einer VM in einem privaten Cloudcluster

1. Wählen Sie auf dem vSphere-Client **Menü** > **Hosts und Cluster** aus.

1. Erweitern Sie die Struktur im linken Bereich, und wählen Sie einen Cluster aus.

1. Wählen Sie **Aktionen** > **Neuer virtueller Computer** aus.

1. Durchlaufen Sie den Assistenten, und ändern Sie die gewünschten Einstellungen.

1. Wählen Sie **Neues CD-/DVD-Laufwerk** > **Clientgerät** > **ISO-Datei der Inhaltsbibliothek** aus.

1. Wählen Sie das im vorherigen Abschnitt hochgeladene ISO-Image und dann **OK** aus.

1. Aktivieren Sie das Kontrollkästchen **Verbinden**, damit das ISO-Image beim Einschalten bereitgestellt wird.

1. Wählen Sie **Neues Netzwerk** > **Dropdownliste auswählen** > **Durchsuchen** aus.

1. Wählen Sie den **logischen Switch (Segment)** und dann **OK** aus.

1. Ändern Sie beliebige andere Hardwareeinstellungen, und wählen Sie **Weiter** aus.

1. Überprüfen Sie die Einstellungen, und wählen Sie **Fertig stellen** aus.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Inhaltsbibliothek zum Bereitstellen von VMs in Azure VMware Solution erstellt haben, informieren Sie sich über die folgenden Themen:

- [Migrieren von VM-Workloads in Ihre Private Cloud](configure-vmware-hcx.md)
- [Integrieren von nativen Azure-Diensten in Azure VMware Solution](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
