---
title: Überlegungen zum Ausführen von ISV-Dateidiensten in Azure
titleSuffix: Azure Storage
description: Grundlegende Anleitung für verschiedene ISV-Optionen zur Ausführung von Dateidiensten in Azure
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 05/24/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 8b49504fdda0b065b28b90f946dd203716933745
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446134"
---
# <a name="running-isv-file-services-in-azure"></a>Ausführen von ISV-Dateidiensten in Azure

Azure bietet verschiedene Optionen zum Speichern von Dateidaten. Native Azure-Dienste sind die folgenden:
- [Azure Files](https://azure.microsoft.com/services/storage/files/) – Vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll SMB und NFS-Protokolle zugegriffen werden kann. Azure Files bietet zwei verschiedene Typen (Standard und Premium) mit unterschiedlichen Leistungsmerkmalen.
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) – Vollständig verwaltete Dateifreigaben in der Cloud, die die Leistungsanforderungen für branchenspezifische Unternehmensanwendungen erfüllen. Azure NetApp Files bietet mehrere Servicelevel mit unterschiedlichen Leistungseinschränkungen (Standard, Premium und Ultra).
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) – große Objektspeicherplattform zum Speichern von unstrukturierten Daten. Azure Blob Storage bietet zwei verschiedene Typen (Standard und Premium) mit unterschiedlichen Leistungsmerkmalen. 
  
