---
title: Best Practices für Sicherheit und Compliance in Batch
description: Informieren Sie sich über Best Practices und nützliche Tipps für die Verbesserung der Sicherheit Ihrer Azure Batch-Lösungen.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: f9bfbeb7a89e3ca1bc71001b173926302548a988
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435544"
---
# <a name="batch-security-and-compliance-best-practices"></a>Best Practices für Sicherheit und Compliance in Batch

Dieser Artikel stellt Anleitungen und Best Practices für die Verbesserung der Sicherheit bei der Verwendung von Azure Batch bereit.

Standardmäßig verfügen Azure Batch-Konten über einen öffentlichen Endpunkt und sind öffentlich zugänglich. Wenn ein Azure Batch-Pool erstellt wird, wird dieser in einem angegebenen Subnetz eines virtuellen Azure-Netzwerks bereitgestellt. Auf virtuelle Computer im Batch-Pool wird über öffentliche IP-Adressen zugegriffen, die von Batch erstellt werden. Computeknoten in einem Pool können bei Bedarf miteinander kommunizieren, beispielsweise bei der Ausführung von Aufgaben mit mehreren Instanzen. Die Knoten können aber nicht mit virtuellen Computern außerhalb des Pools kommunizieren.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagramm einer typischen Batch-Umgebung":::

Es steht eine ganze Reihe von Features zur Verfügung, die Sie beim Erstellen einer sichereren Azure Batch-Bereitstellung unterstützen. Sie können den Zugriff auf Knoten einschränken und die Auffindbarkeit der Knoten im Internet verringern, indem Sie den [Pool ohne öffentliche IP-Adressen bereitstellen](batch-pool-no-public-ip-address.md). Die Computeknoten können sicher mit anderen virtuellen Computern oder einem lokalen Netzwerk kommunizieren, indem Sie den [Pool in einem Subnetz eines virtuellen Azure-Netzwerks bereitstellen](batch-virtual-network.md). Darüber hinaus können Sie den [privaten Zugriff aus virtuellen Netzwerken](private-connectivity.md) über einen durch Azure Private Link gestützten Dienst aktivieren.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagramm einer sichereren Batch-Umgebung":::

## <a name="general-security-related-best-practices"></a>Allgemeine Best Practices für die Sicherheit

### <a name="pool-configuration"></a>Poolkonfiguration

