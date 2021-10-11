---
title: Neues in Azure NetApp Files | Microsoft-Dokumentation
description: Bietet eine Übersicht über die neuesten Features und Verbesserungen in Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/05/2021
ms.author: b-juche
ms.openlocfilehash: 2dff42fcf855a41e2e907563b1aecccad0a1661a
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545923"
---
# <a name="whats-new-in-azure-netapp-files"></a>Neues in Azure NetApp Files

Azure NetApp Files wird regelmäßig aktualisiert. Dieser Artikel bietet eine Übersicht über die neuesten Features und Verbesserungen. 

    
## <a name="october-2021"></a>Oktober 2021

* [Standard-Netzwerkfeatures](configure-network-features.md) (Vorschau)

    Azure NetApp Files unterstützt jetzt **Standard**-Netzwerkfeatures für Volumes, die von der Kundschaft seit der Einführung gefordert wurden. Diese Funktion wurde durch innovative Hardware- und Softwareintegrationen ermöglicht. Standard-Netzwerkfeatures bieten eine erweiterte virtuelle Netzwerkumgebung über eine Vielzahl von Features für eine nahtlose und konsistente Umgebung sowie einen einheitlichen Sicherheitsstatus für alle Workloads, einschließlich Azure NetApp Files.
    
    Sie können jetzt *Standard*- oder *Basic*-Netzwerkfeatures auswählen, während Sie ein neues Azure NetApp Files-Volume erstellen. Wenn Sie Standard-Netzwerkfeatures auswählen, können Sie die folgenden unterstützten Features für Azure NetApp Files-Volumes und delegierte Subnetze nutzen:   
    * Höhere IP-Grenzwerte für VNets mit Azure NetApp Files-Volumes im Vergleich zu VMs
    * Verbesserte Netzwerksicherheit mit Unterstützung für [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) im delegierten Azure NetApp Files-Subnetz
    * Verbesserte Netzwerksteuerung mit Unterstützung für [benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md#custom-routes) zum und vom delegierten Azure NetApp Files-Subnetz
    * Konnektivität über die Einrichtung eines Aktiv/Aktiv-VPN-Gateways
    * [ExpressRoute FastPath](../expressroute/about-fastpath.md)-Konnektivität mit Azure NetApp Files

    Diese öffentliche Vorschauversion ist derzeit zunächst in der Region **USA, Norden-Mitte** verfügbar und wird nach und nach auch in anderen Regionen bereitgestellt.  Informieren Sie sich über [Azure Update](https://azure.microsoft.com/updates/), wann weitere Regionen und Features verfügbar gemacht werden.  
 
    Weitere Informationen finden Sie unter [Konfigurieren von Netzwerkfeatures für ein Azure NetApp Files-Volume](configure-network-features.md).

## <a name="september-2021"></a>September 2021

* [Sicherung bei Azure NetApp Files](backup-introduction.md) (Vorschau)

    Azure NetApp Files Onlinemomentaufnahmen werden jetzt durch die Sicherung von Momentaufnahmen erweitert. Mit dieser neuen Sicherungsfunktion können Sie Ihre Azure NetApp Files-Momentaufnahmen schnell und kostengünstig in einen kostengünstigen und ZRS-fähigen Azure-Speicher tresor und so Ihre Daten vor versehentlichem Löschen schützen. Azure NetApp Files Backup erweitert die integrierte Momentaufnahmetechnologie von ONTAP. Wenn Momentaufnahmen im Tresor in Azure Storage gespeichert werden, werden nur geänderte Blöcke relativ zu zuvor tresorgespeicherten Momentaufnahmen in einem effizienten Format kopiert und gespeichert. Tresormomentaufnahmen werden jedoch weiterhin vollständig dargestellt und können einzeln und direkt auf einem neuen Volume wiederhergestellt werden, sodass kein iterativer, vollständig inkrementeller Wiederherstellungsprozess mehr benötigt wird. Diese erweiterte Technologie minimiert die Menge an Daten, die zum Speichern in und Abrufen aus Azure Storage erforderlich sind, und spart so Datenübertragungs- und Speicherkosten. Auch die Replikationszeit verkürzt sich dadurch, sodass Sie ein kürzeres Restore Point Objective (RPO) erreichen können. Sie können jetzt eine Mindestanzahl von Momentaufnahmen online im Azure NetApp Files-Dienst für die unmittelbarsten, nahezu sofortigen Datenwiederherstellungsanforderungen speichern und einen längeren Verlauf von Momentaufnahmen zu geringeren Kosten für die langfristige Aufbewahrung im Azure NetApp Files-Sicherungstresor erstellen. Weitere Informationen finden Sie unter [Funktionsweise von Azure NetApp Files-Momentaufnahmen](snapshots-introduction.md).

* Option [**Administratoren"**](create-active-directory-connections.md#create-an-active-directory-connection) in Active Directory-Verbindungen (Vorschau)

    Die Seite Active Directory-Verbindungen enthält jetzt das Feld **Administratoren.** Sie können Benutzer oder Gruppen angeben, denen Administratorrechte auf dem Volume erteilt werden sollen.

## <a name="august-2021"></a>August 2021

* Unterstützung für das [Aktivieren der fortlaufenden Verfügbarkeit auf den vorhandenen SMB-Volumes](enable-continuous-availability-existing-SMB.md)

    Sie können das SMB-Feature „Fortlaufende Verfügbarkeit“ (Continuous Availability, CA) bereits aktivieren, wenn Sie [ein neues SMB-Volume erstellen](azure-netapp-files-create-volumes-smb.md#continuous-availability). Sie können jetzt auch die SMB-Zertifizierungsstelle auf einem vorhandenen SMB-Volume aktivieren. Siehe [Aktivieren der fortlaufenden Verfügbarkeit auf den vorhandenen SMB-Volumes](enable-continuous-availability-existing-SMB.md).

* [Momentaufnahmerichtlinie](snapshots-manage-policy.md) ab sofort allgemein verfügbar.  

    Das Feature „Momentaufnahmerichtlinie“ ist jetzt allgemein verfügbar. Sie müssen die Funktionen nicht mehr registrieren, bevor Sie sie verwenden.

* [NFS `Chown Mode`-Exportrichtlinie und UNIX-Exportberechtigungen](configure-unix-permissions-change-ownership-mode.md) (Vorschau)   

    Sie können jetzt die Unix-Berechtigungen und die „Besitz ändern“-Modusoptionen (`Chown Mode`) für Azure NetApp Files NFS- und Doppelprotokollvolumes mit dem Unix-Sicherheitsstil festlegen. Sie können diese Einstellungen während der Volumeerstellung oder danach angeben.   

    Die Funktion des „Besitz ändern“-Modus (`Chown Mode`) ermöglicht es Ihnen, die Besitzverwaltungsfunktionen von Dateien und Verzeichnissen festzulegen. Sie können die Einstellung unter der Exportrichtlinie eines Volumes angeben oder ändern. Es stehen zwei Optionen für `Chown Mode` zur Verfügung: *Eingeschränkt* (Standardeinstellung), wobei nur der Stammbenutzer (root) den Besitz von Dateien und Verzeichnissen ändern kann, und *Nicht eingeschränkt*, wobei Benutzer, die keine Stammbenutzer (root) sind, den Besitz für Dateien und Verzeichnisse ändern können, deren Besitzer sie sind.   

    Mit der Azure NetApp Files-Funktion „Unix-Berechtigungen“ können Sie Änderungsberechtigungen für den Einbindepfad angeben. 

    Diese neuen Funktionen bieten Optionen zum Verschieben der Zugriffssteuerung bestimmter Dateien und Verzeichnisse in die Hände des Datenbenutzers anstelle des Dienstbetreibers.   

* [Doppelprotokollvolume (NFSv4.1 und SMB)](create-volumes-dual-protocol.md) (Vorschau)   

    Azure NetApp Files unterstützt bereits den Doppelprotokollzugriff auf NFSv3- und SMB-Volumes ab [Juli 2020](#july-2020). Sie können jetzt ein Azure NetApp Files-Volume erstellen, das den gleichzeitigen Doppelprotokollzugriff (NFSv4.1 und SMB) mit Unterstützung für LDAP-Benutzerzuordnung ermöglicht. Dieses Feature ermöglicht Anwendungsfälle, in denen Sie möglicherweise über eine Linux-basierte Workload verfügen, die NFSv4.1 für den Zugriff verwendet, und die Workload Daten generiert und in einem Azure NetApp Files-Volume speichert. Gleichzeitig müssen Ihre Mitarbeiter möglicherweise Windows-basierte Clients und Software verwenden, um die neu generierten Daten von demselben Azure NetApp Files-Volume zu analysieren. Durch die Funktion für den gleichzeitigen Doppelprotokollzugriff entfällt die Notwendigkeit, die von der Workload generierten Daten zur Nachanalyse mit einem anderen Protokoll auf ein separates Volume zu kopieren, was Speicherkosten und Betriebszeit spart. Diese Funktion ist kostenlos (die normalen Azure NetApp Files-Speicherkosten fallen weiterhin an) und allgemein verfügbar. Weitere Informationen finden Sie in der Dokumentation zum [gleichzeitigen Doppelprotokollzugriff (NFSv4.1/SMB)](create-volumes-dual-protocol.md).

## <a name="june-2021"></a>Juni 2021  

* [Azure NetApp Files-Speicherdienst-Add-Ons](storage-service-add-ons.md)

    Die neue Menüoption **Speicherdienst-Add-Ons** von Azure NetApp Files stellt eine „Startplattform“ im Azure-Portal für verfügbare Ökosystem-Add-Ons von Drittanbietern für den Azure NetApp Files-Speicherdienst bereit. Mit dieser neuen Menüoption im Portal können Sie eine Landing Page eingeben, indem Sie für den schnellen Zugriff auf ein Add-On auf die entsprechende Add-On-Kachel klicken.  

    **NetApp-Add-Ons** ist die erste Kategorie von Add-Ons, die unter **Speicherdienst-Add-Ons** angezeigt wird. Sie bietet Zugriff auf **NetApp Cloud Compliance**. Wenn Sie auf die Kachel **NetApp Cloud Compliance** klicken, wird ein neuer Browser geöffnet, und Sie werden zur Add-On-Installationsseite weitergeleitet. 

* Der [Manuelle QoS-Kapazitätspool](manual-qos-capacity-pool-introduction.md) ist jetzt allgemein verfügbar (GA)   

    Die Funktion „Manueller QoS-Kapazitätspool“ ist jetzt allgemein verfügbar. Sie müssen die Funktionen nicht mehr registrieren, bevor Sie sie verwenden. 

* [Unterstützung von „Freigegebenes AD“ für mehrere Konten in einem Active Directory pro Region und Abonnement](create-active-directory-connections.md#shared_ad) (Vorschau)   

    Bisher wurde von Azure NetApp Files nur ein einzelnes Active Directory (AD) pro Region unterstützt, wobei nur ein einzelnes NetApp-Konto für den Zugriff auf das AD konfiguriert werden konnte. Dank des neuen Features **Freigegebenes AD** kann von allen NetApp-Konten, die über eines der zu einem Abonnement und einer Region gehörenden NetApp-Konten erstellt wurden, eine AD-Verbindung gemeinsam genutzt werden. Bei Verwendung dieses Features kann beispielsweise von allen zu einem Abonnement und einer Region gehörenden NetApp-Konten die allgemeine AD-Konfiguration verwendet werden, um ein SMB-Volume, ein NFSv4.1-Kerberos-Volume oder ein Dualprotokollvolume zu erstellen. Wenn Sie dieses Feature verwenden, ist die AD-Verbindung in allen zu einem Abonnement und einer Region gehörenden NetApp-Konten sichtbar.

## <a name="may-2021"></a>Mai 2021 

* Das Azure NetApp Files-Tool für konsistente Momentaufnahmen in Anwendungen [(AzAcSnap)](azacsnap-introduction.md) ist ab sofort allgemein verfügbar. 

    AzAcSnap ist ein Befehlszeilentool, mit dem Sie den Datenschutz für Datenbanken von Drittanbietern (SAP HANA) in Linux-Umgebungen (z. B SUSE und RHEL) vereinfachen können. Die neuesten Änderungen am Tool finden Sie in den [Versionshinweisen zu AzAcSnap](azacsnap-release-notes.md).   

* [Unterstützung von Abrechnungstags für Kapazitätspools](manage-billing-tags.md)   

    Für Azure NetApp Files werden jetzt Abrechnungstags unterstützt, damit Sie die Kosten für Geschäftseinheiten oder andere interne Consumer vergleichen können. Abrechnungstags werden auf der Ebene des Kapazitätspools und nicht auf Volumeebene zugewiesen, und sie werden auf der Kundenrechnung angezeigt.

* [ADDS LDAP über TLS](configure-ldap-over-tls.md) (Vorschau) 

    Die LDAP-Kommunikation zwischen Client- und Serveranwendungen wird standardmäßig nicht verschlüsselt. Dies bedeutet, dass mit einem Netzwerküberwachungsgerät oder einer entsprechenden Software die Kommunikation zwischen einem LDAP-Client und den Servercomputern angezeigt werden kann. Dieses Szenario kann in nicht isolierten oder freigegebenen VNETs problematisch sein, wenn eine einfache LDAP-Bindung verwendet wird. Der Grund ist, dass die Anmeldeinformationen (Benutzername und Kennwort), die zum Binden des LDAP-Clients an den LDAP-Server verwendet werden, in unverschlüsselter Form über das Netzwerk übergeben werden. LDAP über TLS (auch als LDAPS bezeichnet) ist ein Protokoll, bei dem TLS zum Schützen der Kommunikation zwischen LDAP-Clients und -Servern verwendet wird. Für Azure NetApp Files wird jetzt die sichere Kommunikation für AD DS-Instanzen (Active Directory Domain Services) per LDAP über TLS unterstützt. Bei Azure NetApp Files kann jetzt LDAP über TLS genutzt werden, um authentifizierte Sitzungen zwischen den LDAP-Servern mit Active Directory-Integration einzurichten. Sie können das Feature „LDAP über TLS“ für Volumes mit NFS, SMB und dualem Protokoll aktivieren. Standardmäßig ist LDAP über TLS für Azure NetApp Files deaktiviert.  

* Unterstützung für [Durchsatzmetriken](azure-netapp-files-metrics.md)    

    Mit Azure NetApp Files wird die Unterstützung für die folgenden Metriken hinzugefügt:   
    * Durchsatzmetriken für Kapazitätspools
        * *Zugeordneter Pool für Volumedurchsatz*
        * *Verbrauchter Durchsatz eines Pools*
        * *Prozentsatz des zugeordneten Pools für Volumedurchsatz*
        * *Prozentsatz des verbrauchten Durchsatzes eines Pools*
    * Durchsatzmetriken für Volumes
        * *Dem Volume zugeteilter Durchsatz*
        * *Verbrauchter Durchsatz eines Volumes*
        * *Prozentsatz des verbrauchten Durchsatzes eines Volumes*

* Unterstützung der [dynamischen Änderung des Servicelevels](dynamic-change-volume-service-level.md) von Replikationsvolumes   

    Azure NetApp Files unterstützt jetzt dynamische Änderungen des Servicelevels von Quell- und Zielvolumes einer Replikation.

## <a name="april-2021"></a>April 2021

* [Manuelle Volume- und Kapazitätspoolverwaltung](volume-quota-introduction.md) (hartes Kontingent) 

    Das Verhalten der Volume- und Kapazitätspoolbereitstellung von Azure NetApp Files wurde in einen manuellen und kontrollierbaren Mechanismus geändert. Die Speicherkapazität eines Volumes ist auf die festgelegte Größe (Kontingent) des Volumes beschränkt. Wenn der Volumeverbrauch ein Maximum erreicht, wächst weder das Volume noch der zugrunde liegende Kapazitätspool automatisch. Stattdessen erhält das Volume den Zustand „Kein Speicherplatz mehr“. Sie können jedoch [die Größe des Kapazitätspools oder eines Volumes nach Bedarf ändern](azure-netapp-files-resize-capacity-pools-or-volumes.md). Sie sollten die [Kapazität eines Volumes](monitor-volume-capacity.md) und den zugrunde liegenden Kapazitätspool aktiv überwachen.

    Diese Verhaltensänderung ist ein Ergebnis der folgenden Hauptanforderungen, die von vielen Benutzern gestellt wurden:

    * Vorher wurde VM-Clients nur die schlank zugewiesene Kapazität (100 TiB) eines bestimmten Volumes angezeigt, wenn der Betriebssystemspeicherplatz oder Kapazitätsüberwachungstools verwendet wurden.  Diese Situation konnte zu einer ungenauen Anzeige der Kapazität auf Client- oder Anwendungsseite führen. Dieses Verhalten wurde nun korrigiert.  
    * Das bisherige automatische Vergrößerungsverhalten von Kapazitätspools hat Anwendungsbesitzern keine Kontrolle über den bereitgestellten Kapazitätspoolspeicher (und die damit verbundenen Kosten) gegeben. Diese Situation war besonders in Umgebungen umständlich, in denen „Endlosprozesse“ ausgeführt wurden, die die bereitgestellte Kapazität schnell auslasten und so die bereitgestellte Kapazität erhöhen konnten. Dieses Verhalten wurde korrigiert.  
    * Benutzer möchten eine direkte Korrelation zwischen Volumegröße (Kontingent) und Leistung sehen. Das vorherige Verhalten ließ (implizite) Überabonnements eines Volumes (Kapazität) und die automatische Vergrößerung von Kapazitätspools zu. Daher konnten Benutzer erst dann eine direkte Korrelation herstellen, wenn das Volumekontingent aktiv festgelegt oder zurückgesetzt wurde. Dieses Verhalten wurde nun korrigiert.

    Benutzer haben die direkte Kontrolle über die bereitgestellte Kapazität gefordert. Benutzer möchten die Speicherkapazität und -auslastung kontrollieren und ausgleichen. Zudem möchten Sie die Kosten, die anwendungs- und clientseitige Sichtbarkeit der verfügbaren, genutzten und bereitgestellten Kapazität sowie die Leistung ihrer Anwendungsvolumes kontrollieren. Mit diesem neuen Verhalten wurden nun alle diese Funktionen aktiviert.

* [Unterstützung von SMB-Freigaben für fortlaufende Verfügbarkeit (Continuous Availability, CA) für FSLogix-Benutzerprofilcontainer](azure-netapp-files-create-volumes-smb.md#continuous-availability) (Vorschau)  

    [FSLogix](/fslogix/overview) ist eine Zusammenstellung von Lösungen, mit denen sich nicht persistente Windows-Datenverarbeitungsumgebungen verbessern, aktivieren und vereinfachen lassen. FSLogix-Lösungen eignen sich für virtuelle Umgebungen in öffentlichen und privaten Clouds. Mit FSLogix-Lösungen können auch portierbarere Sitzungen erstellt werden, wenn physische Geräte zum Einsatz kommen. Mit FSLogix kann dynamischer Zugriff auf persistente Benutzerprofilcontainer bereitgestellt werden, die im freigegebenen SMB-Netzwerkspeicher gespeichert sind, einschließlich Azure NetApp Files. Um die FSLogix-Resilienz bei Wartungsereignissen des Speicherdiensts weiter zu verbessern, bietet Azure NetApp Files erweiterte Unterstützung für SMB Transparent Failover über [SMB-CA-Freigaben in Azure NetApp Files](azure-netapp-files-create-volumes-smb.md#continuous-availability) für Benutzerprofilcontainer. Weitere Informationen finden Sie unter [Azure Virtual Desktop-Lösungen](azure-netapp-files-solution-architectures.md#windows-virtual-desktop) für Azure NetApp Files.  

* [SMB3-Protokollverschlüsselung](azure-netapp-files-create-volumes-smb.md#smb3-encryption) (Vorschau) 

    Sie können jetzt für SMB-Volumes und Volumes mit dualem Protokoll von Azure NetApp Files die SMB3-Protokollverschlüsselung aktivieren. Mit diesem Feature wird die Verschlüsselung für In-Flight-SMB3-Daten mithilfe des [AES-CCM-Algorithmus für SMB 3.0-Verbindungen und des AES-GCM-Algorithmus für SMB 3.1.1-Verbindungen](/windows-server/storage/file-server/file-server-smb-overview#features-added-in-smb-311-with-windows-server-2016-and-windows-10-version-1607) aktiviert. SMB-Clients ohne Verwendung der SMB3-Verschlüsselung können nicht auf dieses Volume zugreifen. Ruhende Daten werden unabhängig von dieser Einstellung verschlüsselt. Die SMB-Verschlüsselung trägt zur weiteren Erhöhung der Sicherheit bei. Dies kann sich jedoch auf den Client auswirken (CPU-Auslastung für das Verschlüsseln und Entschlüsseln von Nachrichten). Es kann sich auch auf die Speicherressourcenverwendung auswirken (Reduzierung des Durchsatzes). Sie sollten die Auswirkung auf die Verschlüsselungsleistung Ihrer Anwendungen testen, bevor Sie Workloads in der Produktion bereitstellen.

* [Active Directory Domain Services (Adds) LDAP-Benutzerzuordnung mit erweiterten NFS-Gruppen](configure-ldap-extended-groups.md) (Vorschau)   

    Standardmäßig werden von Azure NetApp Files bis zu 16 Gruppen-IDs unterstützt, wenn NFS-Benutzeranmeldeinformationen so behandelt werden, wie in [RFC 5531](https://tools.ietf.org/html/rfc5531) definiert. Mit dieser neuen Funktion können Sie jetzt den maximalen Wert auf bis zu 1.024 erhöhen, wenn Sie über Benutzer verfügen, die Mitglieder in mehr als der Standardanzahl von Gruppen sind. Um diese Funktion zu unterstützen, können NFS-Volumes nun auch zu ADDS LDAP hinzugefügt werden, wodurch Active Directory LDAP-Benutzer mit erweiterten Gruppeneinträgen (mit bis zu 1024 Gruppen) auf das Volume zugreifen können. 

## <a name="march-2021"></a>März 2021
 
* [SMB-Freigaben für fortlaufende Verfügbarkeit (Continuous Availability, CA)](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) (Vorschau)  

    SMB Transparent Failover ermöglicht Wartungsvorgänge für den Azure NetApp Files-Dienst ohne Unterbrechung der Verbindung mit Serveranwendungen, die Daten auf SMB-Volumes speichern und auf diese Daten zugreifen. Zur Unterstützung von SMB Transparent Failover unterstützt Azure NetApp Files jetzt die Option für SMB-Freigaben für fortlaufende Verfügbarkeit zur Verwendung mit SQL Server-Anwendungen über SMB, die auf virtuellen Azure-Computern ausgeführt werden. Dieses Feature wird derzeit für SQL Server unter Windows unterstützt. SQL Server unter Linux wird zurzeit nicht unterstützt. Die Aktivierung dieses Features steigert die SQL Server-Leistung erheblich und bietet Skalierungs- und Kostenvorteile für [Bereitstellungen von einzelnen Instanzen, Always On-Failoverclusterinstanzen und Always On-Verfügbarkeitsgruppen](azure-netapp-files-solution-architectures.md#sql-server). Weitere Informationen finden Sie unter [Vorteile der Verwendung von Azure NetApp Files für die SQL Server-Bereitstellung](solutions-benefits-azure-netapp-files-sql-server.md).

* [Automatische Größenänderung eines regionsübergreifenden Replikationszielvolumes](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)

    In einer regionsübergreifenden Replikationsbeziehung wird die Größe eines Zielvolumes basierend auf der Größe des Quellvolumes automatisch angepasst. Daher müssen Sie die Größe des Zielvolumes nicht separat ändern. Dieses Verhalten zur automatischen Größenänderung gilt, wenn sich die Volumes in einer aktiven Replikationsbeziehung befinden oder wenn das Replikationspeering mit dem Vorgang zur Neusynchronisierung unterbrochen wird. Damit dieses Feature funktioniert, müssen Sie einen ausreichenden Toleranzbereich in den Kapazitätspools für die Quell- und Zielvolumes sicherstellen.

## <a name="december-2020"></a>Dezember 2020

* [Tool für konsistente Momentaufnahmen in Azure-Anwendungen](azacsnap-introduction.md) (Vorschau)    

    Das Tool für konsistente Momentaufnahmen in Azure-Anwendungen (Azure Application Consistent Snapshot Tool, AzAcSnap) ist ein Befehlszeilentool, mit dem Sie den Schutz von Daten für Drittanbieterdatenbanken (SAP HANA) in Linux-Umgebungen (z. B. SUSE und RHEL) vereinfachen können.   

    Für AzAcSnap werden die Funktionen für Volumemomentaufnahmen und die Replikation von Azure NetApp Files und Azure (große Instanz) genutzt. Ihnen bieten sich folgende Vorteile:

    * Anwendungskonsistenter Schutz von Daten 
    * Datenbankkatalog-Verwaltung 
    * *Ad-hoc*-Schutz von Volumes 
    * Klonen von Speichervolumes 
    * Unterstützung der Notfallwiederherstellung 

## <a name="november-2020"></a>November 2020

* [Momentaufnahme wiederherstellen](snapshots-revert-volume.md)

    Mit der Funktion zum Wiederherstellen von Momentaufnahmen können Sie schnell ein Volume wieder in den Zustand versetzen, in dem es sich befand, als eine bestimmte Momentaufnahme erstellt wurde. In den meisten Fällen ist das Wiederherstellen eines Volumes wesentlich schneller als das Wiederherstellen einzelner Dateien aus einer Momentaufnahme in das aktive Dateisystem. Im Vergleich zur Wiederherstellung einer Momentaufnahme auf einem neuen Volume ist es auch wesentliche effizienter hinsichtlich des Speicherplatzes.

## <a name="september-2020"></a>September 2020

* [Regionsübergreifende Replikation in Azure NetApp Files](cross-region-replication-introduction.md) (Vorschau)

  Azure NetApp Files unterstützt jetzt regionsübergreifende Replikation. Mit dieser neuen Notfallwiederherstellungsfunktion können Sie Ihre Azure NetApp Files-Volumes schnell und kostengünstig von einer Azure-Region in eine andere replizieren und so Ihre Daten vor unvorhersehbaren regionalen Ausfällen schützen. Die regionsübergreifende Replikation in Azure NetApp Files nutzt die SnapMirror®-Technologie, bei der nur geänderte Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet werden. Diese proprietäre Technologie minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Auch die Replikationszeit verkürzt sich dadurch, sodass Sie eine kürzere Recovery Point Objective (RPO) erreichen können.

* [Manueller QoS-Kapazitätspool](manual-qos-capacity-pool-introduction.md) (Vorschau)  

    Bei einem manuellen QoS-Kapazitätspool können Sie die Kapazität und den Durchsatz für ein Volume unabhängig voneinander zuweisen. Der Gesamtdurchsatz aller Volumes, die mit einem Kapazitätspool mit dem QoS-Typ „Manuell“ erstellt werden, ist durch den Gesamtdurchsatz des Pools begrenzt. Dieser wird anhand der Kombination von Poolgröße und Serviceleveldurchsatz ermittelt. Alternativ kann der [QoS-Typ](azure-netapp-files-understand-storage-hierarchy.md#qos_types) eines Kapazitätspools „auto“ (automatisch) sein, wobei es sich um die Standardeinstellung handelt. In einem automatischen QoS-Kapazitätspool wird der Durchsatz den Volumes im Pool automatisch zugewiesen. Dies erfolgt proportional zum Größenkontingent, das den Volumes zugewiesen ist.

* [LDAP-Signatur](create-active-directory-connections.md#create-an-active-directory-connection) (Vorschau)   

    Azure NetApp Files unterstützt jetzt LDAP-Signatur für sichere LDAP-Lookups zwischen dem Azure NetApp Files-Dienst und den benutzerseitig angegebenen Active Directory Domain Services-Domänencontrollern. Diese Funktion steht derzeit als Vorschau zur Verfügung.

* [AES-Verschlüsselung für AD-Authentifizierung](create-active-directory-connections.md#create-an-active-directory-connection) (Vorschau)

    Azure NetApp Files unterstützt jetzt AES-Verschlüsselung bei LDAP-Verbindungen mit dem Domänencontroller, um die AES-Verschlüsselung für ein SMB-Volume zu ermöglichen. Diese Funktion steht derzeit als Vorschau zur Verfügung. 

* Neue [Metriken](azure-netapp-files-metrics.md):   

    * Neue Volumemetriken: 
        * *Zugeordnete Größe des Volumes*: Die bereitgestellte Größe eines Volumes
    * Neue Poolmetriken: 
        * *Zugeordnete Poolgröße*: Die bereitgestellte Größe des Pools. 
        * *Gesamtgröße der Momentaufnahme für den Pool*: Die Summe der Momentaufnahmegrößen aller Volumes in dem Pool.

## <a name="july-2020"></a>Juli 2020

* [Doppelprotokollvolume (NFSv3 und SMB)](create-volumes-dual-protocol.md)

    Sie können jetzt ein Azure NetApp Files-Volume erstellen, das den gleichzeitigen Doppelprotokollzugriff (NFS v3 und SMB) mit Unterstützung für LDAP-Benutzerzuordnung ermöglicht. Diese Funktion ermöglicht Anwendungsfälle, bei denen Sie eine Linux-basierte Workload haben können, die Daten in einem Azure NetApp Files-Volume generiert und speichert. Gleichzeitig müssen Ihre Mitarbeiter Windows-basierte Clients und Software verwenden, um die neu generierten Daten von demselben Azure NetApp Files-Volume zu analysieren. Durch die Funktion für den gleichzeitigen Doppelprotokollzugriff entfällt die Notwendigkeit, die von der Workload generierten Daten zur Nachanalyse mit einem anderen Protokoll auf ein separates Volume zu kopieren, was Speicherkosten und Betriebszeit spart. Diese Funktion ist kostenlos (die normalen [Azure NetApp Files-Speicherkosten](https://azure.microsoft.com/pricing/details/netapp/) fallen weiterhin an) und allgemein verfügbar. Weitere Informationen finden Sie in der [Dokumentation zum gleichzeitigen Doppelprotokollzugriff](create-volumes-dual-protocol.MD).

* [NFS v4.1 Kerberos-Verschlüsselung während der Übertragung](configure-kerberos-encryption.MD)

    Azure NetApp Files unterstützt jetzt die NFS-Clientverschlüsselung in Kerberos-Modi (krb5, krb5i und krb5p) mit AES-256-Verschlüsselung. Dies bedeutet für Sie eine Erhöhung der Datensicherheit. Diese Funktion ist kostenlos (die normalen [Azure NetApp Files-Speicherkosten](https://azure.microsoft.com/pricing/details/netapp/) fallen weiterhin an) und allgemein verfügbar. Weitere Informationen finden Sie in der [Dokumentation zur NFS v4.1 Kerberos-Verschlüsselung](configure-kerberos-encryption.MD).

* [Dynamische Änderung des Servicelevels von Volumes](dynamic-change-volume-service-level.MD) (Vorschauversion) 

    Die Cloud verspricht Flexibilität bei den IT-Ausgaben. Sie können den Servicelevel eines vorhandenen Azure NetApp Files-Volumes jetzt ändern, indem Sie das Volume in einen anderen Kapazitätspool verschieben, der den von Ihnen für das Volume gewünschten Servicelevel verwendet. Diese direkte Änderung des Servicelevels für das Volume erfordert keine Migration von Daten. Auch der Zugriff auf das Volume auf Datenebene wird hiervon nicht beeinträchtigt. Sie können ein vorhandenes Volume so ändern, dass es einen höheren Servicelevel verwendet, um die Leistung zu verbessern, oder so dass es einen niedrigeren Servicelevel verwendet, um die Kosten zu optimieren. Diese Funktion ist kostenlos (die normalen [Azure NetApp Files-Speicherkosten](https://azure.microsoft.com/pricing/details/netapp/) fallen weiterhin an). Sie befindet sich derzeit in der Vorschauphase. Sie können sich für die Featurevorschau registrieren, indem Sie der [Dokumentation zur dynamischen Änderung des Servicelevels](dynamic-change-volume-service-level.md) folgen.

* [Volumemomentaufnahme-Richtlinie](snapshots-manage-policy.md) (Vorschau) 

    Mit Azure NetApp Files können Sie Zeitpunktmomentaufnahmen Ihrer Volumes erstellen. Sie können jetzt eine Momentaufnahmerichtlinie erstellen, damit Azure NetApp Files automatisch Volumemomentaufnahmen mit einer Häufigkeit Ihrer Wahl erstellt. Sie können die Erstellung der Momentaufnahmen in stündlichen, täglichen, wöchentlichen oder monatlichen Zyklen planen. Außerdem können Sie als Bestandteil der Momentaufnahmerichtlinie die maximale Anzahl aufzubewahrender Momentaufnahmen angeben. Diese Funktion ist kostenlos (die normalen [Azure NetApp Files-Speicherkosten](https://azure.microsoft.com/pricing/details/netapp/) fallen weiterhin an) und befindet sich derzeit in der Vorschauphase. Sie können sich für die Featurevorschau registrieren, indem Sie der [Dokumentation für Volumemomentaufnahme-Richtlinien](snapshots-manage-policy.md) folgen.

* [Exportrichtlinie mit NFS-Stammzugriff](azure-netapp-files-configure-export-policy.md)

    Sie können in Azure NetApp Files jetzt angeben, ob das Stammkonto auf das Volume zugreifen kann. 

* [Momentaufnahmepfad ausblenden](snapshots-edit-hide-path.md)

    Sie können in Azure NetApp Files jetzt angeben, ob ein Benutzer das Verzeichnis `.snapshot` (NFS-Clients) oder den Ordner `~snapshot` (SMB-Clients) auf einem eingebundenen Volume anzeigen und darauf zugreifen kann.

## <a name="may-2020"></a>Mai 2020

* [Microsoft Azure Backup-Richtlinienbenutzer](create-active-directory-connections.md) (Vorschau)

    Sie können in Azure NetApp Files jetzt weitere Konten einschließen, die erhöhte Rechte für das für Azure NetApp Files erstellte Computerkonto erfordern. Die angegebenen Konten dürfen die NTFS-Berechtigungen auf Datei- oder Ordnerebene ändern. Beispielsweise können Sie ein nicht privilegiertes Dienstkonto angeben, das zum Migrieren von Daten zu einer SMB-Dateifreigabe in Azure NetApp Files verwendet wird. Das Feature Sicherungsrichtlinienbenutzer steht derzeit als Vorschau zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte
* [Was ist Azure NetApp Files?](azure-netapp-files-introduction.md)
* [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 
