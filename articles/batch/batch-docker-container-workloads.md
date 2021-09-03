---
title: Containerworkloads
description: Erfahren Sie, wie Sie Apps aus Containerimages in Azure Batch ausführen und skalieren. Erstellen Sie einen Pool mit Serverknoten, der das Ausführen von Containeraufgaben unterstützt.
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e8effa7daf0c30edaef9924cbefe35cdad1b20e1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445773"
---
# <a name="run-container-applications-on-azure-batch"></a>Ausführen von Containeranwendungen in Azure Batch

Mit Azure Batch können Sie eine große Anzahl von Batchcomputingaufträgen in Azure ausführen und skalieren. Stapelverarbeitungsaufgaben lassen sich direkt auf virtuellen Computern (Knoten) in einem Batch-Pool ausführen, doch Sie können auch einen Batch-Pool zum Ausführen von Aufgaben in Docker-kompatiblen Containern auf den Knoten einrichten. In diesem Artikel wird erläutert, wie Sie einen Pool von Computeknoten erstellen, die das Ausführen von Containeraufgaben unterstützen. Außerdem erfahren Sie, wie Sie anschließend Containeraufgaben im Pool ausführen.

In den Codebeispielen werden der .NET-Batch und Python-SDKs verwendet. Sie können auch andere Batch-SDKs und -Tools, einschließlich des Azure-Portals, verwenden, um containerfähige Batch-Pools zu erstellen und Containeraufgaben auszuführen.

## <a name="why-use-containers"></a>Gründe für die Verwendung von Containern

Die Verwendung von Containern bietet eine einfache Möglichkeit zum Ausführen von Batch-Aufgaben, ohne eine Umgebung und Abhängigkeiten zum Ausführen von Anwendungen verwalten zu müssen. Container stellen Anwendungen als einfache, portable, eigenständige Einheiten bereit, die in vielen verschiedenen Umgebungen ausgeführt werden können. Sie können beispielsweise einen Container lokal erstellen und testen und dann das Containerimage in eine Registrierung in Azure oder an anderer Stelle hochladen. Das Containerbereitstellungsmodell stellt sicher, dass die Laufzeitumgebung der Anwendung immer ordnungsgemäß installiert und konfiguriert ist, und zwar ganz unabhängig davon, wo Sie die Anwendung hosten. Containerbasierte Vorgänge in Batch können auch Funktionen von Nicht-Containeraufgaben nutzen, z.B. Anwendungspakete und die Verwaltung von Ressourcendateien und Ausgabedateien.

## <a name="prerequisites"></a>Voraussetzungen

Sie sollten mit Containerkonzepten sowie dem Erstellen von Batch-Pools und -aufträgen vertraut sein.

- **SDK-Versionen**: Die Batch SDKs unterstützen Containerimages ab den folgenden Versionen:
  - Batch REST API, Version 2017-09-01.6.0
  - Batch .NET SDK, Version 8.0.0
  - Batch Python SDK, Version 4.0
  - Batch Java SDK, Version 3.0
  - Batch Node.js SDK, Version 3.0

- **Konten**: Sie müssen in Ihrem Azure-Abonnement ein [Batch-Konto](accounts.md) und optional ein Azure Storage-Konto erstellen.

- **Ein unterstütztes VM-Image**: Container werden nur in Pools unterstützt, die mit der Konfiguration des virtuellen Computers aus einem unterstützten (im folgenden Abschnitt aufgelisteten) Image erstellt wurden. Wenn Sie ein benutzerdefiniertes Image bereitstellen, finden Sie hierzu Überlegungen im folgenden Abschnitt und Anforderungen unter [Verwenden eines verwalteten benutzerdefinierten Images zum Erstellen eines VM-Pools](batch-custom-images.md).

Beachten Sie die folgenden Einschränkungen:

- Batch bietet RDMA-Unterstützung nur für Container, die in Linux-Pools ausgeführt werden.
- Für Windows-Containerworkloads wird empfohlen, eine Größe für VMs mit mehreren Kernen für Ihren Pool auszuwählen.

