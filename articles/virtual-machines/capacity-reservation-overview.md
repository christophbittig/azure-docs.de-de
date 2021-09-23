---
title: Reservierung von Bedarfskapazitäten in Azure (Vorschau)
description: Erfahren Sie, wie Sie Computekapazität in einer Azure-Region oder einer Verfügbarkeitszone mit der Kapazitätsreservierung reservieren können.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: ee14ea525575a49abd4e4026201c3fa39ffa84b9
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273347"
---
# <a name="on-demand-capacity-reservation-preview"></a>Reservierung von Bedarfskapazitäten (Vorschau)

Mit der Reservierung von Bedarfskapazitäten können Sie Computekapazität für einen beliebigen Zeitraum in einer Azure-Region oder einer Verfügbarkeitszone reservieren. Im Gegensatz zu [reservierten Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) müssen Sie sich nicht für eine Laufzeit von einem Jahr oder drei Jahren registrieren. Erstellen und löschen Sie Reservierungen jederzeit, und üben Sie vollständige Kontrolle darüber aus, wie Sie Ihre Reservierungen verwalten möchten.  

Sobald die Kapazitätsreservierung erstellt wurde, ist die Kapazität sofort verfügbar und ausschließlich für Ihre Nutzung reserviert, bis die Reservierung gelöscht wird.  


> [!IMPORTANT]
> Die Kapazitätsreservierung befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Die Kapazitätsreservierung verfügt über einige grundlegende Eigenschaften, die immer zum Zeitpunkt der Erstellung definiert werden: 
- **VM-Größe** : Jede Reservierung gilt für eine VM-Größe. Beispielsweise `Standard_D2s_v3`. 
- **Standort** : Jede Reservierung gilt für einen Standort (Region). Wenn dieser Standort über Verfügbarkeitszonen verfügt, kann die Reservierung auch eine der Zonen angeben. 
- **Menge** : Jede Reservierung verfügt über eine bestimmte Anzahl von Instanzen, die reserviert werden sollen. 

Um eine Kapazitätsreservierung zu erstellen, werden diese Parameter als Kapazitätsanforderung an Azure übergeben. Wenn für das Abonnement das erforderliche Kontingent fehlt oder Azure keine Kapazität zur Verfügung hat, die der Spezifikation entspricht, kann die Reservierung nicht bereitgestellt werden. Um eine fehlgeschlagene Bereitstellung zu vermeiden, fordern Sie mehr Kontingent an, oder versuchen Sie eine andere VM-Größe, einen anderen Standort bzw. eine andere Zonenkombination. 

Sobald Azure eine Reservierungsanforderung akzeptiert, kann sie von VMs mit entsprechenden Konfigurationen genutzt werden. Um die Kapazitätsreservierung zu nutzen, muss die VM die Reservierung als eine ihrer Eigenschaften angeben. Andernfalls bleibt die Kapazitätsreservierung ungenutzt. Ein Vorteil dieses Entwurfs besteht darin, dass Sie nur kritische Workloads für Reservierungen vorsehen können und andere nicht kritische Workloads ohne reservierte Kapazität ausgeführt werden können.   

> [!NOTE]
> Die Kapazitätsreservierung enthält auch die Azure-Verfügbarkeits-SLA für die Verwendung mit virtuellen Computern. Die SLA wird während der öffentlichen Vorschau nicht erzwungen und erst definiert, wenn die Kapazitätsreservierung allgemein verfügbar ist.


## <a name="register-for-capacity-reservation"></a>Registrieren für die Kapazitätsreservierung 

Bevor Sie das Feature zur Kapazitätsreservierung verwenden können, müssen Sie Ihr Abonnement für die Vorschauversion registrieren. Die Registrierung kann mehrere Minuten dauern. Sie können die Azure CLI oder PowerShell verwenden, um die Featureregistrierung abzuschließen.

### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli1)

