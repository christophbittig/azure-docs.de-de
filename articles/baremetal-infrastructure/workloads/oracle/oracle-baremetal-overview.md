---
title: Worum handelt es sich bei der Bare-Metal-Infrastruktur für Oracle?
description: Hier finden Sie Informationen zu den Features der Bare-Metal-Infrastruktur für Oracle-Workloads.
ms.topic: conceptual
ms.subservice: baremetal-oracle
ms.date: 09/03/2021
ms.openlocfilehash: e74d1a3e3b6cb1b2cd0b99927a57a88117715e7b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542438"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Worum handelt es sich bei der Bare-Metal-Infrastruktur für Oracle?

In diesem Artikel geben wir einen Überblick über die Funktionen, die BareMetal Infrastructure für Oracle-Workloads bietet.

Die Bare-Metal-Infrastruktur für Oracle basiert auf dem von Oracle zertifizierten Unified Computing System (UCS) sowie auf FLexPod. Die FlexPod-Plattform zeichnet sich durch vorab überprüfte Speicher-, Netzwerk- und Servertechnologien aus. Es bietet NetApp Network File System (NFS)-Speicher und ermöglicht die Integration über das Direct NFS-Protokoll. Die Bare-Metal-Server sind dedizierte Server ohne Hypervisor für die Bare-Metal-Instanzen. 

Diese Instanzen sind für die Ausführung unternehmenskritischer Anwendungen vorgesehen, die eine Oracle-Workload erfordern. Bare-Metal-Instanzen zeichnen sich durch eine geringe Wartezeit (0,35 ms) für Ihre auf virtuellen Azure-Computern (virtual machines, VMs) ausgeführten Anwendungen aus. BareMetal bietet gemeinsamen Speicher. Es unterstützt auch Multi-Casting, das für die Knoten-zu-Knoten-Kommunikation mit einem dedizierten privaten Verbindungsnetzwerk erforderlich ist. 

Weitere Features der Bare-Metal-Infrastruktur für Oracle:

- Oracle-zertifizierte UCS-Blades: UCSB200-M5, UCSB460-M4, UCSB480-M5
- Oracle RAC-Kommunikation (Real Application Clusters) Kommunikation zwischen Knoten (Multicasting) mit privatem virtuellem LAN (VLAN): 40 GB
- Von Microsoft verwaltete Hardware
  - Redundanz bei Speicher, Netzwerk, Stromversorgung und Verwaltung
  - Überwachung für Infra-Knoten, Reparaturen und Austausch
  - Einschließlich Azure ExpressRoute zum Domänencontroller des Kunden
  - Physischer Schutz und Netzwerksicherheit, Zugriff auf alle Azure-Clouddienste

### <a name="supported-protocols"></a>Unterstützte Protokolle

Folgende Protokolle werden für verschiedene Bereitstellungspunkte auf Bare-Metal-Servern für Oracle-Workloads verwendet:

- Betriebssystemeinbindung: iSCSI
- Daten/Protokoll: NFSv3
- Sicherung/Archiv: NFSv4

### <a name="licensing"></a>Lizenzierung

- Sie verwenden Ihre eigenen lokalen Betriebssystem- und Oracle-Lizenzen.

### <a name="operating-system"></a>Betriebssystem

Auf Servern ist das Betriebssystem RHEL 7.6 vorab geladen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die SKUs für Oracle-Bare-Metal-Workloads.

> [!div class="nextstepaction"]
> [Bare-Metal-SKUs für Oracle-Workloads](oracle-baremetal-skus.md)
