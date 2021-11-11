---
title: Häufig gestellte Fragen zur Anwendungsresilienz in Azure NetApp Files | Microsoft-Dokumentation
description: Beantwortet häufig gestellte Fragen (FAQs) zur Anwendungsresilienz in Azure NetApp Files.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 3a0b243203c0edb01bc5b647a15093ee52b78e7c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270659"
---
# <a name="application-resilience-faqs-for-azure-netapp-files"></a>Häufig gestellte Fragen zur Anwendungsresilienz in Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen (FAQs) zur Anwendungsresilienz in Azure NetApp Files beantwortet.

## <a name="what-do-you-recommend-for-handling-potential-application-disruptions-due-to-storage-service-maintenance-events"></a>Was empfehlen Sie für die Behandlung potenzieller Anwendungsunterbrechungen aufgrund von Wartungsereignissen des Speicherdiensts?

Azure NetApp Files kann gelegentlich geplant gewartet werden (z. B. Plattformupdates, Dienst- oder Softwareupgrades). Aus der Perspektive eines Dateiprotokolls (NFS/SMB) gelten Wartungsvorgänge als unterbrechungsfrei, solange die App die E/A-Pausen verarbeiten kann, die während dieser Ereignisse kurzzeitig auftreten können. Die E/A-Pausen sind für gewöhnlich kurz, zwischen ein paar Sekunden bis zu 30 Sekunden. Das NFS-Protokoll ist besonders robust, und Client-Server-Dateivorgänge werden normal fortgesetzt. Einige Anwendungen erfordern möglicherweise eine Optimierung, um E/A-Pausen von bis zu 30 bis 45 Sekunden zu verarbeiten. Stellen Sie daher sicher, dass Sie die Resilienzeinstellungen der Anwendung kennen, um die Wartungsereignisse des Speicherdiensts zu verarbeiten. Für interaktive Anwendungen, die das SMB-Protokoll nutzen, reichen die Standardprotokolleinstellungen in der Regel aus. 

## <a name="do-i-need-to-take-special-precautions-for-smb-based-applications"></a>Muss ich besondere Vorsichtsmaßnahmen für SMB-basierte Apps treffen?

