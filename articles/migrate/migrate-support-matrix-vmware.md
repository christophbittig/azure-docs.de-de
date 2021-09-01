---
title: Unterstützung der VMware-Serverermittlung in Azure Migrate
description: Erfahren Sie mehr über die Azure Migrate-Unterstützung für Ermittlung und Bewertung von Servern in einer VMware-Umgebung.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: dedb05df1713238a6271af4dfd7b9cf1695d0bc7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356378"
---
# <a name="support-matrix-for-vmware-discovery"></a>Unterstützungsmatrix für VMware-Ermittlung 

Dieser Artikel fasst die Voraussetzungen und Supportanforderungen für die Verwendung des Tools [Azure Migrate: Ermittlung und Bewertung](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) zum Ermitteln und Bewerten von Servern in einer VMware-Umgebung für die Migration zu Azure zusammen.

Um Server zu bewerten, erstellen Sie zunächst ein Azure Migrate-Projekt. Das Azure Migrate-Tool zur Ermittlung und Bewertung wird dem Projekt automatisch hinzugefügt. Stellen Sie dann die Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Konfigurations- und Leistungsmetadaten an Azure. Nachdem die Ermittlung abgeschlossen ist, fassen Sie die ermittelten Server in Gruppen zusammen und führen Bewertungen pro Gruppe aus.

Ziehen Sie beim Planen der Migration von VMware-Servern zu Azure die [Migrationsunterstützungsmatrix](migrate-support-matrix-vmware-migration.md) zu Rate.

## <a name="limitations"></a>Einschränkungen

