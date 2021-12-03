---
title: Weitere Informationen zu Azure Image Builder
description: Erfahren Sie mehr über Azure Image Builder für virtuelle Computer in Azure.
author: sumit-kalra
ms.author: sukalra
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 702a4b624041a20c925bfc65d92542788f381800
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551310"
---
# <a name="azure-image-builder-overview"></a>Übersicht über Azure Image Builder

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Standardisierte VM-Images ermöglichen es Organisationen, in die Cloud zu migrieren und die Konsistenz bei ihrer Bereitstellung sicherzustellen. Die Images beinhalten üblicherweise vordefinierte Sicherheits- und Konfigurationseinstellungen sowie die notwendige Software. Das Einrichten einer eigenen Imaging-Pipeline erfordert Zeit, Infrastruktur und das Setup. Mit Azure VM Image Builder (Image Builder) müssen Sie lediglich eine Konfiguration erstellen, die Ihr Image beschreibt, und es an den Dienst übermitteln, in dem das Image erstellt und anschließend verteilt wird.

Mit Image Builder können Sie Ihre vorhandene Image-Anpassungspipeline zu Azure migrieren und gleichzeitig weiterhin vorhandene Skripts, Befehle und Prozesse zum Anpassen von Images verwenden. Mit Image Builder können Sie Ihre Kernanwendungen in ein VM-Image integrieren, damit Ihre VMs nach der Erstellung sofort Workloads übernehmen können. Sie können sogar Konfigurationen hinzufügen, um Images für Azure Virtual Desktop oder als VHDs zur Verwendung in Azure Stack oder zur Vereinfachung des Exports zu erstellen.

Mit Image Builder können Sie mit Windows- oder Linux-Images aus dem Azure Marketplace oder vorhandenen benutzerdefinierten Images beginnen und Ihre eigenen Anpassungen hinzufügen. Sie können auch angeben, wo Ihre resultierenden Images gehostet werden sollen: in [Azure Compute Gallery](shared-image-galleries.md) (ehemals Shared Image Gallery), als verwaltetes Image oder als VHD.

## <a name="features"></a>Funktionen

