---
title: Weitere Informationen zu Azure Image Builder
description: Erfahren Sie mehr über Azure Image Builder für virtuelle Computer in Azure.
author: kof-f
ms.author: kofiforson
ms.date: 05/24/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 805b2a8bf29edfd76804aec3607acc11155edd16
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697069"
---
# <a name="azure-image-builder-overview"></a>Übersicht über Azure Image Builder

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Standardisierte VM-Images ermöglichen es Organisationen, in die Cloud zu migrieren und die Konsistenz der Bereitstellung sicherzustellen. Die Images beinhalten üblicherweise vordefinierte Sicherheits- und Konfigurationseinstellungen und die notwendige Software. Das Einrichten einer eigenen Imaging-Pipeline erfordert Zeit, Infrastruktur und Aufwand. Mit Azure VM Image Builder müssen Sie lediglich eine Konfiguration bereitstellen, die Ihr Image beschreibt, und an den Dienst senden. Dann wird das Image erstellt und verteilt.
 
Mit Azure VM Image Builder (Azure Image Builder) können Sie mit einem Windows- oder Linux-basierten Azure Marketplace-Image oder vorhandenen benutzerdefinierten Images beginnen und dann eigene Anpassungen vornehmen. Da Image Builder auf [HashiCorp Packer](https://packer.io/) aufbaut, gibt es einige Ähnlichkeiten, Sie haben jedoch den Vorteil eines verwalteten Diensts. Sie können auch angeben, wo Ihre Images gehostet werden sollen: in [Azure Shared Image Gallery](shared-image-galleries.md), als verwaltetes Image oder als VHD.


## <a name="features"></a>Funktionen

Azure Image Builder unterstützt die folgenden Funktionen:

- Erstellung von Basisimages, die Ihre minimalen Sicherheits- und Unternehmenskonfigurationen umfassen und die die Abteilungen weiter anpassen können
- Integration von Kernanwendungen, sodass Workloads nach der Erstellung auf VMs verwendet werden oder Konfigurationen zur Unterstützung von Azure Virtual Desktop-Images hinzugefügt werden können
- Patchen von vorhandenen Images: Image Builder ermöglicht es Ihnen, bestehende benutzerdefinierte Images kontinuierlich zu patchen.
- Verbinden Sie Image Builder mit Ihren vorhandenen virtuellen Netzwerken, damit Sie eine Verbindung mit vorhandenen Konfigurationsservern (DSC, Chef, Puppet usw.), Dateifreigaben oder anderen routingfähigen Servern/Diensten herstellen können.
- Die Integration mit dem Azure-Katalog mit freigegebenen Images ermöglicht es Ihnen, Images global zu verteilen, zu versionieren und zu skalieren, und bietet Ihnen ein System für die Imageverwaltung.
- Für die Integration in bestehende Image Buildpipelines, rufen Sie einfach Image Builder aus Ihrer Pipeline auf, oder verwenden Sie die einfache Version der Azure DevOps-Task von Image Builder.
- Migrieren Sie eine bestehende Pipeline zur Imageanpassung zu Azure. Verwenden Sie Ihre vorhandenen Skripts, Befehle und Prozesse, um Images anzupassen.
- Erstellung von Images im VHD-Format zur Unterstützung von Azure Stack.
 

## <a name="regions"></a>Regions

Der Azure Image Builder-Dienst ist in [diesen](./linux/image-builder-json.md#location) Regionen verfügbar. Images können außerhalb dieser Regionen verteilt werden.

## <a name="os-support"></a>Betriebssystemunterstützung
AIB unterstützt Basisbetriebssystem-Images aus dem Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise (mehrere Sitzungen)/Professional
- Windows 2016
- Windows 2019

Hier sind die Betriebssysteme aufgelistet, die getestet wurden und mit Azure Image Builder funktionieren. Der Azure Image Builder sollte jedoch mit jedem Linux- oder Windows-Image im Marketplace funktionieren.

## <a name="how-it-works"></a>Funktionsweise

Azure VM Image Builder ist ein vollständig verwalteter Azure-Dienst, der über einen Azure-Ressourcenanbieter zugänglich ist. Sie legen eine Konfiguration für den Dienst fest, die das Quellimage, die durchzuführende Anpassung und den Ort angibt, an den das neue Image verteilt werden soll. In der folgenden Abbildung ist ein allgemeiner Workflow dargestellt:

![Konzeptionelle Darstellung des Azure Image Builder-Prozesses mit den Quellen (Windows/Linux), Anpassungen (Shell, PowerShell, Windows-Neustart und Windows-Update, Hinzufügen von Dateien) und der globalen Verteilung mit Azure Shared Image Gallery](./media/image-builder-overview/image-builder-flow.png)

Vorlagenkonfigurationen können mithilfe von PowerShell, der Azure-Befehlszeilenschnittstelle, ARM-Vorlagen und der DevOps-Aufgabe von Azure VM Image Builder übergeben werden. Wenn Sie sie an den Dienst senden, wird eine Imagevorlagenressource erstellt. Wenn die Imagevorlagenressource erstellt wird, wird eine Stagingressourcengruppe im Format IT_\<DestinationResourceGroup> _\<TemplateName>_ \(GUID) in Ihrem Abonnement erstellt und angezeigt. Die Stagingressourcengruppe enthält Dateien und Skripts, auf die in der Datei-, Shell- und PowerShell-Anpassung in der ScriptURI-Eigenschaft verwiesen wird.

Zum Ausführen des Builds rufen Sie `Run` in der Imagevorlagenressource auf. Im Dienst werden dann weitere Ressourcen für den Build bereitgestellt, z. B. eine VM, ein Netzwerk, ein Datenträger oder ein Netzwerkadapter. Wenn Sie ein Image ohne Verwendung eines vorhandenen VNet erstellen, werden in Image Builder auch eine öffentliche IP-Adresse und eine Netzwerksicherheitsgruppe bereitgestellt. Die Verbindung mit der Build-VM wird über SSH oder WinRM hergestellt. Wenn Sie ein vorhandenes VNet verwenden, erfolgt die Bereitstellung im Dienst über Azure Private Link, ohne dass eine öffentliche IP-Adresse erforderlich ist. Weitere Informationen zu Image Builder-Netzwerken finden Sie [hier](./linux/image-builder-networking.md).

Nach Fertigstellung des Builds werden alle Ressourcen gelöscht, mit Ausnahme der Stagingressourcengruppe und des Speicherkontos. Diese können Sie durch Löschen der Imagevorlagenressource entfernen oder beibehalten, um den Build erneut auszuführen.

Diese Dokumentation umfasst mehrere Beispiele und ausführliche Leitfäden, die auf Konfigurationsvorlagen und -lösungen im [GitHub-Repository für Azure Image Builder](https://github.com/azure/azvmimagebuilder) verweisen.

### <a name="move-support"></a>Unterstützung für das Verschieben
Die Imagevorlagenressource ist unveränderlich und enthält Links zu Ressourcen und der Stagingressourcengruppe. Daher kann der Ressourcentyp nicht verschoben werden. Wenn Sie die Imagevorlagenressource verschieben möchten, erstellen Sie eine Kopie der Konfigurationsvorlage (extrahieren Sie dazu gegebenenfalls die vorhandene Konfiguration aus der Ressource), erstellen Sie eine neue Imagevorlagenressource in der neuen Ressourcengruppe mit einem neuen Namen, und löschen Sie die vorherige Imagevorlagenressource. 

## <a name="permissions"></a>Berechtigungen
Wenn Sie sich für die (AIB) registrieren, wird dem AIB-Dienst die Berechtigung zum Erstellen, Verwalten und Löschen einer Stagingressourcengruppe (IT_ *) und zum Hinzufügen von Ressourcen erteilt, die für die Imageerstellung erforderlich sind. Dies erfolgt dadurch, dass im Rahmen einer erfolgreichen Registrierung ein AIB-Dienstprinzipalname (Service Principal Name, SPN) in Ihrem Abonnement verfügbar gemacht wird.

Damit Azure VM Image Builder Images an die verwalteten Images oder an eine Shared Image Gallery verteilen kann, müssen Sie eine benutzerseitig zugewiesene Azure-Identität erstellen, die über Berechtigungen zum Lesen und Schreiben von Images verfügt. Wenn Sie auf Azure Storage zugreifen, sind dafür Berechtigungen zum Lesen privater und öffentlicher Container erforderlich.

Die Berechtigungen werden ausführlicher für [PowerShell](./linux/image-builder-permissions-powershell.md) und die [Azure-Befehlszeilenschnittstelle](./linux/image-builder-permissions-cli.md) erläutert.

## <a name="costs"></a>Kosten
Bei der Erstellung, dem Aufbau und der Speicherung von Images mit Azure Image Builder fallen einige Computing-, Netzwerk- und Speicherkosten an. Diese Kosten sind mit Kosten beim manuellen Erstellen von benutzerdefinierten Images vergleichbar. Für die Ressourcen werden Ihre üblichen Azure-Preisen abgerechnet. 

Während der Imagestellung werden Dateien heruntergeladen und in der `IT_<DestinationResourceGroup>_<TemplateName>`-Ressourcengruppe gespeichert. Dadurch werden die Speicherkosten reduziert. Sie können die **Imagevorlage** nach der Erstellung löschen, wenn sie nicht mehr benötigt wird.
 
Image Builder erstellt eine VM mit einer D1v2 VM-Größe und dem für die VM erforderlichen Speicher und Netzwerk. Diese Ressourcen werden für die Dauer des Erstellungsprozesses benötigt und werden gelöscht, sobald der Image Builder die Erstellung des Images abgeschlossen hat. 
 
Azure Image Builder verteilt das Image an die von Ihnen ausgewählten Regionen. Das könnte zu Kosten für ausgehenden Datenverkehr führen.

## <a name="hyper-v-generation"></a>Hyper-V Generation
Image Builder unterstützt derzeit nativ nur die Erstellung von Images der Hyper-V-Generation 1 (Gen1) für Azure Shared Image Gallery (SIG) oder Managed Image. 
 
## <a name="next-steps"></a>Nächste Schritte 
 
Um den Azure Image Builder auszuprobieren, lesen Sie die Artikel zum Erstellen von [Linux](./linux/image-builder.md)- oder [Windows](./windows/image-builder.md)-Images.
