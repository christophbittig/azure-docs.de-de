---
title: Grundlegendes zu cloud-init
description: Ausführliche Informationen zum Bereitstellen einer Azure-VM mit cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: 4ca42c259e10742e61fbd7f36b73f332cba5a8db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445828"
---
# <a name="diving-deeper-into-cloud-init"></a>Tiefere Einblicke in cloud-init
Um sich tiefergehendes Wissen zu [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) anzueignen oder Problembehandlung für cloud-init auf einer tieferen Ebene auszuführen, müssen Sie verstehen, wie cloud-init funktioniert. In diesem Dokument werden die wichtigen Teile hervorgehoben, und es werden die Azure-Besonderheiten erläutert.

Ist cloud-init in einem generalisierten Image enthalten, und wird eine VM mit diesem Image erstellt, durchläuft cloud-init beim Erststart fünf Phasen und verarbeitet dabei Konfigurationen. Diese Phasen sind wichtig, da Sie Ihnen zeigen, an welchem Punkt cloud-init Konfigurationen anwendet. 


## <a name="understand-cloud-init-configuration"></a>Grundlegendes zur cloud-init-Konfiguration
Wenn ein virtueller Computer für die Ausführung auf einer Plattform konfiguriert wird, müssen von cloud-init mehrere Konfigurationen angewendet werden. Sie als Imagenutzer interagieren in erster Linie mit Konfigurationen vom Typ `User data` (customData). Dabei werden mehrere Formate unterstützt. Weitere Informationen finden Sie in der [Dokumentation zu Benutzerdatenformaten und cloud-init 21.2](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). Sie können zur weiteren Konfiguration auch Skripts (/var/lib/cloud/scripts) hinzuzufügen und auszuführen. Dies ist weiter unten ausführlicher erläutert.

Einige Konfigurationen sind bereits in Azure Marketplace-Images integriert, die mit cloud-init bereitgestellt werden. Dazu gehören:

1. **Cloud-Datenquelle**: cloud-init enthält Code, der mit Cloudplattformen interagieren kann. Diese werden als „Datenquellen“ (Datasources) bezeichnet. Wenn eine VM aus einem cloud-init-image in [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure) erstellt wird, lädt cloud-init die Azure-Datenquelle, die mit den Azure-Metadatenendpunkten interagiert, um die VM-spezifische Konfiguration abzurufen.
2. **Laufzeitkonfiguration** (/run/cloud-init)
3. **Imagekonfiguration** (/etc/cloud), z. B. `/etc/cloud/cloud.cfg`, `/etc/cloud/cloud.cfg.d/*.cfg`. Ein Beispiel dafür, wo dies in Azure verwendet wird: Üblicherweise haben Linux-Betriebssystem-Images mit cloud-init eine Azure-Datenquellendirektive, in der cloud-init mitgeteilt wird, welche Datenquelle es verwenden soll. Dadurch kann cloud-init Zeit sparen:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Cloud-init-Startphasen (Verarbeitungskonfiguration)

Ist in einer Bereitstellung cloud-init enthalten, gibt es fünf Startphasen, in denen die Konfiguration verarbeitet wird und die in den Protokollen zu sehen sind.

1. [Generator-Phase](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): Der cloud-init-Generator systemd wird gestartet. Dieser ermittelt dann, ob cloud-init in die Startziele einbezogen werden soll, und ist dies der Fall, aktiviert er cloud-init. 

2. [Cloud-init-Phase Local](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): Hier sucht cloud-init nach der lokalen Datenquelle „Azure“. Dadurch wird es cloud-init ermöglicht, eine Verbindung mit Azure herzustellen und eine Netzwerkkonfiguration, einschließlich Fallback, anzuwenden.

3. [Cloud-init-Phase Network](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): Das Netzwerk muss funktionsbereit sein, und die NIC- und Routingtabelleninformationen müssen generiert werden. In dieser Phase werden die Module ausgeführt, die in „/etc/cloud/cloud.cfg“ in `cloud_init_modules` aufgeführt sind. Die VM wird in Azure eingebunden, der kurzlebige Datenträger wird formatiert, der Hostname wird festgelegt, und es werden weitere Aufgaben ausgeführt.

   Diese Module sind einige der `cloud_init_modules`:
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Nach dieser Phase signalisiert cloud-init der Azure-Plattform, dass die VM erfolgreich bereitgestellt wurde. Bei einigen Modulen sind möglicherweise Fehler aufgetreten, nicht alle Modulfehler führen zu einem Bereitstellungsfehler.

4. [Cloud-init-Phase Config](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): In dieser Phase werden die Module in `cloud_config_modules` ausgeführt, die in „/etc/cloud/cloud.cfg“ definiert und aufgeführt sind.


5. [Cloud-init-Phase Final](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): In dieser abschließenden Phase werden die Module ausgeführt, die in „/etc/cloud/cloud.cfg“ in `cloud_final_modules` aufgeführt sind. Hier werden Module ausgeführt, die spät im Startprozess ausgeführt werden müssen, z. B. Paketinstallationen, werden Skripts ausgeführt usw. 

   -   In dieser Phase können Sie Skripts ausführen, indem Sie sie in den Verzeichnissen unter `/var/lib/cloud/scripts` platzieren:
   - `per-boot`: Skripts in diesem Verzeichnis werden bei jedem Neustart ausgeführt.
   - `per-instance`: Skripts in diesem Verzeichnis werden ausgeführt, wenn eine neue Instanz erstmalig gestartet wird.
   - `per-once`: Skripts in diesem Verzeichnis werden nur ein Mal ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung bei VM-Bereitstellung mit cloud-init](cloud-init-troubleshooting.md)