Es ist zwar möglich, benutzerdefinierte VM-Images manuell oder mithilfe von anderen Tools zu erstellen, jedoch kann der Prozess umständlich und unzuverlässig sein. Azure VM Image Builder basiert auf [HashiCorp Packer](https://www.packer.io/) und bietet Ihnen die Vorteile eines verwalteten Diensts.

### <a name="simplicity"></a>Einfachheit

- Komplexe Tools, Prozesse und manuelle Schritte zum Erstellen eines VM-Images werden überflüssig. Image Builder abstrahiert all diese Details und blendet Azure-spezifische Anforderungen wie die Notwendigkeit des Generalisierens des Images (Sysprep) aus und bietet auch erweiterten Benutzern die Möglichkeit, sie außer Kraft zu setzen.
- Image Builder kann in vorhandene Image-Buildpipelines integriert werden, um eine Klick-und-Los-Erfahrung zu ermöglichen. Sie können Image Builder einfach aus Ihrer Pipeline aufrufen oder die [DevOps Task von Azure Image Builder Service (Vorschau)](./linux/image-builder-devops-task.md) verwenden.
- Image Builder kann Anpassungsdaten aus verschiedenen Quellen abrufen, ohne dass sie an einem Ort gesammelt werden müssen, um ein VM-Image zu erstellen.
- Durch die Integration von Image Builder mit Azure Compute Gallery erhalten Sie ein Imageverwaltungssystem, mit dem Sie Images global verteilen, replizieren, versionieren und skalieren können. Darüber hinaus können Sie das resultierende Image als VHD oder als ein oder mehrere verwaltete Images verteilen, ohne es neu erstellen zu müssen.

### <a name="infrastructure-as-code"></a>Infrastructure-as-Code

- Es ist nicht erforderlich, eine langfristige Infrastruktur (*z. B. Speicherkonten zur Speicherung von Anpassungsdaten*) oder eine vorübergehende Infrastruktur (*z. B. eine temporäre VM zur Erstellung des Images*) zu verwalten. 
- Image Builder speichert Ihre VM-Image-Buildspezifikation und Anpassungsartefakte als Azure-Ressourcen, wodurch die Notwendigkeit der Verwaltung von Offlinedefinitionen und das Risiko von Umgebungsdrifts aufgrund von versehentlichen Löschungen oder Updates entfällt.

### <a name="security"></a>Sicherheit

- Image Builder ermöglicht die Erstellung von Basisimages (*diese können Ihre minimalen Sicherheits- und Unternehmenskonfigurationen umfassen*) und erlaubt weitere Anpassungen durch die entsprechenden Abteilungen. Diese Images können mit Image Builder sicher und konform verwaltet werden, um rasch ein Golden Image mit der neuesten gepatchten Version eines Quellimages wiederherzustellen. Image Builder erleichtert Ihnen auch das Erstellen von Images, die der Azure Windows Baseline entsprechen. Weitere Informationen finden Sie unter [Image Builder – Windows Baselinevorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/imagebuilder-windowsbaseline).
- Sie müssen Ihre Anpassungsartefakte nicht öffentlich zugänglich machen, damit Image Builder sie abrufen kann. Image Builder kann Ihre [Azure Managed Identity](../active-directory/managed-identities-azure-resources/overview.md) verwenden, um diese Ressourcen abzurufen, und Sie können die Berechtigungen dieser Identität mithilfe von Azure-RBAC weit wie erforderlich einschränken. Dies bedeutet nicht nur, dass Sie Ihre Artefakte geheim halten können, sondern auch, dass sie nicht von nicht autorisierten Akteuren manipuliert werden können.
- Kopien von Anpassungsartefakten, vorübergehenden Rechen- und Speicherressourcen und die resultierenden Images werden sicher innerhalb Ihres Abonnements gespeichert, wobei die Zugriffssteuerung durch Azure-RBAC erfolgt. Dies schließt den Build-VM ein, der zum Erstellen des benutzerdefinierten Images verwendet wird, und stellt sicher, dass Ihre Anpassungsskripts und -dateien nicht auf eine unbekannte VM in ein unbekanntes Abonnement kopiert werden. Darüber hinaus können Sie mithilfe [isolierter VM-Angebote](./isolation.md) für die Build-VM ein hohes Maß an Isolation von den Workloads anderer Kunden erreichen.
- Sie können Image Builder mit Ihren vorhandenen virtuellen Netzwerken verbinden, damit Sie mit vorhandenen Konfigurationsservern (DSC, Chef, Puppet usw.), Dateifreigaben oder anderen routingfähigen Servern/Diensten kommunizieren können.

## <a name="regions"></a>Regions

Der Azure Image Builder-Dienst ist in den folgenden Regionen verfügbar: Regionen. 

>[!NOTE]
> Images können auch weiterhin außerhalb dieser Regionen verteilt werden.
> 
- East US
- USA (Ost) 2
- USA, Westen-Mitte
- USA (Westen)
- USA, Westen 2
- USA Süd Mitte
- Nordeuropa
- Europa, Westen
- Südostasien
- Australien, Südosten
- Australien (Osten)
- UK, Süden
- UK, Westen

## <a name="os-support"></a>Betriebssystemunterstützung
Azure Image Builder unterstützt Basisbetriebssystem-Images aus dem Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise (mehrere Sitzungen)/Professional
- Windows 2016
- Windows 2019

>[!IMPORTANT]
> Aufgelistete Betriebssysteme wurden getestet und funktionieren jetzt mit Azure Image Builder. Der Azure Image Builder sollte jedoch mit jedem Linux- oder Windows-Image im Marketplace funktionieren.

## <a name="how-it-works"></a>Funktionsweise

Azure VM Image Builder ist ein vollständig verwalteter Azure-Dienst, der über einen Azure-Ressourcenanbieter zugänglich ist. Sie legen eine Konfiguration für den Dienst fest, die das Quellimage, die durchzuführende Anpassung und den Ort angibt, an den das neue Image verteilt werden soll. In der folgenden Abbildung ist ein allgemeiner Workflow dargestellt:

![Konzeptionelle Darstellung des Azure Image Builder-Prozesses mit den Quellen (Windows/Linux), Anpassungen (Shell, PowerShell, Windows-Neustart und Windows-Update, Hinzufügen von Dateien) und der globalen Verteilung mit Azure Compute Gallery](./media/image-builder-overview/image-builder-flow.png)

Vorlagenkonfigurationen können mithilfe von PowerShell, der Azure-Befehlszeilenschnittstelle, Azure Resource Manager-Vorlagen und der DevOps Task von Azure VM Image Builder übergeben werden. Wenn Sie sie an den Dienst senden, wird eine Imagevorlagenressource erstellt. Wenn die Imagevorlagenressource erstellt wird, wird eine Stagingressourcengruppe in Ihrem Abonnement erstellt und in folgendem Format angezeigt: `IT_\<DestinationResourceGroup>_\<TemplateName>_\(GUID)`. Die Stagingressourcengruppe enthält Dateien und Skripts, auf die in der Datei-, Shell- und PowerShell-Anpassung in der ScriptURI-Eigenschaft verwiesen wird.

Zum Ausführen des Builds rufen Sie `Run` in der Imagevorlagenressource auf. Im Dienst werden dann weitere Ressourcen für den Build bereitgestellt, z. B. eine VM, ein Netzwerk, ein Datenträger oder ein Netzwerkadapter. Wenn Sie ein Image ohne Verwendung eines vorhandenen VNet erstellen, werden in Image Builder auch eine öffentliche IP-Adresse und eine Netzwerksicherheitsgruppe bereitgestellt. Die Verbindung mit der Build-VM wird über SSH oder WinRM hergestellt. Wenn Sie ein vorhandenes VNet verwenden, erfolgt die Bereitstellung des Dienstes über Azure Private Link, ohne dass eine öffentliche IP-Adresse erforderlich ist. Weitere Informationen finden Sie unter [Übersicht zu Image Builder-Netzwerken](./linux/image-builder-networking.md).

Nach Fertigstellung des Builds werden alle Ressourcen gelöscht, mit Ausnahme der Stagingressourcengruppe und des Speicherkontos. Diese können Sie durch Löschen der Imagevorlagenressource entfernen oder beibehalten, um den Build erneut auszuführen.

Diese Dokumentation umfasst mehrere Beispiele und ausführliche Leitfäden, die auf Konfigurationsvorlagen und -lösungen im [GitHub-Repository für Azure Image Builder](https://github.com/azure/azvmimagebuilder) verweisen.

### <a name="move-support"></a>Unterstützung für das Verschieben
Die Imagevorlagenressource ist unveränderlich und enthält Links zu Ressourcen und der Stagingressourcengruppe. Daher kann der Ressourcentyp nicht verschoben werden. Wenn Sie die Imagevorlagenressource verschieben möchten, erstellen Sie eine Kopie der Konfigurationsvorlage (extrahieren Sie dazu gegebenenfalls die vorhandene Konfiguration aus der Ressource), erstellen Sie eine neue Imagevorlagenressource in der neuen Ressourcengruppe mit einem neuen Namen, und löschen Sie die vorherige Imagevorlagenressource. 

## <a name="permissions"></a>Berechtigungen
Wenn Sie sich für Azure Image Builder (AIB) registrieren, wird dem AIB-Dienst die Berechtigung zum Erstellen, Verwalten und Löschen einer Stagingressourcengruppe `(IT_*)` und zum Hinzufügen von Ressourcen erteilt, die für die Imageerstellung erforderlich sind. Dies erfolgt dadurch, dass im Rahmen einer erfolgreichen Registrierung ein AIB-Dienstprinzipalname (Service Principal Name, SPN) in Ihrem Abonnement verfügbar gemacht wird.

Damit Azure VM Image Builder Images an die verwalteten Images oder an eine Azure Compute Gallery verteilen kann, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen zum Lesen und Schreiben von Images verfügt. Wenn Sie auf Azure Storage zugreifen, sind dafür Berechtigungen zum Lesen privater und öffentlicher Container erforderlich.

Die Berechtigungen werden ausführlicher für [PowerShell](./linux/image-builder-permissions-powershell.md) und die [Azure-Befehlszeilenschnittstelle](./linux/image-builder-permissions-cli.md) erläutert.

## <a name="costs"></a>Kosten
Bei der Erstellung, dem Aufbau und der Speicherung von Images mit Azure Image Builder fallen einige Computing-, Netzwerk- und Speicherkosten an. Diese Kosten sind mit Kosten beim manuellen Erstellen von benutzerdefinierten Images vergleichbar. Für die Ressourcen werden Ihre üblichen Azure-Preisen abgerechnet. 

Während der Imagestellung werden Dateien heruntergeladen und in der `IT_<DestinationResourceGroup>_<TemplateName>`-Ressourcengruppe gespeichert. Dadurch werden die Speicherkosten reduziert. Sie können die **Imagevorlage** nach der Erstellung löschen, wenn sie nicht mehr benötigt wird.
 
Image Builder erstellt eine VM mit der Standard-VM-Größe D1 v2 für Gen1-Images und D2ds v4 für Gen2-Images sowie den Speicher und das Netzwerk, die für die VM erforderlich sind. Diese Ressourcen werden für die Dauer des Buildprozesses bereitgestellt und gelöscht, sobald Image Builder die Erstellung des Images abgeschlossen hat. 
 
Azure Image Builder verteilt das Image an die von Ihnen ausgewählten Regionen. Das könnte zu Kosten für ausgehenden Datenverkehr führen.

## <a name="hyper-v-generation"></a>Hyper-V Generation
Image Builder unterstützt derzeit die Erstellung von Hyper-V-Gen1- und -Gen2-Images in der Azure Compute Gallery. 
 
## <a name="next-steps"></a>Nächste Schritte 
 
Um den Azure Image Builder auszuprobieren, lesen Sie die Artikel zum Erstellen von [Linux](./linux/image-builder.md)- oder [Windows](./windows/image-builder.md)-Images.