1. Verwenden Sie [az feature register](/cli/azure/feature#az_feature_register), um die Vorschauversion für Ihr Abonnement zu aktivieren:

    ```azurecli-interactive
    az feature register --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. Die Featureregistrierung kann bis zu 15 Minuten dauern. Überprüfen des Registrierungsstatus:

    ```azurecli-interactive
    az feature show --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. Schließen Sie den Aktivierungsvorgang ab, nachdem Sie das Feature für Ihr Abonnement registriert haben, indem Sie die Änderung an den Computeressourcenanbieter weitergeben:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Compute
    ``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Verwenden Sie das Cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature), um die Vorschauversion für Ihr Abonnement zu aktivieren:

    ```powershell-interactive
    Register-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. Die Featureregistrierung kann bis zu 15 Minuten dauern. Überprüfen des Registrierungsstatus:

    ```powershell-interactive
    Get-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. Schließen Sie den Aktivierungsvorgang ab, nachdem Sie das Feature für Ihr Abonnement registriert haben, indem Sie die Änderung an den Computeressourcenanbieter weitergeben:

    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ``` 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="benefits-of-capacity-reservation"></a>Vorteile der Kapazitätsreservierung 

- Nach der Bereitstellung ist die Kapazität für Ihre Nutzung reserviert und immer innerhalb des Bereichs der geltenden SLAs verfügbar.  
- Sie kann jederzeit ohne Laufzeitverpflichtung bereitgestellt und gelöscht werden. 
- Sie kann automatisch mit reservierten Instanzen kombiniert werden, um Laufzeitrabatte zu nutzen.  

## <a name="sla-for-capacity-reservation"></a>SLA für Kapazitätsreservierung 

Die SLA für die Kapazitätsreservierung wird später definiert, wenn das Feature allgemein verfügbar ist.  


## <a name="limitations-and-restrictions"></a>Einschränkungen 

- Das Erstellen von Kapazitätsreservierungen erfordert auf die gleiche Weise ein Kontingent wie das Erstellen virtueller Computer. 
- Spot-VMs und Azure Dedicated Host-Knoten werden bei der Kapazitätsreservierung nicht unterstützt. 
- Einige Bereitstellungseinschränkungen werden nicht unterstützt: 
    - Näherungsplatzierungsgruppe 
    - Updatedomänen 
    - UltraSSD-Speicher  
- Nur die VM-Serien Av2, B, D, E und F werden während der öffentlichen Vorschau unterstützt. 
- Für die unterstützte VM-Serien werden während der öffentlichen Vorschau bis zu drei Fehlerdomänen (FDs) unterstützt. Eine Bereitstellung mit mehr als 3 FDs kann nicht mit der Kapazitätsreservierung bereitgestellt werden. 
- Verfügbarkeitsgruppen werden bei der Kapazitätsreservierung nicht unterstützt. 
- Während dieser Vorschau kann die Reservierung nur von dem Abonnement verwendet werden, das sie erstellt hat. 
- Reservierungen sind nur für kostenpflichtige Azure-Kunden verfügbar. Unterstützte Konten wie die kostenlose Testversion und Azure for Students sind nicht berechtigt, dieses Feature zu verwenden. 


## <a name="pricing-and-billing"></a>Preise und Abrechnung 

Kapazitätsreservierungen werden mit der gleichen Rate wie die zugrunde liegende VM-Größe berechnet. Wenn Sie beispielsweise eine Reservierung für 10 D2s_v3-VMs erstellen, werden Ihnen nach der Erstellung der Reservierung 10 D2s_v3-VMs in Rechnung gestellt, auch wenn die Reservierung nicht verwendet wird.  

Wenn Sie dann eine D2s_v3-VM bereitstellen und die Reservierung als Eigenschaft angeben, wird die Kapazitätsreservierung verwendet. Sobald sie verwendet wird, zahlen Sie nur für die VM. Es entstehen keine Extrakosten für die Kapazitätsreservierung. Angenommen, Sie stellen fünf D2s_v3-VMs für die zuvor erwähnte Kapazitätsreservierung bereit. Es wird eine Rechnung für 5 D2s_v3-VMs und 5 ungenutzte Kapazitätsreservierungen angezeigt, die beide zum gleichen Tarif wie eine D2s_v3-VM berechnet werden.    

Sowohl die verwendete als auch die nicht verwendete Kapazitätsreservierung ist für Laufzeitrabatte für reservierte Instanzen berechtigt. Wenn Sie im Beispiel oben reservierte Instanzen für 2 D2s_v3-VMs in derselben Azure-Region haben, wird die Abrechnung für 2 Ressourcen (entweder VM oder ungenutzte Kapazitätsreservierung) auf Null gesetzt, und Sie zahlen nur für die restlichen 8 Ressourcen (d. h. 5 ungenutzte Kapazitätsreservierungen und 3 D2s_v3-VMs). In diesem Fall können die Laufzeitrabatte entweder auf die VM oder auf die ungenutzte Kapazitätsreservierung angewendet werden, die beide zum gleichen PAYG-Tarif in Rechnung gestellt werden. 


## <a name="work-with-capacity-reservation"></a>Arbeiten mit Kapazitätsreservierung 

Die Kapazitätsreservierung kann für eine bestimmte VM-Größe in einer Azure-Region oder einer Verfügbarkeitszone erstellt werden. Alle Reservierungen werden als Teil einer Kapazitätsreservierungsgruppe erstellt und verwaltet, wodurch die Erstellung einer Gruppe ermöglicht wird, um verschiedene VM-Größen in einer einzelnen Anwendung mit mehreren Ebenen zu verwalten. Jede Reservierung gilt für eine VM-Größe, und eine Gruppe kann nur eine Reservierung pro VM-Größe aufweisen.  

Geben Sie zum Nutzen der Kapazitätsreservierung die Kapazitätsreservierungsgruppe als eine der VM-Eigenschaften an. Wenn die Gruppe keine entsprechende Reservierung aufweist, gibt Azure eine Fehlermeldung zurück. 

Die für die Reservierung reservierte Menge kann nach der ersten Bereitstellung angepasst werden, indem die Kapazitätseigenschaft geändert wird. Andere Änderungen an der Kapazitätsreservierung, z. B. VM-Größe oder Standort, sind nicht zulässig. Der empfohlene Ansatz besteht darin, die vorhandene Reservierung zu löschen und eine neue Reservierung mit den neuen Anforderungen zu erstellen. 

Bei der Kapazitätsreservierung gelten keine Grenzwerte für die Anzahl von VM-Bereitstellungen. Azure unterstützt die Zuweisung so vieler VMs wie gewünscht für die Reservierung. Da die Reservierung selbst ein Kontingent erfordert, fallen die Kontingentüberprüfungen für die VM-Bereitstellung bis zur reservierten Menge weg. Die Zuordnung weiterer VMs zur Reservierung unterliegt Quotenprüfungen und setzt voraus, dass Azure die zusätzliche Kapazität erfüllt. Nach der Bereitstellung können diese zusätzlichen VM-Instanzen dazu führen, dass die Menge der VMs, die der Reservierung zugeordnet sind, die reservierte Menge überschreitet. Dieser Zustand wird als „Überlasten der Kapazitätsreservierung“ bezeichnet. Weitere Informationen finden Sie unter [Überlasten der Kapazitätsreservierung](capacity-reservation-overallocate.md). 


## <a name="capacity-reservation-lifecycle"></a>Lebenszyklus der Kapazitätsreservierung

Wenn eine Reservierung erstellt wird, reserviert Azure die angeforderte Anzahl von Kapazitätsinstanzen am angegebenen Standort: 

![Kapazitätsreservierung: Abbildung 1.](\media\capacity-reservation-overview\capacity-reservation-1.jpg) 

Verfolgen Sie den Status der Gesamtreservierung über die folgenden Eigenschaften nach:  
- `capacity` = Gesamtmenge der vom Kunden reservierten Instanzen 
- `virtualMachinesAllocated` = Liste der VMs, die für die Kapazitätsreservierung zugeordnet sind und für die Nutzung der Kapazität gezählt werden. Diese VMs weisen entweder den Status *Wird ausgeführt* oder *Beendet* (*Zugeordnet*) auf oder befinden sich möglicherweise in einem Übergangszustand wie *Wird gestartet* oder *Wird beendet*. Diese Liste enthält nicht die VMs, die den Status „Zuordnung aufgehoben“ aufweisen, auch als *Beendet* (*Zuordnung aufgehoben*) bezeichnet. 
- `virtualMachinesAssociated` = Liste der VMs, die der Kapazitätsreservierung zugeordnet sind. Diese Liste enthält alle VMs, die für die Verwendung der Reservierung konfiguriert wurden, einschließlich der VMs, die den Status „Zuordnung aufgehoben“ aufweisen.  

Das Beispiel oben beginnt mit `capacity` als 2 und einer Länge von `virutalMachinesAllocated` und `virtualMachinesAssociated` als 0.  

Wenn eine VM dann anhand der Kapazitätsreservierung zugeordnet wird, nutzt sie logisch eine der reservierten Kapazitätsinstanzen: 

![Kapazitätsreservierung: Abbildung 2.](\media\capacity-reservation-overview\capacity-reservation-2.jpg) 

Der Status der Kapazitätsreservierung zeigt nun `capacity` als 2 und eine Länge von `virutalMachinesAllocated` und `virtualMachinesAssociated` als 1 an.  

Zuweisungen anhand der Kapazitätsreservierung sind erfolgreich, solange die VMs entsprechende Eigenschaften aufweisen und mindestens eine leere Kapazitätsinstanz vorhanden ist.  

Wenn in unserem Beispiel eine dritte VM für die Kapazitätsreservierung zugeordnet wird, geht die Reservierung in den Status [Überlastet](capacity-reservation-overallocate.md) über. Diese dritte VM erfordert ein nicht genutztes Kontingent und zusätzliche Kapazitätserfüllung von Azure. Nachdem die dritte VM zugeordnet wurde, sieht die Kapazitätsreservierung jetzt wie folgt aus: 

![Kapazitätsreservierung: Abbildung 3.](\media\capacity-reservation-overview\capacity-reservation-3.jpg) 

`capacity` ist 2, und die Länge von `virutalMachinesAllocated` und `virtualMachinesAssociated` ist 3. 

Nehmen wir nun an, dass die Anwendung auf die Mindestanzahl von zwei VMs herunterskaliert wird. Da VM 0 ein Update benötigt, wird sie für die Belegungsfreigabe ausgewählt. Die Reservierung geht automatisch in diesen Status über: 

![Kapazitätsreservierung: Abbildung 4.](\media\capacity-reservation-overview\capacity-reservation-4.jpg) 

`capacity` und die Länge von `virtualMachinesAllocated` sind beide 2. Die Länge für `virtualMachinesAssociated` beträgt jedoch immer noch 3, weil VM 0 (obwohl ihre Zuordnung aufgehoben wurde) weiterhin der Kapazitätsreservierung zugeordnet ist.  

Die Kapazitätsreservierung bleibt vorhanden, bis sie explizit gelöscht wird. Um eine Kapazitätsreservierung zu löschen, besteht der erste Schritt im Trennen aller VMs in der `virtualMachinesAssociated`-Eigenschaft. Nach Abschluss der Trennung sollte die Kapazitätsreservierung wie folgt aussehen: 

![Kapazitätsreservierung: Abbildung 5.](\media\capacity-reservation-overview\capacity-reservation-5.jpg) 

Der Status der Kapazitätsreservierung zeigt nun `capacity` als 2 und die Länge von `virtualMachinesAssociated` und `virtualMachinesAllocated` als 0 an. In diesem Zustand kann die Kapazitätsreservierung gelöscht werden. Nach dem Löschen zahlen Sie nicht mehr für die Reservierung.  

![Kapazitätsreservierung: Abbildung 6.](\media\capacity-reservation-overview\capacity-reservation-6.jpg)


## <a name="usage-and-billing"></a>Nutzung und Abrechnung 

Wenn eine Kapazitätsreservierung leer ist, wird die VM-Nutzung für die entsprechende VM-Größe und den Standort gemeldet. [Reservierte VM-Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) können einen Teil oder die gesamte Kapazitätsreservierungsnutzung abdecken, auch wenn VMs nicht bereitgestellt werden. 

### <a name="example"></a>Beispiel

Angenommen, eine Kapazitätsreservierung mit der reservierten Menge 2 wurde erstellt. Das Abonnement hat Zugriff auf eine entsprechende reservierte VM-Instanz der gleichen Größe. Das Ergebnis sind zwei Nutzungsdatenströme für die Kapazitätsreservierung, von denen einer von der reservierten Instanz abgedeckt wird: 

![Kapazitätsreservierung: Abbildung 7.](\media\capacity-reservation-overview\capacity-reservation-7.jpg)

In der Abbildung oben wird ein Rabatt für eine reservierte VM-Instanz auf eine der ungenutzten Instanzen angewendet, und die Kosten für diese Instanz werden auf Null gesetzt. Für die andere Instanz wird der PAYG-Tarif für die reservierte VM-Größe berechnet.  

Wenn eine VM anhand der Kapazitätsreservierung zugeordnet wird, müssen auch die anderen VM-Komponenten wie Datenträger, Netzwerk, Erweiterungen und alle anderen angeforderten Komponenten zugeordnet werden. In diesem Zustand spiegelt die VM-Nutzung eine zugeordnete VM und eine nicht genutzte Kapazitätsinstanz wider. Die reservierte VM-Instanz setzt die Kosten für die VM oder die nicht genutzte Kapazitätsinstanz auf null. Die anderen Kosten für Datenträger, Netzwerke und andere Komponenten, die der zugeordneten VM zugeordnet sind, werden ebenfalls in der Rechnung angezeigt. 

![Kapazitätsreservierung: Abbildung 8.](\media\capacity-reservation-overview\capacity-reservation-8.jpg)

In der Abbildung oben wird der Rabatt für die reservierte VM-Instanz auf die VM 0 angewendet, für die nur andere Komponenten wie Datenträger und Netzwerk in Rechnung gestellt werden. Für die andere nicht verwendete Instanz wird die reservierte VM-Größe mit dem PAYG-Tarif in Rechnung gestellt.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen 

- **Wie hoch ist der Preis für die Reservierung von Bedarfskapazitäten?**

    Der Preis Ihrer Reservierung von Bedarfskapazitäten ist identisch mit dem Preis der zugrunde liegenden VM-Größe, die der Reservierung zugeordnet ist. Bei Verwendung von Kapazitätsreservierung wird Ihnen die VM-Größe in Rechnung gestellt, die Sie zu den Preisen für nutzungsbasierte Bezahlung ausgewählt haben, unabhängig davon, ob der virtuelle Computer bereitgestellt wurde.  Besuchen Sie die VM-Preisseiten für [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) und [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), um weitere Einzelheiten zu erfahren.

- **Werden mir die Kosten für die Reservierung von Bedarfskapazitäten und für die tatsächliche VM, wenn ich sie schließlich bereitstelle, doppelt in Rechnung gestellt?**

    Nein, die Reservierung von Bedarfskapazitäten wird nur ein Mal berechnet.   

- **Kann ich reservierte VM-Instanzen (RI) auf die Reservierung von Bedarfskapazitäten anwenden, um meine Kosten zu senken?**

    Ja, Sie können vorhandene oder zukünftige RIs auf die Reservierung von Bedarfskapazitäten anwenden und RI-Rabatte erhalten. Verfügbare RIs werden automatisch auf die gleiche Weise auf Kapazitätsreservierung angewendet wie auf VMs.

- **Worin besteht der Unterschied zwischen einer reservierten VM-Instanz (RI) und der Reservierung von Bedarfskapazitäten?**

    Sowohl RIs als auch die Reservierung von Bedarfskapazitäten gelten für Azure-VMs. RIs bieten jedoch im Vergleich zu nutzungsbasierter Bezahlung vergünstigte Reservierungstarife für Ihre VMs aufgrund einer Laufzeitverpflichtung (1 Jahr oder 3 Jahre). Im Gegensatz dazu ist für die Reservierung von Bedarfskapazitäten keine Laufzeitverpflichtung erforderlich. Sie können eine Kapazitätsreservierung jederzeit erstellen oder stornieren. Es werden jedoch keine Rabatte angewendet, und nach der erfolgreichen Bereitstellung Ihrer Kapazitätsreservierung fallen Gebühren zu den Tarifen nutzungsbasierter Bezahlung an. Im Gegensatz zu RIs, die Kapazität priorisieren, aber nicht garantieren, sieht Azure beim Kauf einer Reservierung von Bedarfskapazitäten Computekapazität für Ihre VM vor und bietet eine SLA-Garantie.  

- **Welche Szenarien würden von der Reservierung von Bedarfskapazitäten am meisten profitieren?**

    Typische Szenarien sind Geschäftskontinuität, Notfallwiederherstellung und horizontale Skalierung unternehmenskritischer Anwendungen.  


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine Kapazitätsreservierung, und beginnen Sie mit dem Reservieren von Computekapazität in einer Azure-Region oder Verfügbarkeitszone. 

> [!div class="nextstepaction"]
> [Erstellen einer Kapazitätsreservierung](capacity-reservation-create.md)