## <a name="supported-virtual-machine-images"></a>Unterstützte Images virtueller Computer

Sie müssen eines der folgenden unterstützten Windows- oder Linux-Images verwenden, um einen Pool mit VM-Computeknoten für Containerworkloads zu erstellen. Weitere Informationen zu Marketplace-Images, die mit Azure Batch kompatibel sind, finden Sie in der [Liste mit VM-Images](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Windows-Unterstützung

Batch unterstützt Windows Server-Images, die Container-Unterstützungsbezeichnungen aufweisen. In der Regel ist an diese Image-SKU-Namen das Suffix `-with-containers` oder `-with-containers-smalldisk` angehängt. Außerdem bezeichnet [die API zum Auflisten aller unterstützten Images in Batch](batch-linux-nodes.md#list-of-virtual-machine-images) eine `DockerCompatible`-Funktion, wenn das Image Docker-Container unterstützt.

Sie können auch benutzerdefinierte Images über VMs erstellen, die Docker unter Windows ausführen.

### <a name="linux-support"></a>Linux-Unterstützung

Für Linux-Containerworkloads unterstützt Batch derzeit die folgenden von Microsoft Azure Batch in Azure Marketplace veröffentlichten Linux-Images, ohne dass ein benutzerdefiniertes Image erforderlich ist.

#### <a name="vm-sizes-without-rdma"></a>VM-Größen ohne RDMA

- Herausgeber: `microsoft-azure-batch`
  - Angebot: `centos-container`
  - Angebot: `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>VM-Größen mit RDMA

- Herausgeber: `microsoft-azure-batch`
  - Angebot: `centos-container-rdma`
  - Angebot: `ubuntu-server-container-rdma`

Diese Images werden nur für die Verwendung in Azure Batch-Pools unterstützt und sind für die Docker-Containerausführung konzipiert. Sie bieten:

- Eine vorinstallierte Docker-kompatible [Moby](https://github.com/moby/moby)-Container-Runtime
- Vorinstallierte NVIDIA GPU-Treiber und NVIDIA-Container-Runtime zum Optimieren der Bereitstellung auf Azure-VMs der N-Serie
- VM-Images mit dem Suffix „-rdma“ sind mit Unterstützung für InfiniBand RDMA-VM-Größen vorkonfiguriert. Diese VM-Images sollten nicht mit VM-Größen ohne InfiniBand-Unterstützung verwendet werden.

Sie können auch benutzerdefinierte Images über VMs erstellen, die Docker unter einer der Linux-Distributionen ausführen, die mit Batch kompatibel sind. Wenn Sie ein eigenes benutzerdefiniertes Linux-Image bereitstellen möchten, finden Sie Anleitungen dazu unter [Verwenden eines verwalteten benutzerdefinierten Images zum Erstellen eines VM-Pools](batch-custom-images.md).

Für Docker-Unterstützung für ein benutzerdefiniertes Image installieren Sie [Docker Community Edition (CE)](https://www.docker.com/community-edition) oder [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Weitere Überlegungen zum Verwenden eines benutzerdefinierten Linux-Images:

- Wenn Sie die GPU-Leistung von Azure-Größen der N-Serie bei der Verwendung eines benutzerdefinierten Images optimal nutzen möchten, installieren Sie im Voraus NVIDIA-Treiber. Außerdem müssen Sie das Docker-Engine-Hilfsprogramm für NVIDIA-GPUs, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker), installieren.
- Verwenden Sie für den Zugriff auf das RDMA-Netzwerk von Azure eine RDMA-fähige VM-Größe. Erforderliche RDMA-Treiber sind in den von Batch unterstützten CentOS-HPC- und Ubuntu-Images installiert. Zum Ausführen von MPI-Workloads können zusätzliche Konfigurationsschritte erforderlich sein. Informationen finden Sie unter [Verwenden RDMA-fähiger oder GPU-fähiger Instanzen in Batch-Pools](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Containerkonfiguration für Batch-Pool

Damit ein Batch-Pool Containerworkloads ausführen kann, müssen Sie [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration)-Einstellungen im [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration)-Objekt des Pools festlegen. (Dieser Artikel enthält Links zur .NET-API-Referenz von Batch. Die entsprechenden Einstellungen befinden sich in der [Batch Python](/python/api/overview/azure/batch)-API.)

Sie können einen containerfähigen Pool mit oder ohne vorabgerufene Containerimages erstellen, wie in den folgenden Beispielen gezeigt. Mithilfe des Pull-Vorgangs (oder Vorabrufs) können Sie Containerimages entweder über Docker Hub oder eine andere Containerregistrierung im Internet vorab laden. Verwenden Sie für optimale Leistung eine [Azure-Containerregistrierung](../container-registry/container-registry-intro.md) in der gleichen Region wie das Batch-Konto.

Der Vorteil vorabgerufener Containerimages ist, dass bei der ersten Ausführung von Aufgaben diese nicht auf das Herunterladen des Containerimages warten müssen. Die Containerkonfiguration führt beim Erstellen des Pools eine Pullübertragung von Containerimages auf die virtuellen Computer durch. Für den Pool ausgeführte Aufgaben können dann auf die Liste der Containerimages und auf die Containerausführungsoptionen verweisen.

### <a name="pool-without-prefetched-container-images"></a>Pool ohne vorabgerufene Containerimages

Zum Konfigurieren eines containerfähigen Pools ohne vorab abgerufene Containerimages definieren Sie die Objekte `ContainerConfiguration` und `VirtualMachineConfiguration`, wie in den folgenden Beispielen gezeigt. In diesem Beispiel wird für das Containerpool-Image „Ubuntu Server für Azure Batch“ aus Marketplace verwendet.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>Vorabrufen von Images für die Containerkonfiguration

Zum Vorabrufen von Containerimages im Pool fügen Sie die Liste der Containerimages (`container_image_names`, in Python) zur `ContainerConfiguration` hinzu.

Im folgenden grundlegenden Python-Beispiel wird gezeigt, wie ein standardmäßiges Ubuntu-Containerimage aus [Docker Hub](https://hub.docker.com) vorabgerufen wird.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

Im folgenden C#-Beispiel wird davon ausgegangen, dass Sie vorab ein TensorFlow-Image über [Docker Hub](https://hub.docker.com) abrufen möchten. Dieses Beispiel enthält eine Startaufgabe, die auf den Poolknoten im VM-Host ausgeführt wird. Beispielsweise können Sie eine Startaufgabe auf dem Host ausführen, um einen Dateiserver einzubinden, der über die Container zugänglich ist.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>Vorabrufen von Images aus einer privaten Containerregistrierung

Sie können Containerimages auch durch Authentifizierung bei einem Server für die Containerregistrierung vorabrufen. Beim folgenden Beispiel rufen die Objekte `ContainerConfiguration` und `VirtualMachineConfiguration` ein privates TensorFlow-Image aus einer privaten Azure-Containerregistrierung vorab ab. Der Bildverweis ist derselbe wie im vorherigen Beispiel.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

### <a name="managed-identity-support-for-acr"></a>Unterstützung verwalteter Identitäten für ACR

Beim Zugriff auf Container, die in [Azure Container Registry](https://azure.microsoft.com/services/container-registry) gespeichert sind, kann entweder ein Benutzername/Kennwort oder eine verwaltete Identität für die Authentifizierung beim Dienst verwendet werden. Um eine verwaltete Identität zu verwenden, stellen Sie zunächst sicher, dass die Identität [dem Pool zugewiesen](managed-identity-pools.md) wurde und dass der Identität die Rolle `AcrPull` für die Containerregistrierung zugewiesen ist, auf die Sie zugreifen möchten. Teilen Sie Batch dann einfach mit, welche Identität bei der Authentifizierung bei ACR verwendet werden soll.

```csharp
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    identityReference: new ComputeNodeIdentityReference() { ResourceId = "/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name" }
);

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Containereinstellungen für die Aufgabe

Geben Sie containerspezifische Einstellungen an, wenn Sie eine Containeraufgabe für einen containerfähigen Pool ausführen möchten. Die Einstellungen umfassen das zu verwendende Image, die Registrierung sowie Containerausführungsoptionen.

- Verwenden Sie die `ContainerSettings`-Eigenschaft der Aufgabenklassen zum Konfigurieren containerspezifischer Einstellungen. Diese Einstellungen werden durch die [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings)-Klasse definiert. Beachten Sie, dass die Containeroption `--rm` keine zusätzliche Option `--runtime` erfordert, da dies von Batch übernommen wird.

- Wenn Sie Aufgaben in Containerimages ausführen, sind für die [Cloudaufgabe](/dotnet/api/microsoft.azure.batch.cloudtask) und die [Auftrags-Manager-Aufgabe](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) Containereinstellungen erforderlich. Die [Startaufgabe](/dotnet/api/microsoft.azure.batch.starttask), die [Auftragsvorbereitungsaufgabe](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) und die [Auftragsfreigabeaufgabe](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) benötigen jedoch keine Containereinstellungen (diese können in einem Containerkontext oder direkt auf dem Knoten ausgeführt werden).

- Bei Windows müssen Aufgaben mit [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) als `admin` ausgeführt werden.

- Bei Linux ordnet Batch die Benutzer-/Gruppenberechtigung dem Container zu. Wenn für den Zugriff auf einen Ordner innerhalb des Containers Administratorberechtigungen erforderlich sind, müssen Sie die Aufgabe möglicherweise als Poolbereich mit Administratorrechten ausführen. Dadurch wird sichergestellt, dass Batch den Task als Stamm im Containerkontext ausführt. Andernfalls hat ein Benutzer, der kein Administrator ist, möglicherweise keinen Zugriff auf diese Ordner.

- Bei Containerpools mit GPU-aktivierter Hardware aktiviert Batch automatisch die GPU für Containertasks, sodass Sie das `–gpus`-Argument nicht einbeziehen sollten.

### <a name="container-task-command-line"></a>Befehlszeile für Containeraufgaben

Wenn Sie eine Containeraufgabe ausführen, verwendet Batch automatisch den Befehl [docker create](https://docs.docker.com/engine/reference/commandline/create/), um unter Verwendung des in der Aufgabe angegebenen Images einen Container zu erstellen. Anschließend steuert Batch die Aufgabenausführung im Container.

Für eine Containeraufgabe muss genau wie bei containerfremden Batch-Aufgaben eine Befehlszeile festgelegt werden. Da Batch den Container automatisch erstellt, gibt die Befehlszeile nur die Befehle an, die im Container ausgeführt werden sollen.

Wenn das Containerimage für eine Batch-Aufgabe mit einem [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example)-Skript konfiguriert wird, können Sie Ihre Befehlszeile für die Verwendung des Standardeinstiegspunkts konfigurieren oder ihn überschreiben:

- Wenn Sie den Standardeinstiegspunkt des Containerimages verwenden möchten, legen Sie die Aufgabenbefehlszeile auf die leere Zeichenfolge `""` fest.

- Wenn Sie den Standardeinstiegspunkt überschreiben möchten oder das Image keinen Einstiegspunkt besitzt, legen Sie eine für den Container geeignete Befehlszeile fest (beispielsweise `/app/myapp` oder `/bin/sh -c python myscript.py`).

Bei den Optionen für die Containerausführung ([ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions)) handelt es sich um optionale Argumente für den Befehl `docker create`, die Batch bei der Erstellung und Ausführung des Containers verwendet. So können Sie beispielsweise mit der Option `--workdir <directory>` ein Arbeitsverzeichnis für den Container festlegen. Weitere Optionen finden Sie in der Referenz zu [docker create](https://docs.docker.com/engine/reference/commandline/create/).

### <a name="container-task-working-directory"></a>Arbeitsverzeichnis für Containeraufgaben

Eine Batch-Containeraufgabe wird in einem Arbeitsverzeichnis des Containers ausgeführt, das stark dem Verzeichnis ähnelt, das Batch für eine reguläre (containerfremde) Aufgabe einrichtet. Beachten Sie, dass sich dieses Arbeitsverzeichnis von [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) (sofern im Image konfiguriert) bzw. vom standardmäßigen Containerarbeitsverzeichnis (`C:\` für Windows-Container, `/` für Linux-Container) unterscheidet.

Für Batch-Containeraufgaben gilt Folgendes:

- Alle Verzeichnisse unter `AZ_BATCH_NODE_ROOT_DIR` auf dem Hostknoten (Stamm der Azure Batch-Verzeichnisse) werden dem Container zugeordnet.
- Alle Variablen der Aufgabenumgebung werden dem Container zugeordnet.
- Das Aufgabenarbeitsverzeichnis `AZ_BATCH_TASK_WORKING_DIR` auf dem Knoten wird auf die gleiche Weise festgelegt wie für eine reguläre Aufgabe und im Container zugeordnet.

Dank dieser Zuordnungen können Containeraufgaben ganz ähnlich verwendet werden wie containerfremde Aufgaben. So können Sie beispielsweise Anwendungen unter Verwendung von Anwendungspaketen installieren, auf Ressourcendateien aus Azure Storage zugreifen, Aufgabenumgebungseinstellungen verwenden und Aufgabenausgabedateien über die Beendigung des Containers hinaus beibehalten.

### <a name="troubleshoot-container-tasks"></a>Behandeln von Problemen mit Containeraufgaben

Sollte Ihre Containeraufgabe nicht wie erwartet funktionieren, müssen Sie ggf. Informationen zur WORKDIR- oder ENTRYPOINT-Konfiguration des Containerimages abrufen. Führen Sie den Befehl [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) aus, um die Konfiguration anzuzeigen.

Passen Sie ggf. die Einstellungen der Containeraufgabe auf der Grundlage des Images an:

- Geben Sie in der Aufgabenbefehlszeile einen absoluten Pfad an. Falls der Standardeinstiegspunkt des Images für die Aufgabenbefehlszeile verwendet wird, vergewissern Sie sich, dass ein absoluter Pfad festgelegt ist.
- Ändern Sie in den Containerausführungsoptionen das Arbeitsverzeichnis, sodass es dem Arbeitsverzeichnis (WORKDIR) im Image entspricht. Legen Sie beispielsweise `--workdir /app` fest.

## <a name="container-task-examples"></a>Beispiele für Containeraufgaben

Der folgende Python-Codeausschnitt zeigt eine einfache Befehlszeile, die in einem Container ausgeführt wird, der auf der Grundlage eines fiktiven, aus Docker Hub gepullten Images erstellt wurde. Hier entfernt die Containeroption `--rm` den Container, nachdem die Aufgabe abgeschlossen ist, und die Option `--workdir` legt ein Arbeitsverzeichnis fest. Die Befehlszeile überschreibt den Einstiegspunkt des Containers mithilfe eines einfachen Shell-Befehls, der eine kleine Datei in das Aufgabenarbeitsverzeichnis auf dem Host schreibt.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

Im folgenden Beispiel C# werden die grundlegenden Containereinstellungen für eine Cloudaufgabe gezeigt:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich auch über das Toolkit [Batch Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) für die einfache Bereitstellung von Containerworkloads in Azure Batch über [Shipyard-Rezepte](https://github.com/Azure/batch-shipyard).
- Weitere Informationen zur Installation und Verwendung von Docker CE unter Linux finden Sie in der Dokumentation zu [Docker](https://docs.docker.com/engine/installation/).
- Erfahren Sie mehr zum [Verwenden eines verwalteten benutzerdefinierten Images zum Erstellen eines VM-Pools](batch-custom-images.md).
- Informieren Sie sich über das [Moby-Projekt](https://mobyproject.org/), ein Framework zum Erstellen containerbasierter Systeme.
