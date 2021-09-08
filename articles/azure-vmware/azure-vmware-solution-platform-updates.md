---
title: Plattformupdates für Azure VMware Solution
description: Erfahren Sie mehr über die Plattformupdates für Azure VMware Solution.
ms.topic: reference
ms.date: 07/23/2021
ms.openlocfilehash: 01667e4a22dc220e9895071d047210e8487ff2bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355972"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Plattformupdates für Azure VMware Solution

Die Azure VMware Solution wird ab März 2021 wichtige Aktualisierungen anwenden. Sie erhalten eine Benachrichtigung über Azure Service Health, die den Zeitplan der Wartung enthält. Weitere Informationen finden Sie unter [Wartung des Hosts und Lebenszyklusmanagement](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).

## <a name="july-23-2021"></a>23. Juli 2021

Alle neuen privaten Azure VMware Solution-Clouds werden jetzt mit der NSX-T-Version [!INCLUDE [nsxt-version](includes/nsxt-version.md)] bereitgestellt. Für die NSX-T-Version in bestehenden privaten Clouds wird bis September 2021 ein Ugrade auf die NSX-T-Version [!INCLUDE [nsxt-version](includes/nsxt-version.md)] durchgeführt.
 
Sie erhalten eine E-Mail mit dem Datum und der Uhrzeit der geplanten Wartung. Sie können ein Upgrade erneut planen. Die E-Mail enthält auch Details über die aktualisierte Komponente, ihre Auswirkungen auf Arbeitslasten, den Zugriff auf die Private Cloud und andere Azure-Dienste. 

Weitere Informationen zu dieser NSX-T-Version finden Sie in den [Versionshinweisen zum VMware NSX-T Data Center [!INCLUDE [nsxt-version](includes/nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html).




## <a name="may-25-2021"></a>25. Mai 2021
Laut VMware-Sicherheitsempfehlung [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html) wurden mehrere Sicherheitsrisiken in VMware ESXi und vSphere Client (HTML5) an VMware gemeldet. 

Um die Sicherheitsrisiken ([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) und [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)) zu beheben, die in der VMware-Sicherheitsempfehlung [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html) gemeldet wurden, wurde vCenter Server in allen privaten Azure VMware Solution-Clouds aktualisiert.

Es ist keine weitere Aktion erforderlich.

## <a name="may-21-2021"></a>21. Mai 2021
 
Für den Azure VMware Solution-Dienst werden bis zum 23. Mai 2021 Wartungsarbeiten ausgeführt, um wichtige Updates auf den vCenter-Server in Ihrer privaten Cloud anzuwenden.  Sie erhalten eine Benachrichtigung über Azure Service Health, die den Zeitplan der Wartung für Ihre private Cloud enthält.
 
Während dieser Zeit ist VMware vCenter nicht verfügbar, und Sie können keine VMs verwalten (anhalten, starten, erstellen oder löschen). Es wird empfohlen, während dieser Zeit keine weiteren Aktivitäten wie das Hochskalieren der privaten Cloud, das Erstellen neuer Netzwerke usw. in Ihrer privaten Cloud zu planen.
 
Auf Workloads, die in Ihrer privaten Cloud ausgeführt werden, hat dies keine Auswirkungen.


## <a name="april-26-2021"></a>26. April 2021
Alle neuen Azure VMware Solution Private Clouds werden jetzt mit VMware vCenter Version 6.7U3l und NSX-T Version 2.5.2 implementiert. Wir nutzen NSX-T 3.1.1 nicht für neue Private Clouds, da in NSX-T 3.1.1 ein Problem identifiziert wurde, welches die VM-Konnektivität des Kunden beeinträchtigt. 

Die empfohlene Abschwächung seitens VMware wurde auf alle bestehenden Private Clouds angewendet, in denen derzeit NSX-T 3.1.1 auf Azure VMware Solution ausgeführt wird. Es wurde bestätigt, dass der Workaround keine Auswirkungen auf die VM-Konnektivität des Kunden hat.

## <a name="march-24-2021"></a>24. März 2021
Alle neuen Azure VMware Solution Private Clouds werden mit VMware vCenter Version 6.7U3l und NSX-T Version 3.1.1 implementiert. Alle bestehenden privaten Clouds werden **bis Juni 2021** auf die oben genannten Versionen aktualisiert und aufgerüstet.

Sie erhalten eine E-Mail mit dem Datum und der Uhrzeit der geplanten Wartung. Sie können ein Upgrade erneut planen. Die E-Mail enthält auch Details über die aktualisierte Komponente, ihre Auswirkungen auf Arbeitslasten, den Zugriff auf die Private Cloud und andere Azure-Dienste.  Eine Stunde vor dem Upgrade erhalten Sie eine Benachrichtigung und dann erneut, sobald Sie abgeschlossen ist.

## <a name="march-15-2021"></a>15. März 2021 

- Der Azure VMware Solution Service wird **bis zum 19. März 2021** Wartungsarbeiten durchführen, um den vCenter-Server in Ihrer privaten Cloud auf die Version vCenter Server 6.7 Update 3l zu aktualisieren.

- Während dieser Zeit ist VMware vCenter nicht verfügbar, sodass Sie Ihre VMs nicht verwalten (stoppen, starten, erstellen, löschen) oder die private Cloud skalieren können (Hinzufügen/Entfernen von Servern und Clustern). Die VMware High Availability (HA) wird jedoch weiterhin betrieben, um bestehende VMs zu schützen. 
 
Weitere Informationen zu dieser vCenter-Version finden Sie in den [Versionshinweisen zu VMware vCenter Server 6.7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4\. März 2021

- Die Azure VMware Solution wendet das [VMware ESXi 6.7, Patch Release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) **bis zum 15. März 2021** auf bestehende Privates an.

- Dokumentierte Workarounds für den vSphere-Stapel gemäß [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) werden ebenfalls bis zum **15. März 2021 angewendet**.

>[!NOTE]
>Diese Updates werden unterbrechungsfrei durchgeführt und sollten sich nicht auf Dienste oder Workloads in Azure VMware auswirken. Während der Wartung werden verschiedene VMware-Warnungen, z. B. _Lost network connectivity on DVPorts_ (Netzwerkverbindung für DVPorts getrennt) und _Lost uplink redundancy on DVPorts_ (Uplinkredundanz für DVPorts nicht mehr vorhanden), in vCenter angezeigt und mit Fortschreiten der Wartung automatisch gelöscht.

## <a name="post-update"></a>Nach dem Update
Nach Abschluss der Updates werden neuere Versionen der VMware-Komponenten angezeigt. Wenn Probleme auftreten oder Sie Fragen haben, erstellen Sie ein Supportticket, um unser Supportteam zu kontaktieren.