Viele Sicherheitsfeatures sind nur für Pools verfügbar, die per [VM-Konfiguration](nodes-and-pools.md#configurations) konfiguriert wurden, nicht für Pools mit Clouddienstkonfiguration. Wir empfehlen die Verwendung von Pools mit VM-Konfiguration, die nach Möglichkeit [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) verwenden.

### <a name="batch-account-authentication"></a>Authentifizierung von Batch-Konten

Für den Zugriff auf Batch-Konten werden zwei Authentifizierungsmethoden unterstützt: Gemeinsam verwendeter Schlüssel und [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Es wird dringend empfohlen, Azure AD für die Authentifizierung von Batch-Konten zu verwenden. Für einige Batch-Funktionen ist diese Authentifizierungsmethode obligatorisch. Dies schließt auch viele der hier beschriebenen sicherheitsbezogenen Features ein. Der Dienst-API-Authentifizierungsmechanismus für ein Batch-Konto kann mithilfe der Eigenschaft [allowedAuthenticationModes](/rest/api/batchmanagement/batch-account/create) auf Azure AD beschränkt werden. Wenn diese Eigenschaft festgelegt ist, werden API-Aufrufe mit Shared Key-Authentifizierung abgelehnt.

### <a name="batch-account-pool-allocation-mode"></a>Poolzuordnungsmodus für Batch-Konten

Beim Erstellen eines Batch-Kontos können Sie zwischen zwei [Poolzuordnungsmodi](accounts.md#batch-accounts) wählen:

- **Batch-Dienst**: Dies ist die Standardoption. Die zugrunde liegenden Ressourcen für den Clouddienst oder die VM-Skalierungsgruppe, die zum Zuordnen und Verwalten von Poolknoten verwendet werden, werden in internen Abonnements erstellt und sind im Azure-Portal nicht direkt sichtbar. Sichtbar sind nur die Batch-Pools und -Knoten.
- **Benutzerabonnement**: Die zugrunde liegenden Ressourcen für den Clouddienst oder die VM-Skalierungsgruppe werden im selben Abonnement erstellt, in dem sich auch das Batch-Konto befindet. Daher sind diese Ressourcen im Abonnement zusätzlich zu den entsprechenden Batch-Ressourcen sichtbar.

Im Benutzerabonnementmodus werden virtuelle Batchcomputer und andere Ressourcen direkt in Ihrem Abonnement erstellt, wenn ein Pool erstellt wird. Der Benutzerabonnementmodus ist erforderlich, wenn Sie Batch-Pools mithilfe von Azure Reserved VM Instances erstellen, Azure Policy in VM-Skalierungsgruppenressourcen verwenden und/oder das Kernkontingent im Abonnement verwalten möchten (das Kontingent wird von allen Batch-Konten im Abonnement gemeinsam genutzt). Wenn Sie ein Batch-Konto im Benutzerabonnementmodus erstellen möchten, müssen Sie auch Ihr Abonnement in Azure Batch registrieren und das Konto einer Azure Key Vault-Instanz zuordnen.

## <a name="restrict-network-endpoint-access"></a>Einschränken des Zugriffs auf einen Netzwerkendpunkt

### <a name="batch-network-endpoints"></a>Batch-Netzwerkendpunkte

Beachten Sie, dass für die Kommunikation mit Batch-Konten, Batch-Pools und Poolknoten standardmäßig Endpunkte mit öffentlichen IP-Adressen verwendet werden.

### <a name="batch-account-api"></a>Batch-Konto-API

 Beim Erstellen eines Batch-Kontos wird ein öffentlicher Endpunkt erstellt, der zum Aufrufen der meisten Vorgänge für das Konto über eine [REST-API](/rest/api/batchservice/) verwendet wird. Der Kontoendpunkt weist eine Basis-URL mit dem Format `https://{account-name}.{region-id}.batch.azure.com` auf. Der Zugriff auf das Batch-Konto ist geschützt: Die Kommunikation mit dem Kontoendpunkt wird über HTTPS verschlüsselt, und jede Anforderung wird entweder mit einem gemeinsam verwendeten Schlüssel oder über Azure Active Directory (Azure AD) authentifiziert.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Zusätzlich zu den für ein Batch-Konto spezifischen Vorgängen werden [Verwaltungsvorgänge](/rest/api/batchmanagement/) für einzelne und mehrere Batch-Konten ausgeführt. Der Zugriff auf diese Verwaltungsvorgänge erfolgt über Azure Resource Manager.

Batch-Verwaltungsvorgänge über Azure Resource Manager werden über HTTPS verschlüsselt, und jede Anforderung wird über Azure AD authentifiziert.

### <a name="batch-pool-nodes"></a>Batch-Poolknoten

Der Batch-Dienst kommuniziert mit Batch-Knoten-Agents, die aus jedem Knoten im Pool ausgeführt werden. Der Dienst kann den Knoten-Agent z. B. anweisen, eine Aufgabe auszuführen, eine Aufgabe zu beenden oder die Dateien für eine Aufgabe abzurufen. Die Kommunikation mit dem Knoten-Agent wird über mindestens ein Lastenausgleichsmodul ermöglicht. Die Anzahl der Module richtet sich nach der Anzahl von Knoten in einem Pool. Das Lastenausgleichsmodul leitet die Kommunikation an den gewünschten Knoten weiter, und jeder Knoten wird über eine eindeutige Portnummer kontaktiert. Standardmäßig sind Lastenausgleichsmodulen öffentliche IP-Adressen zugeordnet. Sie können auch über RDP oder SSH remote auf Poolknoten zugreifen (dieser Zugriff ist standardmäßig aktiviert, die Kommunikation erfolgt über Lastenausgleichsmodule).

### <a name="restricting-access-to-batch-endpoints"></a>Einschränken des Zugriffs auf Batch-Endpunkte 

Es steht eine Reihe von Funktionen zur Verfügung, mit denen Sie den Zugriff auf die verschiedenen Batch-Endpunkte einschränken können. Dies ist insbesondere dann wichtig, wenn Ihre Lösung ein virtuelles Netzwerk nutzt. 

#### <a name="use-private-endpoints"></a>Verwenden privater Endpunkte

[Azure Private Link](../private-link/private-link-overview.md) ermöglicht den Zugriff auf Azure-PaaS-Dienste und auf in Azure gehostete kundeneigene Dienste/Partnerdienste über einen privaten Endpunkt in Ihrem virtuellen Netzwerk. Mit Private Link können Sie den Zugriff auf ein Batch-Konto aus dem virtuellen Netzwerk oder aus einem mittels Peering verbundenen Netzwerk einschränken. Auf Ressourcen, die Private Link zugeordnet sind, kann auch lokal über privates Peering über VPN oder Azure ExpressRoute zugegriffen werden.

Damit private Endpunkte verwendet werden können, muss ein Batch-Konto bei der Erstellung entsprechend konfiguriert werden: Die Konfiguration des Zugriffs aus öffentlichen Netzwerken muss deaktiviert werden. Nach der Erstellung können private Endpunkte erstellt und dem Batch-Konto zugeordnet werden. Weitere Informationen finden Sie unter [Verwenden privater Endpunkte mit Azure Batch-Konten](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Erstellen von Pools in virtuellen Netzwerken

Computeknoten in einem Batch-Pool können miteinander kommunizieren, z. B. zum Ausführen von Aufgaben mit mehreren Instanzen, ohne dass ein virtuelles Netzwerk (VNET) erforderlich ist. Allerdings können Knoten in einem Pool standardmäßig nicht mit virtuellen Computern kommunizieren, die sich außerhalb des Pools in einem virtuellen Netzwerk befinden und private IP-Adressen aufweisen. Beispiele hierfür sind Lizenzserver oder Dateiserver.

Um eine sichere Kommunikation zwischen Computeknoten und virtuellen Computern oder lokalen Netzwerken zu ermöglichen, können Sie den Pool in einem Subnetz eines Azure VNETs konfigurieren.

Wenn die Pools über öffentliche IP-Endpunkte verfügen, muss das Subnetz die eingehende Kommunikation vom Batch-Dienst zulassen, damit auf dem Computeknoten Aufgaben geplant und andere Vorgänge ausgeführt werden können. Die ausgehende Kommunikation muss ebenfalls zugelassen sein, damit entsprechend den Anforderungen Ihrer Workload mit Azure Storage und anderen Ressourcen kommuniziert werden kann. Für Pools in der VM-Konfiguration fügt Batch auf der Ebene der Netzwerkschnittstelle Netzwerksicherheitsgruppen (NSGs) hinzu, die an die Computeknoten angefügt sind. Diese Netzwerksicherheitsgruppen enthalten Regeln, um Folgendes zu ermöglichen:

- Eingehender TCP-Datenverkehr von IP-Adressen des Batch-Diensts
- Eingehender TCP-Datenverkehr für den Remotezugriff
- Ausgehender Datenverkehr an allen Ports zum virtuellen Netzwerk (kann gemäß NSG-Regeln auf Subnetzebene geändert werden)
- Ausgehender Datenverkehr an allen Ports zum Internet (kann gemäß NSG-Regeln auf Subnetzebene geändert werden)

Sie müssen keine NSGs auf der Subnetzebene des virtuellen Netzwerks angeben, da Batch eigene NSGs konfiguriert. Wenn eine Ihrer NSGs dem Subnetz zugeordnet ist, in dem Batch-Computeknoten bereitgestellt sind, oder wenn Sie benutzerdefinierte NSG-Regeln anwenden möchten, um die angewendeten Standardeinstellungen außer Kraft zu setzen, müssen Sie diese NSG mindestens mit Eingangs- und Ausgangssicherheitsregeln konfigurieren. Dies ist für die Kommunikation zwischen dem Batch-Dienst und den Poolknoten sowie den Poolknoten und Azure Storage erforderlich.

Weitere Informationen finden Sie unter [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Erstellen von Pools mit statischen öffentlichen IP-Adressen

Standardmäßig sind die öffentlichen IP-Adressen, die Pools zugeordnet sind, dynamisch. Sie werden während der Poolerstellung erstellt und können bei einer Änderung der Poolgröße hinzugefügt oder entfernt werden. Wenn die auf den Poolknoten ausgeführten Anwendungen für Aufgaben auf externe Dienste zugreifen müssen, muss dieser Zugriff möglicherweise auf bestimmte IP-Adressen beschränkt werden.  In diesem Fall ist die Verwendung von dynamischen IP-Adressen nicht praktikabel.

Sie können statische öffentliche IP-Adressen im selben Abonnement erstellen, in dem sich das Batch-Konto befindet, bevor Sie den Pool erstellen. Dann können Sie beim Erstellen des Pools diese Adressen angeben.

Weitere Informationen finden Sie unter [Erstellen eines Azure Batch-Pools mit angegebenen öffentlichen IP-Adressen](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Erstellen von Pools ohne öffentliche IP-Adressen

Standardmäßig wird allen Computeknoten in einem Azure Batch-Konfigurationspool für virtuelle Computer mindestens eine öffentliche IP-Adresse zugewiesen. Diese Endpunkte werden Batch-Dienst zur Planung von Aufgaben und zur Kommunikation mit Computeknoten verwendet, einschließlich des ausgehenden Zugriffs auf das Internet.

Um den Zugriff auf diese Knoten einzuschränken und die Auffindbarkeit dieser Knoten im Internet zu verringern, können Sie den Pool ohne öffentliche IP-Adressen bereitstellen.

Weitere Informationen finden Sie unter [Erstellen eines Pools ohne öffentliche IP-Adressen](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Beschränken des Remotezugriffs auf Poolknoten

Standardmäßig kann Batch es einem Knotenbenutzer mit Netzwerkkonnektivität ermöglichen, über RDP oder SSH eine externe Verbindung mit einem Computeknoten in einem Batch-Pool herzustellen.

Um den Remotezugriff auf Knoten zu beschränken, verwenden Sie eine der folgenden Methoden:

- Konfigurieren Sie die [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) so, dass der Zugriff verweigert wird. Dem Pool wird die entsprechende Netzwerksicherheitsgruppe zugeordnet.
- Erstellen Sie Ihren Pool [ohne öffentliche IP-Adressen](batch-pool-no-public-ip-address.md). Standardmäßig ist kein Zugriff auf diese Pools von außerhalb des virtuellen Netzwerks möglich.
- Ordnen Sie dem virtuellen Netzwerk eine Netzwerksicherheitsgruppe zu, um den Zugriff auf das RDP oder die SSH-Ports zu verweigern.
- Erstellen Sie keine Benutzer auf dem Knoten. Ohne Knotenbenutzer ist kein Remotezugriff möglich.

## <a name="encrypt-data"></a>Verschlüsseln von Daten

### <a name="encrypt-data-in-transit"></a>Verschlüsseln von Daten während der Übertragung

Die gesamte Kommunikation mit dem Batch-Kontoendpunkt (oder über Azure Resource Manager) muss HTTPS verwenden. Beim Herstellen einer Verbindung mit dem Batch-Dienst müssen Sie `https://` in den Batch-Konto-URLs verwenden, die in den APIs angegeben sind.

Clients, die mit dem Batch-Dienst kommunizieren, müssen für die Verwendung von TLS 1.2 (Transport Layer Security) konfiguriert sein.

### <a name="encrypt-batch-data-at-rest"></a>Verschlüsseln ruhender Batch-Daten

Einige der in Batch-APIs angegebenen Informationen – z. B. Kontozertifikate, Auftrags- und Aufgabenmetadaten und Aufgabenbefehlszeilen – werden beim Speichern durch den Batch-Dienst automatisch verschlüsselt. Diese Daten werden standardmäßig mit Schlüsseln verschlüsselt, die von der Azure Batch-Plattform verwaltet werden und für jedes Batch-Konto eindeutig sind.

Sie können diese Daten auch mithilfe von [kundenseitig verwalteten Schlüsseln](batch-customer-managed-key.md) verschlüsseln. Die Schlüssel werden in [Azure Key Vault](../key-vault/general/overview.md) generiert und gespeichert, und die Schlüsselbezeichner werden bei Ihrem Batch-Konto registriert.

### <a name="encrypt-compute-node-disks"></a>Verschlüsseln von Datenträgern auf Computeknoten

Batch-Computeknoten weisen standardmäßig zwei Datenträger auf: einen Betriebssystemdatenträger sowie das lokale temporäre SSD. Von Batch verwaltete [Dateien und Verzeichnisse](files-and-directories.md) befinden sich auf dem temporären SSD. Dies ist der Standardspeicherort für Dateien wie beispielsweise für die Aufgabenausgabe. Batch-Aufgabenanwendungen können den Standardspeicherort auf dem SSD oder den Betriebssystemdatenträger verwenden.

Um für zusätzliche Sicherheit zu sorgen, verschlüsseln Sie diese Datenträger mithilfe einer der folgenden Azure-Funktionen für die Datenträgerverschlüsselung:

- [Verschlüsselung von verwalteten Datenträgern im Ruhezustand mit plattformseitig verwalteten Schlüsseln](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Verschlüsselung auf dem Host mithilfe eines plattformseitig verwalteten Schlüssels](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure-Datenträgerverschlüsselung](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Sicherer Zugriff auf Dienste von Computeknoten

Batch-Knoten können sicher auf in [Azure Key Vault](../key-vault/general/overview.md) gespeicherte Anmeldeinformationen zugreifen, die von Task-Anwendungen für den Zugriff auf andere Dienste verwendet werden können. Den Poolknoten wird mithilfe eines Zertifikats Zugriff auf Key Vault gewährt. Durch [Aktivierung der automatischen Zertifikatsrotation in Ihrem Batch-Pool](automatic-certificate-rotation.md) werden die Berechtigungsnachweise automatisch erneuert. Dies ist die empfohlene Option für Batch-Knoten für den Zugriff auf in Azure Key Vault gespeicherte Anmeldeinformationen, obwohl Sie auch [Batch-Knoten für den sicheren Zugriff auf Anmeldeinformationen und Geheimnisse mit einem Zertifikat](credential-access-key-vault.md) ohne automatische Zertifikatsrotation einrichten können.

## <a name="governance-and-compliance"></a>Governance und Einhaltung

### <a name="compliance"></a>Konformität

Um Kunden bei der Erfüllung ihrer Complianceverpflichtungen in stark regulierten Branchen und Märkten auf der ganzen Welt zu unterstützen, pflegt Azure ein [umfangreiches Portfolio mit Complianceangeboten](https://azure.microsoft.com/overview/trusted-cloud/compliance).

Diese Angebote basieren auf unterschiedlichen Arten von Zusicherungen, z. B. formale Zertifizierungen, Nachweise, Validierungen, Autorisierungen und Bewertungen, die von unabhängigen externen Prüfunternehmen erstellt wurden, sowie Vertragsänderungen, Selbstbewertungen und Kundenleitfäden, die von Microsoft erstellt wurden. Sehen Sie sich [umfassende Übersicht über unsere Complianceangebote](https://aka.ms/AzureCompliance) an, um herauszufinden, welche Angebote für Ihre Batch-Lösungen relevant sein können.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) hilft bei der Durchsetzung von Organisationsstandards und bei der Bewertung der Compliance im großen Stil. Häufige Anwendungsfälle für Azure Policy sind die Implementierung von Governance für Ressourcenkonsistenz, Einhaltung gesetzlicher Bestimmungen, Sicherheit, Kosten und Verwaltung.

Je nachdem, welchen Poolzuordnungsmodus Sie nutzen und für welche Ressourcen eine Richtlinie gelten soll, können Sie Azure Policy auf eine der folgenden Arten mit Batch verwenden:

- Direkte Nutzung: Verwenden Sie die Ressource „Microsoft.Batch/batchAccounts“. Hierbei können Sie einen Teil der Eigenschaften für ein Batch-Konto verwenden. Ein Beispiel: Ihre Richtlinie kann gültige Batch-Kontoregionen und einen gültigen Poolzuordnungsmodus einschließen und festlegen, ob ein öffentliches Netzwerk für die Konten aktiviert ist.
- Indirekte Nutzung: Verwenden Sie die Ressource „Microsoft.Compute/virtualMachineScaleSets“. Bei Batch-Konten mit dem Poolzuordnungsmodus „Benutzerabonnement“ kann eine Richtlinie für die VM-Skalierungsgruppenressourcen festgelegt werden, die im Batch-Kontoabonnement erstellt werden. Sie können beispielsweise zulässige VM-Größen angeben und sicherstellen, dass bestimmte Erweiterungen auf jedem Poolknoten ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Azure-Sicherheitsbaseline für Batch](security-baseline.md).
- Erfahren Sie mehr über [Best Practices für Azure Batch](best-practices.md).
