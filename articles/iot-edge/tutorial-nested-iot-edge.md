---
title: 'Tutorial: Erstellen einer Hierarchie für IoT Edge-Geräte – Azure IoT Edge'
description: In diesem Tutorial wird veranschaulicht, wie Sie eine hierarchische Struktur für IoT Edge-Geräte mit Gateways erstellen.
author: v-tcassi
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 5c204a07a0457852d2faefacdd77740f147b9d88
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225989"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Tutorial: Erstellen einer Hierarchie für IoT Edge-Geräte

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Stellen Sie netzwerkübergreifend Azure IoT Edge-Knoten bereit, die in hierarchischen Ebenen angeordnet sind. Jede Ebene einer Hierarchie entspricht einem Gatewaygerät, mit dem Nachrichten und Anforderungen von den Geräten der direkt untergeordneten Ebene verarbeitet werden.

Sie können eine Hierarchie mit Geräten so strukturieren, dass nur die oberste Ebene über Konnektivität mit der Cloud verfügt und die untergeordneten Ebenen nur mit der Ebene direkt darüber bzw. darunter kommunizieren können. Diese Netzwerkebenen sind die Grundlage der meisten Unternehmensnetzwerke, die auf der [ISA-95-Norm](https://en.wikipedia.org/wiki/ANSI/ISA-95) basieren.

Das Ziel dieses Tutorials ist die Erstellung einer Hierarchie mit IoT Edge-Geräten, mit der eine vereinfachte Produktionsumgebung simuliert wird. Zum Schluss stellen Sie das [Simulated Temperature Sensor-Modul](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) auf einem Gerät einer untergeordneten Ebene ohne Internetzugriff bereit, indem Sie Containerimages über die Hierarchie herunterladen.

Zur Erreichung dieses Ziels werden in diesem Tutorial Schritt für Schritt die Erstellung einer Hierarchie mit IoT Edge-Geräten, die Bereitstellung von IoT Edge-Runtimecontainern auf Ihren Geräten und die lokale Konfiguration der Geräte beschrieben. In diesem Tutorial verwenden Sie ein automatisiertes Konfigurationstool für folgende Zwecke:

> [!div class="checklist"]
>
> * Erstellen und Definieren der Beziehungen in einer Hierarchie mit IoT Edge-Geräten
> * Konfigurieren der IoT Edge-Runtime auf den Geräten in Ihrer Hierarchie
> * Installieren von einheitlichen Zertifikaten in Ihrer gesamten Gerätehierarchie
> * Hinzufügen von Workloads zu den Geräten Ihrer Hierarchie
> * Verwenden eines [IoT Edge-API-Proxymoduls](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) zum sicheren Weiterleiten von HTTP-Datenverkehr über einen einzelnen Port von Ihren Geräten der unteren Ebenen

>[!TIP]
>Dieses Tutorial enthält eine Mischung aus manuellen und automatisierten Schritten, mit denen Features für geschachtelte IoT Edge-Geräte veranschaulicht werden.
>
>Wenn Sie sich einen Einblick in die vollständig automatisierte Einrichtung einer Hierarchie mit IoT Edge-Geräten verschaffen möchten, können Sie das Beispielskript unter [Azure IoT Edge-Beispiel für industrielles IoT](https://aka.ms/iotedge-nested-sample) ausführen. In diesem geskripteten Szenario werden Azure-VMs als vorkonfigurierte Geräte bereitgestellt, um eine Factoryumgebung zu simulieren.
>
>Ausführliche Informationen zu den manuellen Schritten zum Erstellen und Verwalten einer Hierarchie mit IoT Edge-Geräten finden Sie in der [Schrittanleitung zu Gatewayhierarchien für IoT Edge-Geräte](how-to-connect-downstream-iot-edge-device.md).

In diesem Tutorial werden die folgenden Netzwerkebenen definiert:

* **Oberste Ebene**: IoT Edge-Geräte auf dieser Ebene können eine direkte Verbindung mit der Cloud herstellen.

* **Untere Ebenen**: IoT Edge-Geräte in Ebenen unterhalb der obersten Ebene können keine direkte Verbindung mit der Cloud herstellen. Zum Senden und Empfangen von Daten müssen sie den Weg über ein oder mehrere zwischengeschaltete IoT Edge-Geräte nehmen.

In diesem Tutorial wird der Einfachheit halber eine Hierarchie mit nur zwei Geräten verwendet, die weiter unten dargestellt ist. Ein Gerät (**Top Layer Device**, „Gerät der obersten Ebene“) befindet sich auf der obersten Ebene der Hierarchie und kann eine direkte Verbindung mit der Cloud herstellen. Dieses Gerät wird auch als **übergeordnetes Gerät** bezeichnet. Das andere Gerät (**Lower Layer Device**, „Gerät der unteren Ebene“) befindet sich auf der untergeordneten Ebene der Hierarchie und kann keine direkte Verbindung mit der Cloud herstellen. Sie können nach Bedarf weitere Geräte der unteren Ebenen hinzufügen, um Ihre Produktionsumgebung darzustellen. Geräte in unteren Ebenen werden auch als **untergeordnete Geräte** bezeichnet.

![Struktur der Tutorialhierarchie, die zwei Geräte enthält: das Gerät der obersten und das der unteren Ebene](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um eine Hierarchie mit IoT Edge-Geräten zu erstellen:

* Einen Computer (Windows oder Linux) mit Internetverbindung.
* Ein Azure-Konto mit einem gültigen Abonnement. Wenn Sie kein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.
* Eine [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) in Azure mit dem Tarif „Free“ oder „Standard“.
* Eine Bash-Shell in Azure Cloud Shell mit der Azure CLI v2.3.1, für die die Azure IoT-Erweiterung v0.10.6 oder höher installiert ist. In diesem Tutorial wird [Azure Cloud Shell](../cloud-shell/overview.md) verwendet. Falls Sie mit Azure Cloud Shell noch nicht vertraut sein sollten, helfen Ihnen die [Informationen in der Schnellstartanleitung](./quickstart-linux.md#prerequisites) weiter.
  * Wenn Sie die aktuellen Versionen der Azure CLI-Module und -Erweiterungen anzeigen möchten, führen Sie [az version](/cli/azure/reference-index?#az_version) aus.
* Ein Linux-Gerät, das für jedes Gerät in der Hierarchie als IoT Edge-Gerät konfiguriert wird. In diesem Tutorial werden zwei Geräte verwendet: Falls Sie keine verfügbaren Geräte haben, können Sie mit dem unten angegebenen Befehl virtuelle Azure-Computer für jedes Gerät Ihrer Hierarchie erstellen.

   Ersetzen Sie den Platzhaltertext im folgenden Befehl, und führen Sie ihn zweimal aus (einmal für jeden virtuellen Computer). Für jeden virtuellen Computer wird ein eindeutiges DNS-Präfix benötigt, das gleichzeitig als Name dient. Das DNS-Präfix muss die Bedingungen des folgenden regulären Ausdrucks erfüllen: `[a-z][a-z0-9-]{1,61}[a-z0-9]`.

   ```azurecli
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   Der virtuelle Computer nutzt SSH-Schlüssel für die Authentifizierung der Benutzer. Falls Sie sich mit der Erstellung und Verwendung von SSH-Schlüsseln noch nicht auskennen, hilft Ihnen die [Kurzanleitung zum Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md) weiter.

   Da Version 1.2 von IoT Edge mit dieser ARM-Vorlage vorinstalliert wird, müssen Sie die Ressourcen nicht manuell auf den einzelnen virtuellen Computern installieren. Informationen zur Installation von IoT Edge auf Ihren eigenen Geräten finden Sie unter [Installieren oder Deinstallieren von Azure IoT Edge für Linux (Version 1.2)](how-to-provision-single-device-linux-symmetric.md) oder [Sonderfall: Update von 1.0 oder 1.1 auf 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Bei einer erfolgreichen Erstellung eines virtuellen Computers mit dieser ARM-Vorlage werden das `SSH`-Handle und der vollqualifizierte Domänenname (`FQDN`) des virtuellen Computers ausgegeben. In den späteren Schritten verwenden Sie das SSH-Handle und entweder den FQDN oder die IP-Adresse der einzelnen virtuellen Computer für die Konfiguration. Daher sollten Sie sich diese Informationen notieren. Unten ist eine Beispielausgabe angegeben.

   >[!TIP]
   >Sie können die IP-Adresse und den FQDN auch im Azure-Portal ermitteln. Navigieren Sie zur Ermittlung der IP-Adresse zu Ihrer Liste mit den virtuellen Computern, und notieren Sie sich den Inhalt des Felds **Öffentliche IP-Adresse**. Ermitteln Sie den FQDN, indem Sie jeweils zur Übersichtsseite eines virtuellen Computers navigieren und sich das Feld **DNS-Name** ansehen.

   ![Der virtuelle Computer gibt bei der Erstellung JSON-Code aus, der das SSH-Handle enthält.](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Stellen Sie sicher, dass die folgenden Ports in Eingangsrichtung für alle Geräte (mit Ausnahme des Geräts der untersten Ebene) geöffnet sind: 443, 5671 und 8883:
  * 443: Wird zwischen übergeordneten und untergeordneten Edge-Hubs für REST-API-Aufrufe und zum Pullen von Docker-Containerimages verwendet.
  * 5671, 8883: Wird für AMQP und MQTT verwendet.

  Weitere Informationen finden Sie unter [Öffnen von Ports zu einem virtuellen Computer mit dem Azure-Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurieren Ihrer Hierarchie für IoT Edge-Geräte

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Erstellen einer Hierarchie für IoT Edge-Geräte

IoT Edge-Geräte bilden die Ebenen Ihrer Hierarchie. In diesem Tutorial wird eine Hierarchie aus zwei IoT Edge-Geräten erstellt: aus dem **Gerät der obersten** und dem der **unteren Ebene**. Sie können je nach Bedarf weitere untergeordnete Geräte erstellen.

Zum Erstellen und Konfigurieren Ihrer Hierarchie mit IoT Edge-Geräten verwenden Sie das Tool `iotedge-config`. Mit diesem Tool wird die Konfiguration der Hierarchie vereinfacht, indem mehrere Schritte automatisiert und in zwei Schritten zusammengefasst werden:

1. Einrichten der Cloudkonfiguration und Vorbereiten der einzelnen Gerätekonfigurationen, z. B.:

   * Erstellen von Geräten auf Ihrer IoT Hub-Instanz
   * Festlegen der Beziehungen zwischen über- und untergeordneten Geräten zum Autorisieren der Kommunikation zwischen Geräten
   * Generieren einer Kette mit Zertifikaten für jedes Gerät, um die sichere Kommunikation zwischen den Geräten zu ermöglichen
   * Generieren von Konfigurationsdateien für jedes Gerät

1. Installieren der einzelnen Gerätekonfigurationen, z. B.:

   * Installieren von Zertifikaten auf jedem Gerät
   * Anwenden der Konfigurationsdateien für jedes Gerät

Mit dem Tool `iotedge-config` werden auch die Modulbereitstellungen für Ihr IoT Edge-Gerät automatisch durchgeführt.

Führen Sie die folgenden Schritte in der Azure CLI aus, um das Tool `iotedge-config` für die Erstellung und Konfiguration Ihrer Hierarchie zu verwenden:

1. Erstellen Sie in [Azure Cloud Shell](https://shell.azure.com/) ein Verzeichnis für Ihre Ressourcen, die in diesem Tutorial verwendet werden:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Laden Sie das Release des Konfigurationstools und die Konfigurationsvorlagen herunter:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Dies führt dazu, dass in Ihrem Tutorialverzeichnis der Ordner `iotedge_config_cli_release` erstellt wird.

   Die Vorlagendatei, die zum Erstellen Ihrer Gerätehierarchie verwendet wird, ist die Datei `iotedge_config.yaml` unter `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`. Dasselbe Verzeichnis enthält auch die JSON-Bereitstellungsdatei `deploymentLowerLayer.json`. Hierin sind Anweisungen dazu enthalten, welche Module für Ihr **Gerät der unteren Ebene** bereitgestellt werden sollen. Die Datei `deploymentTopLayer.json` ist bis auf Ihr **Gerät der obersten Ebene** identisch. Dies liegt daran, dass die für die einzelnen Geräte bereitgestellten Module nicht gleich sind. Die Datei `device_config.toml` ist eine Vorlage für Konfigurationen von IoT Edge-Geräten und wird verwendet, um die Konfigurationspakete für die Geräte Ihrer Hierarchie automatisch zu generieren.

   Den Quellcode und die Skripts für das Tool `iotedge-config` finden Sie im [Repository mit den Azure-Beispielen auf GitHub](https://github.com/Azure-Samples/iotedge_config_cli).

1. Öffnen Sie die Konfigurationsvorlage des Tutorials, und fügen Sie Ihre Informationen ein:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Füllen Sie im Abschnitt **iothub** die Felder `iothub_hostname` und `iothub_name` mit Ihren Informationen. Sie finden diese Informationen im Azure-Portal auf der Übersichtsseite für Ihre IoT Hub-Instanz.

   Im optionalen Abschnitt **Zertifikate** können Sie die absoluten Pfade zu Ihrem Zertifikat und Schlüssel einfügen. Wenn Sie diese Felder leer lassen, werden vom Skript automatisch selbstsignierte Testzertifikate für Sie generiert. Falls Sie mit der Verwendung von Zertifikaten in einem Gatewayszenario noch nicht vertraut sein sollten, hilft Ihnen der [Abschnitt „Vorbereiten von Zertifikaten“ in der entsprechenden Anleitung](how-to-connect-downstream-iot-edge-device.md#prepare-certificates) weiter.

   Im Abschnitt **configuration** ist `template_config_path` der Pfad zur Vorlage `device_config.toml`, die zum Erstellen Ihrer Gerätekonfigurationen genutzt wird. Anhand des Felds `default_edge_agent` wird bestimmt, welche Geräte der unteren Ebenen für das Edge-Agent-Image gepullt werden und von welchem Ort dies erfolgt.

   Im Abschnitt **edgedevices** können Sie für ein Produktionsszenario die Hierarchiestruktur so ändern, dass sie Ihrer gewünschten Struktur entspricht. Übernehmen Sie für dieses Tutorial die Standardstruktur. Für jedes Gerät ist das Feld `device_id` vorhanden, in dem Sie einen Namen angeben können. Im Feld `deployment` können Sie den Pfad zur JSON-Bereitstellungsdatei für das Gerät angeben.

   Darüber hinaus können Sie IoT Edge-Geräte in Ihrer IoT Hub-Instanz auch manuell über das Azure-Portal, Azure Cloud Shell oder Visual Studio Code registrieren. Informationen dazu finden Sie [am Anfang des umfassenden Leitfadens zur manuellen Bereitstellung eines IoT Edge Geräts unter Linux](how-to-provision-single-device-linux-symmetric.md#register-your-device).

   Sie können die Beziehungen zwischen über- und untergeordneten Elementen auch manuell definieren. Weitere Informationen finden Sie in der Anleitung im Abschnitt [Erstellen einer Gatewayhierarchie](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy).

   ![Im Abschnitt „edgedevices“ der Konfigurationsdatei können Sie Ihre Hierarchie definieren.](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Speichern und schließen Sie die Datei:

   `CTRL + S`, `CTRL + Q`

1. Erstellen Sie in Ihrem Verzeichnis mit den Tutorialressourcen das Verzeichnis „outputs“ für die Konfigurationspakete:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Navigieren Sie zu Ihrem Verzeichnis `iotedge_config_cli_release`, und führen Sie das Tool aus, um Ihre Hierarchie mit IoT Edge-Geräten zu erstellen:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Mit dem Flag `--output` erstellt das Tool die Gerätezertifikate, Zertifikatpakete und eine Protokolldatei in einem Verzeichnis Ihrer Wahl. Wenn das Flag `-f` angegeben ist, sucht das Tool automatisch nach vorhandenen IoT Edge-Geräten auf Ihrer IoT Hub-Instanz und entfernt diese, um Fehler zu vermeiden und für Ihren Hub den bereinigten Zustand sicherzustellen.

   Das Konfigurationstool erstellt Ihre IoT Edge-Geräte und richtet hierfür die Beziehungen zwischen über- und untergeordneten Elementen ein. Optional werden Zertifikate erstellt, die von Ihren Geräten verwendet werden können. Wenn Pfade zu JSON-Bereitstellungsdateien angegeben werden, führt das Tool automatisch die Erstellung dieser Bereitstellungen für Ihre Geräte durch. Dies ist aber keine Vorgehensweise, die für Sie obligatorisch ist. Abschließend generiert das Tool die Konfigurationspakete für Ihre Geräte und fügt sie in das Verzeichnis „outputs“ ein. Eine ausführliche Beschreibung der Schritte, die vom Konfigurationstool ausgeführt werden, finden Sie in der Protokolldatei im Verzeichnis „outputs“.

   ![Skript: Anzeige einer Topologie Ihrer Hierarchie während der Ausführung](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Überprüfen Sie, ob die vom Skript ausgegebene Topologie korrekt aussieht. Wenn Sie mit der Struktur Ihrer Hierarchie zufrieden sind, können Sie fortfahren.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurieren der IoT Edge-Runtime

Zusätzlich zur Bereitstellung Ihrer Geräte wird durch diese Konfigurationsschritte die vertrauenswürdige Kommunikation zwischen den Geräten in der Hierarchie mithilfe Ihrer zuvor erstellten Zertifikate eingerichtet. Außerdem beginnen diese Schritte mit der Einrichtung der Netzwerkstruktur Ihrer Hierarchie. Das Gerät der obersten Ebene erhält die Internetverbindung aufrecht und ermöglicht es, Images für seine Runtime aus der Cloud zu pullen, während die Geräte der unteren Ebene über das Gerät der obersten Ebene auf diese Images zugreifen können.

Zum Konfigurieren der IoT Edge-Runtime müssen Sie die Konfigurationspakete, die vom Setupskript erstellt werden, auf Ihre Geräte anwenden. Die Konfigurationen unterscheiden sich für das **Gerät der obersten Ebene** und ein **Gerät der unteren Ebene** geringfügig. Sie sollten daher genau darauf achten, welche Konfigurationsdatei Sie jeweils auf ein Gerät anwenden.

1. Für jedes Gerät wird das entsprechende richtige Konfigurationspaket benötigt. Sie können ein USB-Laufwerk oder [Secure File Copy](https://www.ssh.com/ssh/scp/) verwenden, um die Konfigurationspakete auf die einzelnen Geräte zu verschieben.

   Stellen Sie sicher, dass Sie jeweils das richtige Konfigurationspaket an ein Gerät senden.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Mit `:~` wird angegeben, dass der Konfigurationsordner auf dem virtuellen Computer im Basisverzeichnis angeordnet wird.

1. Melden Sie sich bei Ihrem virtuellen Computer an, um das Konfigurationspaket auf das Gerät anzuwenden:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Entzippen Sie auf den einzelnen Geräten jeweils das Konfigurationspaket. Hierfür müssen Sie zunächst die ZIP-Installation durchführen:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Wenden Sie auf jedem Gerät das Konfigurationspaket auf das Gerät an:

   ```bash
   sudo ./install.sh
   ```

   Für das **Gerät der obersten Ebene** wird eine Aufforderung zum Eingeben des Hostnamens angezeigt. Auf dem **Gerät der unteren Ebene** werden der Hostname und der Hostname des übergeordneten Elements abgefragt. Geben Sie für diese Aufforderungen jeweils die entsprechende IP-Adresse bzw. den FQDN an. Sie können beide Angaben verwenden, aber es ist ratsam, hierfür eine Entscheidung zu treffen und für alle Geräte dann einheitlich vorzugehen. Die Ausgabe des Installationsskripts ist unten in der Abbildung dargestellt.

   Falls Sie sich genauer ansehen möchten, welche Änderungen an der Konfigurationsdatei Ihres Geräts vorgenommen werden, hilft Ihnen der [Abschnitt „Konfigurieren von IoT Edge auf Geräten“ in der Anleitung](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) weiter.

  ![Installation der Konfigurationspakete: Aktualisierung der „config.toml“-Dateien auf Ihrem Gerät und automatischer Neustart aller IoT Edge-Dienste](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Wenn Sie die obigen Schritte richtig ausgeführt haben, können Sie überprüfen, ob Ihre Geräte ordnungsgemäß konfiguriert sind.

Führen Sie die Konfigurations- und Konnektivitätsprüfungen auf Ihren Geräten aus. Für das **Gerät der obersten Ebene**:

   ```bash
   sudo iotedge check
   ```

Für das **Gerät der unteren Ebene** muss das Diagnoseimage manuell im Befehl übergeben werden:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:443/azureiotedge-diagnostics:1.2
   ```

Auf Ihrem **Gerät der obersten Ebene** sollte eine Ausgabe mit mehreren bestandenen Auswertungen angezeigt werden. Unter Umständen werden einige Warnungen zu Protokollrichtlinien und – je nach Netzwerk – DNS-Richtlinien angezeigt.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Wenn Sie überzeugt sind, dass die Konfigurationen auf den einzelnen Geräten richtig sind, können Sie fortfahren.

## <a name="deploy-modules-to-your-devices"></a>Bereitstellen von Modulen auf Ihren Geräten

Die Modulbereitstellungen für Ihre Geräte wurden automatisch generiert, als die Geräte erstellt wurden. Vom Tool `iotedge-config-cli` wurden JSON-Bereitstellungsdateien für die **Geräte der obersten und unteren Ebenen** bereitgestellt, nachdem diese erstellt wurden. Die Modulbereitstellung hat sich im ausstehenden Zustand befunden, während Sie die IoT Edge-Runtime auf den einzelnen Geräten konfiguriert haben. Nach dem Konfigurieren der Runtime wurde mit den Bereitstellungen auf dem **Gerät der obersten Ebene** begonnen. Nach Abschluss dieser Bereitstellungen konnte das **Gerät der unteren Ebene** das Modul **IoT Edge-API-Proxy** nutzen, um die erforderlichen Images zu pullen.

In [Azure Cloud Shell](https://shell.azure.com/) können Sie sich die JSON-Bereitstellungsdatei des **Geräts der obersten Ebene** ansehen, um zu ermitteln, welche Module auf Ihrem Gerät bereitgestellt wurden:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Zusätzlich zu den Runtimemodulen **IoT Edge-Agent** und **IoT Edge-Hub** erhält das **Gerät der obersten Ebene** auch die Module **Docker-Registrierung** und **IoT Edge-API-Proxy**.

Das Modul **Docker-Registrierung** verweist auf eine vorhandene Azure Container Registry-Instanz. In diesem Fall verweist `REGISTRY_PROXY_REMOTEURL` auf Microsoft Container Registry. Die **Docker-Registrierung** lauscht standardmäßig an Port 5000.

Das Modul **IoT Edge-API-Proxy** leitet HTTP-Anforderungen an andere Module weiter, um zu ermöglichen, dass Geräte der unteren Ebene Containerimages pullen oder Blobs in Speicher pushen. In diesem Tutorial erfolgt die Kommunikation hierbei über Port 443. In der Konfiguration ist festgelegt, dass Pull Requests für Docker-Containerimages an das Modul **Docker-Registrierung** an Port 5000 gesendet werden. Alle Anforderungen zum Hochladen in den Blobspeicher werden zudem an das Modul „AzureBlobStorageonIoTEdge“ an Port 11002 geleitet. Weitere Informationen zum Modul **IoT Edge-API-Proxy** und zu dessen Konfiguration finden Sie in der zugehörigen [Anleitung](how-to-configure-api-proxy-module.md).

Informationen dazu, wie Sie eine Bereitstellung dieser Art über das Azure-Portal oder Azure Cloud Shell erstellen, finden Sie in der Anleitung unter [Bereitstellen von Modulen auf Geräten oberer Ebenen](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

In [Azure Cloud Shell](https://shell.azure.com/) können Sie sich die JSON-Bereitstellungsdatei des **Geräts der unteren Ebene** ansehen, um zu ermitteln, welche Module auf Ihrem Gerät bereitgestellt wurden:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Unter `systemModules` ist angegeben, dass für die Runtimemodule des **Geräts der unteren Ebene** das Pullen von `$upstream:443` festgelegt ist (nicht von `mcr.microsoft.com` wie beim **Gerät der obersten Ebene**). Das **Gerät der unteren Ebene** sendet Anforderungen von Docker-Images an das Modul **IoT Edge-API-Proxy** an Port 443, weil die Images nicht direkt aus der Cloud gepullt werden können. Das andere Modul, das für das **Gerät der unteren Ebene** bereitgestellt wird (Modul **Simulierter Temperatursensor**), sendet seine Imageanforderung ebenfalls an `$upstream:443`.

Informationen dazu, wie Sie eine Bereitstellung dieser Art über das Azure-Portal oder Azure Cloud Shell erstellen, finden Sie in der Anleitung unter [Bereitstellen von Modulen auf Geräten unterer Ebenen](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Sie können den Status Ihrer Module mit dem folgenden Befehl anzeigen:

   ```azurecli
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Mit diesem Befehl werden alle gemeldeten edgeAgent-Eigenschaften ausgegeben. Hier sind einige hilfreiche Befehle angegeben, mit denen Sie den Status des Geräts überwachen können: *runtime status*, *runtime start time*, *runtime last exit time*, *runtime restart count*.

Sie können den Status Ihrer Module auch im [Azure-Portal](https://ms.portal.azure.com/) anzeigen. Navigieren Sie zum Abschnitt **IoT Edge** Ihrer IoT Hub-Instanz, um Ihre Geräte und Module anzuzeigen.

Wenn Sie mit Ihren Modulbereitstellungen zufrieden sind, können Sie fortfahren.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Mit dem von Ihnen gepushten **simulierten Temperatursensormodul** werden Beispielumgebungsdaten generiert. Es sendet Nachrichten mit Umgebungstemperatur und Luftfeuchtigkeit, Computertemperatur und Druck sowie dem Zeitstempel.

Sie können diese Nachrichten auch über [Azure Cloud Shell](https://shell.azure.com/) anzeigen:

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Problembehandlung

Führen Sie den Befehl `iotedge check` aus, um die Konfiguration zu überprüfen und die Problembehandlung durchzuführen.

Sie können `iotedge check` in einer geschachtelten Hierarchie ausführen. Dies ist auch möglich, wenn die untergeordneten Computer nicht über direkten Internetzugriff verfügen.

Wenn Sie `iotedge check` auf der unteren Ebene ausführen, versucht das Programm, das Image von der übergeordneten Ebene über Port 443 zu pullen.

```bash
sudo iotedge check --diagnostics-image-name $upstream:443/azureiotedge-diagnostics:1.2
```

Der Wert `azureiotedge-diagnostics` wird aus der Containerregistrierung gepullt, die mit dem Registrierungsmodul verknüpft ist. In diesem Tutorial ist er standardmäßig auf https://mcr.microsoft.com: festgelegt.

| Name | Wert |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Stellen Sie bei Verwendung einer privaten Containerregistrierung sicher, dass alle Images („IoTEdgeAPIProxy“, „edgeAgent“, „edgeHub“, „Simulierter Temperatursensor“ und „diagnostics“) in der Containerregistrierung vorhanden sind.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden.

So löschen Sie die Ressourcen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

2. Wählen Sie den Namen der Ressourcengruppe aus, die Ihre IoT Edge-Testressourcen enthält. 

3. Überprüfen Sie die Liste der Ressourcen in Ihrer Ressourcengruppe. Wenn Sie alle löschen möchten, klicken Sie auf **Ressourcengruppe löschen**. Wenn Sie nur einige Ressourcen löschen möchten, klicken Sie auf die jeweiligen Ressourcen, um sie einzeln zu löschen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie zwei IoT Edge-Geräte als Gateways konfiguriert und eins als übergeordnetes Gerät des anderen festgelegt. Anschließend haben Sie gezeigt, wie Sie ein Containerimage über ein Gateway auf das untergeordnete Gerät pullen, indem das Modul „IoT Edge-API-Proxy“ genutzt wird. Weitere Informationen finden Sie in der [Anleitung zur Nutzung des Proxymoduls](how-to-configure-api-proxy-module.md).

Weitere Informationen zur Verwendung von Gateways für die Erstellung von hierarchischen Ebenen mit IoT Edge-Geräten finden Sie in der [Anleitung zum Verbinden eines nachgeschalteten IoT Edge-Geräts](how-to-connect-downstream-iot-edge-device.md).

Sie können nun die anderen Tutorials bearbeiten, um noch mehr darüber zu erfahren, wie sich mit Azure IoT Edge weitere Lösungen für Ihr Unternehmen erstellen lassen.

> [!div class="nextstepaction"]
> [Bereitstellen eines Azure Machine Learning-Modells als Modul](tutorial-deploy-machine-learning.md)
