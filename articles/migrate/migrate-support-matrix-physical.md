---
title: Unterstützung der physischen Ermittlung und Bewertung in Azure Migrate
description: 'Informationen zur Unterstützung der physischen Ermittlung und Bewertung mit „Azure Migrate: Ermittlung und Bewertung“'
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 2d68a74332ef77694d44597e6f879858fa0051bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345970"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Unterstützungsmatrix für die Ermittlung und Bewertung physischer Server 

Dieser Artikel bietet eine Übersicht über die Voraussetzungen und Unterstützungsanforderungen bei der Bewertung von physischen Servern für die Migration zu Azure mithilfe des Tools [Azure Migrate: Ermittlung und Bewertung](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool). Wenn Sie physische Server zu Azure migrieren möchten, überprüfen Sie die [Migrationsunterstützungsmatrix](migrate-support-matrix-physical-migration.md).

Zum Bewerten physischer Server erstellen Sie ein Projekt und fügen dem Projekt das Azure Migrate-Tool zur Ermittlung und Bewertung hinzu. Nachdem das Tool hinzugefügt wurde, stellen Sie die [Azure Migrate-Appliance](migrate-appliance.md) bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Servermetadaten und Leistungsdaten an Azure. Nach Abschluss der Ermittlung ordnen Sie die ermittelten Server in Gruppen und führen eine Bewertung für die Gruppen aus.

## <a name="limitations"></a>Einschränkungen

