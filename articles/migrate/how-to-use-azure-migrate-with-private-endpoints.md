---
title: Verwenden von Azure Migrate mit privaten Endpunkten
description: Verwenden Sie Azure Migrate mit Private Link-Unterstützung für das Entdecken, Bewerten und Migrieren mit Azure Private Link.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: c581e89a8252ea8e084a48dc827d75aba90bc670
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500486"
---
# <a name="use-azure-migrate-with-private-endpoints"></a>Verwenden von Azure Migrate mit privaten Endpunkten

In diesem Artikel wird beschrieben, wie Sie Azure Migrate verwenden, um Server über ein privates Netzwerk mithilfe von [Azure Private Link](../private-link/private-endpoint-overview.md) zu entdecken, zu bewerten und zu migrieren.

Sie können die Tools [Azure Migrate: Ermittlung und Bewertung](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) und [Azure Migrate: Servermigration](./migrate-services-overview.md#azure-migrate-server-migration-tool) verwenden, um eine private und sichere Verbindung mit dem Azure Migrate-Dienst über ein privates Azure ExpressRoute-Peering oder eine S2S-VPN-Verbindung (Site-to-Site) mithilfe von Private Link herzustellen.

Es wird empfohlen, die Verbindung über private Endpunkte herzustellen, wenn die Organisationsanforderungen den Zugriff auf Azure Migrate und andere Azure-Ressourcen über öffentliche Netzwerke verbieten. Mit Private Link können Sie auch vorhandene ExpressRoute-Leitungen zum privaten Peering nutzen, um höhere Bandbreiten- oder Latenzanforderungen zu erfüllen.

## <a name="support-requirements"></a>Supportanforderungen

Überprüfen Sie die folgenden erforderlichen Berechtigungen sowie die unterstützten Szenarien und Tools.

### <a name="supported-geographies"></a>Unterstützte geografischer Regionen

Die Funktionalität befindet sich in unterstützten [öffentlichen](./migrate-support-matrix.md#supported-geographies-public-cloud) und [behördlichen Cloudgeografien](./migrate-support-matrix.md#supported-geographies-azure-government) jetzt in der Vorschau.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

Sie benötigen die Berechtigungen „Mitwirkender“ und „Benutzerzugriffsadministrator“ oder „Besitzer“ für das Abonnement.

### <a name="supported-scenarios-and-tools"></a>Unterstützte Szenarien und Tools

**Bereitstellung** | **Details** | **Extras**
--- | --- | ---
**Ermittlung und Bewertung** | Führen Sie eine bedarfsorientierte Ermittlung und Bewertung Ihrer Server ohne Agent im großen Stil durch, die auf beliebigen Plattformen ausgeführt werden. Beispiele hierfür sind Hypervisorplattformen wie [VMware vSphere](./tutorial-discover-vmware.md) oder [Microsoft Hyper-V](./tutorial-discover-hyper-v.md), öffentliche Clouds wie [AWS](./tutorial-discover-aws.md) oder [GCP](./tutorial-discover-gcp.md) und sogar [Bare-Metal-Server](./tutorial-discover-physical.md). | Azure Migrate: Ermittlung und Bewertung <br/>
**Softwareinventur** | Entdecken von Apps, Rollen und Features, die auf VMware-VMs ausgeführt werden | Azure Migrate: Ermittlung und Bewertung
**Visualisierung von Abhängigkeiten** | Funktionen zur Abhängigkeitsanalyse zum serverübergreifenden Ermitteln und Verstehen von Abhängigkeiten. <br/> Die [Visualisierung von Abhängigkeiten ohne Agent](./how-to-create-group-machine-dependencies-agentless.md) wird nativ durch die Private Link-Unterstützung von Azure Migrate unterstützt. <br/>Für die [Agent-basierte Visualisierung von Abhängigkeiten](./how-to-create-group-machine-dependencies.md) ist eine Internetverbindung erforderlich. Erfahren Sie, wie Sie [private Endpunkte für die Agent-basierte Visualisierung von Abhängigkeiten](../azure-monitor/logs/private-link-security.md) verwenden. | Azure Migrate: Ermittlung und Bewertung |
**Migration** | Ausführen von [Hyper-V-Migrationsvorgängen ohne Agent](./tutorial-migrate-hyper-v.md) oder Verwenden des Agent-basierten Ansatzes zum Migrieren von [VMware-VMs](./tutorial-migrate-vmware-agent.md), [Hyper-V-VMs](./tutorial-migrate-physical-virtual-machines.md), [physischen Servern](./tutorial-migrate-physical-virtual-machines.md), [VMs bei AWS](./tutorial-migrate-aws-virtual-machines.md), [VMs bei GCP](./tutorial-migrate-gcp-virtual-machines.md) oder bei einem anderen Virtualisierungsanbieter ausgeführten VMs | Azure Migrate: Servermigration

>[!Note]
> [VMware-Migrationsvorgänge ohne Agent](./tutorial-migrate-vmware.md) erfordern Internetzugriff oder Konnektivität über ExpressRoute-Microsoft-Peering. Erfahren Sie, wie Sie [mit privaten Endpunkten Replikationen über das private ExpressRoute-Peering oder eine S2S-VPN-Verbindung (Site-to-Site) durchführen](./replicate-using-expressroute.md).

#### <a name="other-integrated-tools"></a>Weitere integrierte Tools

Andere Migrationstools können möglicherweise keine Nutzungsdaten in das Azure Migrate-Projekt hochladen, wenn der Zugriff auf das öffentliche Netzwerk deaktiviert ist. Das Azure Migrate-Projekt sollte so konfiguriert werden, dass Datenverkehr aus allen Netzwerken zugelassen wird, sodass Daten von anderen Microsoft-Angeboten oder Angeboten von externen [unabhängigen Softwareanbietern](./migrate-services-overview.md#isv-integration) (Independent Software Vendor, ISV) empfangen werden können.

Melden Sie sich beim Azure-Portal an, wechseln Sie zur Seite **Eigenschaften von Azure Migrate**, und wählen Sie **Nein** > **Speichern** aus, um den Zugriff über das öffentliche Netzwerk für ein Azure Migrate-Projekt zu aktivieren.

![Screenshot der Änderung des Netzwerkzugriffsmodus.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Weitere Überlegungen

**Überlegungen** | **Details**
--- | ---
**Preise** | Preisinformationen finden Sie unter [Azure-Seitenblobs: Preise](https://azure.microsoft.com/pricing/details/storage/page-blobs/) und [Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).
**Anforderungen für virtuelle Netzwerke** | Der Endpunkt für ExpressRoute bzw. das VPN-Gateway sollte sich im ausgewählten virtuellen Netzwerk oder in einem mit diesem verbundenen virtuellen Netzwerk befinden. Sie benötigen ca. 15 IP-Adressen im virtuellen Netzwerk.

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Erstellen eines Projekts mit Konnektivität zu einem privaten Endpunkt

Informationen zum Einrichten eines neuen Azure Migrate-Projekts finden Sie unter [Erstellen und Verwalten von Projekten](./create-manage-projects.md#create-a-project-for-the-first-time).

> [!Note]
> Sie können die Konnektivitätsmethode für vorhandene Azure Migrate-Projekte nicht in Konnektivität über private Endpunkte ändern.

Geben Sie im Abschnitt **Erweiterte Konfiguration** die folgenden Details an, um einen privaten Endpunkt für Ihr Azure Migrate-Projekt zu erstellen.
1. Wählen Sie als **Verbindungsmethode** die Option **Privater Endpunkt** aus.
1. Behalten Sie unter **Disable public endpoint access** (Zugriff auf öffentlichen Endpunkt deaktivieren) die Standardeinstellung **Nein** bei. Einige Migrationstools können möglicherweise keine Nutzungsdaten in das Azure Migrate-Projekt hochladen, wenn der Zugriff auf das öffentliche Netzwerk deaktiviert ist. Weitere Informationen finden Sie unter [Weitere integrierte Tools](#other-integrated-tools).
1. Wählen Sie unter **VNET-Abonnement** das Abonnement für das virtuelle Netzwerk des privaten Endpunkts aus.
1. Wählen Sie unter **Virtuelles Netzwerk** das virtuelle Netzwerk für den privaten Endpunkt aus. Die Azure Migrate-Appliance und andere Softwarekomponenten, die eine Verbindung mit dem Azure Migrate-Projekt herstellen sollen, müssen sich in diesem oder in einem verbundenen virtuellen Netzwerk befinden.
1. Wählen Sie unter **Subnetz** das Subnetz für den privaten Endpunkt aus.

   ![Screenshot des Abschnitts „Erweitert“ auf der Seite „Projekt erstellen“.](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

1. Wählen Sie **Erstellen** aus, damit ein Migrationsprojekt erstellt und ein privater Endpunkt daran angefügt wird. Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde. Schließen Sie diese Seite nicht, während das Projekt erstellt wird.

## <a name="discover-and-assess-servers-for-migration-by-using-private-link"></a>Ermitteln und Bewerten von Servern für die Migration mithilfe von Private Link

In diesem Abschnitt wird beschrieben, wie Sie die Azure Migrate-Appliance einrichten. Anschließend verwenden Sie diese, um Server für die Migration zu ermitteln und zu bewerten.

### <a name="set-up-the-azure-migrate-appliance"></a>Einrichten der Azure Migrate-Appliance

1. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** den Servertyp aus.
1. Geben Sie unter **Azure Migrate-Projektschlüssel generieren** einen Namen für die Azure Migrate-Appliance an.
1. Wählen Sie **Schlüssel generieren** aus, um die erforderlichen Azure-Ressourcen zu erstellen.

    > [!Important]
    > Schließen Sie die Seite **Computer ermitteln** nicht, während die Ressourcen erstellt werden.
    - In diesem Schritt erstellt Azure Migrate einen Schlüsseltresor, ein Speicherkonto, einen Recovery Services-Tresor (nur bei VMware-Migrationsvorgängen ohne Agent) und einige interne Ressourcen. Azure Migrate fügt an jede Ressource einen privaten Endpunkt an. Die privaten Endpunkte werden in dem virtuellen Netzwerk erstellt, das während der Projekterstellung ausgewählt wurde.
    - Nachdem die privaten Endpunkte erstellt wurden, werden die DNS CNAME-Ressourceneinträge für die Azure Migrate-Ressourcen mit einem Alias in einer Unterdomäne mit dem Präfix *privatelink* aktualisiert. Standardmäßig erstellt Azure Migrate auch für jede Unterdomäne *privatelink* der einzelnen Ressourcentypen eine private DNS-Zone, und fügt DNS-A-Datensätze für die zugeordneten privaten Endpunkte ein. Durch diese Aktion können die Azure Migrate-Appliance und andere Softwarekomponenten im Quellnetzwerk Endpunkte von Azure Migrate-Ressourcen über deren private IP-Adressen erreichen.
    - Azure Migrate aktiviert auch eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) für das Migrationsprojekt und gewährt der verwalteten Identität Berechtigungen für den sicheren Zugriff auf das Speicherkonto.

1. Nachdem der Schlüssel erfolgreich generiert wurde, kopieren Sie die Schlüsseldetails, um die Appliance zu konfigurieren und zu registrieren.

#### <a name="download-the-appliance-installer-file"></a>Herunterladen der Installationsdatei für die Appliance

Für das Azure Migrate-Tool zur Ermittlung und Bewertung wird eine einfache Azure Migrate-Appliance verwendet. Die Appliance führt die Serverermittlung durch und sendet Metadaten zur Serverkonfiguration und -leistung an Azure Migrate.

> [!Note]
> Wenn Sie eine Appliance mithilfe einer Vorlage (OVA für Server in einer VMware-Umgebung und VHD für eine Hyper-V-Umgebung) bereitgestellt haben, können Sie dieselbe Appliance verwenden und sie bei einem Azure Migrate-Projekt mit Verbindungen über private Endpunkte registrieren.

Richten Sie die Appliance wie folgt ein:
  1. Laden Sie die ZIP-Datei mit dem Installationsskript aus dem Portal herunter.
  1. Kopieren Sie die gezippte Datei auf den Server, der die Appliance hostet.
  1. Überprüfen Sie nach dem Herunterladen der ZIP-Datei die Dateisicherheit.
  1. Führen Sie das Installationsskript aus, um die Appliance bereitzustellen.

#### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Server, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Überprüfen Sie die aktuelle Applianceversion und die Hashwerte:

    **Download** | **Hashwert**
    --- | ---
    [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2160648) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> Das gleiche Skript kann verwendet werden, um eine Appliance mit privater Endpunktkonnektivität für jedes der ausgewählten Szenarien (z. B. VMware, Hyper-V, physisch usw.), um eine Appliance mit der gewünschten Konfiguration bereitzustellen.

Stellen Sie sicher, dass der Server die [Hardwareanforderungen](./migrate-appliance.md) für das ausgewählte Szenario (z. B. VMware, Hyper-V, physisch usw.) erfüllt und eine Verbindung mit den [erforderlichen URLs](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) herstellen kann.

#### <a name="run-the-azure-migrate-installer-script"></a>Ausführen des Azure Migrate-Installationsskripts

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Server mit einer vorhandenen Azure Migrate-Appliance aus.

2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).

3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.

4. Führen Sie das Skript `AzureMigrateInstaller.ps1` mit folgendem Befehl aus:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Treffen Sie eine Auswahl aus den Szenario-, Cloud- und Konnektivitätsoptionen, um eine Appliance mit der gewünschten Konfiguration bereitzustellen. Die unten gezeigte Auswahl richtet beispielsweise eine Appliance für die Ermittlung und Bewertung von **Servern in Ihrer VMware-Umgebung** für ein Azure Migrate-Projekt mit **Konnektivität über einen privaten Endpunkt** in der **öffentlichen Azure-Cloud** ein.

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-inline.png" alt-text="Screenshot: Einrichten einer Appliance mit der gewünschten Konfiguration für einen privaten Endpunkt." lightbox="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-expanded.png":::

Nach der erfolgreichen Ausführung des Skripts wird der Appliancekonfigurations-Manager automatisch gestartet.

> [!NOTE]
> Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Konfigurieren der Appliance und Starten der kontinuierlichen Ermittlung

Öffnen Sie einen Browser auf einem beliebigen Computer, der eine Verbindung mit dem Applianceserver herstellen kann. Öffnen Sie die URL des Appliance-Konfigurations-Managers `https://appliance name or IP address: 44368`. Sie können den Konfigurationsmanager auch auf dem Desktop des Servers auf der Appliance öffnen, indem Sie die Verknüpfung für den Konfigurations-Manager auswählen.

#### <a name="set-up-prerequisites"></a>Einrichten erforderlicher Komponenten

1. Lesen Sie die Drittanbieterinformationen, und akzeptieren Sie die **Lizenzbedingungen**.

1. Führen im Konfigurations-Manager unter **Erforderliche Komponenten einrichten** die folgenden Schritte aus:
   - **Konnektivität:** Die Appliance überprüft den Zugriff auf die erforderlichen URLs. Wenn der Server einen Proxy verwendet:
     - Wählen Sie **Proxy einrichten** aus, um die Proxyadresse `http://ProxyIPAddress` oder `http://ProxyFQDN` und den lauschenden Port anzugeben.
     - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert. Es werden nur HTTP-Proxys unterstützt.
     - Sie können eine Liste von URLs oder IP-Adressen hinzufügen, die den Proxyserver umgehen sollen.
        ![Hinzufügen zur Proxyumgehungsliste](./media/how-to-use-azure-migrate-with-private-endpoints/bypass-proxy-list.png)
     - Wenn Sie Proxyserverdetails geändert oder URLs oder IP-Adressen für die Proxyumgehung hinzugefügt haben, wählen Sie **Speichern** aus, damit die Konfiguration registriert wird.

        > [!Note]
        > Wenn Sie während der Konnektivitätsprüfung einen Fehler mit dem Link „aka.ms/*“ erhalten und nicht möchten, dass die Appliance über das Internet auf diese URL zugreift, deaktivieren Sie den Dienst für automatische Updates auf der Appliance. Führen Sie die Schritte unter [Deaktivieren der automatischen Aktualisierung](./migrate-appliance.md#turn-off-auto-update) aus. Nachdem Sie die automatische Aktualisierung deaktiviert haben, wird die URL-Konnektivitätsprüfung über „aka.ms/*“ übersprungen.

   - **Uhrzeitsynchronisierung**: Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
   - **Updates installieren**: Die Appliance stellt sicher, dass die neuesten Updates installiert sind. Nachdem die Überprüfung abgeschlossen ist, wählen Sie **Appliancedienste anzeigen** aus, um den Status und die Versionen der auf dem Applianceserver ausgeführten Dienste anzuzeigen.
        > [!Note]
        > Wenn Sie die automatische Aktualisierung auf der Appliance deaktiviert haben, können Sie die Appliancedienste manuell aktualisieren, um die neuesten Versionen der Dienste abzurufen. Führen Sie die Schritte unter [Manuelle Aktualisierung einer älteren Version](./migrate-appliance.md#manually-update-an-older-version) aus.
   - **Install VDDK** (VDDK installieren) _(nur bei VMware-Appliances erforderlich):_ Die Appliance überprüft, ob das VMware vSphere-VDDK (Virtual Disk Development Kit) installiert ist. Wenn es nicht installiert ist, laden Sie VDDK 6.7 von VMware herunter. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei am angegebenen Ort auf der Appliance, wie in den Installationsanweisungen beschrieben.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Registrieren der Appliance und Starten der kontinuierlichen Ermittlung

Führen Sie nach der Überprüfung der Voraussetzungen die folgenden Schritte aus, um die Appliance zu registrieren und die kontinuierliche Ermittlung für die jeweiligen Szenarien zu starten:
- [Virtuelle VMware-Computer](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Virtuelle Hyper-V-Computer](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [Physische Server](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [AWS-VMs](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [GCP-VMs](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)

>[!Note]
> Wenn bei der Registrierung der Appliance oder beim Starten der Ermittlung Probleme mit der DNS-Auflösung auftreten, stellen Sie sicher, dass die im Schritt zum **Generieren der Schlüssel** im Portal erstellten Azure Migrate Ressourcen über den lokalen Server erreichbar sind, auf dem die Azure Migrate-Appliance gehostet wird. Informieren Sie sich, wie Sie die [Netzwerkkonnektivität](./troubleshoot-network-connectivity.md) überprüfen.

### <a name="assess-your-servers-for-migration-to-azure"></a>Bewerten von Servern für die Migration zu Azure
Bewerten Sie im Anschluss an die Ermittlung Ihre Server (z. B. [VMware-VMs](./tutorial-assess-vmware-azure-vm.md), [Hyper-V-VMs](./tutorial-assess-hyper-v.md), [physische Server](./tutorial-assess-vmware-azure-vm.md), [AWS-VMs](./tutorial-assess-aws.md) und [GCP-VMs](./tutorial-assess-gcp.md)) für eine Migration zu Azure-VMs oder zu Azure VMware Solution (AVS). Dazu verwenden Sie das Tool Azure Migrate: Ermittlung und Bewertung.

Sie können mit dem Tool Azure Migrate: Ermittlung und Bewertung auch Ihre [lokalen Computer bewerten](./tutorial-discover-import.md#prepare-the-csv), indem Sie eine importierte CSV-Datei verwenden.

## <a name="migrate-servers-to-azure-by-using-private-link"></a>Migrieren von Servern zu Azure mithilfe von Private Link

In den folgenden Abschnitten werden die Schritte beschrieben, die zum Verwenden von Azure Migrate mit [privaten Endpunkten](../private-link/private-endpoint-overview.md) für Migrationsvorgänge mit dem private ExpressRoute-Peering oder über VPN-Verbindungen erforderlich sind.

Dieser Artikel zeigt einen Proof of Concept-Bereitstellungspfad für Agent-basierte Replikationen zum Migrieren Ihrer [VMware-VMs](./tutorial-migrate-vmware-agent.md), [Hyper-V-VMs](./tutorial-migrate-physical-virtual-machines.md), [physischen Server](./tutorial-migrate-physical-virtual-machines.md), [VMs bei AWS](./tutorial-migrate-aws-virtual-machines.md), [VMs bei GCP](./tutorial-migrate-gcp-virtual-machines.md) oder VMs, die bei einem anderen Virtualisierungsanbieter ausgeführt werden, mithilfe privater Azure-Endpunkte. Einen ähnlichen Ansatz können Sie auch für [Hyper-V-Migrationsvorgänge ohne Agent](./tutorial-migrate-hyper-v.md) mit Private Link verwenden.

>[!Note]
>[VMware-Migrationsvorgänge ohne Agent](./tutorial-assess-physical.md) erfordern Internetzugriff oder Konnektivität über ExpressRoute-Microsoft-Peering.

### <a name="set-up-a-replication-appliance-for-migration"></a>Einrichten einer Replikationsappliance für die Migration

Das folgende Diagramm veranschaulicht den Workflow bei der Agent-basierten Replikation mit privaten Endpunkten mithilfe des Tools Azure Migrate: Servermigration.

![Diagramm der Replikationsarchitektur.](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Das Tool verwendet eine Replikationsappliance, um Ihre Server in Azure zu replizieren. Informieren Sie sich, wie Sie [einen Computer für die Replikationsappliance vorbereiten und einrichten](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance).

Nachdem Sie die Replikationsappliance eingerichtet haben, führen Sie die folgenden Schritte aus, um die erforderlichen Ressourcen für die Migration zu erstellen.

1. Klicken Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** auf **Nicht virtualisiert/Andere**.
1. Wählen Sie unter **Zielregion** die Azure-Region aus, zu der Sie die Computer migrieren möchten, und bestätigen Sie Ihre Auswahl.
1. Wählen Sie **Ressourcen generieren** aus, um die erforderlichen Azure-Ressourcen zu erstellen. Schließen Sie die Seite nicht, während die Ressourcen erstellt werden.
    - In diesem Schritt werden ein Recovery Services-Tresor im Hintergrund erstellt und eine verwaltete Identität für den Tresor aktiviert. Ein Recovery Services-Tresor ist eine Entität, die die Replikationsinformationen von Servern enthält und zum Initiieren von Site Recovery-Vorgängen verwendet wird.
    - Wenn das Azure Migrate-Projekt über Konnektivität mit privaten Endpunkten verfügt, wird ein privater Endpunkt für den Recovery Services-Tresor erstellt. In diesem Schritt werden dem privaten Endpunkt fünf vollqualifizierte Domänennamen (FQDNs), einer für jeden Microservice, hinzugefügt, die mit dem Recovery Services-Tresor verknüpft sind.
    - Die fünf Domänennamen werden nach folgendem Muster formatiert: <br/> _{Tresor-ID}-asr-pod01-{Typ}-.{Zielregionscode}_ .privatelink.siterecovery.windowsazure.com
    - Standardmäßig erstellt Azure Migrate automatisch eine private DNS-Zone und fügt DNS-A-Datensätze für die Microservices des Recovery Services-Tresors hinzu. Das private DNS wird dann mit dem virtuellen Netzwerk des privaten Endpunkts verknüpft.

1. Stellen Sie vor dem Registrieren der Replikationsappliance sicher, dass die FQDNs für private Links des Tresors über den Computer erreichbar sind, auf dem die Replikationsappliance gehostet wird. Möglicherweise ist eine zusätzliche DNS-Konfiguration erforderlich, damit die lokale Replikationsappliance die FQDNs für private Links in ihre privaten IP-Adressen auflösen kann. Informieren Sie sich, wie Sie die [Netzwerkkonnektivität](./troubleshoot-network-connectivity.md#verify-dns-resolution) überprüfen.

1. Nachdem Sie die Konnektivität überprüft haben, laden Sie die Setup- und Schlüsseldatei der Appliance herunter, führen Sie den Installationsvorgang aus, und registrieren Sie die Appliance bei Azure Migrate. Informieren Sie sich über das [Einrichten der Replikationsappliance](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). Nachdem Sie die Replikationsappliance eingerichtet haben, befolgen Sie [diese Anweisungen](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service), um den Mobilitätsdienst auf den Computern zu installieren, die Sie migrieren möchten.

### <a name="replicate-servers-to-azure-by-using-private-link"></a>Replizieren von Servern zu Azure mithilfe von Private Link

Führen Sie [diese Schritte](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) aus, um Server für die Replikation auszuwählen.

Verwenden Sie die Dropdownliste unter **Replizieren** > **Zieleinstellungen** > **Cache/Replication storage account** (Cache-/Replikationsspeicherkonto), um ein Speicherkonto auszuwählen, das über eine private Verbindung repliziert werden soll.

Wenn Ihr Azure Migrate-Projekt über private Endpunkte verbunden ist, müssen Sie [der verwalteten Identität des Recovery Services-Tresors Berechtigungen gewähren](#grant-access-permissions-to-the-recovery-services-vault), damit sie auf das für Azure Migrate erforderliche Speicherkonto zugreifen kann.

[Erstellen Sie einen privaten Endpunkt für das Speicherkonto](#create-a-private-endpoint-for-the-storage-account-optional), um Replikationen über eine private Verbindung zu ermöglichen.

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Gewähren von Zugriffsberechtigungen für den Recovery Services-Tresor

Sie müssen dem Recovery Services-Tresor die Berechtigungen für den authentifizierten Zugriff auf das Cache-/Replikationsspeicherkonto gewähren.

Ermitteln Sie anhand der folgenden Schritte den Recovery Services-Tresor, der von Azure Migrate erstellt wurde, und gewähren Sie die erforderlichen Berechtigungen.

**Ermitteln des Recovery Services-Tresors und der Objekt-ID der verwalteten Identität**

Die Details zum Recovery Services-Tresor finden Sie auf der **Eigenschaftenseite** des Tools Azure Migrate: Servermigration.

1. Navigieren Sie zum **Azure Migrate**-Hub, und wählen Sie auf der Kachel **Azure Migrate: Servermigration** die Option **Übersicht** aus.

    ![Screenshot der Seite „Übersicht“ im Azure Migrate-Hub](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

1. Wählen Sie im linken Bereich **Eigenschaften** aus. Notieren Sie sich den Namen des Recovery Services-Tresors und die ID der verwalteten Identität. Für den Tresor sind **Privater Endpunkt** als **Konnektivitätstyp** und **Andere** als **Replikationstyp** festgelegt. Sie benötigen diese Informationen, wenn Sie Zugriff auf den Tresor gewähren.

    ![Screenshot der Eigenschaftenseite von Azure Migrate: Servermigration.](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**Berechtigungen für den Zugriff auf das Speicherkonto**

 Der verwalteten Identität des Tresors müssen die folgenden Rollenberechtigungen für das Speicherkonto gewährt werden, das für die Replikation erforderlich ist. In diesem Fall müssen Sie das Speicherkonto im Voraus erstellen.

>[!Note]
> Wenn Sie Hyper-V-VMs mithilfe von Private Link zu Azure migrieren, müssen Sie Zugriff auf das Replikationsspeicherkonto und das Cachespeicherkonto gewähren.

Die Rollenberechtigungen für Azure Resource Manager variieren je nach Typ des Speicherkontos.

|**Speicherkontotyp** | **Rollenberechtigungen**|
|--- | ---|
|Standardtyp | [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)<br>[Mitwirkender an Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|Premium-Typ | [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor)<br>[Besitzer von Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Wechseln Sie zu dem Replikations-/Cachespeicherkonto, das für die Replikation ausgewählt wurde. Klicken Sie im linken Bereich auf **Zugriffssteuerung (IAM)** .

1. Wählen Sie **+ Hinzufügen** und anschließend **Rollenzuweisung hinzufügen** aus.

   ![Screenshot von „Rollenzuweisungen hinzufügen“.](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)

1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** im Feld **Rolle** die passende Rolle aus der oben genannten Berechtigungsliste aus. Geben Sie den zuvor notierten Tresornamen ein, und wählen Sie **Speichern** aus.

    ![Screenshot der Seite „Rollenzuweisung hinzufügen“](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

1. Zusätzlich zu diesen Berechtigungen müssen Sie auch den Zugriff auf vertrauenswürdige Microsoft-Dienste zulassen. Wenn Ihr Netzwerkzugriff auf ausgewählte Netzwerke beschränkt ist, wählen Sie auf der Registerkarte **Netzwerk** im Abschnitt **Ausnahmen** die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aus.

   ![Screenshot der Option „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“.](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)

### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Erstellen eines privaten Endpunkts für das Speicherkonto (optional)

[Erstellen Sie einen privaten Endpunkt](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) für die Cache-/Replikationsspeicherkonten, um die Replikation mithilfe von ExpressRoute mit privatem Peering zu ermöglichen (Zielunterressource: _Blob_).

>[!Note]
> Sie können private Endpunkte nur für ein Speicherkonto des Typs „Universell v2“ erstellen. Preisinformationen finden Sie unter [Azure-Seitenblobs: Preise](https://azure.microsoft.com/pricing/details/storage/page-blobs/) und [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).

Erstellen Sie den privaten Endpunkt für das Speicherkonto im selben virtuellen Netzwerk wie der private Endpunkt des Azure Migrate-Projekts oder in einem anderen, mit diesem Netzwerk verbundenen virtuellen Netzwerk.

Wählen Sie **Ja** aus, und führen Sie die Integration mit einer privaten DNS-Zone aus. Die private DNS-Zone hilft beim Weiterleiten der Verbindungen aus dem virtuellen Netzwerk an das Speicherkonto über eine private Verbindung. Wenn Sie **Ja** auswählen, wird die DNS-Zone automatisch mit dem virtuellen Netzwerk verknüpft. Außerdem werden die DNS-Einträge für die Auflösung neuer IP-Adressen und FQDNs hinzugefügt, die erstellt werden. Informieren Sie sich weiter über [private DNS-Zonen](../dns/private-dns-overview.md).

Wenn der Benutzer, der den privaten Endpunkt erstellt hat, auch der Besitzer des Speicherkontos ist, wird das Erstellen des privaten Endpunkts automatisch genehmigt. Andernfalls muss der Besitzer des Speicherkontos die Verwendung des privaten Endpunkts genehmigen. Navigieren Sie auf der Seite des Speicherkontos unter **Netzwerk** zu **Verbindungen mit privatem Endpunkt**, um eine angeforderte Verbindung mit einem privaten Endpunkt zu genehmigen oder abzulehnen.

Überprüfen Sie vor dem Fortfahren den Verbindungsstatus des privaten Endpunkts.

![Screenshot des Genehmigungsstatus für den privaten Endpunkt.](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Nachdem Sie den privaten Endpunkt erstellt haben, wählen Sie über die Dropdownliste **Replizieren** > **Zieleinstellungen** > **Cachespeicherkonto** das Speicherkonto für die Replikation über eine private Verbindung aus.

Stellen Sie sicher, dass die lokale Replikationsappliance über Netzwerkkonnektivität mit dem Speicherkonto an dessen privaten Endpunkt verfügt. Informieren Sie sich, wie Sie die [Netzwerkkonnektivität](./troubleshoot-network-connectivity.md) überprüfen.

>[!Note]
> Wenn bei Hyper-V-VM-Migrationsvorgängen zu Azure das Replikationsspeicherkonto den Typ _Premium_ aufweist, müssen Sie ein anderes Speicherkonto vom Typ _Standard_ für das Cachespeicherkonto auswählen. In diesem Fall müssen Sie private Endpunkte sowohl für das Replikations- als auch für das Cachespeicherkonto erstellen.

Befolgen Sie als Nächstes die Anweisungen zum [Überprüfen und Starten der Replikation](./tutorial-migrate-physical-virtual-machines.md#replicate-machines) und zum [Durchführen der Migration](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration).

## <a name="next-steps"></a>Nächste Schritte

- Schließen Sie den [Migrationsprozess](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration) ab.
- Machen Sie sich mit den [bewährten Methoden nach der Migration](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices) vertraut.