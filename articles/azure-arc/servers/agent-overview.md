---
title: Übersicht über den Connected Machine-Agent
description: Dieser Artikel bietet eine ausführliche Übersicht über den Agent für Server mit Azure Arc-Unterstützung, der die Überwachung von VMs unterstützt, die in Hybridumgebungen gehostet werden.
ms.date: 11/03/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30c9a32cabf24e54dd41d45d40a8ff07920fd919
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546916"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Übersicht über den Agent für Server mit Azure Arc-Unterstützung

Mit dem Connected Machine-Agent für Server mit Azure Arc-Unterstützung können Sie Ihre Windows- und Linux-Computer verwalten, die außerhalb von Azure in Ihrem Unternehmensnetzwerk oder bei einem anderen Cloudanbieter gehostet werden. Dieser Artikel enthält eine ausführliche Übersicht über den Agent sowie Informationen zu System- und Netzwerkanforderungen und zu den verschiedenen Bereitstellungsmethoden.

>[!NOTE]
> Der [Azure Monitor-Agent](../../azure-monitor/agents/azure-monitor-agent-overview.md) (AMA) ersetzt nicht den Connected Machine-Agent. Der Azure Monitor-Agent ersetzt den Log Analytics-Agent, die Diagnoseerweiterung und den Telegraf-Agent sowohl für Windows- als auch für Linux-Computer. Weitere Informationen zum neuen Agent finden Sie in der Azure Monitor-Dokumentation.

## <a name="agent-component-details"></a>Agent-Komponentendetails

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Azure Arc-aktivierte Server - Agentenübersicht." border="false":::

Das Azure Connected Machine-Agent-Paket enthält mehrere logische Komponenten, die gebündelt werden.

* Hybrid Instance Metadata Service (HIMDS) verwaltet die Verbindung mit Azure und die Azure-Identität des verbundenen Computers.

* Der Gastkonfigurations-Agent stellt Funktionen wie die Überprüfung, ob der Computer den erforderlichen Richtlinien entspricht, sowie das Erzwingen der Compliance bereit.

    Beachten Sie das folgende Verhalten bei der Azure Policy-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md) für einen getrennten Computer:

  * Eine Azure Policy-Zuweisung, deren Ziel getrennte Computer sind, ist nicht betroffen.
  * Die Gastzuweisung wird 14 Tage lang lokal gespeichert. Wenn der Connected Machine-Agent innerhalb dieser 14 Tage erneut eine Verbindung mit dem Dienst herstellt, werden die Richtlinienzuweisungen neu angewendet.
  * Zuweisungen werden nach 14 Tagen gelöscht und nach Ablauf dieses Zeitraums für den betreffenden Computer nicht erneut durchgeführt.

* Der Erweiterungs-Agent verwaltet VM-Erweiterungen, einschließlich Installation, Deinstallation und Upgrade. Erweiterungen werden von Azure heruntergeladen und unter Windows in den Ordner `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` kopiert und unter Linux in den Ordner `/opt/GC_Ext/downloads`. Unter Windows wird die Erweiterung unter dem Pfad `%SystemDrive%\Packages\Plugins\<extension>` installiert, unter Linux unter `/var/lib/waagent/<extension>`.

## <a name="instance-metadata"></a>Instanzmetadaten

Metadateninformationen über den verbundenen Computer werden gesammelt, nachdem der Connected Machine-Agent bei Azure Arc-fähigen Servern registriert wurde. Dies gilt insbesondere in folgenden Fällen:

* Betriebssystemname, -typ und -version
* Computername
* Computerhersteller und -modell
* Vollqualifizierter Domänenname (FQDN) des Computers
* Domänenname (bei Beitritt zu einer Active Directory-Domäne)
* Version des Connected Machine-Agents
* Vollqualifizierter Domänenname (FQDN) für Active Directory und DNS
* UUID (BIOS-ID)
* Connected Machine-Agent-Takt
* Version des Connected Machine-Agents
* Öffentlicher Schlüssel für verwaltete Identität
* Richtlinienkonformitätsstatus und Details (bei Verwendung von Richtlinien für Gastkonfigurationen)
* SQL Server installiert (boolescher Wert)
* Clusterressourcen-ID (für Azure Stack HCI-Knoten)

