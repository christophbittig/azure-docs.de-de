---
title: Azure VMs HA für SAP NW auf Windows mit Azure Files (SMB)| Microsoft-Dokumentationen
description: Hochverfügbarkeit für SAP NetWeaver auf Azure VMs auf Windows mit Azure Files (SMB) für SAP-Anwendungen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: 458c63f27e9e17d71b9af97eda0de56f8f935a25
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251355"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>Hochverfügbarkeit für SAP NetWeaver auf Azure VMs auf Windows mit Azure Files Premium SMB für SAP-Anwendungen

## <a name="introduction"></a>Einführung
Die Azure Files Premium SMB wird jetzt vollständig von Microsoft und SAP unterstützt. **SWPM 1.0 SP32** und **SWPM 2.0 SP09** und höher unterstützen Azure Files Premium SMB-Speicher.  Für die Dimensionierung von Azure Files Premium SMB-Freigaben gelten besondere Anforderungen. Diese Dokumentation enthält spezifische Empfehlungen zur Verteilung der Arbeitslast auf Azure Files Premium SMB, zur angemessenen Größe von Azure Files Premium SMB und zu den Mindestinstallationsanforderungen für Azure Files Premium SMB.

Hochverfügbare SAP-Lösungen benötigen eine hochverfügbare Dateifreigabe für das Hosting der Verzeichnisse **sapmnt**, **trans** und **interface**. Azure Files Premium SMB ist eine einfache Azure PaaS Lösung für Shared File Systems für SAP auf Windows Umgebungen. Azure Files Premium SMB kann in Verbindung mit Availability Sets und Availability Zones verwendet werden. Azure Files Premium SMB kann auch für die Notfallwiederherstellung in einer anderen Region verwendet werden.  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>Dimensionierung und Verteilung von Azure Files Premium SMB für SAP-Systeme

Die folgenden Punkte sollten bei der Planung des Bereitstellungs prozesses von Azure Files Premium SMB berücksichtigt werden:
* Der Datei-Freigabename **sapmnt** kann einmal pro Speicherkonto erstellt werden.  Es ist möglich, zusätzliche SIDs als Verzeichnisse auf der gleichen Freigabe **/sapmnt** zu erstellen, z. B. **/sapmnt/\<SID1\>** und **/sapmnt/\<SID2\>** 
* Wählen Sie eine geeignete Größe, IOPS und Durchsatz.  Eine vorgeschlagene Größe für die Freigabe ist 256 GB pro SID.  Die maximale Größe für eine Freigabe beträgt 5120 GB
* Azure Files Premium SMB funktioniert möglicherweise nicht optimal für sehr große **sapmnt** Freigaben mit mehr als 1-2 Millionen Dateien pro Speicherkonto.  Kunden, die Millionen von Batch-Jobs haben, die Millionen von Job-Protokolldateien erzeugen, sollten diese regelmäßig gemäß [SAP Note 16083][16083] reorganisieren. Bei Bedarf können alte Jobprotokolle auf einen anderen Azure Files Premium SMB verschoben/archiviert werden.  Wenn **sapmnt** voraussichtlich sehr groß sein wird, sollten alternative Optionen (wie Azure ANF) in Betracht gezogen werden.
* Es wird empfohlen, einen Private Network Endpoint zu verwenden
* Vermeiden Sie die Konsolidierung von zu vielen SIDs auf ein einziges Speicherkonto und dessen Dateifreigabe.
* Als allgemeine Richtlinie gilt, dass nicht mehr als 2 bis 4 Nicht-Entwicklungs-SIDs zusammen konsolidiert werden können.
* Konsolidieren Sie nicht die gesamte Entwicklungs-, QAS- und Produktionslandschaft auf ein Speicherkonto und/oder eine Dateifreigabe.  Ein Ausfall der Freigabe führt zu einer Ausfallzeit der gesamten SAP-Landschaft.
* Es ist nicht ratsam, die Verzeichnisse **sapmnt** und **transport** auf demselben Speicherkonto zu konsolidieren, außer bei sehr kleinen Systemen. Während der Installation der SAP PAS Instanz wird die Anforderung eines Transport Hostnamens von SAPInst gestellt.  Der FQDN eines anderen Speicherkontos sollte unter <storage_account>.file.core.windows.net eingegeben werden.
* Konsolidieren Sie das für Schnittstellen verwendete Dateisystem nicht auf demselben Speicherkonto wie **/sapmnt/\<SID>** 
* Die SAP-Benutzer/Gruppen müssen zur Freigabe "sapmnt" hinzugefügt werden und sollten diese Berechtigung im Azure-Portal haben: **Storage File Data SMB Share Elevated Contributor**.