Ja, für bestimmte SMB-basierte Apps ist SMB Transparent Failover erforderlich. SMB Transparent Failover ermöglicht Wartungsvorgänge für den Azure NetApp Files-Dienst ohne Unterbrechung der Verbindung mit Serveranwendungen, die Daten auf SMB-Volumes speichern und auf diese Daten zugreifen. Zur Unterstützung von SMB Transparent Failover für bestimmte Apps unterstützt Azure NetApp Files jetzt die [Freigabe-Option für SMB Continuous Availability](azure-netapp-files-create-volumes-smb.md#continuous-availability). 

## <a name="i-am-running-ibm-mq-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Ich nutze IBM MQ auf Azure NetApp Files. Welche Vorsichtsmaßnahmen kann ich ergreifen, um Unterbrechungen aufgrund von Wartungsereignissen des Speicherdiensts zu vermeiden, obwohl ich das NFS-Protokoll verwende?

Wenn Sie die [IBM MQ-App in einer Konfiguration mit freigegebenen Dateien](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-sharing-mq-files) nutzen, in der die IBM MQ-Daten und -Protokolle auf einem Azure NetApp Files-Volume gespeichert werden, folgen Sie diesen Empfehlungen, um die Resilienz bei Wartungsereignissen des Speicherdiensts zu verbessern:

* Sie dürfen nur das NFS V4.1-Protokoll verwenden.
* Sie sollten eine [IBM MQ-Konfiguration mit mehreren Instanzen, die freigegebene NFS V4.1-Volumes nutzt](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=manager-create-multi-instance-queue-linux), für Hochverfügbarkeit verwenden. 
* Sie sollten die Funktionalität der [IBM-Konfiguration mit mehreren Instanzen, die freigegebene NFS V4.1-Volumes nutzt](https://www.ibm.com/docs/en/ibm-mq/9.2?topic=multiplatforms-verifying-shared-file-system-behavior), überprüfen. 
* Sie sollten eine IBM MQ-Architektur mit horizontaler Skalierung einführen, anstatt eine große IBM MQ-Konfiguration mit mehreren Instanzen zu verwenden. Durch das Verteilen der Nachrichtenverarbeitungslast auf mehrere IBM MQ-Paare mit mehreren Instanzen kann die Wahrscheinlichkeit von Dienstunterbrechungen verringert werden, da jedes MQ-Paar mit mehreren Instanzen weniger Nachrichten verarbeiten muss.

> [!NOTE] 
> Die Anzahl der Nachrichten, die jedes MQ-Paar mit mehreren Instanzen verarbeiten sollte, hängt stark von Ihrer spezifischen Umgebung ab. Sie müssen entscheiden, wie viele MQ-Paare mit mehreren Instanzen benötigt werden, oder wie die Regeln zum Hoch- oder Herunterskalieren lauten werden.

Die Architektur für die horizontale Skalierung besteht aus mehreren IBM MQ-Paaren mit mehreren Instanzen, die hinter einem Azure Load Balancer bereitgestellt werden. Apps, die für die Kommunikation mit IBM MQ konfiguriert sind, werden dann für die Kommunikation mit den IBM MQ-Instanzen über Azure Load Balancer konfiguriert. Um Unterstützung im Zusammenhang mit IBM MQ auf freigegebenen NFS-Volumes zu erhalten, wenden Sie sich an den Anbietersupport bei IBM.

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despite-using-the-nfs-protocol"></a>Ich nutze Apache ActiveMQ mit LevelDB oder KahaDB auf Azure NetApp Files. Welche Vorsichtsmaßnahmen kann ich ergreifen, um Unterbrechungen aufgrund von Wartungsereignissen des Speicherdiensts zu vermeiden, obwohl ich das *NFS*-Protokoll verwende?

>[!NOTE]
> Dieser Abschnitt enthält Verweise auf die Begriffe *Slave* und *Master*, die von Microsoft nicht mehr verwendet werden. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Wenn Sie Apache ActiveMQ nutzen, wird empfohlen, [ActiveMQ-Hochverfügbarkeit mit Pluggable Storage Lockers](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) bereitzustellen. 

ActiveMQ-Hochverfügbarkeitsmodelle (HA-Modelle) stellen sicher, dass stets eine Brokerinstanz online ist und den Nachrichtendatenverkehr verarbeiten kann. Bei den beiden häufigsten ActiveMQ-HA-Modellen muss ein Dateisystem über ein Netzwerk geteilt werden. Der Zweck ist die Bereitstellung von LevelDB oder KahaDB für die aktiven und passiven Brokerinstanzen. Diese HA-Modelle erfordern, dass eine Sperre für eine Datei in den LevelDB- oder KahaDB-Verzeichnissen auf Betriebssystemebene, „Lock“ genannt, eingesetzt und verwaltet wird. Mit diesem ActiveMQ-HA-Modell gibt es ein paar Probleme. Es kann zu einer „No Master“-Situation führen, in der der „Slave“ nicht weiß, dass er die Datei sperren kann.  Es kann auch zu einer „Master-Master“-Konfiguration kommen, die zu einer Beschädigung des Indexes oder Journals und letztendlich zu Nachrichtenverlusten führt. Die meisten dieser Probleme sind auf Faktoren zurückzuführen, auf die ActiveMQ keinen Einfluss hat. Zum Beispiel kann ein schlecht optimierter NFS-Client dafür sorgen, dass Sperrdaten unter Last veralten, was während dem Failover zu „No Master“-Downtime führt. 

Da die meisten Probleme mit dieser Hochverfügbarkeitslösung auf ungenaue Dateisperren auf Betriebssystemebene zurückzuführen sind, hat die ActiveMQ-Community [das Konzept eines Pluggable Storage Lockers](https://www.openlogic.com/blog/pluggable-storage-lockers-activemq) in Version 5.7 des Brokers eingeführt. Dieser Ansatz ermöglicht es Benutzer*innen, eine andere Art gemeinsame Sperre zu nutzen, und zwar eine JDBC-Datenbanksperre auf Zeilenebene anstatt einer Dateisystemsperre auf Betriebssystemebene. Wenn Sie Unterstützung bei ActiveMQ-HA-Architekturen und -Bereitstellungen erhalten möchten, [wenden Sie sich an OpenLogic von Perforce](https://www.openlogic.com/contact-us).

## <a name="i-am-running-apache-activemq-with-leveldb-or-kahadb-on-azure-netapp-files-what-precautions-can-i-take-to-avoid-disruptions-due-to-storage-service-maintenance-events-despites-using-the-smb-protocol"></a>Ich nutze Apache ActiveMQ mit LevelDB oder KahaDB auf Azure NetApp Files. Welche Vorsichtsmaßnahmen kann ich ergreifen, um Unterbrechungen aufgrund von Wartungsereignissen des Speicherdiensts zu vermeiden, obwohl ich das *SMB*-Protokoll verwende?

Die allgemeine Branchenempfehlung ist, Ihren [freigegebenen KahaDB-Speicher nicht unter CIFS/SMB zu nutzen](https://www.openlogic.com/blog/activemq-community-deprecates-leveldb-what-you-need-know). Wenn Sie Probleme beim Aufrechterhalten eines genauen Sperrzustands haben, sehen Sie sich den JDBC Pluggable Storage Locker an, der über einen zuverlässigeren Sperrmechanismus verfügt. Wenn Sie Unterstützung bei ActiveMQ-HA-Architekturen und -Bereitstellungen erhalten möchten, [wenden Sie sich an OpenLogic von Perforce](https://www.openlogic.com/contact-us).

## <a name="next-steps"></a>Nächste Schritte  

- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Häufig gestellte Fragen zum Netzwerk](faq-networking.md)
- [Häufig gestellte Fragen zur Sicherheit](faq-security.md)
- [Häufig gestellte Fragen zur Leistung](faq-performance.md)
- [Häufig gestellte Fragen zu NFS](faq-nfs.md)
- [Häufig gestellte Fragen zu SMB](faq-smb.md)
- [Häufig gestellte Fragen zur Kapazitätsverwaltung](faq-capacity-management.md)
- [Häufig gestellte Fragen zu Datenmigration und -schutz](faq-data-migration-protection.md)
- [Häufig gestellte Fragen zur Azure NetApp Files-Sicherung](faq-backup.md)
- [Häufig gestellte Fragen zur Integration](faq-integration.md)

