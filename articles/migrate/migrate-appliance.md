---
title: Azure Migrate-Appliance
description: Zusammenfassung der Unterstützung der Azure Migrate-Appliance.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: d8aa5f6bf955bcc3ee3fa86c17375d47402628ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339017"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-Appliance

In diesem Artikel werden die Voraussetzungen und Supportanforderungen für die Azure Migrate-Appliance zusammengefasst.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Die Azure Migrate-Appliance wird in den folgenden Szenarien verwendet.

**Szenario** | **Tool** | **Verwendung für**
--- | --- | ---
**Ermittlung und Bewertung von Servern, die in einer VMware-Umgebung ausgeführt werden** | Azure Migrate: Ermittlung und Bewertung | Ermitteln von Servern, die in Ihrer VMware-Umgebung ausgeführt werden<br/><br/> Ermitteln Sie das installierte Softwareinventar, ASP.NET-Web-Apps, SQL Server-Instanzen und -Datenbanken und Abhängigkeitsanalyse ohne Agent.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.
**Migration ohne Agent von Servern, die in VMware-Umgebungen ausgeführt werden** | Azure Migrate: Servermigration | Ermitteln Sie Server, die in Ihrer VMware-Umgebung ausgeführt werden. <br/><br/> Replizieren Sie Server ohne Installation von Agents.
**Ermittlung und Bewertung von Servern, die in einer Hyper-V-Umgebung ausgeführt werden** | Azure Migrate: Ermittlung und Bewertung | Ermitteln Sie Server, die in Ihrer Hyper-V-Umgebung ausgeführt werden.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.
**Ermittlung und Bewertung lokaler physischer oder virtualisierter Server** |  Azure Migrate: Ermittlung und Bewertung |  Erkennen Sie lokale physische oder virtualisierte Server.<br/><br/> Sammeln Sie Serverkonfigurations- und Leistungsmetadaten für Bewertungen.

## <a name="deployment-methods"></a>Bereitstellungsmethoden

Die Appliance kann mithilfe verschiedener Methoden bereitgestellt werden:

- Die Appliance kann mithilfe einer Vorlage für Server bereitgestellt werden, die in einer VMware- oder Hyper-V-Umgebung ausgeführt werden ([OVA-Vorlage für VMware](how-to-set-up-appliance-vmware.md) oder [VHD für Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Wenn Sie keine Vorlage verwenden möchten, können Sie die Appliance für eine VMware- oder Hyper-V-Umgebung mithilfe eines [PowerShell-Installationsskripts](deploy-appliance-script.md) bereitstellen.
- In Azure Government sollten Sie die Appliance mithilfe eines PowerShell-Installationsskripts bereitstellen. Die Schritte zur Bereitstellung finden Sie [hier](deploy-appliance-script-government.md).
- Für physische oder virtualisierte Server in der lokalen Umgebung oder in einer beliebigen anderen Cloud stellen Sie die Appliance immer mithilfe eines PowerShell-Installationsskripts bereit. Die Schritte zur Bereitstellung finden Sie [hier](how-to-set-up-appliance-physical.md).
- Die entsprechenden Downloadlinks finden Sie in den nachstehenden Tabellen.

## <a name="appliance---vmware"></a>Appliance – VMware

In der folgenden Tabelle sind die Anforderungen der Azure Migrate-Appliance für VMware zusammengefasst.

**Anforderung** | **VMware**
--- | ---
**Berechtigungen** | Für den lokalen Zugriff oder Remotezugriff auf den Appliance-Konfigurations-Manager benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancedienste** | Die Appliance verfügt über die folgenden Dienste:<br/><br/> - **Appliance-Konfigurations-Manager:** Dies ist eine Webanwendung, die mit Quelldetails konfiguriert werden kann, um die Ermittlung und Bewertung von Servern zu starten.<br/> - **VMware-Ermittlungs-Agent:** Der Agent sammelt Serverkonfigurationsmetadaten, die zum Erstellen von lokalen Bewertungen verwendet werden können.<br/>- **VMware-Bewertungsagent:** Der Agent sammelt Serverleistungsmetadaten, die zum Erstellen von leistungsbasierten Bewertungen verwendet werden können.<br/>- **Dienst für automatische Aktualisierungen:** Der Dienst hält alle Agents, die auf der Appliance ausgeführt werden, auf dem neuesten Stand. Er wird automatisch alle 24 Stunden ausgeführt.<br/>- **DRA-Agent**: Orchestriert die Serverreplikation und koordiniert die Kommunikation zwischen replizierten Servern und Azure. Wird nur bei der Replikation von Servern in Azure mithilfe der Migration ohne Agent verwendet.<br/>- **Gateway**: Sendet replizierte Daten an Azure. Wird nur bei der Replikation von Servern in Azure mithilfe der Migration ohne Agent verwendet.<br/>- **SQL Discovery and Assessment Agent**: Sendet die Konfigurations- und Leistungsmetadaten von SQL Server-Instanzen und -Datenbanken an Azure.<br/>- **Ermittlungs- und Bewertungs-Agent für Web-Apps:** Sendet die Konfigurationsdaten der Web-Apps an Azure.
**Projekteinschränkungen** |  Eine Appliance kann nur mit einem einzelnen Projekt registriert werden.<br/> Ein einzelnes Projekt kann über mehrere registrierte Appliances verfügen.
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 10.000 Server ermitteln, die auf einer vCenter Server-Instanz ausgeführt werden.<br/> Eine Appliance kann eine Verbindung mit einer einzelnen vCenter Server-Instanz herstellen.
**Unterstützte Bereitstellung** | Bereitstellung als neuer Server, der auf einer vCenter Server-Instanz ausgeführt wird, mithilfe einer OVA-Vorlage.<br/><br/> Bereitstellung auf einem vorhandenen Server, auf dem Windows Server 2016 ausgeführt wird, mithilfe eines PowerShell-Installationsskripts.
**OVA-Vorlage** | Herunterladen aus dem Projekt oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Die Downloadgröße beträgt 11,9 GB.<br/><br/> Die heruntergeladene Appliancevorlage ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist.<br/>Wenn der Evaluierungszeitraum fast abgelaufen ist, empfiehlt es sich, eine neue Appliance herunterzuladen und mithilfe einer OVA-Vorlage bereitzustellen oder die Betriebssystemlizenz des Applianceservers zu aktivieren.
**OVA-Überprüfung** | [Überprüfen](tutorial-discover-vmware.md#verify-security) Sie die aus dem Projekt heruntergeladene OVA-Vorlage, indem Sie die Hashwerte überprüfen.
**PowerShell-Skript** | Informationen zum Bereitstellen einer Appliance mithilfe des PowerShell-Installationsskripts finden Sie in diesem [Artikel](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Hardware- und Netzwerkanforderungen** |  Die Appliance sollte auf einem Server mit Windows Server 2016, 32 GB RAM, 8 vCPUs, etwa 80 GB Speicherplatz und einem externen virtuellen Switch ausgeführt werden.<br/> Die Appliance erfordert Internetzugriff, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance mithilfe der OVA-Vorlage bereitstellen, benötigen Sie genügend Ressourcen auf der vCenter Server-Instanz, um einen Server zu erstellen, der die Hardwareanforderungen erfüllt.<br/><br/> Wenn Sie die Appliance auf einem vorhandenen Server ausführen, stellen Sie sicher, dass auf dem Server Windows Server 2016 ausgeführt wird und die Hardwareanforderungen erfüllt werden.<br/>_(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_
**VMware-Anforderungen** | Wenn Sie die Appliance als Server auf vCenter Server bereitstellen, muss sie auf einer vCenter Server-Instanz, auf der 5.5, 6.0, 6.5, 6.7 oder 7.0 ausgeführt wird, und auf einem ESXi-Host, auf dem Version 5.5 oder höher ausgeführt wird, bereitgestellt werden.<br/><br/> 
**VDDK (Migration ohne Agent)** | Um die Appliance für die Migration von Servern ohne Agent zu nutzen, muss das VMware vSphere-VDDK auf dem Applianceserver installiert sein.

## <a name="appliance---hyper-v"></a>Appliance – Hyper-V

**Anforderung** | **Hyper-V**
--- | ---
**Berechtigungen** | Für den lokalen Zugriff oder Remotezugriff auf den Appliance-Konfigurations-Manager benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancedienste** | Die Appliance verfügt über die folgenden Dienste:<br/><br/> - **Appliance-Konfigurations-Manager:** Dies ist eine Webanwendung, die mit Quelldetails konfiguriert werden kann, um die Ermittlung und Bewertung von Servern zu starten.<br/> - **Ermittlungs-Agent:** Der Agent sammelt Serverkonfigurationsmetadaten, die zum Erstellen von lokalen Bewertungen verwendet werden können.<br/>- **Bewertungsagent:** Der Agent sammelt Serverleistungsmetadaten, die zum Erstellen von leistungsbasierten Bewertungen verwendet werden können.<br/>- **Dienst für automatische Aktualisierungen:** Der Dienst hält alle Agents, die auf der Appliance ausgeführt werden, auf dem neuesten Stand. Er wird automatisch alle 24 Stunden ausgeführt.
**Projekteinschränkungen** |  Eine Appliance kann nur mit einem einzelnen Projekt registriert werden.<br/> Ein einzelnes Projekt kann über mehrere registrierte Appliances verfügen.
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 5000 Server ermitteln, die in einer Hyper-V-Umgebung ausgeführt werden.<br/> Eine einzelne Appliance kann Verbindungen mit bis zu 300 Hyper-V-Hosts herstellen.
**Unterstützte Bereitstellung** | Bereitstellung als Server, der auf einem Hyper-V-Host ausgeführt wird, mithilfe einer VHD-Vorlage.<br/><br/> Bereitstellung auf einem vorhandenen Server, auf dem Windows Server 2016 ausgeführt wird, mithilfe eines PowerShell-Installationsskripts.
**VHD-Vorlage** | ZIP-Datei, die eine VHD enthält. Laden Sie sie aus dem Projekt oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140422) herunter.<br/><br/> Die Downloadgröße beträgt 8,91 GB.<br/><br/> Die heruntergeladene Appliancevorlage ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist.<br/> Wenn der Evaluierungszeitraum fast abgelaufen ist, empfiehlt es sich, eine neue Appliance herunterzuladen und bereitzustellen oder die Betriebssystemlizenz des Applianceservers zu aktivieren.
**VHD-Überprüfung** | [Überprüfen](tutorial-discover-hyper-v.md#verify-security) Sie die aus dem Projekt heruntergeladene VHD-Vorlage, indem Sie die Hashwerte überprüfen.
**PowerShell-Skript** | Informationen zum Bereitstellen einer Appliance mithilfe des PowerShell-Installationsskripts finden Sie in diesem [Artikel](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/>
**Hardware- und Netzwerkanforderungen**  |  Die Appliance sollte auf einem Server mit Windows Server 2016, 16 GB RAM, 8 vCPUs, etwa 80 GB Speicherplatz und einem externen virtuellen Switch ausgeführt werden.<br/> Die Appliance erfordert eine statische oder dynamische IP-Adresse sowie Zugriff auf das Internet, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance als Server ausführen, der auf einem Hyper-V-Host ausgeführt wird, benötigen Sie genügend Ressourcen auf dem Host, um einen Server zu erstellen, der die Hardwareanforderungen erfüllt.<br/><br/> Wenn Sie die Appliance auf einem vorhandenen Server ausführen, stellen Sie sicher, dass auf dem Server Windows Server 2016 ausgeführt wird und die Hardwareanforderungen erfüllt werden.<br/>_(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_
**Hyper-V-Anforderungen** | Wenn Sie die Appliance mit der VHD-Vorlage bereitstellen, entspricht die von Azure Migrate bereitgestellte Appliance der Hyper-V VM-Version 5.0.<br/><br/> Auf dem Hyper-V-Host muss mindestens Windows Server 2012 R2 ausgeführt werden.

## <a name="appliance---physical"></a>Appliance – physisch

**Anforderung** | **Physisch**
--- | ---
**Berechtigungen** | Für den lokalen Zugriff oder Remotezugriff auf den Appliance-Konfigurations-Manager benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancedienste** | Die Appliance verfügt über die folgenden Dienste:<br/><br/> - **Appliance-Konfigurations-Manager:** Dies ist eine Webanwendung, die mit Quelldetails konfiguriert werden kann, um die Ermittlung und Bewertung von Servern zu starten.<br/> - **Ermittlungs-Agent:** Der Agent sammelt Serverkonfigurationsmetadaten, die zum Erstellen von lokalen Bewertungen verwendet werden können.<br/>- **Bewertungsagent:** Der Agent sammelt Serverleistungsmetadaten, die zum Erstellen von leistungsbasierten Bewertungen verwendet werden können.<br/>- **Dienst für automatische Aktualisierungen:** Der Dienst hält alle Agents, die auf der Appliance ausgeführt werden, auf dem neuesten Stand. Er wird automatisch alle 24 Stunden ausgeführt.
**Projekteinschränkungen** |  Eine Appliance kann nur mit einem einzelnen Projekt registriert werden.<br/> Ein einzelnes Projekt kann über mehrere registrierte Appliances verfügen.<br/>
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 1.000 physische Server ermitteln.
**Unterstützte Bereitstellung** | Bereitstellung auf einem vorhandenen Server, auf dem Windows Server 2016 ausgeführt wird, mithilfe eines PowerShell-Installationsskripts.
**PowerShell-Skript** | Laden Sie das Skript (AzureMigrateInstaller.ps1) in einer ZIP-Datei aus dem Projekt oder von [hier](https://go.microsoft.com/fwlink/?linkid=2140334) herunter. [Weitere Informationen](tutorial-discover-physical.md)<br/><br/> Die Downloadgröße beträgt 85.8 MB.
**Skriptüberprüfung** | [Überprüfen](tutorial-discover-physical.md#verify-security) Sie das aus dem Projekt heruntergeladene PowerShell-Installationsskript, indem Sie die Hashwerte überprüfen.
**Hardware- und Netzwerkanforderungen** |  Die Appliance sollte auf einem Server mit Windows Server 2016, 16 GB RAM, 8 vCPUs und etwa 80 GB Speicherplatz ausgeführt werden.<br/> Die Appliance erfordert eine statische oder dynamische IP-Adresse sowie Zugriff auf das Internet, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance auf einem vorhandenen Server ausführen, stellen Sie sicher, dass auf dem Server Windows Server 2016 ausgeführt wird und die Hardwareanforderungen erfüllt werden.<br/>_(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_

## <a name="url-access"></a>URL-Zugriff

Das Azure Migrate-Gerät muss mit dem Internet verbunden sein.

- Wenn Sie die Appliance bereitstellen, führt Azure Migrate eine Konnektivitätsprüfung für die erforderlichen URLs durch.
- Sie müssen den Zugriff auf alle URLs in der Liste zulassen. Wenn Sie nur eine Bewertung durchführen, können Sie die URLs überspringen, die für die VMware-Migration ohne Agent als erforderlich gekennzeichnet sind.
- Wenn Sie einen URL-basierten Proxy für die Internetverbindung verwenden, stellen Sie sicher, dass der Proxy alle CNAME-Einträge auflöst, die beim Abrufen der URLs empfangen werden.

### <a name="public-cloud-urls"></a>URLs für die öffentliche Cloud

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigieren Sie zum Azure-Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com  <br/> *.microsoftazuread-sso.com | Verwendet für Zugriffssteuerung und Identitätsverwaltung durch Azure Active Directory
management.azure.com | Verwendet für Ressourcenbereitstellungen und Verwaltungsvorgänge
*.services.visualstudio.com | Hochladen von Applianceprotokollen, die für die interne Überwachung verwendet werden.
*.vault.azure.net | Verwalten von Geheimnissen in Azure Key Vault<br/> Hinweis: Stellen Sie sicher, dass die zu replizierenden Server darauf zugreifen können.
aka.ms/* | Zulassen des Zugriffs auf diese Links, die für das Herunterladen und Installieren der neuesten Updates für Appliancedienste verwendet werden
download.microsoft.com/download | Zulassen von Downloads vom Microsoft Download Center.
*.servicebus.windows.net | Kommunikation zwischen der Appliance und dem Azure Migrate-Dienst
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.hypervrecoverymanager.windowsazure.com | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.blob.core.windows.net |  **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/>Hochladen von Daten in Storage für die Migration

### <a name="government-cloud-urls"></a>URLs für Azure Government-Clouds

**URL** | **Details**  
--- | --- |
*.portal.azure.us  | Navigieren Sie zum Azure-Portal.
graph.windows.net | Melden Sie sich bei Ihrem Azure-Abonnement an.
login.microsoftonline.us  | Verwendet für Zugriffssteuerung und Identitätsverwaltung durch Azure Active Directory
management.usgovcloudapi.net |  Verwendet für Ressourcenbereitstellungen und Verwaltungsvorgänge
*.services.visualstudio.com | Hochladen von Applianceprotokollen, die für die interne Überwachung verwendet werden.
*.vault.usgovcloudapi.net | Verwalten von Geheimnissen in Azure Key Vault
aka.ms/* | Zulassen des Zugriffs auf diese Links, die für das Herunterladen und Installieren der neuesten Updates für Appliancedienste verwendet werden
download.microsoft.com/download | Zulassen von Downloads vom Microsoft Download Center.
*.servicebus.usgovcloudapi.net  | Kommunikation zwischen der Appliance und dem Azure Migrate-Dienst
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.hypervrecoverymanager.windowsazure.us | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.blob.core.usgovcloudapi.net  |  **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/>Hochladen von Daten in Storage für die Migration
*.applicationinsights.us | Hochladen von Applianceprotokollen, die für die interne Überwachung verwendet werden.  

### <a name="public-cloud-urls-for-private-link-connectivity"></a>URLs für öffentliche Clouds für Private Link-Konnektivität

Die Appliance benötigt Zugriff auf die folgenden URLs (direkt oder über einen Proxy) über den Private Link-Zugriff hinaus.

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigieren Sie zum Azure-Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> *.microsoftazuread-sso.com  | Verwendet für Zugriffssteuerung und Identitätsverwaltung durch Azure Active Directory
management.azure.com |  Verwendet für Ressourcenbereitstellungen und Verwaltungsvorgänge
*.services.visualstudio.com (optional) | Hochladen von Applianceprotokollen, die für die interne Überwachung verwendet werden.
aka.ms/* (optional) | Zulassen des Zugriffs auf diese Links, die für das Herunterladen und Installieren der neuesten Updates für Appliancedienste verwendet werden
download.microsoft.com/download | Zulassen von Downloads vom Microsoft Download Center.
*.servicebus.windows.net | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Kommunikation zwischen der Appliance und dem Azure Migrate-Dienst
*.hypervrecoverymanager.windowsazure.com | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.blob.core.windows.net |  **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/>Hochladen von Daten in Storage für die Migration <br/>Dies ist optional und nicht erforderlich, wenn an die Speicherkonten (sowohl das Cachespeicherkonto als auch das Gatewayspeicherkonto) ein privater Endpunkt angefügt ist.

## <a name="collected-data---vmware"></a>Gesammelte Daten – VMware

Die Appliance sammelt Konfigurationsmetadaten, Leistungsmetadaten und Serverabhängigkeitsdaten (wenn die [Abhängigkeitsanalyse](concepts-dependency-visualization.md) ohne Agent verwendet wird).

### <a name="metadata"></a>Metadaten

Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Servern für die Migration zu Azure, der richtigen Größe für Server, der Kosten der Planung und bei der Analyse von Anwendungsabhängigkeiten. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Nachstehend finden Sie die vollständige Liste der Servermetadaten, die die Appliance sammelt und an Azure sendet.

**DATEN** | **ZÄHLER**
--- | ---
**Serverdetails** |
Server-ID | vm.Config.InstanceUuid
Servername | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
Serverbeschreibung | vm.Summary.Config.Annotation
Lizenzproduktname | vm.Client.ServiceContent.About.LicenseProductName
Betriebssystemtyp | vm.SummaryConfig.GuestFullName
Starttyp | vm.Config.Firmware
Anzahl von Kernen | vm.Config.Hardware.NumCPU
Arbeitsspeicher (MB) | vm.Config.Hardware.MemoryMB
Anzahl der Datenträger | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Liste der Datenträgergrößen | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Liste der Netzwerkadapter | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU-Auslastung | cpu.usage.average
Arbeitsspeichernutzung |mem.usage.average
**Details pro Datenträger** |
Datenträgerschlüsselwert | disk.Key
Diskunit-Nummer | disk.UnitNumber
Datenträgercontroller-Schlüsselwert | disk.ControllerKey.Value
Bereitgestellte Gigabytes | virtualDisk.DeviceInfo.Summary
Name des Datenträgers | Mithilfe von disk.UnitNumber, disk.Key und disk.ControllerKey.Value generierter Wert
Lesevorgänge pro Sekunde | virtualDisk.numberReadAveraged.average
Schreibvorgänge pro Sekunde | virtualDisk.numberWriteAveraged.average
Lesedurchsatz (MB pro Sekunde) | virtualDisk.read.average
Schreibdurchsatz (MB pro Sekunde) | virtualDisk.write.average
**Details pro NIC** |
Netzwerkadaptername | nic.Key
MAC-Adresse | ((VirtualEthernetCard)nic).MacAddress
IPv4-Adressen | vm.Guest.Net
IPv6-Adressen | vm.Guest.Net
Lesedurchsatz (MB pro Sekunde) | net.received.average
Schreibdurchsatz (MB pro Sekunde) | net.transmitted.average
**Inventurpfaddetails** |
Name | container.GetType().Name
Typ des untergeordneten Objekts | container.ChildType
Referenzdetails | container.MoRef
Details des übergeordneten Objekts | Container.Parent
Ordnerdetails pro Server | ((Folder)container).ChildEntity.Type
Details zum Rechenzentrum pro Server | ((Datacenter)container).VmFolder
Details zum Rechenzentrum pro Hostordner | ((Datacenter)container).HostFolder
Clusterdetails pro Host | ((ClusterComputeResource)container).Host
Hostdetails pro Server | ((HostSystem)container).VM

### <a name="performance-data"></a>Leistungsdaten

Die folgende Tabelle enthält die Leistungsdaten, die eine Appliance für einen in der VMware-Umgebung ausgeführten Server erfasst und an Azure sendet:

**Daten** | **Leistungsindikator** | **Auswirkungen auf die Bewertung**
--- | --- | ---
CPU-Auslastung | cpu.usage.average | Empfohlene Servergröße/Kosten
Arbeitsspeichernutzung | mem.usage.average | Empfohlene Servergröße/Kosten
Datenträgerlesedurchsatz (MB pro Sekunde) | virtualDisk.read.average | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
Datenträgerschreibdurchsatz (MB pro Sekunde) | virtualDisk.write.average | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
Datenträgerlesevorgänge pro Sekunde | virtualDisk.numberReadAveraged.average | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
Datenträgerschreibvorgänge pro Sekunde | virtualDisk.numberWriteAveraged.average  | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
NIC-Lesedurchsatz (MB pro Sekunde) | net.received.average | Berechnung der Servergröße
NIC-Schreibdurchsatz (MB pro Sekunde) | net.transmitted.average  |Berechnung der Servergröße

### <a name="installed-software-inventory"></a>Inventur der installierten Software

Die Appliance erfasst Daten zur Inventur der installierten Software auf den Servern.

#### <a name="windows-server-software-inventory-data"></a>Softwareinventurdaten für Windows Server

Die folgende Tabelle enthält die Softwareinventurdaten, die die Appliance für jede in der VMware-Umgebung ermittelte Windows Server-Instanz erfasst:

**Daten** | **Registrierungsstandort** | **Schlüssel**
--- | --- | ---
Anwendungsname  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Anbieter  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Herausgeber

#### <a name="windows-server-features-data"></a>Daten der Windows-Server-Features

Im Folgenden finden Sie die Daten der Features, die die Appliance von jedem in der VMware-Umgebung ermittelten Windows-Server sammelt.

**Daten**  | **PowerShell-Cmdlet** | **Eigenschaft**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Featuretyp | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="sql-server-metadata"></a>SQL Server-Metadaten

Im Folgenden finden Sie die SQL Server-Daten, die die Appliance von jedem in der VMware-Umgebung ermittelten Windows-Server sammelt.

**Daten**  | **Registrierungsstandort**  | **Schlüssel**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edition  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Edition
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Version

#### <a name="aspnet-web-apps-data"></a>Daten von ASP.NET-Web-Apps

Die folgende Tabelle enthält die Web-App-Konfigurationsdaten, die die Appliance für jede in der VMware-Umgebung ermittelte Windows Server-Instanz erfasst:

**Entität** | **Daten**
--- | ---
Web-Apps | Anwendungsname <br/>Konfigurationspfad <br/>Front-End-Bindungen <br/>Aktivierte Frameworks <br/>Hostwebserver<br/>Unteranwendungen und virtuelle Anwendungen <br/>Name des Anwendungspools <br/>Laufzeitversion <br/>Verwalteter Pipelinemodus
Webserver | Servername <br/>Servertyp (derzeit nur IIS) <br/>Speicherort der Konfiguration <br/>Version <br/>FQDN <br/>Für die Ermittlung verwendete Anmeldeinformationen <br/>Liste der Anwendungen

#### <a name="windows-server-operating-system-data"></a>Windows-Server-Betriebssystemdaten

Im Folgenden finden Sie die Betriebssystemdaten, die die Appliance von jedem in der VMware-Umgebung ermittelten Windows-Server sammelt.

**Daten**  | **WMI-Klasse**  | **WMI-Klasseneigenschaft**
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Aufbau  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Softwareinventurdaten für Linux-Server

Die folgende Tabelle enthält die Softwareinventurdaten, die die Appliance für jeden in der VMware-Umgebung ermittelten Linux-Server erfasst: Basierend auf dem Betriebssystem des Servers werden einer oder mehrere der Befehle ausgeführt.

**Daten**  | **Befehle**
--- | ---
Name | rpm, dpkg-query, snap
Version | rpm, dpkg-query, snap
Anbieter | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Linux-Server-Betriebssystemdaten

Im Folgenden finden Sie die Betriebssystemdaten, die die Appliance von jedem in der VMware-Umgebung ermittelten Linux-Server sammelt.

**Daten**  | **Befehle**
--- | ---
Name <br/> version | Erfasst aus mindestens einer der folgenden Dateien:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Aufbau | uname

### <a name="sql-server-instances-and-databases-data"></a>Daten von SQL Server-Instanzen und -Datenbanken

Die Appliance sammelt Daten auf SQL Server-Instanzen und -Datenbanken.

#### <a name="sql-database-metadata"></a>SQL-Datenbankmetadaten

**Datenbankmetadaten** | **Ansichten/ SQL Server-Eigenschaften**
--- | ---
Eindeutiger Bezeichner der Datenbank | sys.databases
Serverdefinierte Datenbank-ID | sys.databases
Name der Datenbank | sys.databases
Kompatibilitätsgrad der Datenbank | sys.databases
Sortierungsname der Datenbank | sys.databases
Status der Datenbank | sys.databases
Größe der Datenbank (in MB) | sys.master_files
Laufwerkbuchstabe des Speicherorts mit Datendateien | SERVERPROPERTY und Software\Microsoft\MSSQLServer\MSSQLServer
Liste der Datenbankdateien | sys.databases, sys.master_files
Service Broker ist aktiviert oder nicht | sys.databases
Datenbank ist für Change Data Capture aktiviert oder nicht | sys.databases

#### <a name="sql-server-metadata"></a>SQL Server-Metadaten

**Servermetadaten** | **Ansichten/ SQL Server-Eigenschaften**
--- | ---
Servername |SERVERPROPERTY
FQDN | Von der Ermittlung installierter Anwendungen abgeleitete Verbindungszeichenfolge
Installations-ID | sys.dm_server_registry
Serverversion | SERVERPROPERTY
Serveredition | SERVERPROPERTY
Serverhostplattform (Windows/Linux) | SERVERPROPERTY
Produktebene des Servers (RTM SP CTP) | SERVERPROPERTY
Standardsicherungspfad | SERVERPROPERTY
Standardpfad der Datendateien | SERVERPROPERTY und Software\Microsoft\MSSQLServer\MSSQLServer
Standardpfad der Protokolldateien | SERVERPROPERTY und Software\Microsoft\MSSQLServer\MSSQLServer
Nein. der Kerne auf dem Server | sys.dm_os_schedulers, sys.dm_os_sys_info
Serversortierungsname | SERVERPROPERTY
Nein. der Kerne auf dem Server mit VISIBLE ONLINE-Status | sys.dm_os_schedulers
Eindeutige Server-ID | sys.dm_server_registry
Hochverfügbarkeit aktiviert oder nicht | SERVERPROPERTY
Pufferpoolerweiterung aktiviert oder nicht | sys.dm_os_buffer_pool_extension_configuration
Failovercluster konfiguriert oder nicht | SERVERPROPERTY
Server, der nur den Windows-Authentifizierungsmodus verwendet | SERVERPROPERTY
Der Server installiert PolyBase | SERVERPROPERTY
Nein. der logischen CPUs im System | sys.dm_server_registry, sys.dm_os_sys_info
Verhältnis der Anzahl von logischen oder physischen Kernen, die von einem physischen Prozessorpaket verfügbar gemacht werden | sys.dm_os_schedulers, sys.dm_os_sys_info
Anzahl der physischen CPUs auf dem System | sys.dm_os_schedulers, sys.dm_os_sys_info
Datum und Uhrzeit des letzten Starts des Servers | sys.dm_server_registry
Max. Serverarbeitsspeicherverwendung (in MB) | sys.dm_os_process_memory
Gesamtanzahl der Benutzer in allen Datenbanken | sys.databases, sys.logins
Gesamtgröße aller Benutzerdatenbanken | sys.databases
Größe der temporären Datenbank | sys.master_files, sys.configurations, sys.dm_os_sys_info
Nein. der Anmeldungen | sys.logins
Liste von Verbindungsservern | sys.servers
Liste des Agentauftrags | [msdb].[dbo].[sysjobs], [sys].[syslogins], [msdb].[dbo].[syscategories]

### <a name="performance-metadata"></a>Leistungsmetadaten

**Leistung** | **Ansichten/ SQL Server-Eigenschaften** | **Auswirkungen auf die Bewertung**
--- | --- | ---
SQL Server-CPU-Auslastung| sys.dm_os_ring_buffers| Empfohlene SKU-Größe (CPU-Dimension)
Anzahl von logischen SQL-CPUs| sys.dm_os_sys_info| Empfohlene SKU-Größe (CPU-Dimension)
Belegter physischer SQL-Speicher| sys.dm_os_process_memory| Nicht verwendet
Prozentuale Auslastung des SQL-Speichers| sys.dm_os_process_memory | Nicht verwendet
Datenbank-CPU-Auslastung| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Empfohlene SKU-Größe (CPU-Dimension)
Belegter Datenbankspeicher (Pufferpool)| sys.dm_os_buffer_descriptors| Empfohlene SKU-Größe (Speicherdimension)
Datei lesen/schreiben E/A| sys.dm_io_virtual_file_stats, sys.master_files| Empfohlene SKU-Größe (E/A-Dimension)
Dateianzahl von Lese-/Schreibvorgängen| sys.dm_io_virtual_file_stats, sys.master_files| Empfohlene SKU-Größe (Durchsatzdimension)
Datei-E/A-Verzögerung lesen/schreiben (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Empfohlene SKU-Größe (E/A-Latenzdimension)
Dateigröße| sys.master_files| Empfohlene SKU-Größe (Speicherdimension)


### <a name="application-dependency-data"></a>Daten zu Anwendungsabhängigkeit

Die Abhängigkeitsanalyse ohne Agent sammelt Verbindungs- und Prozessdaten.

#### <a name="windows-server-dependencies-data"></a>Windows-Server-Abhängigkeitsdaten

Im Folgenden finden Sie die Verbindungsdaten, die die Appliance von jedem Windows-Server sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist.

**Daten** | **Befehle**
--- | ---
Lokaler Port | netstat
Lokale IP-Adresse | netstat
Remoteport | netstat
Remote-IP-Adresse | netstat
TCP-Verbindungsstatus | netstat
Prozess-ID | netstat
Anzahl der aktiven Verbindungen | netstat

Im Folgenden finden Sie die Verbindungsdaten, die die Appliance von jedem Windows-Server sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
Prozessname | Win32_Process | ExecutablePath
Prozessargumente | Win32_Process | CommandLine
Anwendungsname | Win32_Process | Parameter „VersionInfo.ProductName“ der ExecutablePath-Eigenschaft

#### <a name="linux-server-dependencies-data"></a>Linux-Server-Abhängigkeitsdaten

Im Folgenden finden Sie die Verbindungsdaten, die die Appliance von jedem Linux-Server sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist.

**Daten** | **Befehle**
--- | ---
Lokaler Port | netstat
Lokale IP-Adresse | netstat
Remoteport | netstat
Remote-IP-Adresse | netstat
TCP-Verbindungsstatus | netstat
Anzahl der aktiven Verbindungen | netstat
Prozess-ID  | netstat
Prozessname | ps
Prozessargumente | ps
Anwendungsname | dpkg oder rpm

## <a name="collected-data---hyper-v"></a>Gesammelte Daten – Hyper-V

Die Appliance sammelt Konfigurations- und Leistungsmetadaten von Servern, die in einer Hyper-V-Umgebung ausgeführt werden.

### <a name="metadata"></a>Metadaten
Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Servern für die Migration zu Azure, der richtigen Größe für Server und der Kosten der Planung. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Nachstehend finden Sie die vollständige Liste der Servermetadaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
**Serverdetails** | 
Seriennummer des BIOS | Msvm_BIOSElement | BIOSSerialNumber
Servertyp (1. oder 2. Generation) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Anzeigename des Servers | Msvm_VirtualSystemSettingData | ElementName
Serverversion | Msvm_ProcessorSettingData | VirtualQuantity
Arbeitsspeicher (Bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximaler Arbeitsspeicher, der vom Server genutzt werden kann | Msvm_MemorySettingData | Begrenzung
Dynamischer Arbeitsspeicher aktiviert | Msvm_MemorySettingData | DynamicMemoryEnabled
Name/Version/FQDN des Betriebssystems | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems-Namensdaten
Energiestatus des Servers | Msvm_ComputerSystem | EnabledState
**Details pro Datenträger** |
Datenträgeridentifikator | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtueller Datenträgertyp | Msvm_VirtualHardDiskSettingData | type
Virtuelle Datenträgergröße | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtueller übergeordneter Datenträger | Msvm_VirtualHardDiskSettingData | ParentPath
**Details pro NIC** |
IP-Adressen (synthetische NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP aktiviert (synthetische NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (synthetische NICs) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC-MAC-Adresse (synthetische NICs) | Msvm_SyntheticEthernetPortSettingData | Adresse
NIC-ID (Legacy-NICs) | MsvmEmulatedEthernetPortSetting-Daten | InstanceID
NIC-MAC-ID (Legacy-NICs) | MsvmEmulatedEthernetPortSetting-Daten | Adresse

### <a name="performance-data"></a>Leistungsdaten

Nachstehend finden Sie die Serverleistungsdaten, die die Appliance sammelt und an Azure sendet.

**Leistungsindikatorklasse** | **Leistungsindikator** | **Auswirkungen auf die Bewertung**
--- | --- | ---
Virtueller Hyper-V-Hypervisor-Prozessor | % Gastausführungszeit | Empfohlene Servergröße/Kosten
Hyper-V-Server mit dynamischem Arbeitsspeicher | Aktueller Druck (%)<br/> Sichtbarer physischer Speicher des Gastcomputers (MB) | Empfohlene Servergröße/Kosten
Virtuelles Hyper-V-Speichergerät | Lesebytes/Sekunde | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
Virtuelles Hyper-V-Speichergerät | Schreibbytes/Sekunde | Berechnung von Datenträgergröße, Speicherkosten und Servergröße
Virtueller Hyper-V-Netzwerkadapter | Empfangene Bytes/Sekunde | Berechnung der Servergröße
Virtueller Hyper-V-Netzwerkadapter | Gesendete Bytes/Sekunde | Berechnung der Servergröße

- Die CPU-Auslastung ist die Summe aller Auslastungen für alle an einen Server angeschlossenen virtuellen Prozessoren.
- Die Speichernutzung ist (Aktueller Druck * Sichtbarer physischer Speicher des Gastcomputers) / 100.
- Die Werte für die Festplatten- und Netzwerkauslastung werden von den aufgeführten Hyper-V-Leistungsindikatoren gesammelt.

## <a name="collected-data---physical"></a>Gesammelte Daten – physisch

Die Appliance sammelt Konfigurations- und Leistungsmetadaten von physischen oder virtuellen Servern, die lokal ausgeführt werden.

### <a name="metadata"></a>Metadaten

Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Servern für die Migration zu Azure, der richtigen Größe für Server und der Kosten der Planung. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

### <a name="windows-server-metadata"></a>Windows-Servermetadaten

Nachstehend finden Sie die vollständige Liste der Windows-Servermetadaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain, Name, PartOfDomain
Anzahl der Prozessorkerne | Win32_PRocessor | NumberOfCores
Zugeordneter Arbeitsspeicher | Win32_ComputerSystem | TotalPhysicalMemory
Seriennummer des BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS-GUID | Win32_ComputerSystemProduct | UUID
Starttyp | Win32_DiskPartition | Überprüfen auf Partition mit Type = **GPT:System** für EFI/BIOS
Betriebssystemname | Win32_OperatingSystem | Caption
Betriebssystemversion |Win32_OperatingSystem | Version
Betriebssystemarchitektur | Win32_OperatingSystem | OSArchitecture
Datenträgeranzahl | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Datenträgergröße | Win32_DiskDrive | Size
NIC-Liste | Win32_NetworkAdapterConfiguration | Description, Index
NIC-IP-Adresse | Win32_NetworkAdapterConfiguration | IPAddress
NIC-MAC-Adresse | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Linux-Servermetadaten

Nachstehend finden Sie die vollständige Liste der Linux-Servermetadaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **Befehle**
--- | ---
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Anzahl der Prozessorkerne |  cat/proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Zugeordneter Arbeitsspeicher | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Seriennummer des BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS-GUID | cat /sys/class/dmi/id/product_uuid
Starttyp | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Betriebssystemname/-version | Für die Version und den Namen des Betriebssystems wird auf folgende Dateien zugegriffen:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Betriebssystemarchitektur | uname -m
Datenträgeranzahl | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Startdatenträger | df /boot \| sed -n 2p \| awk '{print $1}'
Datenträgergröße | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC-Liste | ip -o -4 addr show \| awk '{print $2}'
NIC-IP-Adresse | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC-MAC-Adresse | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows-Leistungsdaten

Nachstehend finden Sie die Windows-Serverleistungsdaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
CPU-Auslastung | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Speicherauslastung | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC-Anzahl | Win32_PerfFormattedData_Tcpip_NetworkInterface | Ruft die Anzahl der Netzwerkgeräte ab.
Pro NIC empfangene Daten | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Pro NIC übertragene Daten | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Datenträgeranzahl | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Anzahl der Datenträger
Datenträgerdetails | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-Leistungsdaten

Nachstehend finden Sie die Linux-Serverleistungsdaten, die die Appliance sammelt und an Azure sendet.

| **Daten** | **Befehle** |
| --- | --- |
| CPU-Auslastung | cat /proc/stat/ \| grep „cpu“ /proc/stat |
| Speicherauslastung | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| NIC-Anzahl | lshw -class network \| grep eth[0-60] \| wc -l |
| Pro NIC empfangene Daten | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Pro NIC übertragene Daten | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Datenträgeranzahl | Fdisk -l \| egrep 'Disk.\*bytes' \| awk '{print $2}' \| cut -f1 -d ':' |
| Datenträgerdetails | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Appliance-Upgrades

Die Appliance wird aktualisiert, wenn die auf der Appliance ausgeführten Azure Migrate-Dienste aktualisiert werden. Dies geschieht automatisch, da auf der Appliance standardmäßig die automatische Aktualisierung aktiviert ist. Sie können diese Standardeinstellung ändern, um die Appliancedienste manuell zu aktualisieren.

### <a name="turn-off-auto-update"></a>Deaktivieren der automatischen Aktualisierung

1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Zum Deaktivieren der automatischen Aktualisierung erstellen Sie einen Registrierungsschlüssel **AutoUpdate** mit dem DWORD-Wert 0.

    ![Festlegen des Registrierungsschlüssels](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivieren der automatischen Aktualisierung

Sie können die automatische Aktualisierung über eine der folgenden Methoden aktivieren:

- Löschen des AutoUpdate-Registrierungsschlüssels unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance“.
- Klicken Sie in den Überprüfungen auf aktuelle Updates im Bereich **Erforderliche Komponenten einrichten** auf **Appliancedienste anzeigen**, um die automatische Aktualisierung zu aktivieren.

So löschen Sie den Registrierungsschlüssel:

1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Löschen Sie den Registrierungsschlüssel **AutoUpdate**, der zuvor zum Deaktivieren der automatischen Aktualisierung erstellt wurde.

Aktivierung im Appliance-Konfigurations-Manager nach Abschluss der Ermittlung:

1. Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
2. Klicken Sie in der Überprüfung auf aktuelle Updates auf **Appliancedienste anzeigen** und dann auf den Link zum Aktivieren der automatischen Aktualisierung.

    ![Aktivieren der automatischen Aktualisierung](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Überprüfen der Appliancediensteversion

Sie können die Version der Appliancedienste über eine der folgenden Methoden überprüfen:

- Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
- Auf der Appliance unter **Systemsteuerung** > **Programme und Funktionen**.

So überprüfen Sie die Version im Appliance-Konfigurations-Manager:

1. Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
2. Klicken Sie in der Überprüfung auf aktuelle Updates auf **Appliancedienste anzeigen**.

    ![Überprüfen der Version](./media/migrate-appliance/versions.png)

So überprüfen Sie die Version in der Systemsteuerung:

1. Klicken Sie in der Appliance auf **Start** > **Systemsteuerung** > **Programme und Features**
2. Überprüfen Sie die Version der Appliancedienste in der Liste.

    ![Überprüfen der Version in der Systemsteuerung](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Manuelle Aktualisierung einer älteren Version

Wenn bei einem oder mehreren Diensten eine ältere Version vorhanden ist, müssen Sie den Dienst deinstallieren und eine manuelle Aktualisierung auf die aktuelle Version durchführen.

1. In diesem [Download](https://aka.ms/latestapplianceservices) der LatestComponents.json-Datei finden Sie die jeweils aktuelle Version der Appliancedienste.
2. Öffnen Sie die Datei „LatestComponents.json“ nach dem Herunterladen im Editor.
3. Suchen Sie in der Datei nach der aktuellen Dienstversion und dem zugehörigen Downloadlink. Beispiel:

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Laden Sie die aktuelle Version eines überholten Diensts über den Downloadlink in der Datei herunter.
5. Führen Sie nach dem Herunterladen den folgenden Befehl in einem Befehlsfenster für Administratoren aus, um die Integrität der heruntergeladenen MSI-Datei zu überprüfen.

    Beispiel: ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Überprüfen Sie, ob die Befehlsausgabe dem Hashwerteintrag für den Dienst in der Datei (z. B. der oben stehende Hashwert MD5) entspricht.
6. Führen Sie dann die MSI-Datei aus, um den Dienst zu installieren. Es handelt sich um eine unbeaufsichtigte Installation, und das Installationsfenster wird geschlossen, sobald der Vorgang beendet ist.
7. Überprüfen Sie nach Abschluss der Installation die Version des Diensts unter **Systemsteuerung** > **Programme und Features**. Die Dienstversion sollte auf die aktuelle Version aus der JSON-Datei aktualisiert worden sein.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie](how-to-set-up-appliance-vmware.md) Sie die Appliance für VMware einrichten.
- [Erfahren Sie, wie](how-to-set-up-appliance-hyper-v.md) Sie die Appliance für Hyper-V einrichten.
- [Erfahren Sie, wie](how-to-set-up-appliance-physical.md) Sie die Appliance für physische Server einrichten.
