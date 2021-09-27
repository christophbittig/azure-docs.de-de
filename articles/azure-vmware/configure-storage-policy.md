---
title: Konfigurieren der Speicherrichtlinie
description: Erfahren Sie, wie Sie die Speicherrichtlinie für Ihre virtuellen Azure VMware Solution-Computer konfigurieren.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: df8f6931dbae377833d3f380e3fd5fcad8bd73ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431330"
---
# <a name="configure-storage-policy"></a>Konfigurieren der Speicherrichtlinie

Die Speicheranforderungen für Ihre virtuellen Computer (VMs) werden mithilfe von vSAN-Speicherrichtlinien definiert. Diese Richtlinien steuern, wie Speicher dem virtuellen Computer zugeordnet wird, um die erforderliche Dienstebene für Ihre virtuellen Computer zu garantieren. Jeder in einem vSAN-Datenspeicher bereitgestellten VM wird mindestens eine VM-Speicherrichtlinie zugewiesen.

Sie können eine VM-Speicherrichtlinie zuweisen, wenn Sie eine VM erstmals bereitstellen oder wenn Sie andere VM-Vorgänge ausführen (z. B. Klonen oder Migrieren). CloudAdmin-Benutzer oder entsprechende Rollen nach der Bereitstellung können die Standardspeicherrichtlinie für eine VM nicht ändern. Änderungen der **VM-Speicherrichtlinie** pro Datenträger sind jedoch zulässig. 

Mit dem Befehl „Ausführen“ können autorisierte Benutzer die Standard- oder vorhandene VM-Speicherrichtlinie in eine verfügbare Richtlinie für eine VM nach der Bereitstellung ändern. An der Richtlinie für die Speicherung von VMs auf Datenträgerebene wurden keine Änderungen vorgenommen. Sie können die Richtlinie für die Speicherung von VMs auf Datenträgerebene jederzeit nach Ihren Anforderungen ändern.


>[!NOTE]
>Skriptausführungen erfolgen nacheinander in der übermittelten Reihenfolge.


In dieser Schrittanleitung erfahren Sie Folgendes:

> [!div class="checklist"]
> * Auflisten aller Speicherrichtlinien
> * Festlegen der Speicherrichtlinie für einen virtuellen Computer
> * Angeben der Speicherrichtlinie für einen Cluster



## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass die [Mindestanzahl von Hosts erfüllt ist](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html).

|  **RAID-Konfiguration** | **Zu tolerierende Fehler (Failures to Tolerate, FTT)** | **Mindestens erforderliche Hostanzahl** |
| --- | :---: | :---: |
| RAID-1 (Spiegelung) <br />Standardeinstellung.  | 1  | 3  |
| RAID-5 (Erasure Coding)  | 1  | 4  |
| RAID-1 (Spiegelung)  | 2  | 5  |
| RAID-6 (Erasure Coding)  | 2  | 6  |
| RAID-1 (Spiegelung)  | 3  | 7  |


 

## <a name="list-storage-policies"></a>Auflisten von Speicherrichtlinien

Sie führen das Cmdlet `Get-StoragePolicy` aus, um die vSAN-basierten Speicherrichtlinien aufzulisten, die für eine VM festgelegt werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Skriptausführung** > **Pakete** > **Get-StoragePolicies** aus.

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="Screenshot: Zugriff auf die verfügbaren Skriptausführungen für die Speicherrichtlinie" lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="Screenshot: Auflisten der verfügbaren Speicherrichtlinien":::
   
   | **Feld** | **Wert** |
   | --- | --- |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **Get-StoragePolicies-Exec1**. |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.




## <a name="set-storage-policy-on-vm"></a>Festlegen der Speicherrichtlinie auf einem virtuellen Computer

Sie führen das Cmdlet `Set-AvsVMStoragePolicy` zum Ändern von vSAN-basierten Speicherrichtlinien auf einem einzelnen virtuellen Computer aus. 

>[!NOTE]
>Sie können den vSphere-Client nicht verwenden, um die Standardspeicherrichtlinie oder vorhandene Speicherrichtlinien für eine VM zu ändern. 

1. Wählen Sie **Skriptausführung** > **Pakete** > **Set-AvsVMStoragePolicy** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **VMName** | Name der Ziel-VM. |
   | **StoragePolicyName** | Name der festzulegenden Speicherrichtlinie. Beispiel: **RAID-FTT-1**. |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **changeVMStoragePolicy**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.


## <a name="specify-storage-policy-for-a-cluster"></a>Angeben der Speicherrichtlinie für einen Cluster

Führen Sie das Cmdlet `Set-ClusterDefaultStoragePolicy` aus, um die Standardspeicherrichtlinie für einen Cluster anzugeben.

>[!NOTE]
>Das Ändern der Speicherrichtlinie des Standardverwaltungsclusters ist nicht zulässig.

1. Wählen Sie **Skriptausführung** > **Pakete** > **Set-ClusterDefaultStoragePolicy** aus.

1. Geben Sie die erforderlichen Werte an, oder ändern Sie die Standardwerte, und wählen Sie **Ausführen** aus.

   | **Feld** | **Wert** |
   | --- | --- |
   | **ClusterName** | Name des Clusters |
   | **StoragePolicyName** | Name der festzulegenden Speicherrichtlinie. Beispiel: **RAID-FTT-1**. |
   | **Beibehalten bis**  | Aufbewahrungszeitraum der Cmdlet-Ausgabe. Der Standardwert beträgt ist 60.  |
   | **Name für Ausführung angeben**  | Alphanumerischer Name, z. B. **Set-ClusterDefaultStoragePolicy-Exec1**.  |
   | **Timeout**  |  Der Zeitraum, nach dem ein Cmdlet beendet wird, wenn der Abschluss zu lange dauert.  |

1. Überprüfen Sie **Benachrichtigungen**, um den Fortschritt anzuzeigen.



## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt erfahren haben, wie vSAN-Speicherrichtlinien konfiguriert werden, können Sie mehr über Folgendes erfahren:

- [Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)](attach-disk-pools-to-azure-vmware-solution-hosts.md): Sie können Datenträger als beständigen Speicher für Azure VMware Solution verwenden, um die Kosten und die Leistung zu optimieren.

- [Konfigurieren externer Identitäten für vCenter](configure-identity-source-vcenter.md): vCenter verfügt über einen lokalen Benutzer namens „cloudadmin“, dem die Rolle „CloudAdmin“ zugewiesen ist. Mithilfe des lokalen cloudadmin-Benutzers werden Benutzer in Active Directory eingerichtet. Mit dem Feature „Skriptausführung“ können Sie Active Directory über LDAP oder LDAPS für vCenter als externe Identitätsquelle konfigurieren.
