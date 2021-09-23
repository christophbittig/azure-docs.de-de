---
title: Azure-Hybridvorteil und virtuelle Linux-Computer
description: Erfahren Sie, wie Sie mit dem Azure-Hybridvorteil bei virtuellen Linux-Computern, die in Azure ausgeführt werden, Geld sparen können.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: c794d3e18eaf25241cb9c55c14a32e4e5c9ff7b0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692257"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Anwendung des Azure-Hybridvorteils auf virtuelle Linux-Computer

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Der Azure-Hybridvorteil ist ein Lizenzierungsvorteil, der Ihnen hilft, die Kosten für die Ausführung Ihrer VMs auf Red Hat Enterprise Linux (RHEL) und SUSE Linux Enterprise Server (SLES) in der Cloud erheblich zu senken. Dank diesem Vorteil bezahlen Sie lediglich die Infrastrukturkosten Ihres virtuellen Computers, da die Softwaregebühr durch Ihr RHEL- bzw. SLES-Abonnement abgedeckt ist. Der Vorteil ist für alle RHEL- und SLES-Images im Marketplace mit nutzungsbasierter Bezahlung verfügbar.

Der Azure-Hybridvorteil für Linux-VMs ist jetzt öffentlich verfügbar.

## <a name="benefit-description"></a>Beschreibung des Vorteils

Mit dem Azure-Hybridvorteil können Sie Ihre lokalen RHEL- und SLES-Server zu Azure migrieren, indem Sie die nutzungsbasierte Bezahlung (PAYG, Pay As You Go) von vorhandenen RHEL- und SLES-VMs in Azure in eine BYOS-Abrechnung (Bring Your Own Subscription) umwandeln. In der Regel wird für virtuelle Computer, die über Images mit nutzungsbasierter Bezahlung in Azure bereitgestellt werden, eine Infrastrukturgebühr und eine Softwaregebühr berechnet. Mit dem Azure-Hybridvorteil können virtuelle Computer mit nutzungsbasierter Bezahlung ohne erneute Bereitstellung auf ein BYOS-Abrechnungsmodell umgestellt werden, wodurch Sie das Risiko eines Ausfalls vermeiden können.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Darstellung der Kosten für den Azure-Hybridvorteil auf virtuellen Linux-Computern.":::

Nachdem Sie den Vorteil für eine RHEL- oder SLES-VM aktiviert haben, wird Ihnen keine zusätzliche Softwaregebühr berechnet, die normalerweise für VMs mit nutzungsbasierter Bezahlung anfällt. Stattdessen wird für Ihren virtuellen Computer eine BYOS-Gebühr berechnet, die nur die Hardwaregebühr, jedoch keine Softwaregebühr beinhaltet.

Wahlweise können Sie auch einen virtuellen Computer, für den der Vorteil aktiviert wurde, wieder auf ein Abrechnungsmodell mit nutzungsbasierter Bezahlung umstellen.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Umfang der Azure-Hybridvorteilsberechtigung für virtuelle Linux-Computer

Der Azure-Hybridvorteil ist für alle RHEL- und SLES-Images mit nutzungsbasierter Bezahlung vom Azure Marketplace verfügbar. Für RHEL- oder SLES-BYOS-Images oder benutzerdefinierte Images vom Azure Marketplace ist der Vorteil noch nicht verfügbar.

Die Hybridvorteile, Azure Dedicated Host-Instanzen und SQL berechtigen nicht zum Azure-Hybridvorteil, wenn Sie bereits den Vorteil für virtuelle Linux-Computer nutzen.

## <a name="get-started"></a>Erste Schritte

### <a name="red-hat-customers"></a>Red Hat-Kunden

Der Azure-Hybridvorteil für RHEL steht Red Hat-Kunden zur Verfügung, die beide Kriterien erfüllen:

- Sie haben aktive oder nicht verwendete RHEL-Abonnements, die für die Verwendung in Azure geeignet sind.
- Mindestens ein Abonnement ist für die Verwendung in Azure mit dem [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)-Programm aktiviert.

> [!IMPORTANT]
> Stellen Sie sicher, dass das richtige Abonnement für das [Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)-Programm aktiviert wurde.

So beginnen Sie mit der Verwendung des Vorteils für Red Hat:

1. Aktivieren Sie mindestens eines Ihrer berechtigten RHEL-Abonnements für die Verwendung in Azure über die [Red Hat Cloud Access-Benutzeroberfläche für Kunden](https://access.redhat.com/management/cloud).

   Die von Ihnen während des Red Hat Cloud Access-Aktivierungsprozesses bereitgestellten Azure-Abonnements sind dann berechtigt, den Azure-Hybridvorteil zu nutzen.
1. Wenden Sie den Azure-Hybridvorteil auf alle Ihre vorhandenen RHEL-VMs mit nutzungsbasierter Zahlung sowie auf alle neuen RHEL-VMs an, die Sie auf Grundlage von Images im Azure Marketplace mit nutzungsbasierter Zahlung bereitstellen. Sie können den Vorteil über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle aktivieren.
1. Befolgen Sie die empfohlenen [weiteren Schritte](https://access.redhat.com/articles/5419341) für das Konfigurieren von Updatequellen für Ihre RHEL-VMs und für Compliancerichtlinien für RHEL-Abonnements.


### <a name="suse-customers"></a>SUSE-Kunden

Der Azure-Hybridvorteil für SUSE ist für Kunden verfügbar, die über Folgendes verfügen:

- Nicht genutzte SUSE-Abonnements, die zur Verwendung in Azure berechtigt sind.
- Mindestens ein aktives SUSE-Abonnement, das lokal verwendet und in Azure verschoben werden soll.
- Erworbene Abonnements, die sie im SUSE Customer Center für die Verwendung in Azure aktiviert haben. 

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie das richtige Abonnement für die Verwendung in Azure auswählen.

So beginnen Sie mit der Verwendung des Vorteils für SUSE:

1. Registrieren Sie das Abonnement, das Sie von SUSE oder einem SUSE-Vertriebspartner erworben haben, im [SUSE Customer Center](https://scc.suse.com).
2. Aktivieren Sie das Abonnement im SUSE Customer Center.
3. Registrieren Sie Ihre VMs, die den Vorteil erhalten, beim SUSE Customer Center, um die Updates vom SUSE Customer Center zu erhalten.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Aktivieren und Deaktivieren des Vorteils im Azure-Portal

Im Azure-Portal können Sie den Vorteil für bestehende VMs aktivieren oder den Vorteil für neue VMs aktivieren, wenn Sie die VM erstellen.

### <a name="enable-the-benefit-for-an-existing-vm-in-the-azure-portal"></a>Aktivieren des Vorteils für eine vorhandene VM im Azure-Portal

So aktivieren Sie den Vorteil für eine vorhandene VM

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).
1. Öffnen Sie die Seite der VM, auf die Sie die Konvertierung anwenden möchten.
1. Navigieren Sie zur Option **Konfiguration** auf der linken Seite. Der Abschnitt „Lizenzierung“ wird angezeigt. Aktivieren Sie das Optionsfeld „Ja“ und das Kontrollkästchen zur Bestätigung, um die AHB-Konvertierung zu aktivieren.
![Blatt für die AHB-Konfiguration nach dem Erstellen](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="enable-the-benefit-when-you-create-the-vm-in-the-azure-portal"></a>Aktivieren des Vorteils beim Erstellen der VM im Azure-Portal

So aktivieren Sie den Vorteil, wenn Sie die VM erstellen (der SUSE-Workflow ist derselbe wie das hier gezeigte RHEL-Beispiel):

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).
1. Navigieren Sie im Portal zur Seite „Virtuellen Computer erstellen“.
 ![Azure-Hybridvorteil beim Erstellen einer VM](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Aktivieren Sie das Kontrollkästchen, um die AHB-Konvertierung zu aktivieren und Cloudzugriffslizenzen zu verwenden.
 ![Kontrollkästchen für den Azure-Hybridvorteil beim Erstellen einer VM](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Erstellen Sie anhand der folgenden Anweisungen eine VM.
1. Überprüfen Sie auf dem Blatt **Konfiguration**, ob die Option aktiviert ist. 
![Blatt für die AHB-Konfiguration nach dem Erstellen](./media/azure-hybrid-benefit/create-configuration-blade.png)

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Aktivieren und Deaktivieren des Vorteils über die Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl `az vm update`, um die vorhandenen VMs zu aktualisieren. Führen Sie den Befehl bei virtuellen RHEL-Computern mit einem `--license-type`-Parameter mit dem Wert `RHEL_BYOS` aus. Führen Sie den Befehl bei virtuellen SLES-Computern mit einem `--license-type`-Parameter mit dem Wert `SLES_BYOS` aus.

### <a name="cli-example-to-enable-the-benefit"></a>Beispiel für die Aktivierung des Vorteils über die Befehlszeilenschnittstelle
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Beispiel für die Deaktivierung des Vorteils über die Befehlszeilenschnittstelle
Verwenden Sie zum Deaktivieren des Vorteils den `--license-type`-Wert `None`:

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Beispiel für die Aktivierung des Vorteils für eine Vielzahl von virtuellen Computern über die Befehlszeilenschnittstelle
Zum Aktivieren des Vorteils für eine Vielzahl von virtuellen Computern können Sie den `--ids`-Parameter in der Azure-Befehlszeilenschnittstelle verwenden:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Anhand der folgenden Beispiele werden zwei Methoden zum Abrufen einer Liste mit Ressourcen-IDs vorgestellt: eine auf der Ressourcengruppenebene, die andere auf der Abonnementebene.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Anwenden des Azure-Hybridvorteils zum Zeitpunkt der VM-Erstellung
Sie können den Azure-Hybridvorteil nicht nur auf vorhandene VMs mit nutzungsbasierter Bezahlung anwenden, sondern auch zum Zeitpunkt der VM-Erstellung aufrufen. Dies bietet einen dreifachen Vorteil:
- Sie können mit dem gleichen Image und Prozess sowohl VMs mit nutzungsbasierter Zahlung als auch BYOS-VMs bereitstellen.
- Es ermöglicht zukünftige Änderungen des Lizenzierungsmodus, was bei einem reinen BYOS-Image oder bei Verwendung ihrer eigenen VM nicht verfügbar ist.
- Der virtuelle Computer wird standardmäßig mit der Red Hat-Updateinfrastruktur (RHUI) verbunden, um sicherzustellen, dass er aktuell und sicher bleibt. Sie können den aktualisierten Mechanismus nach der Bereitstellung jederzeit ändern.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Überprüfen des Status des Azure-Hybridvorteils eines virtuellen Computers
Sie können den Status des Azure-Hybridvorteils einer VM über die Azure CLI oder mithilfe des Azure Instance Metadata Service anzeigen.

### <a name="azure-cli"></a>Azure CLI

Hierfür können Sie den Befehl `az vm get-instance-view` verwenden. Suchen Sie in der Antwort nach einem `licenseType`-Feld. Wenn das `licenseType`-Feld vorhanden ist und den Wert `RHEL_BYOS` oder `SLES_BYOS` enthält, ist der Vorteil ihrer VM aktiviert.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure-Instanzmetadatendienst

Innerhalb des virtuellen Computers können Sie die bestätigten Metadaten in Azure Instance Metadata Service abfragen, um den `licenseType`-Wert der VM zu ermitteln. Ein `licenseType`-Wert von `RHEL_BYOS` oder `SLES_BYOS` gibt an, dass der Vorteil ihrer VM aktiviert ist. [Erfahren Sie mehr über bestätigte Metadaten](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Compliance

### <a name="red-hat"></a>Red Hat

Kunden, die den Azure-Hybridvorteil für RHEL verwenden, stimmen den [rechtlichen Standardbedingungen](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) und den [Standarddatenschutzbestimmungen](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) für RHEL-Angebote im Azure Marketplace zu.

Kunden, die den Azure-Hybridvorteil für RHEL nutzen, haben drei Möglichkeiten, Softwareupdates und Patches für diese VMs bereitzustellen:

- [Red Hat-Updateinfrastruktur (RHUI)](../workloads/redhat/redhat-rhui.md) (Standardoption)
- Red Hat Satellite Server
- Red Hat Subscription Manager

Kunden, die sich für die RHUI-Option entschließen, können diese weiterhin als Hauptquelle für Updates für ihre RHEL-VMs mit Azure-Hybridvorteil nutzen, ohne diesen VMs RHEL-Abonnements anfügen zu müssen. Kunden, die sich für die RHUI-Option entscheiden, sind dafür verantwortlich, für die RHEL-Abonnementscompliance zu sorgen.

Kunden, die sich für Red Hat Satellite-Server oder Red Hat Subscription Manager entscheiden, sollten die RHUI-Konfiguration entfernen und dann ihren RHEL-VMs mit Azure-Hybridvorteil ein RHEL-Abonnement mit Cloud Access-Aktivierung anfügen.  

Weitere Informationen zur Konformität von Red Hat-Abonnements, Softwareupdates und Quellen für RHEL-VMs mit Azure-Hybridvorteil finden Sie im Red Hat-Artikel [Using Red Hat Enterprise Linux subscriptions with Microsoft Azure Hybrid Benefit](https://access.redhat.com/articles/5419341) (Verwendung von RHEL-Abonnements mit Microsoft Azure-Hybridvorteil).

### <a name="suse"></a>SUSE

Kunden, die den Azure-Hybridvorteil verwenden, müssen die Infrastruktur für Cloudupdates auf eine der drei Optionen umstellen, die Softwareupdates und Patches für jene VMs bereitstellen:
- [SUSE Customer Center](https://scc.suse.com)
- SUSE Manager
- SUSE Repository Mirroring Tool (RMT) 

Wenn Sie den Azure-Hybridvorteil für Ihre SLES-VMs verwenden möchten oder wenn Sie Informationen zum Umstieg von der nutzungsbasierten Bezahlung für SLES auf BYOS oder umgekehrt benötigen, lesen Sie unter [SUSE Linux Enterprise and Azure Hybrid Benefit](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/) (SUSE Linux Enterprise und der Azure-Hybridvorteil) nach. 

## <a name="azure-hybrid-benefit-on-reserved-instances"></a>Azure-Hybridvorteil auf reservierten Instanzen 

Mit Azure-Reservierungen (Azure Reserved Virtual Machine Instances) können Sie Geld sparen, indem Sie sich bei mehreren Produkten für Pläne mit einer Laufzeit von einem Jahr oder drei Jahren entscheiden. Hier können Sie [mehr über reservierte Instanzen](../../cost-management-billing/reservations/save-compute-costs-reservations.md) erfahren. Der Azure Hybridvorteil ist als Vorschau für die [reservierte VM-Instanz (RIs)](../../cost-management-billing/reservations/save-compute-costs-reservations.md#charges-covered-by-reservation) verfügbar. 

Dies bedeutet Folgendes: Wenn Sie mithilfe von RI Computekosten zu einem reduzierten Preis erworben haben, können Sie zusätzlich auf die Lizenzkosten für RHEL und SUSE den AHB-Vorteil anwenden. Die Schritte zum Anwenden des AHB-Vorteils für eine RI-Instanz bleiben genau dieselben wie für eine reguläre VM.
![AHB für RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Wenn Sie bereits Reservierungen für RHEL- oder SUSE-PAYG-Software im Azure Marketplace gekauft haben, warten Sie, bis die Reservierungsdauer abgelaufen ist, bevor Sie den Azure-Hybridvorteil verwenden.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
*F: Kann ich den Lizenztyp `RHEL_BYOS` mit einem SLES-Image oder umgekehrt verwenden?*

A: Nein, das ist nicht möglich. Wenn Sie versuchen, einen Lizenztyp einzugeben, der nicht mit der auf Ihrem virtuellen Computer ausgeführten Distribution übereinstimmt, werden die Metadaten für die Abrechnung nicht aktualisiert. Wenn Sie jedoch versehentlich den falschen Lizenztyp eingeben und Ihren virtuellen Computer dann noch mal mit dem richtigen Lizenztyp aktualisieren, wird der Vorteil dagegen aktiviert.

*F: Ich habe mich bei Red Hat Cloud Access registriert, kann aber den Vorteil auf meinen virtuellen RHEL-Computern dennoch nicht aktivieren. Wie soll ich vorgehen?*

A: Es kann eine Weile dauern, bis die Registrierung Ihres Red Hat Cloud Access-Abonnements von Red Hat an Azure weitergegeben wird. Wenn der Fehler nach einem Werktag weiterhin auftritt, wenden Sie sich an den Microsoft-Support.

*F: Ich habe eine VM mit einem „Golden Image“ für RHEL BYOS bereitgestellt. Kann ich die Abrechnung für diese Images von BYOS zur nutzungsbasierten Bezahlung ändern?*

A: Nein, das ist nicht möglich. Der Azure-Hybridvorteil unterstützt eine Konvertierung nur für Images mit nutzungsbasierter Bezahlung.

*F: Ich habe mein eigenes RHEL- oder SLES-Image aus meinem lokalen System (über Azure Migrate, Azure Site Recovery oder auf andere Weise) in Azure hochgeladen. Kann ich die Abrechnung für diese Images von BYOS auf die nutzungsbasierte Zahlung umstellen?*

A: Nein, das ist nicht möglich. Der Azure-Hybridvorteil ist derzeit nur für RHEL- und SLES-Images in Azure Marketplace verfügbar. 

*F: Ich habe mein eigenes RHEL- oder SLES-Image aus meinem lokalen System (über Azure Migrate, Azure Site Recovery oder auf andere Weise) in Azure hochgeladen. Muss ich bestimmte Maßnahmen ergreifen, um vom Azure-Hybridvorteil zu profitieren?*

A: Nein, müssen Sie nicht. Die von Ihnen hochgeladenen RHEL- oder SLES-Images gelten bereits als BYOS, und Ihnen werden nur die Kosten für die Azure-Infrastruktur in Rechnung gestellt. Sie sind für die Kosten der RHEL-Abonnements genauso verantwortlich wie für Ihre lokalen Umgebungen. 

*F: Kann ich den Azure-Hybridvorteil auf VMs verwenden, die mithilfe von RHEL- und SLES-SAP-Images von Azure Marketplace bereitgestellt wurden?*

Antwort: Ja, das ist möglich. Sie können den Lizenztyp `RHEL_BYOS` für RHEL-VMs und den Typ `SLES_BYOS` zum Konvertieren von VMs verwenden, die mithilfe von RHEL- bzw. SLES-SAP-Images vom Azure Marketplace bereitgestellt wurden.

*F: Kann ich den Azure-Hybridvorteil in VM-Skalierungsgruppen für RHEL und SLES verwenden?*

A: Ja, Azure Hybridvorteil auf Skalierungssätze für virtuelle Geräte für RHEL und SLES ist für alle Benutzer verfügbar. Weitere [Informationen zu diesem Vorteil und seiner Verwendung finden Sie hier](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md). 

*F: Kann ich den Azure-Hybridvorteil auf reservierten Instanzen für RHEL und SLES verwenden?*

A: Ja, Azure Hybridvorteil auf reservierter Instanz für RHEL und SLES ist für alle Benutzer verfügbar. Weitere [Informationen zu diesem Vorteil und seiner Verwendung finden Sie hier](#azure-hybrid-benefit-on-reserved-instances).

*F: Kann ich den Azure-Hybridvorteil auf einer VM nutzen, die für SQL Server auf RHEL-Images bereitgestellt wurde?*

A: Nein, das ist nicht möglich. Es ist keine Unterstützung für diese VMs geplant.

*F: Kann ich den Azure-Hybridvorteil für mein RHEL Virtual Data Center-Abonnement verwenden?*

A: Nein, das ist nicht möglich. VDC wird in Azure nicht unterstützt, das gilt auch für den AHB.  
 

## <a name="common-problems"></a>Häufige Probleme
In diesem Abschnitt werden häufig auftretende Probleme und die Schritte zur Entschärfung beschrieben.

| Fehler | Minderung |
| ----- | ---------- |
| „Die Aktion konnte nicht durchgeführt werden, da Red Hat Cloud Access laut unseren Datensätzen nicht für Ihr Azure-Abonnement aktiviert wurde.“ | Um den Vorteil virtueller RHEL-Computer zu nutzen, müssen Sie zunächst [Ihre Azure-Abonnements bei Red Hat Cloud Access](https://access.redhat.com/management/cloud) registrieren.

## <a name="next-steps"></a>Nächste Schritte
* [Erfahren Sie, wie Sie mit der Azure CLI VMs erstellen und aktualisieren und Lizenztypen (RHEL_BYOS, SLES_BYOS) für den Azure-Hybridvorteil hinzufügen.](/cli/azure/vm)
* Azure Hybridvorteil auf Skalierungssätze für virtuelle Geräte für RHEL und SLES ist für alle Benutzer verfügbar. Weitere [Informationen zu diesem Vorteil und seiner Verwendung finden Sie hier](../../virtual-machine-scale-sets/azure-hybrid-benefit-linux.md).