Es gibt wichtige Gründe für die Aufteilung von **Transport**, **Interface** und **sapmnt** auf separate Speicherkonten.  Die Verteilung dieser Komponenten auf separate Speicherkonten verbessert den Durchsatz, die Ausfallsicherheit und vereinfacht die Leistungsanalyse.  Wenn viele SIDs und andere Dateisysteme auf einem einzigen Azure Files Storage-Konto konsolidiert sind und die Leistung des Speicherkontos aufgrund des Erreichens der Durchsatzgrenzen schlecht ist, ist es äußerst schwierig, die SID oder Anwendung zu identifizieren, die das Problem verursacht. 

## <a name="planning"></a>Planung 
> [!IMPORTANT]
> Die Installation von SAP-Hochverfügbarkeitssystemen auf Azure Files Premium SMB mit Active Directory Integration erfordert eine teamübergreifende Zusammenarbeit.  Es wird dringend empfohlen, dass das Basis Team, das Active Directory Team und das Azure Team zusammenarbeiten, um diese Aufgaben zu erledigen: 
>
* Azure-Team - Einrichtung und Konfiguration des Speicherkontos, Skriptausführung und AD Directory-Synchronisierung.
* Active Directory Team - Erstellung von Benutzerkonten und Gruppen.
* Basis Team - Ausführen von SWPM und Festlegen von ACLs (falls erforderlich).

Voraussetzungen für die Installation von SAP NetWeaver High Availability Systems auf Azure Files Premium SMB mit Active Directory Integration.