Es gibt mehrere Artikel, in denen die Unterschiede und Empfehlungen zur Auswahl des nativen Dateidiensts beschrieben werden. Hier können Sie mehr erfahren:
- In unserem Migrationsleitfaden wird das [grundlegende Flussdiagramm](../../../common/storage-migration-overview.md#choose-a-target-storage-service) beschrieben.
- [Detaillierter Vergleich zwischen Azure Files und Azure NetApp Files](../../../files/storage-files-netapp-comparison.md)

Mit vielen unabhängigen Softwareherstellerlösungen (Independent Software Vendor, ISV) können Dateidienste in Azure bereitgestellt werden. In diesem Artikel werden zwei Themen behandelt:
- Er enthält allgemeine Überlegungen zum Auswählen von Dateidiensten.
- Es werden darin die Unterschiede zwischen ISV-Lösungen beschrieben.
  
Die vollständige Liste von überprüften ISV-Lösungen steht unter [Azure Storage-Partner für primären und sekundären Speicher](./partner-overview.md) zur Verfügung.

## <a name="considerations"></a>Überlegungen

Bei Auswahl der optimalen Lösung für die Ausführung von Dateidiensten in Azure sind mehrere Überlegungen wichtig.

### <a name="basic-considerations"></a>Allgemeine Überlegungen

Bei grundlegenden Überlegungen zur Funktionalität werden die gängigsten Funktionen der zugrunde liegenden Speicherplattformen untersucht. Dieser Schritt wird zuerst ausgeführt, um zu untersuchen, welche Lösungen die grundlegende Funktionalität bieten, die wir zur Erfüllung unserer Anforderungen benötigen. Wichtige grundlegende Features variieren von Fall zu Fall. Die am häufigsten verwendeten sind Protokollunterstützung, Namespacegröße, automatisches Tiering, Verschlüsselung, WORM-Unterstützung und Authentifizierung. Die Liste der grundlegenden Funktionen, die von unseren ISV-Lösungen unterstützt werden, finden Sie im [Featurevergleich](#isv-solutions-comparison).

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Nach Auswahl der Lösung, die den grundlegenden Funktionen entspricht, muss die erforderliche Leistung berücksichtigt werden. Eine Schätzung der Leistung ist abhängig von drei grundlegenden Leistungsmerkmalen:
  - Wartezeit
  - Bandbreite
  - IOPS oder TPS

Die Wichtigkeit grundlegender Leistungsmerkmale ist abhängig von der Parallelität der Workloads (Anzahl paralleler Anforderungen) und der Blockgröße (Anforderungsgröße).

| Workloadtyp | Empfehlungen |
| -------------------- | --------------- |
| **Geringe Parallelität** | Die Wartezeit (Latenz) ist der wichtigste Aspekt. Je geringer die Wartezeit, desto mehr Leistung kann erreicht werden. In Workloads mit geringer Parallelität werden IOPS- und Bandbreiteneinschränkungen selten überschritten. |
| **Hohe Parallelität** | Die Auswirkung auf die Wartezeit ist aufgrund von hoher Parallelität wesentlich geringer. IOPS und/oder Bandbreite müssen berücksichtigt werden. |
| **Blockgröße** | Bei Workloads mit kleinen Blockgrößen müssen IOPS-Grenzwerte eher berücksichtigt werden, während Bandbreitenlimits für Workloads mit großen Blockgrößen wichtiger sind. |

Jede Speicherworkload kann unter Berücksichtigung dieser Merkmale beschrieben werden. OLTP-Workloads haben beispielsweise in der Regel eine hohe Parallelität und kleine Blockgrößen. HPC-Workloads haben normalerweise eine hohe Parallelität, aber die Blockgröße kann von klein bis groß variieren. 
    
Einige allgemeine Regeln werden immer empfohlen:
  - Protokollauswahl: Verwenden Sie nach Möglichkeit SMB3 mit Multichannelunterstützung oder NFSv3 mit nconnect-Unterstützung, da dies eine bessere Leistung bietet. Vermeiden Sie die Verwendung von Legacyprotokollen sowohl aus Leistungsgründen als auch aus Sicherheitsaspekten. Beachten Sie außerdem, dass Clients ebenfalls optimiert werden können und dies zur Maximierung der Leistung empfohlen wird.
  - Netzwerk: Wenn der VM-Typ dies unterstützt, verwenden Sie beschleunigten Netzwerkbetrieb. Dies reduziert die Wartezeit und hat immer eine positive Auswirkung auf die Leistung. 
  - VM-Typ: Wählen Sie den VM-Typ aus, der für die Workload am besten geeignet ist. Wenn die Workload eine kleine Anzahl von Clients enthält, ist das Ausführen von Dateidiensten in einer kleineren Anzahl von größeren VMs besser geeignet. Im Gegensatz dazu kann eine große Anzahl von Clients von der Ausführung von Dateidiensten in einer größeren Anzahl kleinerer VMs profitieren.
  - Bei Workloads mit geringer Parallelität und geringer Blockgröße untersuchen Sie Lösungen, die:
    - verwaltete Datenträger verwenden (Premium- oder SSD Ultra-Datenträger) oder
    - einen geeigneten Cachealgorithmus haben.
  - Erkunden Sie Lösungen, die Azure Blob Storage als Back-End verwenden, um eine hohe Parallelität und Workloads mit großen Blockgrößen zu ermöglichen.

## <a name="isv-solutions-overview-and-example-use-cases"></a>ISV-Lösungen: Übersicht und Beispiele für Anwendungsfälle

In diesem Artikel werden mehrere ISV-Lösungen verglichen, die Dateidienste in Azure bereitstellen.

| Lösung | Übersicht | Beispiele für Anwendungsfälle |
| -------- | ----------- | ----------------- |
| **Nasuni** | **UniFS** ist ein Unternehmensdateidienst mit einer einfacheren, kostengünstigen Cloudalternative, die auf Microsoft Azure basiert | – Primärer Dateispeicher <br> – Abteilungsdateifreigaben <br> – Zentralisierte Dateiverwaltung <br> – Zusammenarbeit mit mehreren Websites bei globaler Dateisperrung <br> – Windows Virtual Desktop <br> – Remotearbeits-/VDI-Dateifreigaben |
| **NetApp** | **Cloud Volumes ONTAP** optimiert Ihre Cloudspeicherkosten und -leistung, während es gleichzeitig den Schutz, die Sicherheit und die Compliance von Daten verbessert. Umfasst Datenverwaltung, Verfügbarkeit und Dauerhaftigkeit auf Unternehmensniveau | – Geschäftsanwendungen <br> – Relationale und NoSQL-Datenbanken <br> – Big Data und Analysen <br> – Persistente Daten für Container <br> – CI/CD-Pipelines <br> – Notfallwiederherstellung für lokale NetApp-Lösungen |
| **Panzura**| **CloudFS** ist ein globales Dateisystem für Unternehmen, das sich durch zusätzliche Resilienz und hohe Leistung auszeichnet. Es bietet Schutz vor Ransomware. | – Vereinfachter Austausch von Legacyspeicher <br> – Sicherung und Notfallwiederherstellung mit präziser Wiederherstellung <br> – Cloudbasierter Zugriff auf unstrukturierte Daten für Analysen, KI/ML. <br> – Dateizusammenarbeit an mehreren Standorten mit automatischer Dateisperrung und globaler Dateikonsistenz in Echtzeit <br> – Globale Remotearbeit mit Cloud-VDI <br> – Beschleunigte Cloudmigration für Legacyworkloads |
| **Tiger Technology** | **Tiger Bridge** ist eine Softwarelösung für die Datenverwaltung. Bietet Tiering zwischen einem NTFS-Dateisystem und Azure Blob Storage oder verwalteten Azure-Datenträgern. Erstellt einen einzelnen Namespace mit lokaler Dateisperre. | – Cloudarchiv<br> – Fortlaufender Datenschutz (Continuous Data Protection, CDP) <br> – Notfallwiederherstellung für Windows-Server <br> – Synchronisierung und Zusammenarbeit an mehreren Standorten <br> – Remoteworkflows (VDI)<br> – Nativer Zugriff auf Clouddaten für Analyse, KI, ML |
| **XenData** | **Cloud File Gateway** erstellt mithilfe von Windows-Dateiservern ein hochgradig skalierbares globales Dateisystem. | – Globale Freigabe von technischen und wissenschaftlichen Dateien <br> – Gemeinsame Videobearbeitung |

## <a name="isv-solutions-comparison"></a>Vergleich von ISV-Lösungen

### <a name="supported-protocols"></a>Unterstützte Protokolle

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **SMB 2.1**                                         | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **SMB 3.0**                                         | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **SMB 3.1**                                         | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **NFS v3**                                          | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **NFS v4.1**                                        | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **iSCSI**                                           | Nein                   | Ja                            | Nein                        | Ja                   | Nein                    |

### <a name="supported-services-for-persistent-storage"></a>Unterstützte Dienste für persistenten Speicher

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Verwaltete Datenträger**                                   | Nein                   | Ja                            | Ja                       | Ja                   | Nein                    |
| **Nicht verwaltete Datenträger**                                 | Nein                   | Nein                             | Nein                        | Ja                   | Nein                    |
| **Azure Storage-Blockblobs**                       | Ja                  | Ja (Tiering)                  | Ja                       | Ja                   | Ja                   |
| **Azure Storage-Seitenblobs**                        | Nein                   | Ja (für Hochverfügbarkeit)                   | Ja                       | Nein                    | Nein                    |
| **Unterstützung für Azure-Archivspeicherebene**                      | Nein                   | Nein                             | Ja                       | Ja                   | Ja                   |
| **Dateien, auf die in nicht transparentem Format zugegriffen werden kann**           | Nein                   | Nein                             | Nein                        | Ja                   | Ja                   |

### <a name="extended-features"></a>Erweiterte Funktionen

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Betriebsumgebung**                           | UniFS                | ONTAP                          | PFOS                      | Windows Server        | Windows Server              |
| **Hochverfügbarkeit**                               | Ja                  | Ja                            | Ja                       | Ja (erfordert Setup)  | Ja                   |
| **Automatisches Failover zwischen Knoten im Cluster** | Ja                  | Ja                            | Ja                       | Ja (Windows-Cluster) | ja (Windows-Cluster) |
| **Automatisches Failover über Verfügbarkeitszonen hinweg**    | Ja                  | Nein                             | Ja                       | Ja (Windows-Cluster) | ja (Windows-Cluster) |
| **Regionsübergreifendes automatisches Failover**               | Ja (mit Nasuni-Unterstützung)| Nein                         | Nein                        | Ja (Windows-Cluster) | ja (Windows-Cluster) |
| **Unterstützung von Momentaufnahmen**                                | Ja                  | Ja                            | Ja                       | Ja                   | Nein                    |
| **Konsistente Unterstützung von Momentaufnahmen**                     | Ja                  | Ja                            | Ja                       | Ja                   | Nein                    |
| **Integrierte Sicherung**                               | Ja                  | Ja (Add-On)                   | Nein                        | Ja                   | Ja                   |
| **Versionsverwaltung**                                      | Ja                  | Ja                            | Nein                        | Ja                   | Ja                   |
| **Wiederherstellung auf Dateiebene**                              | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **Wiederherstellung auf Volumeebene**                            | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **WORM**                                            | Ja                  | Ja                            | Nein                        | Ja                   | Nein                    |
| **Automatisches Tiering**                               | Ja                  | Ja                            | Nein                        | Ja                   | Ja                   |
| **Globale Dateisperrung**                             | Ja                  | Ja (Globaler NetApp-Dateicache) | Ja                       | Ja                   | Ja                   |
| **Namespace-Aggregation über Back-End-Quellen**      | Ja                  | Ja                            | Nein                        | Ja                   | Ja                   |
| **Zwischenspeichern von aktiven Daten**                          | Ja                  | Ja                            | Ja                       | ja                   | Ja                   |
| **Unterstützte Cachemodi**                         | LRU, manuell angeheftet | LRU                            | LRU, manuell angeheftet      | LRU                   | LRU                   |
| **Verschlüsselung ruhender Daten**                              | Ja                  | Ja                            | Ja                       | Ja                   | Nein                    |
| **Deduplizierung**                                  | Ja                  | Ja                            | Ja                       | Nein                    | Nein                    |
| **Komprimierung**                                     | Ja                  | Ja                            | Ja                       | Nein                    | Nein                    |

### <a name="authentication-sources"></a>Authentifizierungsquellen

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Azure AD-Unterstützung**                                | Ja (über ADDS)       | Ja (über ADDS)                 | Ja (über ADDS)            | Ja (über ADDS)        | Ja (über ADDS)        |
| **Unterstützung für Active Directory**                        | Ja                  | Ja                            | Ja                       | Ja                   | Ja                   |
| **LDAP-Unterstützung**                                    | Ja                  | Ja                            | Nein                        | Ja                   | Ja                   |

### <a name="management"></a>Verwaltung

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **REST-API**                                        | Ja                  | Ja                            | Ja                       | Ja                   | Nein                    |
| **Web-GUI**                                         | Ja                  | Ja                            | Ja                       | Nein                    | Nein                    |

### <a name="scalability"></a>Skalierbarkeit

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Maximale Anzahl von Knoten in einem einzelnen Cluster**     | 100                  | 2 (Hochverfügbarkeit)                         | Bis zu 60 Knoten getestet    | Nicht verfügbar                 | Nicht verfügbar                 |
| **Maximale Anzahl von Volumes**                       | 800                  | 1024                           | Unbegrenzt                 | Nicht verfügbar                 | 1                     |
| **Maximale Anzahl von Momentaufnahmen**                     | Unbegrenzt            | Unbegrenzt                      | Unbegrenzt                 | Nicht verfügbar                 | Nicht verfügbar                 |
| **Maximale Größe eines einzelnen Namespaces**              | Unbegrenzt            | Abhängig von der Infrastruktur      | Unbegrenzt                 | Nicht verfügbar                 | Nicht verfügbar                 |

### <a name="licensing"></a>Lizenzierung

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **BYOL**                                            | Ja                  | Ja                            | Ja                       | Ja                   | ja                   |
| **Für Azure-Vorteil berechtigt**                          | Nein                   | Ja                            | Ja                       | Nein                    | Nein                    |
| **Bereitstellungsmodell (IaaS, SaaS)**                   | IaaS                 | IaaS                           | IaaS                      | IaaS                  | IaaS                  |

### <a name="other-features"></a>Andere Funktionen

**Nasuni**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/nasunicorporation.nasuni-nea-90-prod?tab=Overview)
- Kann verwaltete Datenträger zum Zwischenspeichern von Daten verwenden
- Zentrale Verwaltung aller Edge-Appliances
- Unbegrenzter Verlauf der Dateiversionsverwaltung
- Unbegrenzte Dateisystemgröße
- Unbegrenzte Dateigröße
- Zugriff auf Dateien über HTTPS, REST-API und FTP
- Lastenausgleich für mehrere Filer mit Azure Load Balancer
- Verschlüsselung mit kundenseitig verwalteten Schlüsseln
- Unterstützt Sicherheits- und Überwachungstools von Drittanbietern (z. B. Varonis, Stealthbits)
- White-Glove-Bereitstellung und professionelle Dienste

**NetApp**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/netapp.netapp-ontap-cloud?tab=Overview)
- Einsparungen durch Deduplizierung, die über einen reduzierten Infrastrukturverbrauch an den Kunden weitergegeben werden