**Unterstützung** | **Details**
--- | ---
**Bewertungseinschränkungen** | Sie können bis zu 35.000 physische Server in einem einzelnen [Projekt](migrate-support-matrix.md#project) ermitteln und bewerten.
**Projekteinschränkungen** | Sie können mehrere Projekte in einem Azure-Abonnement erstellen. Zusätzlich zu physischen Servern kann ein Projekt bis zum jeweiligen Bewertungsgrenzwert auch VMware- und Hyper-V-Server enthalten.
**Ermittlung** | Die Azure Migrate-Appliance kann bis zu 1.000 physische Server ermitteln.
**Bewertung** | Sie können einer einzelnen Gruppe bis zu 35.000 Server hinzufügen.<br/><br/> Sie können bis zu 35.000 Server in einem einzelnen Vorgang bewerten.

[Hier erfahren Sie mehr](concepts-assessment-calculation.md) über Bewertungen.

## <a name="physical-server-requirements"></a>Anforderungen für physische Server

**Bereitstellung des physischen Servers**: Der physische Server kann eigenständig sein oder in einem Cluster bereitgestellt werden.

**Betriebssystem:** Alle Windows- und Linux-Betriebssysteme können für die Migration bewertet werden.

**Berechtigungen:**

Richten Sie ein Konto ein, das von der Appliance für den Zugriff auf die physischen Server verwendet werden kann.

**Windows-Server**

- Verwenden Sie für Windows-Server ein Domänenkonto für in die Domäne eingebundene Server und ein lokales Konto für nicht in die Domäne eingebundene Server. 
- Das Benutzerkonto sollte diesen Gruppen hinzugefügt werden: Remoteverwaltungsbenutzer, Leistungsüberwachungsbenutzer und Leistungsprotokollbenutzer. 
- Wenn die Gruppe „Remoteverwaltungsbenutzer“ nicht vorhanden ist, fügen Sie der Gruppe ein Benutzerkonto hinzu: **WinRMRemoteWMIUsers_** .
- Das Konto benötigt diese Berechtigungen, damit die Appliance eine CIM-Verbindung mit dem Server herstellen und die erforderlichen Konfigurations- und Leistungsmetadaten aus den [hier](migrate-appliance.md#collected-data---physical) aufgeführten WMI-Klassen pullen kann.
- In einigen Fällen werden durch das Hinzufügen des Kontos zu diesen Gruppen unter Umständen nicht die erforderlichen Daten aus WMI-Klassen zurückgegeben, da das Konto möglicherweise nach [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) gefiltert ist. Um die UAC-Filterung außer Kraft zu setzen, muss das Benutzerkonto über die erforderlichen Berechtigungen für den CIMV2-Namespace und die untergeordneten Namespaces auf dem Zielserver verfügen. Sie können die [hier](troubleshoot-appliance.md#access-is-denied-when-connecting-to-physical-servers-during-validation) beschriebenen Schritte ausführen, um die erforderlichen Berechtigungen zu aktivieren.

    > [!Note]
    > Stellen Sie unter Windows Server 2008 und 2008 R2 sicher, dass WMF 3.0 auf den Servern installiert ist.

**Linux-Server**

- Sie benötigen ein root-Konto auf den Linux-Servern, die Sie ermitteln möchten. Alternativ können Sie ein Benutzerkonto mit sudo-Berechtigungen bereitstellen.
- Die Unterstützung zum Hinzufügen eines Benutzerkontos mit sudo-Zugriff wird standardmäßig mit dem neuen Installationsskript für die Appliance bereitgestellt, das seit dem 20. Juli 2021 aus dem Portal heruntergeladen werden kann.
- Für ältere Appliances können Sie die Funktion anhand der folgenden Schritte aktivieren:
    1. Öffnen Sie auf dem Server, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
    1. Navigieren Sie zu HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
    1. Erstellen Sie den Registrierungsschlüssel „isSudo“ mit dem DWORD-Wert 1.

    :::image type="content" source="./media/tutorial-discover-physical/issudo-reg-key.png" alt-text="Screenshot: Aktivieren der sudo-Unterstützung.":::

- Wenn Sie die Konfigurations- und Leistungsmetadaten vom Zielserver ermitteln möchten, müssen Sie sudo-Zugriff für die [hier](migrate-appliance.md#linux-server-metadata) aufgeführten Befehle aktivieren. Stellen Sie sicher, dass Sie „NOPASSWD“ für das Konto aktiviert haben, um die erforderlichen Befehle auszuführen, ohne bei jedem Aufruf des sudo-Befehls ein Kennwort eingeben zu müssen.
- Die folgenden Linux-Betriebssystemdistributionen werden für die Ermittlung durch Azure Migrate mit einem Konto mit sudo-Zugriff unterstützt:

    Betriebssystem | Versionen 
    --- | ---
    Red Hat Enterprise Linux | 6, 7, 8
    Cent OS | 6.6, 8.2
    Ubuntu | 14.04, 16.04, 18.04
    SUSE Linux | 11.4, 12.4
    Debian | 7, 10
    Amazon Linux | 2.0.2021
    CoreOS-Container | 2345.3.0

- Wenn Sie kein root-Konto oder Benutzerkonto mit sudo-Zugriff bereitstellen können, können Sie den Registrierungsschlüssel „isSudo“ in der Registrierung „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance“ auf den Wert „0“ festlegen und mithilfe der folgenden Befehle ein Konto mit den erforderlichen Funktionen angeben, bei dem es sich nicht um das root-Konto handelt:

**Befehl** | **Zweck**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(wenn „/usr/sbin/fdisk“ nicht vorhanden ist)_ | Erfassen der Daten zur Datenträgerkonfiguration
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Erfassen der Daten zur Datenträgerleistung
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Erfassen der BIOS-Seriennummer
chmod a+r /sys/class/dmi/id/product_uuid | Erfassen der BIOS-GUID

## <a name="azure-migrate-appliance-requirements"></a>Anforderungen für die Azure Migrate-Appliance

In Azure Migrate erfolgt die Ermittlung und Bewertung über die [Azure Migrate-Appliance](migrate-appliance.md). Die Appliance für physische Server kann auf einem virtuellen Computer oder auf einem physischen Server ausgeführt werden.

- Informationen zu den Applianceanforderungen für physische Server finden Sie [hier](migrate-appliance.md#appliance---physical).
- Informationen zu den URLs, auf die die Appliance Zugriff benötigt, finden Sie unter [URLs für die öffentliche Cloud](migrate-appliance.md#public-cloud-urls) und [URLs für Azure Government-Clouds](migrate-appliance.md#government-cloud-urls).
- Die Einrichtung der Appliance erfolgt mithilfe eines [PowerShell-Skripts](how-to-set-up-appliance-physical.md), das Sie im Azure-Portal herunterladen.
In Azure Government stellen Sie die Appliance [mithilfe des Skripts](deploy-appliance-script-government.md) bereit.

## <a name="port-access"></a>Portzugriff

Die folgende Tabelle fasst die Portanforderungen für die Bewertung zusammen.

**Device** | **Connection**
--- | ---
**Appliance** | Eingehende Verbindungen an TCP-Port 3389, um Remotedesktopverbindungen mit der Appliance zu ermöglichen<br/><br/> Eingehende Verbindungen an Port 44368, um per Remotezugriff über die URL ``` https://<appliance-ip-or-name>:44368 ``` auf die Applianceverwaltungs-App zugreifen zu können<br/><br/> Ausgehende Verbindungen an Port 443 (HTTPS), um Ermittlungs- und Leistungsmetadaten an Azure Migrate zu senden
**Physische Server** | **Windows:** Eingehende Verbindungen am WinRM-Port 5985 (HTTP) zum Pullen von Konfigurations- und Leistungsmetadaten von Windows-Servern. <br/><br/> **Linux:**  Eingehende Verbindungen an Port 22 (TCP) zum Abrufen von Konfigurations- und Leistungsmetadaten von Linux-Servern. |

## <a name="agent-based-dependency-analysis-requirements"></a>Anforderungen der Agent-basierten Abhängigkeitsanalyse

Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen lokalen Servern identifizieren, die Sie bewerten und zu Azure migrieren möchten. In der Tabelle werden die Anforderungen zum Einrichten der Agent-basierten Abhängigkeitsanalyse zusammengefasst. Für physische Server wird derzeit nur die Agent-basierte Abhängigkeitsanalyse unterstützt.

**Anforderung** | **Details**
--- | ---
**Vor der Bereitstellung** | Sie sollten über ein Projekt verfügen, dem das Azure Migrate-Tool zur Ermittlung und Bewertung hinzugefügt wurde.<br/><br/>  Sie stellen die Abhängigkeitsvisualisierung nach dem Einrichten einer Azure Migrate-Appliance zum Ermitteln Ihrer lokalen Server bereit.<br/><br/> [Erfahren Sie](create-manage-projects.md), wie Sie erstmalig ein Projekt erstellen.<br/> [Erfahren Sie](how-to-assess.md), wie Sie einem vorhandenen Projekt ein Bewertungstool hinzufügen.<br/> Erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Bewertung von [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)- oder physischen Servern einrichten.
**Azure Government** | Abhängigkeitsvisualisierung ist in Azure Government nicht verfügbar.
**Log Analytics** | Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../azure-monitor/vm/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/logs/log-query-overview.md).<br/><br/> Sie ordnen einem Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zu. Der Arbeitsbereich für ein Projekt kann nach dem Hinzufügen nicht mehr geändert werden. <br/><br/> Der Arbeitsbereich muss sich in demselben Abonnement befinden wie das Projekt.<br/><br/> Der Arbeitsbereich muss sich in einer der Regionen „USA, Osten“, „Asien, Südosten“ oder „Europa, Westen“ befinden. Arbeitsbereiche in anderen Regionen können keinem Projekt zugeordnet werden.<br/><br/> Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> In Log Analytics wird der Arbeitsbereich, der Azure Migrate zugeordnet ist, mit dem Schlüssel des Migrationsprojekts und dem Projektnamen gekennzeichnet.
**Erforderliche Agents** | Installieren Sie auf jedem Server, den Sie analysieren möchten, die folgenden Agents:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Dependency-Agent](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Wenn lokale Server nicht mit dem Internet verbunden sind, müssen Sie das Log Analytics-Gateway auf diesen Servern herunterladen und installieren.<br/><br/> Erfahren Sie mehr über das Installieren von [Dependency-Agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) und [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-Arbeitsbereich** | Der Arbeitsbereich muss sich in demselben Abonnement befinden wie das Projekt.<br/><br/> Azure Migrate unterstützt die Erstellung von Arbeitsbereichen in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“.<br/><br/>  Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Der Arbeitsbereich für ein Projekt kann nach dem Hinzufügen nicht mehr geändert werden.
**Kosten** | Für die Dienstzuordnungslösung fallen in den ersten 180 Tagen (ab dem Tag der Zuordnung des Log Analytics-Arbeitsbereichs zum Projekt) keine Kosten an.<br/><br/> Nach 180 Tagen fallen die Log Analytics-Standardgebühren an.<br/><br/> Für andere Lösungen als die Dienstzuordnung im zugeordneten Log Analytics-Arbeitsbereich fallen die [Standardgebühren](https://azure.microsoft.com/pricing/details/log-analytics/) für Log Analytics an.<br/><br/> Beim Löschen des Projekts wird der Arbeitsbereich nicht zusammen mit dem Projekt gelöscht. Nach dem Löschen des Projekts ist die Nutzung der Dienstzuordnung nicht mehr kostenlos, und für jeden Knoten werden Kosten gemäß dem kostenpflichtigen Tarif für den Log Analytics-Arbeitsbereich berechnet.<br/><br/>Wenn Sie über Projekte verfügen, die Sie vor der allgemeinen Verfügbarkeit von Azure Migrate (28. Februar 2018) erstellt haben, fallen für Sie möglicherweise zusätzliche Gebühren für die Dienstzuordnung an. Um sicherzustellen, dass Sie wirklich erst nach 180 Tagen bezahlen müssen, wird empfohlen, ein neues Projekt zu erstellen, da vorhandene Arbeitsbereiche, die vor der allgemeinen Verfügbarkeit erstellt wurden, noch immer kostenpflichtig sind.
**Verwaltung** | Wenn Sie Agents im Arbeitsbereich registrieren, verwenden Sie die ID und den Schlüssel, die vom Projekt bereitgestellt werden.<br/><br/> Sie können den Log Analytics-Arbeitsbereich außerhalb von Azure Migrate verwalten.<br/><br/> Wenn Sie das zugehörige Projekt löschen, wird der Arbeitsbereich nicht automatisch gelöscht. [Löschen Sie ihn manuell](../azure-monitor/logs/manage-access.md).<br/><br/> Löschen Sie den von Azure Migrate erstellten Arbeitsbereich nur, wenn Sie auch das Projekt löschen. Wenn Sie dies tun, funktioniert die Abhängigkeitsvisualisierung nicht wie erwartet.
**Internetkonnektivität** | Wenn Server nicht mit dem Internet verbunden sind, müssen Sie das Log Analytics-Gateway auf diesen Servern installieren.
**Azure Government** | Die Agent-basierte Abhängigkeitsanalyse wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Vorbereitung auf die physische Ermittlung und Bewertung](./tutorial-discover-physical.md).