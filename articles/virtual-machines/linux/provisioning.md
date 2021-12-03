---
title: Überblick über die Linux-Bereitstellung
description: Hier erhalten Sie einen Überblick darüber, wie Sie Ihre Linux-VM-Images zur Verwendung in Azure bereitstellen oder neue Images erstellen.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 70c3eaf554d1752b6aaa2a4ed1bfd05e9da67b5c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441151"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux-VM-Bereitstellung

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Wenn Sie einen virtuellen Computer aus einem generalisierten Image (über Azure Compute Gallery oder ein verwaltetes Image) erstellen, ermöglicht Ihnen die Steuerungsebene das Erstellen eines virtuellen Computers und das Übergeben von Parametern und Einstellungen an den virtuellen Computer. Dies wird als VM-*Bereitstellung* bezeichnet. Während der Bereitstellung stellt die Plattform dem virtuellen Computer beim Starten die erforderlichen „VM Create“-Parameterwerte (Hostname, Benutzername, Kennwort, SSH-Schlüssel, CustomData) zur Verfügung. 

Ein im Image erstellter Bereitstellungs-Agent stellt eine Verbindung mit der Plattform her (wobei eine Verbindung mit mehreren unabhängigen Bereitstellungsschnittstellen hergestellt wird) und legt die Eigenschaften und das Signal für die Plattform fest. 

Bei den Bereitstellungs-Agents kann es sich um den [Azure Linux-Agent](../extensions/agent-linux.md) oder [cloud-init](./using-cloud-init.md) handeln. Sie gelten als [Voraussetzungen](create-upload-generic.md) für das Erstellen generalisierter Images.

Die Bereitstellungs-Agents bieten Unterstützung für alle [von Azure unterstützten Linux-Distributionen](./endorsed-distros.md), und in vielen Fällen werden die unterstützten Distributionsimages sowohl mit cloud-init als auch mit dem Linux-Agent ausgeliefert. Dadurch haben Sie die Möglichkeit, dass cloud-init die Bereitstellung übernimmt, und der Linux-Agent bietet dann Unterstützung für die Handhabung der [Azure-Erweiterungen](../extensions/features-windows.md). Die Unterstützung für Erweiterungen bedeutet, dass der virtuelle Computer dann zusätzliche Azure-Dienste unterstützen kann, z. B. die VM-Kennwortzurücksetzung, Azure Monitor, Azure Backup, Azure Disk Encryption usw.

Nachdem die Bereitstellung abgeschlossen ist, wird cloud-init bei jedem Start ausgeführt. Cloud-init überwacht Änderungen am virtuellen Computer, z. B. Netzwerkänderungen, Einbinden und Formatieren des kurzlebigen Datenträgers und Starten des Linux-Agents. Der Linux-Agent wird kontinuierlich auf dem Server ausgeführt und sucht einen „Zielstatus“ (neue Konfiguration) der Azure-Plattform. Wenn Sie also Erweiterungen installieren, kann der Agent diese verarbeiten.

Es gibt aktuell zwei Bereitstellungs-Agents, aber Sie sollten cloud-init als Bereitstellungs-Agent auswählen, und der Linux-Agent sollte für die Erweiterungsunterstützung installiert werden. Dadurch können Sie Plattformoptimierungen nutzen und den Linux-Agent deaktivieren/entfernen. Weitere Informationen zum Erstellen von Images ohne den Agent sowie zum Entfernen des Agents finden Sie in dieser [Dokumentation](disable-provisioning.md).

Wenn Sie über einen Linux-Kernel verfügen, der das Ausführen eines Agents nicht unterstützt, Sie aber einige der „VM Create“-Eigenschaften (wie Hostname, CustomData, Benutzername, Kennwort und SSH-Schlüssel) festlegen möchten, lesen Sie dieses Dokument. Darin wird erläutert, wie Sie [generalisierte Images ohne Agent erstellen](no-agent.md) und Plattformanforderungen erfüllen können.


## <a name="provisioning-agent-responsibilities"></a>Aufgaben des Bereitstellungs-Agents

**Imagebereitstellung**
  
- Erstellen eines Benutzerkontos
- Konfigurieren von SSH-Authentifizierungstypen
- Bereitstellen von öffentlichen SSH-Schlüsseln und -Schlüsselpaaren
- Festlegen des Hostnamens
- Veröffentlichen des Hostnamens beim Plattform-DNS
- Mitteilen des SSH-Hostschlüssel-Fingerabdrucks an die Plattform
- Verwalten von Ressourcendatenträgern
- Formatieren und Bereitstellen des Ressourcendatenträgers
- Nutzen und Verarbeiten von `customData`
 
**Netzwerk**
  
- Verwaltet von Routen zur Verbesserung der Kompatibilität mit Plattform-DHCP-Servern
- Stellt die Stabilität des Netzwerkschnittstellennamens sicher

**Kernel**
  
- Konfiguriert einen virtuellen NUMA (muss für Kernel vor Version `2.6.37` deaktiviert werden)
- Nutzt Hyper-V-Entropie für `/dev/random`
- Konfiguriert SCSI-Zeitüberschreitungen für das Stammgerät (das auch remote sein kann)

**Diagnose**
  
- Konsolenumleitung an den seriellen Port

## <a name="communication"></a>Kommunikation
Der Informationsfluss von der Plattform zum Agenten erfolgt über zwei Kanäle:

- Über eine beim Start angeschlossene DVD für IaaS-Bereitstellungen. Die DVD enthält eine OVF-kompatible Konfigurationsdatei mit allen Bereitstellungsinformationen außer den tatsächlichen SSH-Schlüsselpaaren.
- Über einen TCP-Endpunkt, der eine REST-API verfügbar macht, um die Bereitstellungs- und Topologiekonfiguration abzurufen.


## <a name="azure-provisioning-agent-requirements"></a>Anforderungen des Azure Bereitstellungs-Agents
Für eine ordnungsgemäße Funktionsweise benötigen der Linux-Agent und cloud-init bestimmte Systempakete:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Dateisystemdienstprogramme: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Kennworttools: chpasswd, sudo
- Textverarbeitungstools: sed, grep
- Netzwerktools: ip-route
- Kernel-Unterstützung für die Einbindung von UDF-Dateisystemen.

## <a name="next-steps"></a>Nächste Schritte

Sie können ggf. [die Bereitstellung deaktivieren und den Linux-Agent entfernen](disable-provisioning.md).