**Panzura**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/panzura-file-system.panzura-freedom-filer)
- Sperrung auf Byteebene (mehrere gleichzeitige R/W-Öffnungen)

**Tiger Technology**
- Für Anwendungen unsichtbar
- Partielle Wiederherstellung
- Windows Shell-Integration (Überlagerung, Kontextmenü, Eigenschaftenblatt)
- Azure-Unterstützung für vorläufiges Löschen und Rückgängigmachen des Löschvorgangs
- Option zum Anwenden von Umbenennungen auf das Cloudziel
- Partielles Schreiben in Objekte
- Ransomware-Schutz

**XenData**
- Der Cosmos DB-Dienst ermöglicht eine schnelle Synchronisierung von mehreren Gateways, einschließlich anwendungsspezifischer Besitzerdateien für globale Zusammenarbeit. 
- Jedes Gateway bietet eine hochgradig präzise Kontrolle über lokal zwischengespeicherte Inhalte.
- Unterstützt Videostreaming und partielle Dateiwiederherstellungen
- Unterstützt Azure Data Box-Uploads
- Von Azure Blob Storage bereitgestellte Verschlüsselung

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- [Azure-Datenträger](../../../../virtual-machines/managed-disks-overview.md)
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Geprüfte Partner für primären und sekundären Speicher](./partner-overview.md)
- [Übersicht über die Azure Storage-Migration](../../../common/storage-migration-overview.md)