Anforderung | Details
--- | ---
**Projekteinschränkungen** | Sie können mehrere Azure Migrate-Projekte in einem Azure-Abonnement erstellen.<br /><br /> In einem einzelnen [Projekt](migrate-support-matrix.md#project) können bis zu 50.000 Server in einer VMware-Umgebung ermittelt und bewertet werden. Ein Projekt kann im Rahmen der Bewertungseinschränkungen physische Server und Server aus einer Hyper-V-Umgebung umfassen.
**Ermittlung** | Die Azure Migrate-Appliance kann bis zu 10.000 Server auf einem Server mit vCenter Server ermitteln.
**Bewertung** | Sie können einer einzelnen Gruppe bis zu 35.000 Server hinzufügen.<br /><br /> Sie können bis zu 35.000 Server in einem einzelnen Vorgang bewerten.

Lesen Sie die weiteren Informationen zu [Bewertungen](concepts-assessment-calculation.md).

## <a name="vmware-requirements"></a>VMware-Anforderungen

VMware | Details
--- | ---
**vCenter Server** | Server, die ermittelt und bewertet werden sollen, müssen mit vCenter Server Version 7.0, 6.7, 6.5, 6.0 oder 5.5 verwaltet werden.<br /><br /> Die Ermittlung von Servern durch Bereitstellen von ESXi-Hostdetails in der Appliance wird derzeit nicht unterstützt. <br /><br /> IPv6-Adressen werden für vCenter-Server (für Ermittlung und Bewertung von Servern) und ESXi-Hosts (für Replikation von Servern) nicht unterstützt.
**Berechtigungen** | Das Azure Migrate-Tool für Ermittlung und Bewertung erfordert ein schreibgeschütztes vCenter Server-Konto.<br /><br /> Wenn Sie das Tool für ein Softwareinventar und Abhängigkeitsanalyse ohne Agent verwenden möchten, muss das Konto über Berechtigungen für Gastvorgänge auf VMware-VMs verfügen.

## <a name="server-requirements"></a>Serveranforderungen

VMware | Details
--- | ---
**Betriebssysteme** | Alle Windows- und Linux-Betriebssysteme können für die Migration bewertet werden.
**Storage** | An SCSI-, IDE-und SATA-basierte Controller angefügte Datenträger werden unterstützt.

## <a name="azure-migrate-appliance-requirements"></a>Anforderungen für die Azure Migrate-Appliance

In Azure Migrate erfolgt die Ermittlung und Bewertung über die [Azure Migrate-Appliance](migrate-appliance.md). Sie können die Appliance als Server in Ihrer VMware-Umgebung bereitstellen, indem Sie eine VMware OVA-Vorlage (Open Virtualization Appliance) verwenden, die in vCenter Server importiert wird, oder indem Sie ein [PowerShell-Skript](deploy-appliance-script.md) verwenden. Weitere Informationen zu den [Applianceanforderungen für VMware](migrate-appliance.md#appliance---vmware).

Im Folgenden finden Sie weitere Anforderungen für die Appliance:

- In Azure Government müssen Sie die Appliance mithilfe eines [Skripts](deploy-appliance-script-government.md) bereitstellen.
- Die Appliance muss auf bestimmte URLs in [öffentlichen Clouds](migrate-appliance.md#public-cloud-urls) und [Government-Clouds](migrate-appliance.md#government-cloud-urls) zugreifen können.

## <a name="port-access-requirements"></a>Portzugriffsanforderungen

Sicherungsmedium | Verbindung
--- | ---
**Azure Migrate-Appliance** | Eingehende Verbindungen an TCP-Port 3389, um Remotedesktopverbindungen mit der Appliance zu ermöglichen<br /><br /> Eingehende Verbindungen an Port 44368, um über Remotezugriff über die URL `https://<appliance-ip-or-name>:44368` auf die Applianceverwaltungs-App zugreifen zu können. <br /><br />Ausgehende Verbindungen an Port 443 (HTTPS), um Ermittlungs- und Leistungsmetadaten an Azure Migrate zu senden.
**vCenter Server** | Eingehende Verbindungen an TCP-Port 443, damit die Appliance Konfigurations- und Leistungsmetadaten für Bewertungen sammeln kann <br /><br /> Die Appliance stellt standardmäßig über Port 443 eine Verbindung mit vCenter her. Wenn vCenter Server an einem anderen Port lauscht, können Sie den Port beim Einrichten der Ermittlung ändern.
**ESXi-Hosts** | Wenn eine [Ermittlung von Softwareinventar](how-to-discover-applications.md) oder [Abhängigkeitsanalyse ohne Agent](concepts-dependency-visualization.md#agentless-analysis) erfolgen soll, stellt die Appliance eine Verbindung mit ESXi-Hosts an TCP-Port 443 her, um Softwareinventar bzw. Abhängigkeiten auf den Servern zu ermitteln.

## <a name="software-inventory-requirements"></a>Softwareinventarisierungsanforderungen

Zusätzlich zur Ermittlung von Servern kann die Azure Migrate-Ermittlung und -Bewertung Softwareinventarisierung für Server ausführen. Durch Softwareinventarisierung können Sie einen maßgeschneiderten Migrationspfad für Ihre lokalen Workloads festlegen und planen.

Support | Details
--- | ---
**Unterstützte Server** | Wird derzeit nur für Server in einer VMware-Umgebung unterstützt. Sie können Softwareinventarisierung auf bis zu 10.000 Servern durchführen, und zwar über jede Azure Migrate Appliance.
**Betriebssysteme** | Alle auf Servern ausgeführten Windows- und Linux-Versionen werden unterstützt.
**VM-Anforderungen** | Für Softwareinventarisierung müssen VMware-Tools installiert sein und auf Ihren Servern ausgeführt werden. <br /><br /> Die Version der VMware-Tools muss Version 10.2.1 oder höher sein.<br /><br /> Auf Windows-Servern muss PowerShell ab Version 2.0 installiert sein.
**Ermittlung** | Softewareinventarisierung wird aus vCenter Server mit den auf den Servern installierten VMware-Tools ausgeführt. Die Appliance sammelt die Informationen zum Softwareinventar von dem Server, auf dem vCenter Server ausgeführt wird, über vSphere-APIs. Softwareinventarisierung erfolgt ohne Agent. Auf dem Server ist kein Agent installiert, und die Appliance stellt keine direkte Verbindung mit den Servern her. WMI muss aktiviert und auf Windows-Servern verfügbar sein, um die Details der auf den Servern installierten Rollen und Features zu erfassen.
**vCenter Server-Benutzerkonto** | Um mit den Servern für Softwareinventarisierung zu interagieren, muss das für die Bewertung verwendete schreibgeschützte vCenter Server-Konto über Berechtigungen für Gastvorgänge auf VMware-VMs verfügen.
**Serverzugriff** | Sie können mehrere Domänen- und Nicht-Domänen-Anmeldeinformationen (Windows/Linux) im Appliancekonfigurations-Manager für Softwareinventarisierung hinzufügen.<br /><br /> Sie müssen über ein Gastbenutzerkonto für Windows-Server und ein Standardbenutzerkonto (ohne `sudo`-Zugriff) für alle Linux-Server verfügen.
**Portzugriff** | Auf ESXi-Hosts, auf denen Server ausgeführt werden, auf denen Sie Softwareinventarisierung durchführen möchten, muss die Azure Migrate-Appliance eine Verbindung mit dem TCP-Port 443 herstellen können. Der Server, auf dem vCenter Server ausgeführt wird, gibt eine ESXi-Hostverbindung zurück, um die Datei mit den Details zum Softwareinventar herunterzuladen.

## <a name="sql-server-instance-and-database-discovery-requirements"></a>SQL Server-Instanz- und -Datenbank-Ermittlungsanforderungen

Bei der [Softwareinventarisierung](how-to-discover-applications.md) werden SQL Server-Instanzen identifiziert. Anhand dieser Informationen versucht die Appliance, mithilfe der im Konfigurations-Manager der Appliance angegebenen Anmeldeinformationen für Windows-Authentifizierung oder SQL Server-Authentifizierung eine Verbindung mit den entsprechenden SQL Server-Instanzen herzustellen. Die Appliance kann nur mit den SQL Server-Instanzen verbunden werden, mit denen sie über eine direkte Verbindung verfügt. Für die Softwareinventarisierung selbst ist möglicherweise keine direkte Netzwerkverbindung erforderlich.

Sobald die Appliance verbunden ist, sammelt sie Konfigurations- und Leistungsdaten für SQL Server-Instanzen und -Datenbanken. SQL Server-Konfigurationsdaten werden ein Mal alle 24 Stunden aktualisiert. Leistungsdaten werden alle 30 Sekunden erfasst.

Support | Details
--- | ---
**Unterstützte Server** | Wird derzeit nur für Server unterstützt, die SQL Server in einer VMware-Umgebung ausführen. Sie können bis zu 300 SQL Server-Instanzen oder 6.000 SQL-Datenbanken ermitteln, je nachdem, welcher Wert niedriger ist.
**Windows-Server** | Windows Server 2008 oder höher wird unterstützt.
**Linux-Server** | Wird derzeit nicht unterstützt.
**Authentifizierungsmechanismus** | Sowohl Windows- als auch SQL Server-Authentifizierung werden unterstützt. Sie können im Appliance-Konfigurations-Manager Anmeldeinformationen für beide Authentifizierungstypen angeben.
**SQL-Serverzugriff** | Für Azure Migrate ist ein Windows-Benutzerkonto erforderlich, das Mitglied der sysadmin-Serverrolle ist.
**SQL Server-Versionen** | SQL Server 2008 oder höher wird unterstützt.
**SQL Server-Editionen** | Die Enterprise, Standard, Developer und Express Edition werden unterstützt.
**Unterstützte SQL-Konfiguration** | Derzeit wird nur Ermittlung von eigenständigen SQL Server-Instanzen und der entsprechenden Datenbanken unterstützt.<br /><br /> Die Identifizierung von Failoverclustern und Always On-Verfügbarkeitsgruppen wird nicht unterstützt.
**Unterstützte SQL-Dienste** | Nur die SQL Server-Datenbank-Engine wird unterstützt. <br /><br /> Die Ermittlung von SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) und SQL Server Analysis Services (SSAS) wird nicht unterstützt.

> [!NOTE]
> Standardmäßig verwendet Azure Migrate für Verbindungen mit SQL-Instanzen die sicherste Methode, d. h., Azure Migrate verschlüsselt die Kommunikation zwischen der Azure Migrate-Appliance und den SQL Server-Quellinstanzen, indem die TrustServerCertificate-Eigenschaft auf `true` festgelegt wird. Darüber hinaus verwendet die Transportschicht SSL zum Verschlüsseln des Kanals und Umgehen der Zertifikatkette zur Überprüfung der Vertrauenswürdigkeit. Daher muss der Server der Appliance so eingerichtet sein, dass er die Stammzertifizierungsstelle des Zertifikats als vertrauenswürdig einstuft. 
>
> Sie können die Verbindungseinstellungen jedoch ändern, indem Sie auf der Appliance **SQL Server-Verbindungseigenschaften bearbeiten** auswählen. [Erfahren Sie mehr](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) darüber, was Sie auswählen sollten.

## <a name="aspnet-web-apps-discovery-requirements"></a>Anforderungen an die Ermittlung von ASP.NET-Web-Apps

Bei der [Softwareinventarisierung](how-to-discover-applications.md) wird die auf ermittelten Servern vorhandene Webserverrolle identifiziert. Wenn für einen Server die Webserverrolle aktiviert ist, führt Azure Migrate die Ermittlung von Web-Apps auf dem Server durch.
Der Benutzer kann sowohl Domänenanmeldeinformationen als auch Nicht-Domänenanmeldeinformationen auf der Appliance hinzufügen. Stellen Sie sicher, dass das verwendete Konto über lokale Administratorrechte auf Quellservern verfügt. Azure Migrate ordnet Anmeldeinformationen automatisch den jeweiligen Servern zu, sodass sie nicht manuell zugeordnet werden müssen. Vor allem werden diese Anmeldeinformationen nie an Microsoft gesendet und verbleiben auf der Appliance, die in der Quellumgebung ausgeführt wird.
Nachdem die Appliance verbunden wurde, sammelt sie Konfigurationsdaten für den IIS-Webserver und ASP.NET-Web-Apps. Konfigurationsdaten von Web-Apps werden alle 24 Stunden aktualisiert.

Support | Details
--- | ---
**Unterstützte Server** | Wird derzeit nur für Windows-Server unterstützt, die IIS in Ihrer VMware-Umgebung ausführen.
**Windows-Server** | Windows Server 2008 R2 oder höher wird unterstützt.
**Linux-Server** | Wird derzeit nicht unterstützt.
**IIS-Zugriff** | Für die Ermittlung von Web-Apps ist ein lokales Administratorbenutzerkonto erforderlich.
**IIS-Versionen** | IIS 7.5 oder höher wird unterstützt.

> [!NOTE]
> Daten werden im Ruhezustand und während der Übertragung immer verschlüsselt.

## <a name="dependency-analysis-requirements-agentless"></a>Anforderungen der Abhängigkeitsanalyse (ohne Agent)

Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen lokalen Servern identifizieren, die Sie bewerten und zu Azure migrieren möchten. In der folgenden Tabelle werden die Anforderungen zum Einrichten der Abhängigkeitsanalyse ohne Agent zusammengefasst:

Support | Details
--- | ---
**Unterstützte Server** | Wird derzeit nur für Server in einer VMware-Umgebung unterstützt.
**Windows-Server** | Windows Server 2019<br />Windows Server 2016<br /> Windows Server 2012 R2<br /> Windows Server 2012<br /> Windows Server 2008 R2 (64-Bit)<br />Microsoft Windows Server 2008 (32-Bit)
**Linux-Server** | Red Hat Enterprise Linux 7, 6, 5<br /> Ubuntu Linux 16.04, 14.04<br /> Debian 8, 7<br /> Oracle Linux 7, 6 oder höher<br /> CentOS 7, 6, 5<br /> SUSE Linux Enterprise Server 11 und höher
**Serveranforderungen** | Auf Servern, die Sie analysieren möchten, muss VMware Tools (10.2.1 oder höher) installiert sein und ausgeführt werden.<br /><br /> Auf den Servern muss PowerShell, Version 2.0 oder höher, installiert sein.
**Ermittlungsmethode** |  Abhängigkeitsinformationen zwischen Servern werden mithilfe der VMware-Tools erfasst, die auf dem Server installiert sind, auf dem vCenter Server ausgeführt wird. Die Appliance sammelt die Informationen vom Server mithilfe von vSphere-APIs. Auf dem Server ist kein Agent installiert, und die Appliance stellt keine direkte Verbindung mit den Servern her. WMI muss auf Windows-Servern aktiviert und verfügbar sein.
**vCenter-Konto** | Das schreibgeschützte Konto, das von Azure Migrate für die Bewertung verwendet wird, muss über Berechtigungen für Gastvorgänge auf VMware-VMs verfügen.
**Windows Server-Berechtigungen** |  Ein Benutzerkonto (lokal oder Domänenkonto) mit Administratorberechtigungen für Server.
**Linux-Konto** | Ein root-Benutzerkonto oder ein Konto mit den folgenden Berechtigungen für Dateien in „/bin/netstat“ und „/bin/ls“: <br />CAP_DAC_READ_SEARCH<br /> CAP_SYS_PTRACE<br /><br /> Legen Sie diese Funktionen mithilfe der folgenden Befehle fest:<br /><code>sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br /> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat</code>
**Portzugriff** | Auf ESXi-Hosts, auf denen die Server ausgeführt werden, deren Abhängigkeiten Sie ermitteln möchten, muss die Azure Migrate-Appliance eine Verbindung mit TCP-Port 443 herstellen können. Der Server, der vCenter Server ausführt, gibt eine ESXi-Hostverbindung zurück, um die Datei mit den Abhängigkeitsdaten herunterzuladen.

## <a name="dependency-analysis-requirements-agent-based"></a>Anforderungen der Abhängigkeitsanalyse (Agent-basiert)

Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen lokalen Servern identifizieren, die Sie bewerten und zu Azure migrieren möchten. In der folgenden Tabelle werden die Anforderungen zum Einrichten der Agent-basierten Abhängigkeitsanalyse zusammengefasst:

Anforderung | Details
--- | ---
**Vor der Bereitstellung** | Sie sollten über ein Projekt verfügen, dem das Azure Migrate-Tool zur Ermittlung und Bewertung hinzugefügt wurde.<br /><br />Stellen Sie die Abhängigkeitsvisualisierung nach dem Einrichten einer Azure Migrate-Appliance zum Ermitteln Ihrer lokalen Server bereit.<br /><br />Erfahren Sie, wie Sie [erstmalig ein Projekt erstellen](create-manage-projects.md).<br /> Erfahren Sie, wie Sie einem [vorhandenen Projekt ein Ermittlungs- und Bewertungstool hinzufügen](how-to-assess.md).<br /> Erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Bewertung von [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)- oder physischen Servern einrichten.
**Unterstützte Server** | Wird für alle Server in Ihrer lokalen Umgebung unterstützt.
**Log Analytics** | Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../azure-monitor/vm/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).<br /><br /> Sie ordnen einem Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zu. Der Arbeitsbereich für ein Projekt kann nach dem Hinzufügen nicht mehr geändert werden. <br /><br /> Der Arbeitsbereich muss sich in demselben Abonnement befinden wie das Projekt.<br /><br /> Der Arbeitsbereich muss sich in einer der Regionen „USA, Osten“, „Asien, Südosten“ oder „Europa, Westen“ befinden. Arbeitsbereiche in anderen Regionen können keinem Projekt zugeordnet werden.<br /><br /> Der Arbeitsbereich muss sich in einer [Region befinden, in der Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br /><br /> In Log Analytics wird der Arbeitsbereich, der Azure Migrate zugeordnet ist, mit dem Schlüssel des Projekts und dem Projektnamen gekennzeichnet.
**Erforderliche Agents** | Installieren Sie auf jedem Server, den Sie analysieren möchten, die folgenden Agents:<br />- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)<br />- [Dependency-Agent](../azure-monitor/agents/agents-overview.md#dependency-agent)<br /><br /> Wenn lokale Server nicht mit dem Internet verbunden sind, müssen Sie das Log Analytics-Gateway auf diese Servern herunterladen und installieren.<br /><br /> Erfahren Sie mehr über das Installieren des [Abhängigkeits-Agents](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) und von [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-Arbeitsbereich** | Der Arbeitsbereich muss sich in demselben Abonnement befinden wie das Projekt.<br /><br /> Azure Migrate unterstützt die Erstellung von Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“.<br /><br />  Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br /><br /> Der Arbeitsbereich für ein Projekt kann nach dem Hinzufügen nicht mehr geändert werden.
**Kosten** | Für die Dienstzuordnungslösung fallen in den ersten 180 Tagen (ab dem Tag der Zuordnung des Log Analytics-Arbeitsbereichs zum Projekt) keine Kosten an.<br /><br /> Nach 180 Tagen fallen die Log Analytics-Standardgebühren an.<br /><br /> Für andere Lösungen als die Dienstzuordnung im zugeordneten Log Analytics-Arbeitsbereich fallen die [Standardgebühren](https://azure.microsoft.com/pricing/details/log-analytics/) für Log Analytics an.<br /><br /> Beim Löschen des Projekts wird der Arbeitsbereich nicht automatisch gelöscht. Nach dem Löschen des Projekts ist die Nutzung der Dienstzuordnung nicht mehr kostenlos, und für jeden Knoten werden Kosten gemäß dem kostenpflichtigen Tarif für den Log Analytics-Arbeitsbereich berechnet.<br /><br />Wenn Sie über Projekte verfügen, die Sie vor der allgemeinen Verfügbarkeit von Azure Migrate (28. Februar 2018) erstellt haben, fallen für Sie möglicherweise zusätzliche Gebühren für die Dienstzuordnung an. Um sicherzustellen, dass erst nach 180 Tagen Gebühren anfallen, empfehlen wir Ihnen, ein neues Projekt zu erstellen. Arbeitsbereiche, die vor der allgemeinen Verfügbarkeit erstellt wurden, sind weiterhin kostenpflichtig.
**Verwaltung** | Wenn Sie Agents im Arbeitsbereich registrieren, verwenden Sie die ID und den Schlüssel, die vom Projekt bereitgestellt werden.<br /><br /> Sie können den Log Analytics-Arbeitsbereich außerhalb von Azure Migrate verwalten.<br /><br /> Wenn Sie das zugehörige Projekt löschen, wird der Arbeitsbereich nicht automatisch gelöscht. [Löschen Sie ihn manuell](../azure-monitor/logs/manage-access.md).<br /><br /> Löschen Sie den von Azure Migrate erstellten Arbeitsbereich nur, wenn Sie auch das Projekt löschen. Wenn Sie anders vorgehen, funktioniert die Abhängigkeitsvisualisierung nicht wie erwartet.
**Internetkonnektivität** | Wenn Server nicht mit dem Internet verbunden sind, installieren Sie das Log Analytics-Gateway auf diesen Servern.
**Azure Government** | Die Agent-basierte Abhängigkeitsanalyse wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [bewährten Methoden für die Bewertung](best-practices-assessment.md).
- Erfahren Sie, wie Sie sich [auf eine VMware-Bewertung vorbereiten](./tutorial-discover-vmware.md).