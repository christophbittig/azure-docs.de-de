---
title: Übersicht über die Unterstützung von cloud-init für Linux-VMs in Azure
description: Übersicht über cloud-init-Funktionen zum Konfigurieren einer VM zum Zeitpunkt der Bereitstellung in Azure
author: srijang
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/29/2021
ms.author: srijangupta
ms.openlocfilehash: 7ebabe554121e2ad07ef776ed78046a09db9ca9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054588"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>cloud-init-Unterstützung für virtuelle Computer in Azure

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

In diesem Artikel wird die vorhandene Unterstützung für [cloud-init](https://cloudinit.readthedocs.io) zum Konfigurieren von virtuellen Computern (VMs) oder VM-Skalierungsgruppen während der Bereitstellung in Azure erläutert. Diese cloud-init-Konfigurationen werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden.  

Die VM-Bereitstellung ist der Prozess, bei dem Azure Ihre „VM Create“-Parameterwerte wie Hostname, Benutzername, Kennwort usw. weiterleitet und dem virtuellen Computer beim Starten zur Verfügung stellt. Ein „Bereitstellungs-Agent“ wird diese Werte verarbeiten, den virtuellen Computer konfigurieren und nach Abschluss des Vorgangs entsprechend Bericht erstatten. 

Azure unterstützt zwei Bereitstellungs-Agents [cloud-init](https://cloudinit.readthedocs.io) und den [Azure Linux Agent (WALA)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Übersicht zu cloud-init
[cloud-init](https://cloudinit.readthedocs.io) ist eine gängige Methode für die Anpassung eines virtuellen Linux-Computers beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden.  Weitere Informationen zum ordnungsgemäßen Formatieren Ihrer `#cloud-config`-Dateien oder anderer Eingaben finden Sie auf der [cloud-init-Dokumentationswebsite](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`-Dateien sind Base64-codierte Textdateien.

„cloud-init“ funktioniert auch Distributionen übergreifend. Verwenden Sie z.B. nicht **apt-get install** oder **yum install**, um ein Paket zu installieren. Stattdessen können Sie eine Liste mit zu installierenden Paketen definieren. „cloud-init“ verwendet automatisch das native Paketverwaltungstool für die ausgewählte Distribution.

Wir arbeiten aktiv mit unseren Linux-Distributionspartnern zusammen, um cloud-init-fähige Images im Azure Marketplace zur Verfügung zu stellen. Mit diesen Images funktionieren Ihre cloud-init-Bereitstellungen und -Konfigurationen nahtlos mit VMs und VM-Skalierungsgruppen. Zunächst arbeiten wir mit den empfohlenen Linux-Distributionspartnern und dem Upstream zusammen, um cloud-init-Funktionen mit dem Betriebssystem in Azure sicherzustellen, dann werden die Pakete aktualisiert und in den Distributionspaketrepositorys öffentlich zugänglich gemacht. 

Es gibt zwei Phasen, um cloud-init für die empfohlenen Linux-Distributionsbetriebssysteme in Azure verfügbar zu machen: Paketunterstützung und Imageunterstützung:
* „cloud-init-Paketunterstützung in Azure“ dokumentiert, welche cloud-init-Pakete bereits unterstützt werden oder sich in der Vorschauversion befinden, sodass Sie diese Pakete mit dem Betriebssystem in einem benutzerdefinierten Image verwenden können.
* „Image-cloud-init-fähig“ dokumentiert, ob das Image bereits für die Verwendung von cloud-init konfiguriert ist.


### <a name="canonical"></a>Canonical
| Herausgeber/Version| Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |20.04-LTS |latest |ja | ja |
|Canonical 18.04 |UbuntuServer |18.04-LTS |latest |ja | ja |


### <a name="rhel"></a>RHEL
| Herausgeber/Version| Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7 |RHEL |7.7, 7.8, 7_9 |latest |ja | Ja |
|RedHat 8 |RHEL |8.1, 8.2, 8_3, 8_4 |latest |ja | Ja |

* Alle anderen RedHat-SKUs ab RHEL 7 (Version 7.7) und RHEL 8 (Version 8.1), einschließlich Gen1- und Gen2-Images, werden mit cloud-init bereitgestellt. RHEL 6-Images unterstützen cloud-init nicht. 


### <a name="centos"></a>CentOS
 Herausgeber/Version| Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7 |CentOS |7.7, 7.8, 7.9 |latest |ja | Ja |
|OpenLogic 8 |CentOS |8.1, 8.2, 8.3 |latest |ja | Ja |

* Alle anderen CentOS-SKUs ab CentOS 7 (Version 7.7) und CentOS 8 (Version 8.1), einschließlich Gen1- und Gen2-Images, werden mit cloud-init bereitgestellt. CentOS 6.10-, 7.4-, 7.5- und 7.6-Images unterstützen cloud-init nicht. 

> [!NOTE]
> OpenLogic ist jetzt Rogue Wave Software 



### <a name="oracle"></a>Oracle

 Herausgeber/Version| Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7 |Oracle Linux |77, 78, ol79 |latest |ja | Ja |
|Oracle 8 |Oracle Linux |81, ol82, ol83-lvm, ol84-lvm |latest |ja | Ja |

* Alle anderen Oracle-SKUs ab Oracle 7 (Version 7.7) und Oracle 8 (Version 8.1), einschließlich Gen1- und Gen2-Images, werden mit cloud-init bereitgestellt.


### <a name="suse-sles"></a>SUSE Linux Enterprise Server (SLES)

 Herausgeber/Version| Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |sp1, sp2, sp3 |latest |ja | Ja |
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sp5 |latest |ja | Ja |

* Alle anderen SUSE-SKUs ab SLES 15 (sp1) und SLES 12 (sp5), einschließlich Gen1- und Gen2-Images, werden mit cloud-init bereitgestellt.
* Darüber hinaus werden diese Images auch mit cloud-init bereitgestellt:


 Herausgeber/Version| Angebot | SKU/Version
|:--- |:--- |:---
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp2:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-proxy-4-byosgen1:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-server-4-byos:gen1:2020.06.10


### <a name="debian"></a>Debian
| Herausgeber/Version | Angebot | SKU | Version | Image-cloud-init-fähig | cloud-init-Paketunterstützung in Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| ja | Ja – Unterstützung ab Paketversion: `20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| ja | Ja – Unterstützung ab Paketversion: `20.2-2~deb10u1` |


Derzeit unterstützt Azure Stack die Bereitstellung von cloud-init-fähigen Images.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Was ist der Unterschied zwischen cloud-init und Linux-Agent (WALA)?
WALA ist ein für die Azure-Plattform spezifischer Agent zum Bereitstellen und Konfigurieren von virtuellen Computern und zum Behandeln von [Azure-Erweiterungen](../extensions/features-linux.md). 

Wir ergänzen das Konfigurieren von virtuellen Computern um cloud-init anstelle des Linux-Agents, damit cloud-init-Kunden ihre vorhandenen cloud-init-Skripts und neue Kunden die umfangreichen cloud-init-Konfigurationsfunktionen nutzen können. Wenn Sie in cloud-init-Skripts zum Konfigurieren von Linux-Systemen investiert haben, sind für die Aktivierung **keine zusätzlichen Einstellungen erforderlich**, um die Verarbeitung mit cloud-init zu ermöglichen. 

„cloud-init“ kann keine Azure-Erweiterungen verarbeiten, sodass WALA weiterhin im Image erforderlich ist, um Erweiterungen zu verarbeiten, aber der Bereitstellungscode muss deaktiviert werden. Für unterstützte Linux-Distributionsimages, die für die Bereitstellung durch cloud-init konvertiert werden, muss WALA installiert und ordnungsgemäß eingerichtet sein.

Wenn Sie beim Erstellen eines virtuellen Computers während der Bereitstellung nicht den Switch `--custom-data` der Azure-Befehlszeilenschnittstelle einfügen, verwendet cloud-init oder WALA die minimalen VM-Bereitstellungsparameter zum Bereitstellen des virtuellen Computers und zum Fertigstellen der Bereitstellung mit den Standardeinstellungen.  Wenn Sie auf die cloud-init-Konfiguration mit dem Switch `--custom-data` verweisen, steht alles, was in Ihren benutzerdefinierten Daten enthalten ist, für die cloud-init-Konfiguration zur Verfügung, wenn der virtuelle Computer startet.

„cloud-init“-Konfigurationen, die auf virtuelle Computer angewendet werden, sind nicht zeitlich beschränkt und führen nicht zu Fehlern bei der Bereitstellung durch ein Timeout. Dies gilt nicht für WALA. Wenn Sie die WALA-Standardwerte ändern, um benutzerdefinierte Daten zu verarbeiten, darf die Gesamtdauer der VM-Bereitstellung von 40 Minuten nicht überschritten werden, da sonst ein Fehler bei der VM-Erstellung auftreten kann.

## <a name="cloud-init-vm-provisioning-without-a-udf-driver"></a>VM-Bereitstellung mit cloud-init ohne UDF-Treiber  
Ab cloud-init 21.2 können Sie cloud-init verwenden, um eine VM in Azure ohne einen UDF-Treiber bereitzustellen. Wenn kein UDF-Treiber im Image verfügbar ist, verwendet cloud-init die Metadaten, die im Azure Instance Metadata Service verfügbar sind, um die VM bereitzustellen. Beachten Sie, dass diese Option nur für SSH-Schlüssel und [Benutzerdaten](../user-data.md) funktioniert. Um während der Bereitstellung ein Kennwort oder benutzerdefinierte Daten an einen virtuellen Computer zu übergeben, müssen Sie einen UDF-Treiber verwenden.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Bereitstellen von cloud-init-fähigen virtuellen Computern
Das Bereitstellen eines clout-init-fähigen virtuellen Computers besteht einfach aus dem Verweisen auf eine cloud-init-fähige Distribution während der Bereitstellung.  Die Verwalter von Linux-Distributionen müssen cloud-init in ihren bei Azure veröffentlichten Basisimages aktivieren und integrieren. Nachdem Sie sich vergewissert haben, dass das Image, das Sie bereitstellen möchten, cloud-init unterstützt, können Sie es mit der Azure-Befehlszeilenschnittstelle bereitstellen. 

Der erste Schritt der Bereitstellung dieses Images ist die Erstellung einer Ressourcengruppe mit dem Befehl [az group create](/cli/azure/group). Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Im nächsten Schritt erstellen Sie in der aktuellen Shell eine Datei namens *cloud-init.txt* und fügen die folgende Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud-init.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte Datei „cloud-init“ ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> „cloud-init“ weist mehrere [Eingabetypen](https://cloudinit.readthedocs.io/en/latest/topics/format.html) auf und gibt in der ersten Zeile von „customData/userData“ an, wie die Eingabe verarbeitet werden soll. Beispielsweise gibt `#cloud-config` an, dass der Inhalt als cloud-init-Konfiguration verarbeitet werden soll.

Drücken Sie <kbd>STRG+X</kbd>, um die Datei zu verlassen, geben Sie <kbd>y</kbd> ein, um die Datei zu speichern, und drücken Sie die <kbd>EINGABETASTE</kbd>, um den Dateinamen beim Beenden zu bestätigen.

Erstellen Sie im letzten Schritt mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Das folgende Beispiel erstellt einen virtuellen Computer mit dem Namen `centos74` und SSH-Schlüssel, falls sie nicht bereits an einem Standard-Schlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.  Verwenden Sie den `--custom-data`-Parameter, um Ihre cloud-init-Konfigurationsdatei zu übergeben. Geben Sie den vollständigen Pfad zu der Konfigurationsdatei *cloud-init.txt* an, wenn Sie die Datei außerhalb Ihres vorhandenen Arbeitsverzeichnisses gespeichert haben. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Nach dem Erstellen des virtuellen Computers werden in der Azure-Befehlszeilenschnittstelle Informationen zu Ihrer Bereitstellung angezeigt. Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.  Es dauert einige Minuten, den virtuellen Computer zu erstellen, die Pakete zu installieren und die App zu starten. Es gibt Hintergrundaufgaben, die weiterhin ausgeführt werden, wenn Ihnen von der Azure CLI wieder eine Eingabeaufforderung angezeigt wird. Sie können per SSH eine Verbindung mit dem virtuellen Computer herstellen und anhand der Schritte im Abschnitt zur Problembehandlung die cloud-init-Protokolle anzeigen. 

Sie können auch einen cloud-init-fähigen virtuellen Computer bereitstellen, indem Sie die [Parameter in einer ARM-Vorlage](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters) übergeben.

## <a name="troubleshooting-cloud-init"></a>Beheben von Problemen mit cloud-init
Nachdem der virtuelle Computer bereitgestellt wurde, durchläuft cloud-init alle Module und Skripts, die in `--custom-data` definiert sind, um den virtuellen Computer zu konfigurieren.  Wenn Sie Fehler oder ausgelassene Einstellungen der Konfiguration korrigieren möchten, müssen Sie den Modulnamen (z.B. `disk_setup` oder `runcmd`) im cloud-init-Protokoll unter **/var/log/cloud-init.log** suchen.

> [!NOTE]
> Nicht jeder Modulfehler führt zu einer insgesamt fehlerhaften cloud-init-Konfiguration. Wenn Sie beispielsweise das Modul `runcmd` verwenden und das Skript einen Fehler verursacht, meldet cloud-init trotzdem eine erfolgreiche Bereitstellung, da das Modul runcmd ausgeführt wurde.

Weitere Informationen zur cloud-init-Protokollierung finden Sie in der [cloud-init-Dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

## <a name="next-steps"></a>Nächste Schritte

[Behandlung von Problemen mit cloud-init](cloud-init-troubleshooting.md)


cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Dokumenten:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