* Die SAP-Server müssen mit einer Active Directory-Domäne verbunden sein.
* Die Active Directory-Domäne, in der sich die SAP-Server befinden, muss mit Azure AD connect in Azure Active Directory repliziert werden.
* Es wird dringend empfohlen, dass mindestens ein Active Directory-Domänen controller in der Azure-Landschaft vorhanden ist, um zu vermeiden, dass die Express Route zur Kontaktaufnahme mit Domänencontrollern vor Ort durchlaufen werden muss.
* Das Azure-Supportteam sollte die Dokumentation Azure Files SMB with [Active Directory Integration](../../../storage/files/storage-files-identity-auth-active-directory-enable.md#videos) durchsehen. *Das Video zeigt zusätzliche Konfigurationsoptionen, die aus Vereinfachungsgründen geändert (DNS) und übersprungen (DFS-N) wurden.* Trotzdem sind dies gültige Konfigurationsoptionen. 
* Der Benutzer, der das Azure Files PowerShell-Skript ausführt, muss die Berechtigung haben, Objekte in Active Directory zu erstellen.
* **Sie benötigen SWPM Version 1.0 SP32 und SWPM 2.0 SP09 oder höher. Der SAPInst-Patch muss 749.0.91 oder höher sein.**
* Auf dem Windows Server, auf dem das Skript ausgeführt wird, sollte eine aktuelle Version von PowerShell installiert sein. 

## <a name="installation-sequence"></a>Reihenfolge der Installation
 1. Der Active Directory-Administrator sollte im Vorfeld 3 Domänenbenutzer mit **lokalen Administratorrechten** und eine globale Gruppe im **lokalen Windows AD** erstellen: **SAPCONT_ADMIN@SAPCONTOSO.local** hat Domain Admin Rechte und dient zur Ausführung von **SAPInst**, **\<sid>adm** und **SAPService\<SID>** als SAP Systembenutzer und der Gruppe **SAP_\<SAPSID>_GlobalAdmin**. Der SAP-Installationsleitfaden enthält die spezifischen Details, die für diese Konten erforderlich sind.  **SAP-Benutzerkonten sollten nicht Domänenadministrator** sein. Es wird allgemein empfohlen **nicht \<sid>adm zu verwenden, um SAPInst** auszuführen.
 2. Der Active Directory-Administrator oder Azure-Administrator sollte den **Azure AD Connect** Synchronisierungsdienst-Manager überprüfen. Standardmäßig dauert die Replikation in das **Azure Active Directory** etwa 30 Minuten. 
 3. Die folgenden Aufgaben sollten vom Azure-Administrator durchgeführt werden:
     1. Erstellen Sie ein Speicherkonto mit entweder **Premium ZRS** oder **LRS**. Kunden mit zonaler Bereitstellung sollten ZRS wählen. Hier muss die Wahl zwischen der Einrichtung eines **Standard** oder **Premium Kontos** getroffen werden: ![Azure-Portal Screenshot zur Erstellung eines Speicherkontos - Schritt 1](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png)Azure-Portal Screenshot zur Erstellung eines Speicherkontos - Schritt 1
         > [!IMPORTANT]
         > Für den produktiven Einsatz wird die Verwendung eines **Premium-Kontos** empfohlen. Für den nicht produktiven Einsatz ist ein **Standard Account** ausreichend. 
         >
         ![Azure-Portal Screenshot zum Erstellen eines Speicherkontos - Schritt 2](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)Azure-Portal Screenshot zum Erstellen eines Speicherkontos - Schritt 2
         
         In diesem Bildschirm sollten die Standardeinstellungen in Ordnung sein.
        
         ![Azure-Portal Screenshot zum Erstellen eines Speicherkontos - Schritt 3](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)Azure-Portal Screenshot zum Erstellen eines Speicherkontos - Schritt 3 
         
         In diesem Schritt wird die Entscheidung zur Verwendung eines privaten Endpunkts getroffen.
     1. **Wählen Sie den privaten Netzwerk-Endpunkt** für das Speicherkonto.
     Fügen Sie bei Bedarf einen DNS A-Record in Windows DNS für **<Speicherkontoname>.file.core.windows.net** (dies muss möglicherweise in einer neuen DNS-Zone erfolgen).  Diskutieren Sie dieses Thema mit dem DNS-Administrator.  Außerhalb einer Organisation sollte die neue Zone nicht aktualisiert werden.  
         ![pivate-endpoint-creation](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png)Azure-Portal-Screenshot für die private Endpunktdefinition.
         ![private-endpoint-dns-1](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png)Screenshot des DNS-Servers für die DNS-Definition des privaten Endpunkts.    
     1. Erstellen Sie die **sapmnt** Dateifreigabe mit einer geeigneten Größe.  Die vorgeschlagene Größe ist 256 GB, die 650 IOPS, 75 MB/s Egress und 50 MB/s Ingress liefert.
         ![create-storage-account-5](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)Azure-Portal-Screenshot für die SMB-Freigabedefinition. 
      
     1. Laden Sie den Inhalt von [Azure Files GitHub](../../../storage/files/storage-files-identity-ad-ds-enable.md#download-azfileshybrid-module) herunter und führen Sie das [Skript](../../../storage/files/storage-files-identity-ad-ds-enable.md#run-join-azstorageaccountforauth) aus.   
     Mit diesem Skript wird entweder ein Computerkonto oder ein Dienstkonto in Active Directory erstellt.  Der Benutzer, der das Skript ausführt, muss über die folgenden Eigenschaften verfügen: 
         * Der Benutzer, der das Skript ausführt, muss die Berechtigung haben, Objekte in der Active Directory-Domäne zu erstellen, die SAP-Server enthält. Normalerweise wird ein Domänenadministratorkonto wie **SAPCONT_ADMIN@SAPCONTOSO.local** verwendet 
         * Stellen Sie vor der Ausführung des Skripts sicher, dass dieses Active Directory Domain-Benutzerkonto mit Azure Active Directory (AAD) synchronisiert ist.  Ein Beispiel hierfür wäre, das Azure-Portal zu öffnen und zu AAD-Benutzern zu navigieren und zu überprüfen, ob der Benutzer **SAPCONT_ADMIN@SAPCONTOSO.local** existiert und das AAD-Benutzerkonto **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** zu verifizieren.
         * Gewähren Sie diesem Azure Active Directory-Benutzerkonto die Rolle **Contributor RBAC** für die Ressourcengruppe, die das Speicherkonto mit der Dateifreigabe enthält.  In diesem Beispiel wird dem Benutzer **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** die Rolle **Beteiligter** für die entsprechende Ressourcengruppe gewährt 
         * Das Skript sollte ausgeführt werden, während Sie auf einem Windows-Server mit einem Active Directory-Domänenbenutzerkonto mit der oben angegebenen Berechtigung angemeldet sind; in diesem Beispiel würde das Konto **SAPCONT_ADMIN@SAPCONTOSO.local** verwendet.
         >[!IMPORTANT]
         > Bei der Ausführung des PowerShell-Skriptbefehls **Connect-AzAccount** wird dringend empfohlen, das Azure Active Directory-Benutzerkonto einzugeben, das dem Active Directory-Domänenbenutzerkonto entspricht, das für die Anmeldung bei einem Windows-Server verwendet wird, in diesem Beispiel ist dies das Benutzerkonto **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com**
         >
         In diesem Beispielszenario würde sich der Active Directory-Administrator am Windows Server als **SAPCONT_ADMIN@SAPCONTOSO.local** anmelden und bei Verwendung des **PS-Befehls Connect-AzAccount** eine Verbindung als Benutzer **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** herstellen.  Idealerweise sollten der Active Directory-Administrator und der Azure-Administrator gemeinsam an dieser Aufgabe arbeiten.
         ![powershell-script-1](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png)Screenshot des PowerShell-Skripts zum Erstellen eines lokalen AD-Kontos.

         ![smb-konfigurierter-bildschirmabzug](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)Azure-Portal-Screenshot nach erfolgreicher Ausführung des PowerShell-Skripts. 

         Folgendes sollte als "Configured" erscheinen  
         Speicher -> Dateifreigaben "Active Directory: Konfiguriert"
     1. Weisen Sie die SAP-Benutzer **\<sid>adm**, **SAPService\<SID>** und die Gruppe **SAP_\<SAPSID>_GlobalAdmin** der Azure Files Premium SMB File Share mit der Rolle **Storage File Data SMB Share Elevated Contributor** im Azure-Portal zu 
     1. Überprüfen Sie die ACL auf der Dateifreigabe **sapmnt** nach der Installation und fügen Sie das Konto **DOMAIN\CLUSTER_NAME$** , das Konto **DOMAIN\\\<sid>adm**, das Konto **DOMAIN\SAPService\<SID>** und die Gruppe **SAP_\<SID>_GlobalAdmin** hinzu. Diese Konten und die Gruppe  **sollten volle Kontrolle über das Verzeichnis sapmnt** haben.

         > [!IMPORTANT]
         > Dieser Schritt muss vor der SAPInst-Installation durchgeführt werden, da es sonst schwierig oder unmöglich ist, ACLs zu ändern, nachdem SAPInst Verzeichnisse und Dateien auf dem File Share angelegt hat
         >
         ![ACL-Eigenschaften](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)Windows Explorer-Screenshot der zugewiesenen Benutzerrechte.

         Die folgenden Screenshots zeigen das Hinzufügen von Computerkonten durch Auswahl von Objekttypen -> Computer ![Windows Server Screenshot des Hinzufügens des Clusternamens zum lokalen AD](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)Windows Server Screenshot des Hinzufügens des Clusternamens zum lokalen AD.
         
         Der DOMAIN\CLUSTER_NAME$ kann durch Auswahl von "Computer" unter "Objekttypen" gefunden werden  
         ![Screenshot des Hinzufügens eines AD-Computerkontos - Schritt 2](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png)Screenshot des Hinzufügens eines AD-Computerkontos - Schritt 2 ![Screenshot des Hinzufügens eines AD-Computerkontos - Schritt 3](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png)Screenshot des Hinzufügens eines AD-Computerkontos - Schritt 3 ![Screenshot der Zugriffseigenschaften des Computerkontos ](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png)Screenshot der Zugriffseigenschaften des Computerkontos.

     8. Falls erforderlich, verschieben Sie das für Azure Files erstellte Computerkonto in einen Active Directory-Container, in dem das Konto nicht abläuft.  Der Name des Computerkontos wird der Kurzname des Speicherkontos sein 

     
     > [!IMPORTANT]
     > Um die Windows ACL für die SMB-Freigabe zu initialisieren, muss die Freigabe einmalig auf einen Laufwerksbuchstaben gemountet werden.
     >
     Der Speicherschlüssel ist das Passwort und der Benutzer ist **Azure\\\<SMB share name>** wie hier gezeigt:  ![One time net use mount](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png)Windows-Screenshot des Net Use One Time Mount der SMB-Freigabe.

 4. Die folgenden Aufgaben sollten vom Basis-Administrator ausgeführt werden:
     1. [Installation des Windows-Clusters auf ASCS/ERS-Knoten und Hinzufügen des Cloud-Zeugen](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. Bei der Installation des ersten Clusterknotens wird nach dem Namen des Azure Files SMB-Speicherkontos gefragt.  Geben Sie den FQDN <storage_account_name>.file.core.windows.net ein.  Wenn SAPInst keine >13 Zeichen akzeptiert, ist die SWPM-Version zu alt.
     3. [Änderung des SAP-Profils der ASCS/SCS-Instanz](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     4. [Aktualisieren Sie den Probe Port für die SAP \<SID> Rolle in WSFC](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     5. Setzen Sie die SWPM-Installation für den zweiten ASCS/ERS-Knoten fort. SWPM benötigt nur den Pfad des Profilverzeichnisses.  Tragen Sie den vollständigen UNC-Pfad zum Profilverzeichnis ein.
     6. Geben Sie den UNC-Profilpfad für die DB- und PAS/AAS-Installation ein.
     7. Die PAS-Installation fragt nach dem Transport-Hostnamen. Bitte geben Sie den FQDN eines separaten Speicherkontonamens für das Transportverzeichnis an.
     8. Überprüfen Sie die ACLs für das SID- und Transportverzeichnis.

## <a name="disaster-recovery-setup"></a>Notfallwiederherstellung einrichten
Notfallwiederherstellungs- oder regionenübergreifende Replikationsszenarien werden mit Azure Files Premium SMB unterstützt. Alle Daten in Azure Files Premium SMB-Verzeichnissen können über diesen Link kontinuierlich mit einem Speicherkonto der DR-Region synchronisiert werden. Nach einem Disaster-Recovery-Ereignis und dem Failover der ASCS-Instanz in die DR-Region ändern Sie den Profilparameter SAPGLOBALHOST so, dass er auf Azure Files SMB in der DR-Region zeigt. Die gleichen Vorbereitungsschritte sollten für das DR-Speicherkonto durchgeführt werden, um das Speicherkonto mit Active Directory zu verbinden und RBAC-Rollen für SAP-Benutzer und -Gruppen zuzuweisen.

## <a name="troubleshooting"></a>Problembehandlung
Die in Schritt 3.c heruntergeladenen PowerShell-Skripte enthalten ein Debug-Skript zur Durchführung einiger grundlegender Prüfungen, um die Konfiguration zu validieren.
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Powershell-Skript-Ausgabe](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)PowerShell-Screenshot der Ausgabe des Debug-Skripts.

![Powershell-Skript-technische-Information](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)Der folgende Bildschirm zeigt die technischen Informationen zur Validierung eines erfolgreichen Domänenbeitritts an.
## <a name="useful-links--resources"></a>Nützliche Links und Ressourcen

* SAP-Hinweis [2273806][2273806] SAP-Support für speicher- oder dateisystembezogene Lösungen 
* [Installieren von SAP NetWeaver-Hochverfügbarkeit in einem Windows-Failovercluster und auf einer Windows-Dateifreigabe für SAP ASCS-/SCS-Instanzen in Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Azure Virtual Machines – Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Hinzufügen des Testports in der ASCS-Clusterkonfiguration](sap-high-availability-installation-wsfc-file-share.md)
* [Installieren einer (A)SCS-Instanz auf einem Failovercluster](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806