Die folgenden Metadateninformationen werden vom Agent von Azure angefordert:

* Ressourcenstandort (Region)
* Virtual machine ID (ID des virtuellen Computers)
* `Tags`
* Azure Active Directory-Zertifikat für verwaltete Identitäten
* Richtlinienzuweisungen für Gastkonfigurationen
* Erweiterungsanforderungen: installieren, aktualisieren und löschen

## <a name="download-agents"></a>Herunterladen von Agents

Das Paket für den Azure Connected Machine-Agent für Windows und Linux steht an folgenden Orten zum Download bereit:

* [Windows Installer-Paket für den Windows-Agent](https://aka.ms/AzureConnectedMachineAgent) im Microsoft Download Center

* Das Paket für den Linux-Agent wird über das [Paketrepository](https://packages.microsoft.com/) von Microsoft im bevorzugten Paketformat für die Distribution (RPM oder DEB) verteilt.

Der Azure Connected Machine-Agent für Windows und Linux kann abhängig von Ihren Anforderungen manuell oder automatisch auf das neueste Release aktualisiert werden. Weitere Informationen finden Sie [hier](manage-agent.md).

## <a name="prerequisites"></a>Voraussetzungen

### <a name="supported-environments"></a>Unterstützte Umgebungen

Azure Arc-fähige Server unterstützen die Installation des Connected-Machine-Agenten auf jedem physischen Server und virtuellen Computer, der *außerhalb* von Azure gehostet wird. Dies schließt Unterstützung für virtuelle Computer ein, die auf Plattformen laufen wie:

* VMware
* Azure Stack HCI
* Andere Cloud-Umgebungen

Azure Arc-fähige Server unterstützen *nicht* die Installation des Agenten auf virtuellen Computern, die in Azure ausgeführt werden, oder auf virtuellen Computern, die in Azure Stack Hub oder Azure Stack Edge ausgeführt werden, da sie bereits als virtuelle Azure-Computer ausgewiesen sind.

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Für den Azure Connected Machine-Agent werden offiziell folgende Windows- und Linux-Versionen unterstützt:

* Windows Server 2008 R2 SP1, Windows Server 2012 R2, 2016, 2019 und 2022 (einschließlich Server Core)
* Ubuntu 16.04, 18.04 und 20.04 LTS (x64)
* CentOS Linux 7 und 8  (x64)
* SUSE Linux Enterprise Server (SLES) 12 und 15 (x64)
* Red Hat Enterprise Linux (RHEL) 7 und 8 (x64)
* Amazon Linux 2 (x64)
* Oracle Linux 7

> [!WARNING]
> Der Linux-Hostname oder Windows-Computername darf keines der reservierten Wörter bzw. keine Marken im Namen verwenden. Andernfalls führt der Versuch, den verbundenen Computer bei Azure zu registrieren, zu einem Fehler. Eine Liste der reservierten Wörter finden Sie unter [Beheben von Fehlern bei reservierten Ressourcennamen](../../azure-resource-manager/templates/error-reserved-resource-name.md).

> [!NOTE]
> Während Azure Arc-fähige Server Amazon Linux unterstützen, wird diese Verteilung von folgenden nicht unterstützt:
>
> * Der Abhängigkeit-Agent, der von Azure Monitor VM Insights verwendet wird
> * Azure Automation-Updateverwaltung

### <a name="software-requirements"></a>Softwareanforderungen

* .NET Framework 4.6 oder höher ist erforderlich. [Laden Sie .NET Framework herunter](/dotnet/framework/install/guide-for-developers).
* Windows PowerShell 5.1 ist erforderlich. [Laden Sie Windows Management Framework 5.1 herunter](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="required-permissions"></a>Erforderliche Berechtigungen

* Zum Onboarding von Computern müssen Sie Mitglied der Ressourcengruppe mit der Rolle **Onboarding von Azure Connected Machine** oder der Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) sein.

* Zum Lesen, Ändern oder Löschen eines Computers müssen Sie Mitglied der Ressourcengruppe mit der Rolle **Ressourcenadministrator für Azure Connected Machine** sein.

* Wenn Sie bei Verwendung der Methode **Skript generieren** eine Ressourcengruppe aus der Dropdownliste auswählen möchten, müssen Sie Mitglied der Ressourcengruppe mit mindestens der Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) sein.

### <a name="azure-subscription-and-service-limits"></a>Einschränkungen von Azure-Abonnements und -Diensten

Bevor Sie Ihre Computer für Server mit Azure Arc-Unterstützung konfigurieren, machen Sie sich mit den [Grenzwerten für Abonnements](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) von Azure Resource Manager sowie mit den [Grenzwerten für Ressourcengruppen](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) vertraut, um die Anzahl der zu verbindenden Computer zu planen.

Server mit Azure Arc-Unterstützung unterstützen bis zu 5.000 Computerinstanzen in einer Ressourcengruppe.

### <a name="register-azure-resource-providers"></a>Registrieren von Azure-Ressourcenanbietern

Azure Arc-fähige Server benötigen folgende Azure-Ressourcenanbieter in Ihrem Abonnement, um diesen Dienst nutzen zu können:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Sollten sie nicht registriert sein, können Sie sie mithilfe der folgenden Befehle registrieren:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Sie können die Ressourcenanbieter auch über das Azure-Portal registrieren, indem Sie die Schritte unter [Azure-Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) ausführen.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1.2-Protokoll (TLS)

Um die Sicherheit von Daten bei der Übertragung an Azure zu gewährleisten, wird dringend empfohlen, den Computer so zu konfigurieren, dass er TLS 1.2 (Transport Layer Security) verwendet. Bei älteren Versionen von TLS/Secure Sockets Layer (SSL) wurde ein Sicherheitsrisiko festgestellt. Sie funktionieren aus Gründen der Abwärtskompatibilität zwar noch, werden jedoch **nicht empfohlen**.

|Plattform/Sprache | Support | Weitere Informationen |
| --- | --- | --- |
|Linux | Linux-Distributionen greifen zur Unterstützung von TLS 1.2 tendenziell auf [OpenSSL](https://www.openssl.org) zurück. | Überprüfen Sie anhand des [OpenSSL-Änderungsprotokolls](https://www.openssl.org/news/changelog.html), ob Ihre Version von OpenSSL unterstützt wird.|
| Windows Server 2012 R2 und höhere Versionen | Wird unterstützt und ist standardmäßig aktiviert. | Zur Bestätigung, dass Sie weiterhin die [Standardeinstellungen](/windows-server/security/tls/tls-registry-settings) verwenden.|

## <a name="networking-configuration"></a>Netzwerkkonfiguration

Der Connected Machine-Agent für Linux und Windows kommuniziert ausgehend auf sichere Weise über den TCP-Port 443 mit Azure Arc. Wenn der Computer eine Verbindung über eine Firewall oder einen Proxyserver herstellen muss, um über das Internet zu kommunizieren, kommuniziert der Agent ausgehend und verwendet stattdessen das HTTP-Protokoll. Proxyserver machen den Connected Machine-Agent nicht sicherer, da der Datenverkehr bereits verschlüsselt ist.

Um Ihre Netzwerkkonnektivität mit Azure Arc zu schützen, können Sie anstelle von öffentlichen Netzwerken und Proxyservern einen [Azure Arc Private Link (Vorschau)](private-link-security.md) implementieren.

> [!NOTE]
> Azure Arc-fähige Server unterstützen nicht die Verwendung eines [Log Analytics-Gateways](../../azure-monitor/agents/gateway.md) als Proxy für den Connected Machine-Agent.
>

Sollte die ausgehende Konnektivität durch Ihre Firewall oder Ihren Proxyserver eingeschränkt sein, stellen Sie sicher, dass die unten aufgeführten URLs nicht blockiert werden. Wenn Sie nur die IP-Adressbereiche oder Domänennamen zulassen, die der Agent für die Kommunikation mit dem Dienst benötigt, müssen Sie auch den Zugriff auf die folgenden Diensttags und URLs zulassen.

Diensttags:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* Storage

URLs:

| Agent-Ressource | BESCHREIBUNG |
|---------|---------|
|`azgn*.servicebus.windows.net`|Azure Arc-Konnektivitätsplattform|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`pas.windows.net`|Azure Active Directory|
|`*.guestconfiguration.azure.com` |Erweiterungs- und Gastkonfigurationsdienste|
|`*.his.arc.azure.com`|Metadaten- und Hybrididentitätsdienste|
|`*.blob.core.windows.net`|Herunterladen der Quelle für die Erweiterungen für Azure Arc-fähige Server|
|`dc.services.visualstudio.com`|Agent-Telemetrie|

Eine Liste der IP-Adressen für die einzelnen Diensttags/Regionen finden Sie in der JSON-Datei unter [Azure-IP-Bereiche und -Diensttags – öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft veröffentlicht wöchentliche Updates zu den einzelnen Azure-Diensten und den dafür genutzten IP-Adressbereichen. Bei diesen Informationen in der JSON-Datei handelt es sich um die zum jetzigen Zeitpunkt gültige Liste der IP-Adressbereiche, die den einzelnen Diensttags entsprechen. Die IP-Adressen können sich ändern. Falls IP-Adressbereiche für Ihre Firewallkonfiguration erforderlich sind, sollte das Diensttag **AzureCloud** verwendet werden, um den Zugriff auf alle Azure-Dienste zuzulassen. Deaktivieren Sie weder die Sicherheitsüberwachung noch die Überprüfung dieser URLs. Lassen Sie sie wie anderen Internetdatenverkehr zu.

Weitere Informationen finden Sie in der [Übersicht über Diensttags](../../virtual-network/service-tags-overview.md).

## <a name="installation-and-configuration"></a>Installation und Konfiguration

Für Computer in Ihrer Hybridumgebung kann abhängig von Ihren Anforderungen auf verschiedene Weise eine Direktverbindung mit Azure hergestellt werden. Anhand der folgenden Tabelle können Sie ermitteln, welche der Methoden für Ihre Organisation am besten geeignet ist:

> [!IMPORTANT]
> Der Connected Machine-Agent kann nicht auf einem virtuellen Azure Windows-Computer installiert werden. Wenn Sie dies versuchen, erkennt die Installation diesen Versuch und führt ein Rollback aus.

| Methode | BESCHREIBUNG |
|--------|-------------|
| Interaktiv | Installieren Sie den Agent manuell auf einem einzelnen Computer oder auf einigen wenigen Computern. Eine entsprechende Anleitung finden Sie unter [Verbinden von Hybridcomputern mit Azure über das Azure-Portal](onboard-portal.md).<br> Im Azure-Portal können Sie ein Skript generieren und dieses auf dem Computer ausführen, um die Installations- und Konfigurationsschritte des Agents zu automatisieren.|
| Skalierbar | Installieren und konfigurieren Sie den Agent für mehrere Computer. Eine entsprechende Anleitung finden Sie unter [Schnellstart: Verbinden von Computern mit Azure mithilfe von Azure Arc für Server – PowerShell](onboard-service-principal.md).<br> Mit dieser Methode wird ein Dienstprinzipal für die nicht interaktive Verbindungsherstellung mit Computern erstellt.|
| Skalierbar | Installieren und konfigurieren Sie den Agent für mehrere Computer und befolgen Sie dabei die Methode [Verbinden von Hybridcomputern mit Azure über die Automation Updateverwaltung](onboard-update-management-machines.md).<br> Diese Methode erstellt einen Dienstprinzipal und installiert und konfiguriert den Agenten für mehrere Maschinen, die mit Azure Automation Updateverwaltung verwaltet werden, um Maschinen nicht-interaktiv zu verbinden. |
| Skalierbar | Installieren und konfigurieren Sie den Agent für mehrere Computer. Eine entsprechende Anleitung finden Sie unter [Installieren des Connected Machine-Agent mithilfe von Windows PowerShell DSC](onboard-dsc.md).<br> Mit dieser Methode wird ein Dienstprinzipal für die nicht interaktive Verbindungsherstellung mit Computern mithilfe von PowerShell DSC erstellt. |

## <a name="connected-machine-agent-technical-overview"></a>Technische Übersicht über den Connected Machine-Agent

### <a name="windows-agent-installation-details"></a>Installationsdetails zu Windows-Agents

Der Connected Machine-Agent für Windows kann mit einer der folgenden drei Methoden installiert werden:

* Doppelklicken Sie auf die Datei `AzureConnectedMachineAgent.msi`.
* Führen Sie das Windows Installer-Paket `AzureConnectedMachineAgent.msi` manuell über die Befehlsshell aus.
* Verwenden Sie eine Skriptmethode in einer PowerShell-Sitzung.

Nach der Installation des Connected Machine-Agents für Windows werden die folgenden systemweiten Konfigurationsänderungen angewendet.

* Die folgenden Installationsordner werden während des Setups erstellt.

    |Ordner |BESCHREIBUNG |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |azcmagent CLI und ausführbare Dateien des Instanzmetadatendiensts.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\GC | Ausführbare Dateien des Erweiterungsdiensts.|
    |%ProgramFiles%\AzureConnectedMachineAgent\GuestConfig\GC | Ausführbare Dateien des Gastkonfigurationsdiensts (Richtlinien).|
    |%ProgramData%\AzureConnectedMachineAgent |Konfigurations-, Protokoll- und Identitätstokendateien für die azcmagent CLI und den Instanzmetadatendienst.|
    |%ProgramData%\GuestConfig |Erweiterungspaketdownloads, Downloads von Gastkonfigurationsdefinitionen (Richtlinien) und Protokolle für die Erweiterungs- und Gastkonfigurationsdienste.|

* Die folgenden Windows-Dienste werden während der Installation des Agents auf dem Zielcomputer installiert.

    |Dienstname |`Display name` |Prozessname |BESCHREIBUNG |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Dieser Dienst implementiert den Hybrid Instance Metadata Service (HIMDS) für die Verwaltung der Verbindung mit Azure und der Azure-Identität des verbundenen Computers.|
    |GCArcService |Gastkonfiguration des Arc-Diensts |gc_service |Überwacht die Konfiguration des gewünschten Zustands des Computers|
    |ExtensionService |Gastkonfiguration des Erweiterungsdiensts | gc_service |Installiert die erforderlichen Erweiterungen für den Computer.|

* Die folgenden Umgebungsvariablen werden während der Installation des Agents erstellt.

    |Name |Standardwert |BESCHREIBUNG |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* Für die Problembehandlung stehen mehrere Protokolldateien zur Verfügung. Diese werden in der folgenden Tabelle beschrieben.

    |Log |BESCHREIBUNG |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Zeichnet Details der Heartbeat- und Identitäts-Agent-Komponente auf.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Enthält die Ausgabe der Befehle des azcmagent-Tools.|
    |%ProgramData%\GuestConfig\arc_policy_logs\ |Zeichnet Details zur Gastkonfigurations-Agent-Komponente (Richtlinien) auf.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Dieses Protokoll erfasst Informationen zur Erweiterungs-Agent-Komponente.|
    |%ProgramData%\GuestConfig\extension_logs\\\<Extension>|Dieses Protokoll erfasst Informationen aus der installierten Erweiterung.|

* Die lokale Sicherheitsgruppe **Hybrid agent extension applications** wird erstellt.

* Die folgenden Artefakte werden bei der Deinstallation des Agents nicht gelöscht.

  * %ProgramData%\AzureConnectedMachineAgent\Log
  * %ProgramData%\AzureConnectedMachineAgent und untergeordnete Verzeichnisse
  * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux-Agent-Installationsdetails

Der Linux-Agent für verbundene Computer wird im bevorzugten Paketformat für die Distribution (RPM oder DEB) bereitgestellt, die im [Paketrepository](https://packages.microsoft.com/) von Microsoft gehostet wird. Der Agent wird installiert und mit dem Shellskriptpaket [Install_linux_azcmagent.sh](https://aka.ms/azcmagent) konfiguriert.

Nach der Installation des Connected Machine-Agents für Linux werden die folgenden systemweiten Konfigurationsänderungen angewendet.

* Die folgenden Installationsordner werden während des Setups erstellt.

    |Ordner |BESCHREIBUNG |
    |-------|------------|
    |/opt/azcmagent/ |azcmagent CLI und ausführbare Dateien des Instanzmetadatendiensts.|
    |/opt/GC_Ext/ | Ausführbare Dateien des Erweiterungsdiensts.|
    |/opt/GC_Service/ |Ausführbare Dateien des Gastkonfigurationsdiensts (Richtlinien).|
    |/var/opt/azcmagent/ |Konfigurations-, Protokoll- und Identitätstokendateien für die azcmagent CLI und den Instanzmetadatendienst.|
    |/var/lib/GuestConfig/ |Erweiterungspaketdownloads, Downloads von Gastkonfigurationsdefinitionen (Richtlinien) und Protokolle für die Erweiterungs- und Gastkonfigurationsdienste.|

* Die folgenden Daemons werden während der Installation des Agents auf dem Zielcomputer installiert.

    |Dienstname |`Display name` |Prozessname |BESCHREIBUNG |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine-Agent-Dienst |himds |Dieser Dienst implementiert den Hybrid Instance Metadata Service (HIMDS) für die Verwaltung der Verbindung mit Azure und der Azure-Identität des verbundenen Computers.|
    |gcad.service |Gastkonfiguration des Arc-Diensts |gc_linux_service |Überwacht die Konfiguration des gewünschten Zustands des Computers |
    |extd.service |Erweiterungsdienst |gc_linux_service | Installiert die erforderlichen Erweiterungen für den Computer.|

* Für die Problembehandlung stehen mehrere Protokolldateien zur Verfügung. Diese werden in der folgenden Tabelle beschrieben.

    |Log |BESCHREIBUNG |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Zeichnet Details der Heartbeat- und Identitäts-Agent-Komponente auf.|
    |/var/opt/azcmagent/log/azcmagent.log |Enthält die Ausgabe der Befehle des azcmagent-Tools.|
    |/var/lib/GuestConfig/arc_policy_logs |Zeichnet Details zur Gastkonfigurations-Agent-Komponente (Richtlinien) auf.|
    |/var/lib/GuestConfig/ext_mgr_logs |Zeichnet Details zur Erweiterungs-Agent-Komponente auf.|
    |/var/lib/GuestConfig/extension_logs|Zeichnet Details von Installations-, Aktualisierungs- und Deinstallationsvorgängen der Erweiterung auf.|

* Die folgenden Umgebungsvariablen werden während der Installation des Agents erstellt. Die folgenden Variablen werden in `/lib/systemd/system.conf.d/azcmagent.conf` festgelegt.

    |Name |Standardwert |BESCHREIBUNG |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* Die folgenden Artefakte werden bei der Deinstallation des Agents nicht gelöscht.

  * /var/opt/azcmagent
  * /var/lib/GuestConfig

### <a name="agent-resource-governance"></a>Governance von Agent-Ressourcen

Der Connected Machine-Agent für Azure Arc-fähige Server ist für die Verwaltung des Agent- und Systemressourcenverbrauchs konzipiert. Der Agent übernimmt die Ressourcengovernance unter den folgenden Bedingungen:

* Der Gastkonfigurations-Agent ist auf die Verwendung von bis zu 5 % der CPU bei der Richtlinienauswertung beschränkt.
* Der Erweiterungsdienst-Agent ist auf die Verwendung von bis zu 5 % der CPU beim Installieren und Verwalten von Erweiterungen beschränkt.

  * Nach der Installation ist jede Erweiterung auf die Verwendung von bis zu 5 % der CPU während der Ausführung beschränkt. Wenn Sie beispielsweise zwei Erweiterungen installiert haben, können diese insgesamt 10 % der CPU nutzen.
  * Der Log Analytics-Agent und der Azure Monitor-Agent dürfen während ihrer Installations-/Upgrade-/Deinstallationsvorgänge unter Red Hat Linux, CentOS und anderen Linux-Unternehmensvarianten bis zu 60 % der CPU-Auslastung nutzen. Der Grenzwert für diese Kombination von Erweiterungen und Betriebssystemen ist höher, um die Auswirkungen von [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux) auf die Leistung dieser Systeme zu unterstützen.

## <a name="next-steps"></a>Nächste Schritte

* In dem Artikel [Verbinden von Hybridcomputern mit Azure Arc-fähigen Servern](learn/quick-enable-hybrid-vm.md) erfahren Sie, wie Sie die Evaluierung von Azure Arc-fähigen Servern beginnen.

* Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), bevor Sie den Agent für Azure Arc-fähige Server bereitstellen und in andere Verwaltungs- und Überwachungsdienste von Azure integrieren.

* Informationen zur Problembehandlung finden Sie im [Leitfaden zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).
