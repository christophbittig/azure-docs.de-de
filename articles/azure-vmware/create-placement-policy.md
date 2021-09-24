---
title: Erstellen einer Platzierungsrichtlinie (Vorschau)
description: Hier erfahren Sie, wie Sie in Azure VMware Solution eine Platzierungsrichtlinie erstellen, um über das Azure-Portal die Platzierung von virtuellen Computern auf Hosts in einem Cluster zu steuern.
ms.topic: how-to
ms.date: 8/18/2021
ms.openlocfilehash: 85146ce86dea0d3cfa7397cdaae6fefc8cf4a23b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967542"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>Erstellen einer Platzierungsrichtlinie in Azure VMware Solution (Vorschau)

>[!IMPORTANT]
>Platzierungsrichtlinien in Azure VMware Solution befinden sich derzeit in der Vorschau. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Um das Vorschaufeature zu verwenden, [müssen Sie sowohl das Feature _DRS-Platzierungsrichtlinie_ als auch das Feature _Früher Zugriff_ registrieren](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).  Suchen Sie nach den Features, wählen Sie sie aus, und wählen Sie dann **Registrieren** aus.

In Azure VMware Solution sind Cluster in einer privaten Cloud eine verwaltete Ressource. Daher kann die Cloudadministratorrolle über den vSphere-Client bestimmte Änderungen am Cluster nicht durchführen. Dazu zählt auch die Verwaltung von DRS-Regeln (Distributed Resource Scheduler).

Das Feature „Platzierungsrichtlinie“ ist in allen Azure VMware Solution-Regionen verfügbar.  Mithilfe von Platzierungsrichtlinien können Sie die Platzierung von virtuellen Computern auf Hosts in einem Cluster über das Azure-Portal steuern. Wenn Sie eine Platzierungsrichtlinie erstellen, enthält diese eine DRS-Regel im angegebenen vSphere-Cluster. Sie enthält auch zusätzliche Logik für die Interoperabilität mit Azure VMware Solution-Vorgängen.

Eine Platzierungsrichtlinie besteht aus mindestens fünf erforderlichen Komponenten: 

- **Name**: Definiert den Namen der Richtlinie und unterliegt den Namenseinschränkungen von [Azure-Ressourcen](../azure-resource-manager/management/resource-name-rules.md).

- **Typ**: Definiert die Art der Steuerung, die Sie auf die in der Richtlinie enthaltenen Ressourcen anwenden möchten.

- **Cluster**: Definiert den Cluster für die Richtlinie. Der Gültigkeitsbereich einer Platzierungsrichtlinie ist ein vSphere-Cluster, daher können nur Ressourcen aus ein und demselben Cluster zur selben Platzierungsrichtlinie gehören.

