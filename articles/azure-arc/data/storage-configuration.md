---
title: Speicherkonfiguration
description: Hier werden die Speicherkonfigurationsoptionen für Azure Arc-fähige Datendienste erläutert.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: 503bfec47621e5663e6626e1285840625e92c46c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355116"
---
# <a name="storage-configuration"></a>Speicherkonfiguration


## <a name="kubernetes-storage-concepts"></a>Kubernetes-Speicherkonzepte

Kubernetes bietet eine Infrastrukturabstraktionsschicht über dem zugrunde liegenden Virtualisierungstechnikstapel (optional) und der Hardware. Kubernetes abstrahiert Speicher über **[Speicherklassen](https://kubernetes.io/docs/concepts/storage/storage-classes/)** . Wenn Sie einen Pod bereitstellen, können Sie für jedes Volume eine Speicherklasse angeben. Wenn dieser Pod also bereitgestellt wird, wird die Speicherklasse **[Provisioner](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** (Bereitsteller) zum Bereitstellen des Speichers aufgerufen, und dann wird ein **[persistentes Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** in diesem bereitgestellten Speicher bereitgestellt. Anschließend wird der Pod durch einen **[Anspruch auf persistentes Volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** in das persistente Volume eingebunden.

Kubernetes bietet Speicherinfrastrukturanbietern die Möglichkeit, Treiber (auch „Add-Ons“ bezeichnet) einzubinden, die Kubernetes erweitern. Speicher-Add-Ons müssen dem **[Container Storage Interface-Standard](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** entsprechen. Es gibt Dutzende von Add-Ons, die sich in dieser nicht endgültigen **[Liste der CSI-Treiber](https://kubernetes-csi.github.io/docs/drivers.html)** befinden. Die Wahl des CSI-Treibers hängt unter anderem davon ab, ob die Ausführung in einem cloudgehosteten Managed Kubernetes-Dienst erfolgen soll und welchen OEM-Anbieter Sie für die Hardware verwenden.

Mit dem folgenden Befehl können Sie anzeigen, welche Speicherklassen im Kubernetes-Cluster konfiguriert sind:

```console
kubectl get storageclass
```

Beispielausgabe eines AKS-Clusters (Azure Kubernetes Service):

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Sie können Details zu einer Speicherklasse abrufen, indem Sie den folgenden Befehl ausführen:

```console
kubectl describe storageclass/<storage class name>
```

Beispiel:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Sie können die derzeit bereitgestellten persistenten Volumes und Volumeansprüche anzeigen, indem Sie die folgenden Befehle ausführen:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Beispiel zum Anzeigen persistenter Volumes:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Beispiel zum Anzeigen persistenter Volumeansprüche:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Wichtige Faktoren beim Auswählen der Speicherkonfiguration

Die Auswahl der richtigen Speicherklasse ist für die Resilienz und Leistung von Daten wichtig. Wenn Sie die falsche Speicherklasse auswählen, kann dies dazu führen, dass Ihre Daten im Falle eines Hardwarefehlers gefährdet werden oder die Leistung verringert wird.

Im Allgemeinen stehen zwei Arten von Speicher zur Verfügung:

- **Lokaler Speicher**: Dieser Speicher wird auf lokalen Festplatten auf einem bestimmten Knoten bereitgestellt. Diese Art von Speicher kann sich in Bezug auf die Leistung als ideal erweisen, erfordert allerdings speziell das Entwerfen von Datenredundanz durch die knotenübergreifende Replikation der Daten.
- **Freigegebener Remotespeicher:** Dieser Speicher wird auf einem Remotespeichergerät wie SAN, NAS oder in einem Cloudspeicherdienst wie EBS oder Azure Files bereitgestellt. Diese Art von Speicher sorgt in der Regel für eine automatische Datenredundanz, ist jedoch nicht so schnell wie der lokale Speicher.

## <a name="nfs-based-storage-classes"></a>NFS-basierte Speicherklassen

Abhängig von der Konfiguration Ihres NFS-Servers und des Speicherklassenanbieters müssen Sie möglicherweise die `supplementalGroups` in den Podkonfigurationen für Datenbankinstanzen festlegen, und Sie müssen möglicherweise die NFS-Serverkonfiguration so ändern, dass die vom Client übergebenen Gruppen-IDs verwendet werden (im Gegensatz zum Nachschlagen von Gruppen-IDs auf dem Server mithilfe der übergebenen Benutzer-ID). Wenden Sie sich an Ihren NFS-Administrator, um herauszufinden, ob dies der Fall ist.

Die `supplementalGroups`-Eigenschaft akzeptiert ein Array von Werten und kann als Teil der Bereitstellung des Azure Arc-Datencontrollers festgelegt werden. Sie wird dann von allen Datenbankinstanzen verwendet, die vom Azure Arc-Datencontroller konfiguriert werden.

Führen Sie zum Festlegen dieser Eigenschaft den folgenden Befehl aus:

```azurecli
az arcdata dc config add --path custom/control.json --json-values 'spec.security.supplementalGroups="1234556"'
```

### <a name="data-controller-storage-configuration"></a>Konfiguration von Datencontrollerspeicher

Einige Dienste in Azure Arc für Datendienste sind abhängig davon, ob sie für die Verwendung von freigegebenem Remotespeicher konfiguriert sind, da die Dienste die Daten nicht replizieren können. Diese Dienste befinden sich in der Sammlung der Datencontrollerpods:

|**Service**|**Ansprüche auf persistente Volumes**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**SQL-Controllerinstanz**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Controller-API-Dienst**|`<namespace>/data-controller`|

Zum Zeitpunkt der Bereitstellung des Datencontrollers wird die für jedes dieser persistenten Volumes zu verwendende Speicherklasse entweder durch Übergeben des Parameters „--storage-class | -sc“ an den `az arcdata dc create`-Befehl oder durch Festlegen der Speicherklassen in der verwendeten Bereitstellungsvorlagendatei „control.json“ angegeben.  Wenn Sie das Azure-Portal verwenden, um den Datencontroller im direkten Konnektivitätsmodus zu erstellen, ist in der von Ihnen ausgewählten Bereitstellungsvorlage entweder die Speicherklasse vordefiniert, oder wenn Sie eine Vorlage auswählen, die keine vordefinierte Speicherklasse enthält, werden Sie zur Eingabe einer Speicherklasse aufgefordert.  Wenn Sie eine benutzerdefinierte Bereitstellungsvorlage verwenden, können Sie die Speicherklasse angeben.

Für die vorkonfigurierten Bereitstellungsvorlagen ist eine Standardspeicherklasse angegeben, die für die Zielumgebung geeignet ist. Sie kann aber während der Bereitstellung überschrieben werden. Führen Sie die ausführlichen Schritte zum [Ändern des Bereitstellungsprofils](create-data-controller.md) aus, um die Speicherklassenkonfiguration für Datencontrollerpods zum Zeitpunkt der Bereitstellung zu ändern.

Wenn Sie die Speicherklasse mithilfe des Parameters „--storage-class | -sc“ festlegen, wird die Speicherklasse sowohl für Protokoll- als auch für Datenspeicherklassen verwendet. Wenn Sie die Speicherklassen in der Bereitstellungsvorlagendatei festlegen, können Sie für Protokolle und Daten unterschiedliche Speicherklassen angeben.

Bei der Auswahl einer Speicherklasse für die Datencontrollerpods gilt es die folgenden wichtigen Faktoren zu berücksichtigen:

- Sie **müssen** eine freigegebene Remotespeicherklasse verwenden, um die Dauerhaftigkeit der Daten sicherzustellen. Außerdem kann so ein Pod oder Knoten nach einem Ausfall wieder eine Verbindung mit dem persistenten Volume herstellen.
- Da in der Regel nur geringe Datenmengen in die SQL-Controllerinstanz, Metrikdatenbank und Protokolldatenbank geschrieben werden, die zudem auch nicht wartezeitanfällig sind, ist kein extrem leistungsstarker Speicher erforderlich. Wenn Benutzer häufig die Grafana- und Kibana-Schnittstellen verwenden und Sie über eine große Anzahl von Datenbankinstanzen verfügen, profitieren die Benutzer möglicherweise von einem leistungsstarken Speicher.
- Die erforderliche Speicherkapazität ist in Abhängigkeit von der Anzahl der bereitgestellten Datenbankinstanzen variabel, da Protokolle und Metriken für jede Datenbankinstanz gesammelt werden. Daten werden für zwei (2) Wochen in den Protokoll- und Metrikdatenbanken aufbewahrt, bevor sie gelöscht werden. 
- Das Ändern der Speicherklasse nach der Bereitstellung ist schwierig, nicht dokumentiert und wird nicht unterstützt. Achten Sie darauf, die Speicherklasse zum Zeitpunkt der Bereitstellung ordnungsgemäß auszuwählen.

> [!NOTE]
> Wenn keine Speicherklasse angegeben wird, wird die Standardspeicherklasse verwendet. Pro Kubernetes-Cluster kann nur eine Standardspeicherklasse vorhanden sein. Sie können die [Standardspeicherklasse ändern](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Konfiguration des Datenbankinstanzspeichers

Jede Datenbankinstanz verfügt über persistente Daten-, Protokoll- und Sicherungsvolumes. Die Speicherklassen für diese persistenten Volumes können zum Zeitpunkt der Bereitstellung angegeben werden. Wenn keine Speicherklasse angegeben wird, wird die Standardspeicherklasse verwendet.

Wenn Sie mit `az sql mi-arc create` oder `az postgres arc-server create` eine Instanz erstellen, gibt es vier Parameter, die zum Festlegen der Speicherklassen verwendet werden können:

|Parametername bzw. Kurzname|Syntaxelemente|
|---|---|
|`--storage-class-data`, `-d`|Hiermit wird die Speicherklasse für alle Datendateien einschließlich Transaktionsprotokolldateien angegeben.|
|`--storage-class-logs`, `-g`|Hiermit wird die Speicherklasse für alle Protokolldateien angegeben.|
|`--storage-class-data-logs`|Hiermit wird die Speicherklasse für die Transaktionsprotokolldateien der Datenbank angegeben.|
|`--storage-class-backups`|Hiermit wird die Speicherklasse für alle Sicherungsdateien angegeben.|

In der folgenden Tabelle sind die Pfade im Azure SQL Managed Instance-Container aufgeführt, die dem persistenten Volume für Daten und Protokolle zugeordnet sind:

|Parametername bzw. Kurzname|Pfad im mssql-miaa-Container|BESCHREIBUNG|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt|Hier sind alle Verzeichnisse für die mssql-Installation und andere Systemprozesse enthalten. Das mssql-Verzeichnis enthält Standarddaten (einschließlich Transaktionsprotokolle) sowie Fehlerprotokoll- und Sicherungsverzeichnisse.|
|`--storage-class-logs`, `-g`|/var/log|Hier sind Verzeichnisse enthalten, die die Konsolenausgabe (stderr und stdout) und andere Protokollierungsinformationen von Prozessen innerhalb des Containers speichern.|

In der folgenden Tabelle sind die Pfade im PostgreSQL-Instanzcontainer aufgeführt, die dem persistenten Volume für Daten und Protokolle zugeordnet sind:

|Parametername bzw. Kurzname|Dies ist ein Pfad im Postgres-Container|BESCHREIBUNG|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt/postgresql|Hier sind die Daten- und Protokollverzeichnisse für die Postgres-Installation enthalten.|
|`--storage-class-logs`, `-g`|/var/log|Hier sind Verzeichnisse enthalten, die die Konsolenausgabe (stderr und stdout) und andere Protokollierungsinformationen von Prozessen innerhalb des Containers speichern.|

Jede Datenbankinstanz verfügt über ein separates persistentes Volume für Datendateien, Protokolle und Sicherungen. Dies bedeutet, dass die E/A-Vorgänge für jeden dieser Dateitypen getrennt werden, je nachdem, wie der Volumebereitsteller Speicher bereitstellt. Jede Datenbankinstanz verfügt über eigene persistente Volumeansprüche und Volumes.

Wenn in einer bestimmten Datenbankinstanz mehrere Datenbanken vorhanden sind, verwenden alle Datenbanken denselben Anspruch auf persistentes Volume, dasselbe persistente Volume und dieselbe Speicherklasse. Alle Sicherungen, d. h. sowohl unterschiedliche Protokollsicherungen als auch vollständige Sicherungen, verwenden denselben Anspruch auf persistentes Volume und dasselbe persistente Volume. Die persistenten Volumeansprüche für die Datenbankinstanzpods sind unten dargestellt:

|**Instanz**|**Ansprüche auf persistente Volumes**|
|---|---|
|**Verwaltete Azure SQL-Datenbank-Instanz**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure Database for PostgreSQL-Instanz**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL Hyperscale**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Ordinalzahl zwischen 0 und W, wobei W die Anzahl der Worker ist)*|

Bei der Auswahl einer Speicherklasse für die Datenbankinstanzpods gilt es die folgenden wichtigen Faktoren zu berücksichtigen:

- Datenbankinstanzen können entweder in einem einzelnen Podmuster oder in einem Multipodmuster bereitgestellt werden. Ein Beispiel für ein Einzelpodmuster ist eine Azure SQL Managed Instance-Instanz mit dem Tarif „Universell“. Ein Beispiel für ein Multipodmuster ist eine hochverfügbare Azure SQL Managed Instance-Instanz mit dem Tarif „Unternehmenskritisch“. Datenbankinstanzen, die mit dem Einzelpodmuster bereitgestellt wurden, **müssen** eine freigegebene Remotespeicherklasse verwenden, um die Dauerhaftigkeit der Daten sicherzustellen. Außerdem kann so ein Pod oder Knoten nach einem Ausfall wieder eine Verbindung mit dem persistenten Volume herstellen. Im Gegensatz dazu verwendet eine hochverfügbare Azure SQL Managed Instance-Instanz Always On-Verfügbarkeitsgruppen, um die Daten entweder synchron oder asynchron von einer Instanz in eine andere zu replizieren. Insbesondere in Fällen, in denen die Daten synchron repliziert werden, gibt es immer mehrere Kopien der Daten (in der Regel drei Kopien). Aus diesem Grund ist es möglich, lokalen Speicher oder freigegebene Remotespeicherklassen für Daten- und Protokolldateien zu verwenden. Bei Verwendung des lokalen Speichers bleiben die Daten auch bei einem fehlerhaften Pod, Knoten oder fehlerhafter Speicherhardware erhalten, weil mehrere Kopien der Daten vorhanden sind. Dank dieser Flexibilität können Sie den lokalen Speicher nutzen, um die Leistung zu verbessern.
- Die Datenbankleistung ist größtenteils eine Funktion des E/A-Durchsatzes eines bestimmten Speichergeräts. Wenn bei Ihrer Datenbank sehr umfangreiche Lese- oder Schreibvorgänge vorkommen, sollten Sie eine Speicherklasse auswählen, die über Hardware verfügt, die für diese Art von Workload konzipiert ist. Wenn Ihre Datenbank z. B. größtenteils für Schreibvorgänge verwendet wird, können Sie den lokalen Speicher mit RAID 0 auswählen. Wenn Ihre Datenbank hauptsächlich für Lesevorgänge mit einer geringen Menge von „heißen Daten“ verwendet wird, allerdings eine große Gesamtspeichermenge an „kalten Daten“ vorliegt, können Sie ein SAN-Gerät auswählen, das einen mehrstufigen Speicher zur Verfügung stellt. Die Auswahl der richtigen Speicherklasse unterscheidet sich nicht von der Auswahl der Speicherart, die Sie für eine beliebige Datenbank verwenden würden.
- Stellen Sie bei Verwendung eines lokalen Speichervolumebereitstellers sicher, dass die lokalen Volumes, die für Daten, Protokolle und Sicherungen bereitgestellt werden, jeweils auf unterschiedlichen zugrunde liegenden Speichergeräten gespeichert werden, um Konflikte bei Datenträger-E/A-Vorgängen zu vermeiden. Das Betriebssystem sollte sich auch auf einem Volume befinden, das auf mindestens einem separaten Datenträger bereitgestellt wird. Dabei handelt es sich im Wesentlichen um denselben Leitfaden, der für eine Datenbankinstanz auf physischer Hardware befolgt wird.
- Da alle Datenbanken auf einer bestimmten Instanz einen Anspruch auf persistentes Volume und ein persistentes Volume aufweisen, sollten Sie sicherstellen, dass Sie keine ausgelasteten Datenbankinstanzen in derselben Datenbankinstanz zusammenstellen. Trennen Sie die ausgelasteten Datenbanken ggf. in ihren eigenen Datenbankinstanzen, um E/A-Konflikte zu vermeiden. Verwenden Sie außerdem Knotenbezeichnungsziele, um Datenbankinstanzen auf separaten Knoten zu speichern und somit den gesamten E/A-Datenverkehr auf mehrere Knoten zu verteilen. Wenn Sie die Virtualisierung verwenden, sollten Sie in Erwägung ziehen, nicht nur den E/A-Datenverkehr auf Knotenebene, sondern auch die kombinierten E/A-Aktivitäten zu verteilen, die von allen Knoten-VMs auf einem bestimmten physischen Host ausgeführt werden.

## <a name="estimating-storage-requirements"></a>Schätzen der Speicheranforderungen
Jeder Pod, der zustandsbehaftete Daten enthält, verwendet mindestens zwei persistente Volumes: ein persistentes Volume für Daten und ein anderes persistentes Volume für Protokolle. In der folgenden Tabelle ist die Anzahl der persistenten Volumes aufgelistet, die für einen einzelnen Datencontroller, eine Azure SQL Managed Instance-, eine Azure Database for PostgreSQL- und eine Azure PostgreSQL Hyperscale-Instanz erforderlich sind:

|Ressourcentyp|Anzahl zustandsbehafteter Pods|Erforderliche Anzahl von persistenten Volumes|
|---|---|---|
|Datencontroller|4 (`control`, `controldb`, `logsdb`, `metricsdb`)|4 * 2 = 8|
|Verwaltete Azure SQL-Instanz|1|2|
|Azure Database for PostgreSQL-Instanz|1| 2|
|Azure PostgreSQL Hyperscale|1 + W (W = Anzahl der Worker)|2 * (1 + W)|

In der folgenden Tabelle ist die Gesamtanzahl der persistenten Volumes angegeben, die für eine Beispielbereitstellung erforderlich sind:

|Ressourcentyp|Anzahl von Instanzen|Erforderliche Anzahl von persistenten Volumes|
|---|---|---|
|Datencontroller|1|4 * 2 = 8|
|Verwaltete Azure SQL-Instanz|5|5 * 2 = 10|
|Azure Database for PostgreSQL-Instanz|5| 5 * 2 = 10|
|Azure PostgreSQL Hyperscale|2 (Anzahl der Worker = 4 pro Instanz)|2 * 2 * (1 + 4) = 20|
|***Gesamtanzahl persistenter Volumes***||8 + 10 + 10 + 20 = 48|

Diese Berechnung kann verwendet werden, um den Speicher für den Kubernetes-Cluster auf Grundlage des Speicherbereitstellers oder der Umgebung zu planen. Wenn beispielsweise die lokale Speicherbereitstellung für einen Kubernetes-Cluster mit fünf (5) Knoten verwendet wird, ist in Bezug auf die Beispielbereitstellung oben für jeden Knoten mindestens ein Speicher für zehn (10) persistente Volumes erforderlich. Dies gilt auch für die Bereitstellung eines Azure Kubernetes Service-Clusters (AKS) mit fünf (5) Knoten, bei der eine geeignete VM-Größe für den Knotenpool ausgewählt wird, sodass zehn (10) Datenträger angefügt werden können. Weitere Informationen zum Festlegen der Größe der Knoten im Zusammenhang mit den Speicheranforderungen für AKS-Knoten finden Sie [hier](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Auswählen der richtigen Speicherklasse

### <a name="on-premises-and-edge-sites"></a>Lokale und Edgewebsites

Microsoft und seine OEM-, Betriebssystem- und Kubernetes-Partner verfügen über ein Validierungsprogramm für Azure Arc Data Services. Dieses Programm bietet Kunden vergleichbare Testergebnisse von einem Zertifizierungstesttoolkit. In den Tests werden die Featurekompatibilität, Belastungstestergebnisse sowie die Leistung und Skalierbarkeit bewertet. Jedes dieser Testergebnisse gibt das Betriebssystem, die Kubernetes-Distribution, die Hardware, das CSI-Add-On und die Speicherklassen an, die jeweils verwendet wurden. Dies hilft Kunden dabei, in Abhängigkeit der jeweiligen Anforderungen die beste Wahl hinsichtlich der Speicherklasse, des Betriebssystems, der Kubernetes-Distribution und der Hardware zu treffen. Weitere Informationen zu diesem Programm und den Testergebnissen finden Sie [hier](validation-program.md).

#### <a name="public-cloud-managed-kubernetes-services"></a>Öffentliche Clouddienste und Managed Kubernetes-Dienste

In Bezug auf Managed Kubernetes-Dienste, die auf der öffentlichen Cloud basieren, wird Folgendes empfohlen:

|Öffentlicher Clouddienst|Empfehlung|
|---|---|
|**Azure Kubernetes Service (AKS)**|Azure Kubernetes Service (AKS) verfügt über zwei Arten von Speicher: Azure Files und Azure Managed Disks. Jeder Speichertyp umfasst zwei Preis- bzw. Leistungsstufen: Standard (HDD) und Premium (SSD). Die vier in AKS bereitgestellten Speicherklassen sind also `azurefile` (Azure Files-Tarif „Standard“), `azurefile-premium` (Azure Files-Tarif „Premium“), `default` (Azure Managed Disks-Tarif „Standard“) und `managed-premium` (Azure Managed Disks-Tarif „Premium“). Die Standardspeicherklasse ist `default` (Azure Managed Disks-Tarif „Standard“). Es gibt erhebliche **[Preisunterschiede](https://azure.microsoft.com/pricing/details/storage/)** zwischen den Typen und Tarifen, die bei Ihrer Entscheidung berücksichtigt werden sollten. Für Produktionsworkloads mit leistungsstarken Anforderungen empfiehlt es sich, `managed-premium` für alle Speicherklassen zu verwenden. Für Dev/Test-Workloads, Machbarkeitsstudien und ähnliche Fälle ist `azurefile` die kostengünstigste Option, da hier die Kosten berücksichtigt werden. Alle vier Optionen können für Situationen verwendet werden, für die ein freigegebener Remotespeicher erforderlich ist, da es sich bei allen um mit dem Netzwerk verbundene Speichergeräte in Azure handelt. Weitere Informationen finden Sie unter [AKS-Speicher](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Elastic Kubernetes Service von Amazon verfügt über eine primäre Speicherklasse, die auf dem [EBS CSI-Speichertreiber](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html) basiert. Dies wird für Produktionsworkloads empfohlen. Der neue [EFS CSI-Speichertreiber](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) kann zu einem EKS-Cluster hinzugefügt werden, befindet sich zurzeit aber in einer Betaphase und kann möglicherweise noch geändert werden. Obwohl AWS besagt, dass dieser Speichertreiber für die Produktion unterstützt wird, empfiehlt es sich nicht, ihn zu verwenden, da es sich um eine Betaversion handelt und weiterhin Änderungen daran vorgenommen werden können. Die EBS-Speicherklasse ist die Standardeinstellung und wird als `gp2` bezeichnet. Weitere Informationen finden Sie unter [EKS-Speicher](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) verfügt nur über eine Speicherklasse namens `standard`, die für [persistente GCE-Datenträger](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk) verwendet wird. Da dies die einzige Option ist, handelt es sich auch um die Standardeinstellung. Obwohl es einen [lokalen, statischen Volumebereitsteller](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) für GKE gibt, den Sie mit direkt angefügten SSD-Datenträgern verwenden können, empfiehlt sich dessen Verwendung nicht, da er nicht von Google verwaltet oder unterstützt wird. Weitere Informationen finden Sie unter [GKE-Speicher](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
