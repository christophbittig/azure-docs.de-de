---
title: Konfigurieren von GitHub Enterprise Server in Azure VMware Solution
description: Erfahren Sie, wie Sie GitHub Enterprise Server in Ihrer privaten Azure VMware Solution-Cloud einrichten.
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 7b3289742c765e6b809cd96db3bfa51d8407a282
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351303"
---
# <a name="configure-github-enterprise-server-on-azure-vmware-solution"></a>Konfigurieren von GitHub Enterprise Server in Azure VMware Solution

In diesem Artikel wird erläutert, wie Sie GitHub Enterprise Server, die „lokale“ Version von [GitHub.com](https://github.com/), in Ihrer privaten Azure VMware Solution-Cloud einrichten. Das Szenario gilt für eine GitHub Enterprise Server-Instanz mit Unterstützung von bis zu 3.000 Entwicklern, die bis zu 25 Aufträge pro Minute in GitHub Actions ausführen. Dazu gehört die Einrichtung von Features wie GitHub Actions (bei der Erstellung dieses Dokuments lediglich als *Vorschau* verfügbar). Informationen zum Anpassen der Einrichtung an Ihre speziellen Anforderungen finden Sie in den Anforderungen zum [Installieren von GitHub Enterprise Server unter VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Vorbereitung

Für GitHub Enterprise Server ist ein gültiger Lizenzschlüssel erforderlich. Sie können sich für eine [Testlizenz](https://enterprise.github.com/trial) registrieren. Wenn Sie die Funktionen von GitHub Enterprise Server über eine Integration erweitern möchten, können Sie eine kostenlose Entwicklerlizenz für fünf Arbeitsplätze beantragen. Sie können sich im Rahmen des [GitHub-Partnerprogramms](https://partner.github.com/) für diese Lizenz bewerben.

## <a name="install-github-enterprise-server-on-vmware"></a>Installieren von GitHub Enterprise Server unter VMware

1. Laden Sie [die aktuelle Version von GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) für VMware ESXi/vSphere (OVA) herunter, und [stellen Sie die heruntergeladene OVA-Vorlage bereit](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html).

   :::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Screenshot: Optionen der GitHub Enterprise Server-Installation in VMware"::: 

   :::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Screenshot: Menüoption für das Bereitstellen der OVA-Vorlage":::    

1. Geben Sie einen erkennbaren Namen für den neuen virtuellen Computer an, z. B. GitHubEnterpriseServer. Sie müssen keine Versionsdetails in den VM-Namen einfügen, da diese Details beim Upgrade der Instanz belanglos werden. 

1. Wählen Sie vorerst alle Standardwerte aus (wir werden diese Details in Kürze bearbeiten), und warten Sie, bis die OVA importiert wurde.

1. [Passen Sie die Hardwarekonfiguration](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) nach dem Import Ihren Anforderungen entsprechend an. In unserem Beispielszenario wird die folgende Konfiguration benötigt.

   | Resource | Standardeinrichtung | Standardeinrichtung + „Beta-Features“ (Actions) |
   | --- | --- | --- |
   | vCPUs | 4 | 8 |
   | Arbeitsspeicher | 32 GB | 61 GB |
   | Angeschlossener Speicher | 250 GB | 300 GB |
   | Stammspeicher | 200 GB | 200 GB |

   Ihre Anforderungen weichen möglicherweise ab. Weitere Informationen finden Sie in den Überlegungen zur Hardware bei der [Installation von GitHub Enterprise Server unter VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Informationen zum Anpassen der Hardwarekonfiguration abhängig von der jeweiligen Situation finden Sie in der Beschreibung zum [Hinzufügen von CPU- oder Arbeitsspeicherressourcen für VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware).

## <a name="configure-the-github-enterprise-server-instance"></a>Konfigurieren der GitHub Enterprise Server-Instanz

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Screenshot: Fenster „GitHub Enterprise installieren“"::: 

Wenn der neu bereitgestellte virtuelle Computer hochgefahren ist, [konfigurieren Sie ihn über Ihren Browser](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Sie müssen Ihre Lizenzdatei hochladen und ein Kennwort für die Verwaltungskonsole festlegen. Sie sollten dieses Kennwort unbedingt notieren und an einem sicheren Ort aufbewahren.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Screenshot: GitHub Enterprise-Bildschirm für SSH-Zugriff zum Hinzufügen eines neuen SSH-Schlüssels":::    

Es wird empfohlen, zumindest die folgenden Schritte auszuführen:

1. Laden Sie einen öffentlichen SSH-Schlüssel in die Verwaltungskonsole hoch, damit Sie [über SSH auf die Verwaltungsshell zugreifen](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) können. 

2. [Konfigurieren Sie TLS für Ihre Instanz](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls), damit Sie ein Zertifikat verwenden können, das von einer vertrauenswürdigen Zertifizierungsstelle signiert wurde. Wenden Sie Ihre Einstellungen an.

   :::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Screenshot: Anwenden der Einstellungen auf Ihre Instanz":::

1. Während die Instanz neu gestartet wird, konfigurieren Sie den Blobspeicher für GitHub Actions.

   >[!NOTE]
   >GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).
    
   Um GitHub Actions in GitHub Enterprise Server (derzeit als "Beta"-Feature verfügbar) zu aktivieren, ist ein externer Blobspeicher erforderlich. Actions verwendet diesen externen Blobspeicher zum Speichern von Artefakten und Protokollen. GitHub Actions in GitHub Enterprise Server unterstützt u. a. [Azure Blob Storage als Speicheranbieter](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements). Wir stellen ein neues Azure-Speicherkonto mit dem [Speicherkontotyp](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage bereit.
    
   :::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Screenshot: einzugebende Informationen zur Instanz beim Bereitstellen eines Azure Blob Storage-Kontos":::
    
1. Sobald die Bereitstellung der neuen BlobStorage-Ressource abgeschlossen ist, speichern Sie die Verbindungszeichenfolge (diese finden Sie unter „Zugriffsschlüssel“). Sie werden diese Zeichenfolge in Kürze benötigen.

1. Nachdem die Instanz neu gestartet wurde, erstellen Sie ein neues Administratorkonto auf der Instanz. Sie sollten das Kennwort dieses Benutzers ebenfalls unbedingt notieren und sicher aufbewahren.

   :::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Screenshot: GitHub Enterprise-Bildschirm zum Erstellen eines Administratorkontos":::

### <a name="other-configuration-steps"></a>Weitere Konfigurationsschritte

Zum Schützen Ihrer Instanz für die Verwendung in der Produktion werden die folgenden optionalen Einrichtungsschritte empfohlen:

1. Konfigurieren Sie die [Hochverfügbarkeit](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) zum Schutz vor:

    - Softwareabstürzen (Betriebssystem- oder Anwendungsebene)
    - Hardwarefehlern (Speicher, CPU, RAM usw.)
    - Fehlern im Virtualisierungshostsystem
    - Trennung vom logischen oder physischen Netzwerk

2. [Konfigurieren](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) Sie [Sicherungsdienstprogramme](https://github.com/github/backup-utils), die versionierte Momentaufnahmen für die Notfallwiederherstellung bereitstellen, die getrennt von der primären Instanz verfügbar gehostet werden.
3. Richten Sie die [Isolation von Unterdomänen](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation) unter Verwendung eines gültigen TLS-Zertifikats ein, um Cross-Site Scripting und andere damit verbundene Sicherheitsrisiken zu minimieren.


## <a name="set-up-the-github-actions-runner"></a>Einrichten des GitHub Actions-Runners

> [!NOTE]
> GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).

An diesem Punkt sollten Sie über eine aktive Instanz von GitHub Enterprise Server verfügen, für die ein Administratorkonto erstellt wurde. Darüber hinaus sollten Sie auch über externen Blobspeicher verfügen, den GitHub Actions zur dauerhaften Speicherung verwendet.

Erstellen Sie einen Computer, in dem GitHub Actions ausgeführt wird. Verwenden Sie dafür ebenfalls Azure VMware Solution.

1. Stellen Sie eine neue VM im Cluster bereit, und verwenden Sie als Basis [ein neues Release von Ubuntu Server](http://releases.ubuntu.com/20.04.1/).

   :::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Screenshot: Name und Speicherort der VM zur Bereitstellung einer neuen VM":::

1. Fahren Sie mit dem Setup fort, und wählen Sie Computeressource, Speicher und Kompatibilität aus.

1. Wählen Sie das Gastbetriebssystem aus, das auf der VM installiert werden soll.

   :::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Screenshot: Familie und Version des Gastbetriebssystems, das auf der VM installiert werden soll":::

1. Schalten Sie den virtuellen Computer ein, nachdem er erstellt wurde, und stellen Sie über SSH eine Verbindung her.

1. Installieren Sie die Anwendung [Actions-Runner](https://github.com/actions/runner), die einen Auftrag aus einem GitHub Actions-Workflow ausführt. Ermitteln Sie die neueste Version der Linux x64-Version des Actions-Runners (entweder auf [der Seite mit den Versionen](https://github.com/actions/runner/releases) oder durch Ausführen des folgenden Kurzskripts) und laden Sie diese Version herunter. Für dieses Skript müssen sowohl curl als auch [jq](https://stedolan.github.io/jq/) in der VM vorhanden sein.

   ```bash
   LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \
    
   jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )
    
   DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \
    
   jq -r '.browser\_download\_url' )
    
   curl -OL $DOWNLOAD\_URL
   ```
    
   Sie sollten jetzt über eine lokale Datei in Ihrer VM verfügen, "actions-runner-linux-arm64-\*.tar.gz". Extrahieren Sie diese TAR-Datei lokal:
    
   ```bash
   tar xzf actions-runner-linux-arm64-\*.tar.gz
   ```
    
   Dadurch werden einige Dateien lokal entpackt, z. B. die Skripte `config.sh` und `run.sh`.

## <a name="enable-github-actions"></a>Aktivieren von GitHub Actions

>[!NOTE]
>GitHub Actions ist [derzeit als eingeschränkte Beta Version für GitHub Enterprise Server, Release 2.22 verfügbar](https://docs.github.com/en/enterprise/admin/github-actions).

Konfigurieren und aktivieren Sie GitHub Actions auf der GitHub Enterprise Server-Instanz. 

1. [Greifen Sie über SSH auf die Verwaltungsshell der GitHub Enterprise Server-Instanz zu](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh), und führen Sie dann die folgenden Befehle aus:

1. Legen Sie eine Umgebungsvariable fest, die die Verbindungszeichenfolge für Ihren Blobspeicher enthält.

   ```bash
   export CONNECTION\_STRING="<your connection string from the blob storage step>"
   ```    

1. Konfigurieren Sie einen Actions-Speicher.
    
   ```bash
   ghe-config secrets.actions.storage.blob-provider azure
  
   ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING`      
   ```

1. Wenden Sie die Einstellungen an.

   ```bash
   ghe-config-apply
   ```    

1. Führen Sie eine Vorabprüfung aus, um zusätzliche, von Actions in GitHub Enterprise Server benötigte Softwarekomponenten zu installieren.
    
   ```bash
   ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"
   ```

1. Aktivieren Sie Actions, und wenden Sie die Konfiguration dann erneut an.
 
   ```bash
   ghe-config app.actions.enabled true
    
   ghe-config-apply      
   ```

1. Überprüfen Sie die Integrität Ihres Blobspeichers.

   ```bash
   ghe-actions-check -s blob
   ```

   Folgendes sollte angezeigt werden: _Blob Storage ist fehlerfrei_.

1. Nachdem **GitHub Actions** konfiguriert wurde, können Sie das Feature für Benutzer aktivieren. Melden Sie sich als Administrator bei Ihrer GitHub Enterprise Server-Instanz an, und wählen Sie das Symbol :::image type="icon" source="media/github-enterprise-server/rocket-icon.png"::: aus, das in der rechten oberen Ecke jeder Seite verfügbar ist. 

1. Wählen Sie in der linken Randleiste **Enterprise overview**, **Policies**, **Actions** und dann die Option zum **Aktivieren von GitHub Actions für alle Organisationen** aus.

1. Konfigurieren Sie den Runner über die Registerkarte **Selbstgehostete Runner**. Wählen Sie aus der Dropdownliste **Neu hinzufügen** und dann **Neuer Runner** aus. Es wird eine Reihe von Befehlen angezeigt, die ausgeführt werden können.

1. Kopieren Sie den Befehl zum **Konfigurieren** des Runners, z. B.:

   ```bash
   ./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA
   ```

1. Kopieren Sie den `config.sh`-Befehl, und fügen Sie ihn in eine Sitzung im (zuvor erstellten) Actions-Runner ein.

   :::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Screenshot: Registrierung und Einstellungen des GitHub Actions-Runners":::

1. Verwenden Sie den Befehl `./run.sh`, um den Runner *auszuführen*:

   >[!TIP]
   >Um diesen Runner für Organisationen in Ihrem Unternehmen verfügbar zu machen, bearbeiten Sie den entsprechenden Zugriff für Organisationen. Sie können den Zugriff auf einen Teil der Organisationen und sogar auf bestimmte Repositorys beschränken.
   >
   >:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Screenshot: Bearbeiten des Zugriffs für selbstgehostete Runner":::


## <a name="optional-configure-github-connect"></a>(Optional) Konfigurieren von GitHub Connect

Obwohl dieser Schritt optional ist, wird er empfohlen, wenn Sie beabsichtigen, auf GitHub.com verfügbare Open-Source-Aktionen zu nutzen. Auf diese Weise können Sie auf der Arbeit anderer Benutzer aufbauen, indem Sie in Ihren Workflows auf diese wiederverwendbaren Aktionen verweisen.

Führen Sie die Schritte in den Anweisungen zum [Aktivieren des automatischen Zugriffs auf GitHub.com-Aktionen mit GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect) aus, um GitHub Connect zu aktivieren.

Wählen Sie die Option **Server to use actions from GitHub.com in workflow runs** aus, nachdem GitHub Connect aktiviert wurde.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Screenshot: aktivierte Option „Server kann Aktionen von GitHub.com in Workflowausführungen verwenden“":::

## <a name="set-up-and-run-your-first-workflow"></a>Einrichten und Ausführen Ihres ersten Workflows

Nachdem GitHub Actions und GitHub Connect eingerichtet wurden, können wir all unsere Mühen vorteilhaft nutzen. Nachstehend sehen Sie ein Beispiel für einen Workflow, der auf [octokit/request-action](https://github.com/octokit/request-action) verweist, sodass wir GitHub über Interaktionen mithilfe der GitHub-API und Unterstützung von GitHub Actions "skriptgesteuert" verwenden können.

In diesem einfachen Workflow verwenden wir `octokit/request-action`, um ein GitHub-Issue mithilfe der API zu öffnen.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Screenshot: Beispielworkflow":::

>[!NOTE]
>GitHub.com hostet die Aktion. Wird diese Aktion allerdings auf GitHub Enterprise Server ausgeführt, wird *automatisch* die GitHub Enterprise Server-API verwendet.

Wenn Sie GitHub Connect nicht aktivieren möchten, können Sie den folgenden alternativen Workflow verwenden.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Screenshot: alternativer Beispielworkflow":::

1. Navigieren Sie zu einem Repository in der Instanz, und fügen Sie den obigen Workflow als `.github/workflows/hello-world.yml` hinzu.

   :::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Screenshot: weiterer alternativer Beispielworkflow":::

1. Warten Sie auf der Registerkarte **Actions** für Ihr Repository, bis der Workflow ausgeführt wird.

   :::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Screenshot: ausgeführter Beispielworkflow":::

   Sie können die Ausführung durch den Runner sehen.

   :::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Screenshot: durch den Runner verarbeiteter Workflow":::

Wenn alles erfolgreich ausgeführt wurde, sollte in Ihrem Repository ein neues Issue mit dem Titel "Hello World" angezeigt werden.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Screenshot: durch github-actions erstelltes Hello World-Issue in GitHub":::

Herzlichen Glückwunsch! Sie haben soeben ihren ersten Actions-Workflow in GitHub Enterprise Server ausgeführt, der in Ihrer privaten Azure VMware Solution-Cloud ausgeführt wird.

In diesem Artikel wurde in Ihrer privaten Azure VMware Solution-Cloud eine neue GitHub Enterprise Server-Instanz eingerichtet – das selbstgehostete Äquivalent zu GitHub.com. Die Instanz bietet Unterstützung für GitHub Actions und verwendet Azure Blob Storage zum dauerhaften Speichern von Protokollen und Artefakten. Hier wurde aber nur ein geringer Teil der Möglichkeiten aufgezeigt, die GitHub Actions bietet. Sehen Sie sich die Liste der Aktionen im [Marketplace von GitHub](https://github.com/marketplace) an, oder [erstellen Sie eigene Aktionen](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie GitHub Enterprise Server in Ihrer privaten Azure VMware Solution-Cloud eingerichtet wird, informieren Sie sich über die folgenden Themen: 

- [Wie man mit GitHub Actions anfängt](https://docs.github.com/en/actions)
- [Wie man dem Beta-Programm beitritt](https://resources.github.com/beta-signup/)
- [Verwaltung von GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)