- **Status**: Definiert, ob die Richtlinie aktiviert oder deaktiviert ist. In bestimmten Szenarien kann die Richtlinie automatisch deaktiviert werden, wenn eine damit in Konflikt stehende Regel erstellt wird. Weitere Informationen finden Sie weiter unten im Abschnitt [Überlegungen](#considerations).

- **Virtueller Computer**: Definiert die VMs und Hosts für die Richtlinie. Je nach Art der Regel, die Sie erstellen, erfordert die Richtlinie möglicherweise die Angabe einer bestimmten Anzahl von VMs und Hosts. Weitere Informationen finden Sie weiter unten unter [Platzierungsrichtlinientypen](#placement-policy-types).


## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Zugriff der Ebene _Mitwirkender_ auf die private Cloud, um Platzierungsrichtlinien zu verwalten.

- Die Features _DRS-Platzierungsrichtlinie_ und _Früher Zugriff_ [sind registriert](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).


## <a name="placement-policy-types"></a>Platzierungsrichtlinientypen

### <a name="vm-vm-policies"></a>VM-VM-Richtlinien

**VM-VM**-Richtlinien geben an, ob ausgewählte VMs auf demselben Host oder auf separaten Hosts ausgeführt werden sollen.  Bei einer **VM-VM**-Richtlinie müssen Sie nicht nur einen Namen und einen Cluster auswählen, sondern auch mindestens zwei VMs, denen die Richtlinie zugewiesen werden soll. Eine Zuweisung von Hosts ist für diesen Richtlinientyp weder erforderlich noch zulässig.

- Richtlinien von Typ **VM-VM-Affinität** weisen DRS an, zu versuchen, die angegebenen VMs auf demselben Host auszuführen. Dies kann beispielsweise aus Leistungsgründen nützlich sein.

- Richtlinien vom Typ **VM-VM-Antiaffinität** weisen DRS an, zu versuchen, die angegebenen VMs auf separaten Hosts auszuführen. Das ist in Szenarien nützlich, in denen ein Problem auf einem Host nicht mehrere VMs innerhalb derselben Richtlinie beeinträchtigen darf.


### <a name="vm-host-policies"></a>VM-Host-Richtlinien

Richtlinien vom Typ **VM-Host** geben an, ob ausgewählte VMs auf ausgewählten Hosts ausgeführt werden dürfen.  Um Konflikte mit plattformseitig verwalteten Vorgängen wie Hostwartungsmodus oder Hostaustausch zu vermeiden, sind **VM-Host**-Richtlinien in Azure VMware Solution immer fakultativ (sogenannte „Kann-Regeln“). Dementsprechend werden **VM-Host**-Richtlinien [in bestimmten Szenarien möglicherweise nicht angewendet](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html). Weitere Informationen finden Sie weiter unten unter [Überwachen der Anwendung einer Richtlinie](#monitor-the-operation-of-a-policy).

Bestimmte Plattformvorgänge aktualisieren die Liste der in **VM-Host**-Richtlinien definierten Hosts dynamisch. Wenn Sie beispielsweise einen Host löschen, der zu einer Platzierungsrichtlinie gehört, wird dieser Host entfernt, wenn mehr als ein Host zu dieser Richtlinie gehört. Auch wenn ein Host zu einer Richtlinie gehört und im Rahmen eines plattformseitig verwalteten Vorgangs ausgetauscht werden muss, wird die Richtlinie dynamisch mit dem neuen Host aktualisiert.

Für einen **VM-Host**-Richtlinie müssen Sie zusätzlich zum Namen und Cluster für die Richtlinie auch mindestens eine VM und einen Host auswählen, die der Richtlinie zugewiesen werden.

- Richtlinien vom Typ **VM-Host-Affinität** weisen DRS an, zu versuchen, die angegebenen VMs auf den definierten Hosts auszuführen.

- Richtlinien vom Typ **VM-Host-Antiaffinität** weisen DRS an, zu versuchen, die angegebenen VMs auf anderen als den definierten Hosts auszuführen.


## <a name="considerations"></a>Überlegungen

### <a name="cluster-scale-in"></a>Abskalieren eines Clusters

Azure VMware Solution versucht, bei Abskalierungsvorgängen für einen Cluster bestimmte DRS-Regelverletzungen zu vermeiden.

Sie können den letzten Host aus einer VM-Host-Richtlinie nicht entfernen. Wenn Sie den letzten Host aus einer Richtlinie entfernen müssen, können Sie das Problem beheben, indem Sie vor dem Entfernen dieses Hosts aus dem Cluster einen anderen Host zur Richtlinie hinzufügen. Alternativ dazu können Sie auch die Platzierungsrichtlinie löschen, bevor Sie den Host entfernen.

Eine VM-VM-Antiaffinität-Richtlinie darf nicht mehr VMs enthalten, als Hosts im Cluster vorhanden sind. Wenn durch das Entfernen eines Hosts weniger Hosts als VMs im Cluster verblieben, würden Sie eine Fehlermeldung erhalten, und der Vorgang würde verhindert. Sie können dieses Problem beheben, indem Sie zuerst VMs aus der Regel und dann den Host aus dem Cluster entfernen.


### <a name="rule-conflicts"></a>Regelkonflikte

Wenn beim Erstellen einer VM-VM-Richtlinie DRS-Regelkonflikte erkannt werden, wird diese Regel gemäß dem [standardmäßigen Verhalten von VMware-DRS-Regeln](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html) im deaktivierten Status erstellt. Weitere Informationen zum Anzeigen von Regelkonflikten finden Sie weiter unten unter [Überwachen der Anwendung einer Richtlinie](#monitor-the-operation-of-a-policy).



## <a name="create-a-placement-policy"></a>Erstellen einer Platzierungsrichtlinie

In Bezug auf die Anzahl von Richtlinien, die Sie erstellen können, gibt es keine definierte Obergrenze. Allerdings gilt: Je mehr Platzierungseinschränkungen Sie erstellen, desto schwieriger ist es für vSphere DRS, virtuelle Computer effektiv im Cluster zu verschieben und die von den Workloads benötigten Ressourcen bereitzustellen.      

Lesen Sie unbedingt die Anforderungen an den [Richtlinientyp](#placement-policy-types).

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** den Eintrag **Platzierungsrichtlinien** >  **+ Erstellen** aus.

   >[!TIP]
   >Sie können auch im Übersichtsbereich der Platzierungsrichtlinie die Option „Cluster“ und dann **Erstellen** auswählen.
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="Screenshot: alternative Option für das Erstellen einer Platzierungsrichtlinie":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="Screenshot: Starten des Prozesses zum Erstellen einer VM-VM-Platzierungsrichtlinie" lightbox="media/placement-policies/create-placement-policy.png":::


1. Geben Sie einen aussagekräftigen Namen an, und wählen Sie den Richtlinientyp und den Cluster aus, in dem die Richtlinie erstellt werden soll. Wählen Sie anschließend **Aktivieren** aus.

   >[!WARNING]
   >Wenn Sie die Richtlinie deaktivieren, werden die Richtlinie und die zugrunde liegende DRS-Regel erstellt, aber die Richtlinienaktionen werden so lange ignoriert, bis Sie die Richtlinie aktivieren. 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-1.png" alt-text="Screenshot: Optionen für die Platzierungsrichtlinie" lightbox="media/placement-policies/create-placement-policy-vm-host-affinity-1.png":::   

1. Wenn Sie **VM-Host-Affinität** oder **VM-Host-Antiaffinität** als Typ ausgewählt haben, wählen Sie **+ Host hinzufügen** sowie die Hosts aus, die in die Richtlinie aufgenommen werden sollen. Sie können mehrere Hosts auswählen.

   >[!NOTE]
   >Der Bereich zum Auswählen von Hosts zeigt, wie viele VM-Host-Richtlinien dem Host zugeordnet sind, sowie die Gesamtzahl von VMs, die in diesen zugeordneten Richtlinien enthalten sind.
   >
   >:::image type="content" source="media/placement-policies/hosts-associated-policies-vms.png" alt-text="Screenshot: Anzahl der dem Host zugeordneten VM-Host-Richtlinien sowie die Anzahl der in diesen zugeordneten Richtlinien enthaltenen VMs":::


1. Wählen Sie **+ Virtuellen Computer hinzufügen** und die VMs aus, die in die Richtlinie aufgenommen werden sollen. Sie können mehrere VMs auswählen.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="Screenshot: Liste der VMs zur Auswahl":::
   
   >[!NOTE]
   >Der Bereich zum Auswählen von Hosts zeigt, wie viele VM-Host-Richtlinien dem Host zugeordnet sind, sowie die Gesamtzahl von VMs, die in diesen zugeordneten Richtlinien enthalten sind. 

1. Sobald Sie alle gewünschten VMs ausgewählt haben, wählen Sie **Virtuelle Computer hinzufügen** aus. 

1. Wählen Sie **Weiter: Überprüfen und erstellen** aus, um die Richtlinie zu überprüfen. 

1. Wählen Sie **Create policy** (Richtlinie erstellen) aus. Wenn Sie Änderungen vornehmen möchten, wählen Sie **Zurück: Grundlagen** aus.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="Screenshot: Einstellungen der Platzierungsrichtlinie vor der Erstellung":::

1. Nachdem die Platzierungsrichtlinie erstellt wurde, wählen Sie **Aktualisieren** aus, um sie in der Liste anzuzeigen.

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="Screenshot: Platzierungsrichtlinie nach der Erstellung aktiviert" lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>Bearbeiten einer Platzierungsrichtlinie

Sie können den Status einer Richtlinie ändern, eine neue Ressource hinzufügen oder die Zuweisung einer vorhandenen Ressource aufheben.

### <a name="change-the-policy-state"></a>Ändern des Richtlinienstatus

Sie können den Status einer Richtlinie zu **Aktiviert** oder **Nicht aktiviert** ändern. 

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** den Eintrag **Platzierungsrichtlinien** aus.

1. Wählen Sie für die Richtlinie, die Sie bearbeiten möchten, die Option **Mehr** (...) und dann **Bearbeiten** aus.

   >[!TIP]
   >Sie können eine Richtlinie in der Übersicht der Platzierungsrichtlinie deaktivieren, indem Sie **Deaktivieren** aus dem Dropdownmenü „Einstellungen“ auswählen. Die Aktivierung einer Richtlinie über das Dropdownmenü „Einstellungen“ ist nicht möglich.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Screenshot: Bearbeiten einer Platzierungsrichtlinie" lightbox="media/placement-policies/edit-placement-policy.png":::

1.  Wenn die Richtlinie aktiviert ist, aber deaktiviert werden soll, wählen Sie **Deaktivieren** und dann in der Bestätigungsmeldung erneut **Deaktivieren** aus. Im umgekehrten Fall, wenn die Richtlinie deaktiviert ist und aktiviert werden soll, wählen Sie **Aktivieren** aus.

1.  Wählen Sie **Überprüfen und aktualisieren** aus. 
 
1.  Überprüfen Sie die Änderungen, und wählen Sie **Richtlinie aktualisieren** aus. Wenn Sie Änderungen vornehmen möchten, wählen Sie **Zurück: Grundlagen** aus.


### <a name="update-the-resources-in-a-policy"></a>Aktualisieren der Ressourcen in einer Richtlinie

Sie können einer Richtlinie neue Ressourcen wie eine VM oder einen Host hinzufügen oder Ressourcen aus der Richtlinie entfernen. 

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** den Eintrag **Platzierungsrichtlinien** aus.

1. Wählen Sie für die Richtlinie, die Sie bearbeiten möchten, die Option **Mehr** (...) und dann **Bearbeiten** aus.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Screenshot: Bearbeiten der Ressourcen in einer Platzierungsrichtlinie" lightbox="media/placement-policies/edit-placement-policy.png":::

   - Um vorhandene Ressourcen zu entfernen, wählen Sie die gewünschten Ressourcen und dann **Zuweisung aufheben** aus. 

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="Screenshot: Entfernen einer vorhandenen Ressource aus einer Platzierungsrichtlinie":::

   - Um eine neue Ressource hinzufügen, wählen Sie **Virtuellen Computer bearbeiten** oder **Host bearbeiten** aus. Dann wählen Sie die Ressource, die Sie hinzufügen möchten, und danach **Speichern** aus. 

1. Wählen Sie **Weiter: Überprüfen und aktualisieren** aus. 

1. Überprüfen Sie die Änderungen, und wählen Sie **Richtlinie aktualisieren** aus.  Wenn Sie Änderungen vornehmen möchten, wählen Sie **Zurück: Grundlagen** aus.



## <a name="delete-a-policy"></a>Löschen einer Richtlinie

Sie können eine Platzierungsrichtlinie und die zugehörige DRS-Regel löschen. 

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud unter **Verwalten** den Eintrag **Platzierungsrichtlinien** aus.

1. Wählen Sie für die Richtlinie, die Sie bearbeiten möchten, die Option **Mehr** (...) und dann **Löschen** aus.

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="Screenshot: Löschen einer Platzierungsrichtlinie" lightbox="media/placement-policies/delete-placement-policy.png":::

1. Wählen Sie in der Bestätigungsmeldung **Löschen** aus.



## <a name="monitor-the-operation-of-a-policy"></a>Überwachen der Anwendung einer Richtlinie

Verwenden Sie den vSphere-Client, um die Vorgänge der DRS-Regel zu überwachen, die zu einer Platzierungsrichtlinie gehört. 

Als Inhaber der Cloudadministratorrolle können Sie die von einer Platzierungsrichtlinie erstellten DRS-Regeln auf der Registerkarte „Konfigurieren“ eines Clusters unter „VM-/Hostregeln“ anzeigen, aber nicht bearbeiten. Sie können auch zusätzliche Informationen anzeigen, beispielsweise ob die DRS-Regeln sich in einem Konfliktzustand befinden.

Darüber hinaus können Sie auf der Registerkarte „Überwachen“ des Clusters verschiedene DRS-Regelvorgänge überwachen, z. B. Empfehlungen und Fehler.


## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="are-these-the-same-as-drs-affinity-rules"></a>Sind diese Regeln dieselben wie DRS-Affinititätsregeln?
Ja und nein. vSphere DRS implementiert den aktuellen Richtliniensatz, aber wir haben die Funktionen etwas vereinfacht. Das Ändern von VM-Gruppen und Hostgruppen ist eine mühselige Angelegenheit, insbesondere weil Hosts von Natur aus vergänglich sind und in einer Cloudumgebung schnell ausgetauscht werden können. Wenn Hosts im vSphere-Bestand einer lokalen Umgebung ausgetauscht werden, muss der vSphere-Administrator die Hostgruppe ändern, um sicherzustellen, dass die gewünschten Einschränkungen für die VM-Host-Platzierung weiter gelten. Platzierungsrichtlinien in Azure VMware Solution aktualisieren die Hostgruppen, wenn ein Host rotiert oder geändert wird. Ähnliches gilt für die Skalierung: Wenn Sie einen Cluster abskalieren, wird die Hostgruppe automatisch entsprechend aktualisiert. Damit entfällt für die Kunden der Aufwand für die Verwaltung von Hostgruppen.


### <a name="as-this-is-an-existing-functionality-available-in-vcenter-why-cant-i-use-it-directly"></a>Es handelt sich ja um eine vorhandene Funktionalität in vCenter, warum kann ich sie nicht direkt verwenden? 

Azure VMware Solution bietet eine private VMware-Cloud in Azure. In dieser verwalteten VMware-Infrastruktur verwaltet Microsoft die Cluster, Host, Datenspeicher und verteilten virtuellen Switches in der privaten Cloud. Gleichzeitig ist der Mandant für die Verwaltung der in der privaten Cloud bereitgestellten Workloads zuständig. Daher hat der Mandant, der die private Cloud verwaltet, [nicht dieselben Berechtigungen](concepts-identity.md), die einem VMware-Administrator in einer lokalen Bereitstellung zur Verfügung stehen. 

Darüber hinaus stellt das Fehlen des gewünschten Differenzierungsgrads in den vSphere-Berechtigungen einige Herausforderungen bei der Verwaltung der Platzierung der Workloads in der privaten Cloud dar. Beispielsweise können vSphere DRS-Regeln, die häufig lokal zum Definieren von Affinitäts- und Antiaffinitätsregeln verwendet werden, nicht unverändert in einer VMware-Cloudumgebung verwendet werden, da einige dieser Regeln die täglichen Vorgänge in der privaten Cloud blockieren können. Platzierungsrichtlinien bieten eine Möglichkeit, diese Regeln mithilfe des Azure VMware Solution-Portals zu definieren, wodurch die Notwendigkeit der Verwendung von DRS-Regeln umgangen wird. In Kombination mit einer vereinfachten Funktionalität können sie auch sicherstellen, dass die Regeln keine täglichen Wartungs- und Betriebsaufgaben in der Infrastruktur beeinträchtigen. 


###  <a name="what-caveats-should-i-know-about"></a>Welche Vorbehalte sollte ich kennen?

Die **MUSS**-Regeln des VM-Hosts werden nicht unterstützt, da sie Wartungsvorgänge blockieren. 

Die **KANN**-Regeln für VM-Hosts sind fakultativ, und vSphere DRS versucht, die Regeln soweit wie möglich umzusetzen. Gelegentlich kann vSphere DRS virtuelle Computer, die den **KANN**-Regeln für VM-Hosts unterliegen, per vMotion migrieren. So wird sichergestellt, dass die Workloads die Ressourcen erhalten, die sie benötigen. Das ist ein Standardverhalten von vSphere DRS, und das Feature der Platzierungsregeln ändert dieses zugrunde liegende Verhalten nicht.

Wenn Sie in Konflikt stehende Regeln erstellen, können diese Konflikte sich in vCenter zeigen, und die neu definierten Regeln treten möglicherweise nicht in Kraft. Das ist ein Standardverhalten von vSphere DRS, für das Protokolle in vCenter überwacht werden können.
