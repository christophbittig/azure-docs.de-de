---
title: 'Azure VMware Solution by CloudSimple: Verwalten von VMs in der privaten Cloud in Azure'
description: Enthält Informationen zum Verwalten von VMs in der privaten CloudSimple-Cloud im Azure-Portal, einschließlich Hinzufügen von Datenträgern, Ändern der VM-Kapazität und Hinzufügen von Netzwerkschnittstellen.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4bbab40f094c11ab2f791352364e8e2e953b92a8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322643"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Verwalten Ihrer virtuellen Computer in der privaten CloudSimple-Cloud in Azure

Um die virtuellen Computer zu verwalten, die Sie für die [private CloudSimple-Cloud erstellt haben](azure-create-vm.md), melden Sie sich am [Azure-Portal](https://portal.azure.com) an. Suchen Sie nach dem virtuellen Computer, und wählen Sie ihn aus (suchen Sie im Menü unter **Alle Dienste** oder im seitlichen Menü nach **Virtuelle Computer**).

## <a name="control-virtual-machine-operation"></a>Steuern des Betriebs eines virtuellen Computers

Die folgenden Steuerelemente sind auf der Seite **Übersicht** für den ausgewählten virtuellen Computer verfügbar.

| Control | BESCHREIBUNG |
| ------------ | ------------- |
| Verbinden | Herstellen einer Verbindung mit dem angegebenen virtuellen Computer.  |
| Start | Starten des angegebenen virtuellen Computers.  |
| Neu starten | Herunterfahren und anschließendes Einschalten des angegebenen virtuellen Computers.  |
| Stop | Herunterfahren des jeweiligen virtuellen Computers.  |
| Erfassung | Erfassen eines Images der angegebenen VM, damit es als Image zum Erstellen anderer VMs verwendet werden kann. Weitere Informationen finden Sie unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md).   |
| Move | Verschieben des angegebenen virtuellen Computers.  |
| Löschen | Entfernen des angegebenen virtuellen Computers.  |
| Aktualisieren | Aktualisieren der Daten in der Anzeige.  |

### <a name="view-performance-information"></a>Anzeigen von Leistungsinformationen

In den Diagrammen im unteren Bereich der Seite **Übersicht** werden die Leistungsdaten für das ausgewählte Intervall (letzte Stunde bis letzte 30 Tage, der Standardwert ist die letzte Stunde) angezeigt. Innerhalb jedes Diagramms können Sie die numerischen Werte für beliebige Zeiten innerhalb des Intervalls anzeigen, indem Sie den Cursor auf dem Diagramm hin- und herbewegen.

Die folgenden Diagramme werden angezeigt.

| Element | BESCHREIBUNG |
| ------------ | ------------- |
| CPU (Durchschnitt) | Durchschnittliche CPU-Auslastung in Prozent während des ausgewählten Intervalls.   |
| Netzwerk | Datenverkehr in das und aus dem Netzwerk (MB) im ausgewählten Intervall.  |
| Datenträgerbytes | Die Gesamtmenge der vom Datenträger gelesenen und auf den Datenträger geschriebenen Daten (MB) im ausgewählten Intervall.  |
| Datenträgervorgänge | Die durchschnittliche Rate der Datenträgervorgänge (Vorgänge/Sek.) im ausgewählten Intervall. |

## <a name="manage-vm-disks"></a>Verwalten von VM-Datenträgern

Um einen VM-Datenträger hinzuzufügen, öffnen Sie die Seite **Datenträger** für den ausgewählten virtuellen Computer. Klicken Sie zum Hinzufügen eines Datenträgers auf **Datenträger hinzufügen**. Konfigurieren Sie jede der folgenden Einstellungen, indem Sie eine Inlineoption eingeben oder auswählen. Klicken Sie auf **Speichern**.

   | Element | BESCHREIBUNG |
   | ------------ | ------------- |
   | Name | Geben Sie einen Namen zur Identifizierung des Datenträgers ein.  |
   | Size | Wählen Sie eine der verfügbaren Größen aus.  |
   | SCSI-Controller | Wählen Sie einen SCSI-Controller aus. Die verfügbaren Controller sind für die unterstützten Betriebssysteme unterschiedlich.  |
   | Mode | Bestimmt, wie der Datenträger in Momentaufnahmen beteiligt ist. Wählen Sie eine der Optionen aus: <br> - Unabhängig, persistent: Alle Daten, die auf den Datenträger geschrieben werden, werden dauerhaft geschrieben.<br> - Unabhängig, nicht persistent: Auf dem Datenträger geschriebene Änderungen werden verworfen, wenn Sie die VM ausschalten oder zurücksetzen.  Dieser Modus ermöglicht es Ihnen, die VM immer im gleichen Zustand neu zu starten. Weitere Informationen finden Sie in der [VMware-Dokumentation](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Zum Löschen eines Datenträgers wählen Sie ihn aus und klicken dann auf **Löschen**.

## <a name="change-the-capacity-of-the-vm"></a>Ändern der Kapazität des virtuellen Computers

Um die Kapazität des virtuellen Computers zu ändern, öffnen Sie die Seite **Größe** für den ausgewählten virtuellen Computer. Geben Sie eine der folgenden Angaben an, und klicken Sie dann auf **Speichern**.

| Element | BESCHREIBUNG |
| ------------ | ------------- |
| Anzahl von Kernen | Anzahl der Kerne, die der VM zugewiesen sind.  |
| Hardwarevirtualisierung | Aktivieren Sie das Kontrollkästchen, um die Hardwarevirtualisierung für das Gastbetriebssystem bereitzustellen. Weitere Informationen finden Sie im VMware-Artikel [Verfügbarmachen der hardwareunterstützten VMware-Virtualisierung](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Arbeitsspeichergröße | Wählen Sie die Menge an Arbeitsspeicher aus, die dem virtuellen Computer zugewiesen werden soll.  

## <a name="manage-network-interfaces"></a>Verwalten von Netzwerkschnittstellen

Klicken Sie zum Hinzufügen einer Schnittstelle auf **Netzwerkschnittstelle hinzufügen**. Konfigurieren Sie jede der folgenden Einstellungen, indem Sie eine Inlineoption eingeben oder auswählen. Klicken Sie auf **Speichern**.

   | Control | BESCHREIBUNG |
   | ------------ | ------------- |
   | Name | Geben Sie einen Namen zur Identifizierung der Schnittstelle ein.  |
   | Netzwerk | Treffen Sie eine Auswahl in der Liste der konfigurierten Netzwerke für die vSphere-Instanz Ihrer privaten Cloud.  |
   | Adapter | Wählen Sie einen vSphere-Adapter aus der Liste der verfügbaren Typen, die für die VM konfiguriert sind. Weitere Informationen finden Sie im VMware-Knowledge Base-Artikel [Auswählen eines Netzwerkadapters für Ihren virtuellen Computer](https://kb.vmware.com/s/article/1001805). |
   | Einschalten beim Starten | Wählen Sie, ob die NIC-Hardware beim Booten der VM aktiviert werden soll. Die Standardeinstellung ist **Aktiviert**. |

Um eine Netzwerkschnittstelle zu löschen, wählen Sie sie aus, und klicken Sie dann auf **Löschen**.
