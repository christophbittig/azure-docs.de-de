---
title: Erstellen Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe vom CLI
description: Erstellen Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe vom CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c9987e82fe64dd30584f3ceb8dbacfc857d27ab8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779385"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Erstellen einer Azure Arc-fähigen Servergruppe für PostgreSQL Hyperscale

In diesem Dokument werden die Schritte zur Erstellung einer PostgreSQL Hyperscale-Servergruppe in Azure Arc beschrieben.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Erste Schritte
Wenn Sie bereits mit den folgenden Themen vertraut sind, können Sie diesen Absatz überspringen.
Dabei handelt es sich um wichtige Themen, die Sie lesen sollten, bevor Sie mit dem Erstellen beginnen:
- [Übersicht über Azure Arc-fähige Datendienste](overview.md)
- [Konnektivitätsmodi und -anforderungen](connectivity.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Wenn Sie es vorziehen, dies auszuprobieren, ohne selbst eine vollständige Umgebung bereitzustellen, können Sie mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM einsteigen.


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Vorläufiger und temporärer Schritt nur für OpenShift-Benutzer
Implementieren Sie diesen Schritt, bevor Sie mit dem nächsten fortfahren. Sie müssen die folgenden Befehle zum Updaten der Sicherheitseinschränkungen für Ihren Cluster ausführen, um eine PostgreSQL Hyperscale-Servergruppe in Red Hat OpenShift in einem Projekt bereitzustellen, das nicht dem Standardprojekt entspricht. Dieser Befehl gewährt den Dienstkonten, unter denen Ihre PostgreSQL Hyperscale-Servergruppe ausgeführt wird, die erforderlichen Berechtigungen. Die Sicherheitskontexteinschränkung arc-data-scc haben Sie hinzugefügt, als Sie den Azure Arc-Datencontroller bereitgestellt haben.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name entspricht dem Namen der Servergruppe, die Sie im nächsten Schritt erstellen.**

Weitere Informationen zu Sicherheitskontexteinschränkungen in OpenShift finden Sie in der [OpenShift-Dokumentation](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Sie können nun mit dem nächsten Schritt fortfahren.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Erstellen einer Azure Arc-fähigen Servergruppe für PostgreSQL Hyperscale

Um eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung auf Ihrem Arc-Datencontroller zu erstellen, verwenden Sie den Befehl `az postgres arc-server create`, an den Sie mehrere Parameter übergeben.

Details zu allen Parametern, die Sie bei der Erstellung festlegen können, finden Sie in der Ausgabe des Befehls:
```azurecli
az postgres arc-server create --help
```

Folgende Hauptparameter sollten Sie berücksichtigen:
- **Name der Servergruppe**, die Sie bereitstellen möchten. Geben Sie entweder `--name` oder `-n` an, gefolgt von einem Namen mit maximal 11 Zeichen.

- **Version der PostgreSQL-Engine**, die Sie bereitstellen möchten (die Standardeinstellung ist Version 12). Zum Bereitstellen von Version 12 können Sie entweder diesen Parameter weglassen oder einen der folgenden Parameter übergeben: `--engine-version 12` oder `-ev 12`. Zum Bereitstellen von Version 11 geben Sie `--engine-version 11` oder `-ev 11` an.

- **Anzahl von Workerknoten**, die Sie zum Aufskalieren und ggf. zum Verbessern der Leistung bereitstellen möchten. Lesen Sie die [Konzepte zu Postgres Hyperscale](concepts-distributed-postgres-hyperscale.md), bevor Sie fortfahren. Verwenden Sie zum Angeben der Anzahl von Workerknoten, die bereitgestellt werden sollen, den Parameter `--workers` oder `-w` gefolgt von einer ganzen Zahl. Die folgende Tabelle gibt den Bereich der unterstützten Werte und die Form der Postgres-Bereitstellung an, die Sie damit jeweils erhalten. Wenn Sie z. B. eine Servergruppe mit zwei Workerknoten bereitstellen möchten, geben Sie `--workers 2` oder `-w 2` an. Dadurch werden drei Pods erstellt: einer für den Koordinatorknoten/die Koordinatorinstanz und zwei für die Workerknoten/Workerinstanzen (einer für jeden Worker).



|Sie benötigen Folgendes:   |Form der bereitzustellenden Servergruppe   |zu verwendender -w-Parameter   |Hinweis   |
|---|---|---|---|
|Eine horizontal aufskalierte Form von Postgres, um die Skalierbarkeitsanforderungen Ihrer Anwendungen zu erfüllen.   |3 oder mehr Postgres-Instanzen, 1 ist Koordinator, n sind Worker mit n >= 2.   |Verwenden Sie „-w n“ mit n >= 2.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine grundlegende Form von Postgres Hyperscale, mit der Sie Ihre Anwendung zu minimalen Kosten funktional überprüfen können. Es ist keine gültige Leistungs- und Skalierbarkeitsvalidierung damit möglich. Hierfür müssen Sie den oben beschriebenen Bereitstellungstyp verwenden.   |1 Postgres-Instanz, die sowohl Koordinator als auch Worker ist.   |Verwenden Sie „-w 0“, und laden Sie die Citus-Erweiterung. Verwenden Sie bei der Bereitstellung über die Befehlszeile die folgenden Parameter: -w 0 --extensions Citus.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine einfache Instanz von Postgres, die bei Bedarf horizontal aufskaliert werden kann.   |1 Postgres-Instanz. Die Semantik für Koordinator und Worker ist ihr noch nicht bekannt. Um sie nach der Bereitstellung aufzuskalieren, bearbeiten Sie die Konfiguration, erhöhen Sie die Anzahl der Workerknoten und verteilen Sie die Daten.   |Verwenden Sie „-w 0“, oder geben Sie den -w-Parameter nicht an.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, ist in Ihrer Bereitstellung vorhanden, aber noch nicht geladen.   |
|   |   |   |   |

Obwohl die Verwendung von „-w 1“ funktioniert, wird dies nicht empfohlen. Diese Bereitstellung bietet Ihnen nicht viel Nutzen. Sie umfasst 2 Instanzen von Postgres: 1 Koordinator und 1 Worker. In dieser Konfiguration werden die Daten tatsächlich nicht aufskaliert, da Sie einen einzelnen Worker bereitstellen. Daher erhalten Sie kein höheres Maß an Leistung und Skalierbarkeit. Diese Unterstützung für solche Bereitstellungen wird in einer zukünftigen Version entfernt.

- **Speicherklassen**, die von der Servergruppe verwendet werden sollen. Es ist wichtig, dass Sie die Speicherklasse direkt beim Bereitstellen einer Servergruppe festlegen, da diese nach der Bereitstellung nicht mehr geändert werden kann. Um die Speicherklasse nach der Bereitstellung zu ändern, müssten Sie die Daten extrahieren, Ihre Servergruppe löschen, eine neue Servergruppe erstellen und die Daten importieren. Sie können die Speicherklassen angeben, die für die Daten, Protokolle und Sicherungen verwendet werden sollen. Wenn Sie keine Speicherklassen angeben, werden standardmäßig die Speicherklassen des Datencontrollers verwendet.
    - Um die Speicherklasse für die Daten festzulegen, geben Sie den Parameter `--storage-class-data` oder `-scd` gefolgt vom Namen der Speicherklasse an.
    - Um die Speicherklasse für die Protokolle festzulegen, geben Sie den Parameter `--storage-class-logs` oder `-scl` gefolgt vom Namen der Speicherklasse an.
    - Um die Speicherklasse für die Sicherungen festzulegen: In dieser Vorschauversion von PostgreSQL Hyperscale mit Azure Arc-Unterstützung gibt es je nach Sicherungs-/Wiederherstellungsvorgängen, die Sie ausführen möchten, zwei Möglichkeiten zum Festlegen von Speicherklassen. Wir arbeiten daran, diese Funktion zu vereinfachen. Sie geben entweder eine Speicherklasse oder eine Volumeanspruchseinbindung an. Bei einer Volumeanspruchseinbindung handelt es sich um ein durch einen Doppelpunkt getrenntes Paar, das aus einem Anspruch auf ein vorhandenes persistentes Volume (Persistent Volume Claim, PVC) im selben Namespace und dem Volumetyp (sowie je nach Volumetyp optionalen Metadaten) besteht. Das persistente Volume wird in jedem Pod für die PostgreSQL-Servergruppe eingebunden.
        - Wenn Sie nur vollständige Datenbankwiederherstellungen ausführen möchten, geben Sie den Parameter `--storage-class-backups` oder `-scb` gefolgt vom Namen der Speicherklasse an.
        - Wenn Sie sowohl vollständige Datenbankwiederherstellungen als auch Zeitpunktwiederherstellungen ausführen möchten, geben Sie den Parameter `--volume-claim-mounts` oder `--volume-claim-mounts` gefolgt vom Namen eines Volumeanspruchs und eines Volumetyps an.

Beim Ausführen des Erstellungsbefehls werden Sie zur Eingabe des Kennworts des `postgres`-Standardadministrators aufgefordert. Der Name dieses Benutzers kann in dieser Vorschauversion nicht geändert werden. Sie können die interaktive Eingabeaufforderung überspringen, indem Sie die Sitzungsumgebungsvariable `AZDATA_PASSWORD` festlegen, bevor Sie den Erstellungsbefehl ausführen.

### <a name="examples"></a>Beispiele

**Führen Sie den folgenden Befehl aus, um eine Servergruppe mit Postgres-Version 12 namens „postgres01“ mit zwei Workerknoten bereitzustellen, die die gleichen Speicherklassen wie der Datencontroller verwendet:**
```azurecli
az postgres arc-server create -n postgres01 --workers 2 --k8s-namespace <namespace> --use-k8s
```

**Führen Sie die folgenden Schritte aus, um eine Servergruppe mit Postgres-Version 12 namens „postgres01“ mit zwei Workerknoten bereitzustellen, die die gleichen Speicherklassen wie der Datencontroller für Daten und Protokolle, aber eine bestimmte Speicherklasse für die Ausführung von vollständigen Wiederherstellungen und Zeitpunktwiederherstellungen verwendet:**

 In diesem Beispiel wird davon ausgegangen, dass die Servergruppe in einem AKS-Cluster (Azure Kubernetes Service) gehostet wird. In diesem Beispiel wird der Speicherklassenname „azurefile-premium“ verwendet. Sie können das folgende Beispiel entsprechend Ihrer Umgebung anpassen. Beachten Sie, dass für diese Konfiguration **accessModes ReadWriteMany** erforderlich ist.  

Erstellen Sie zunächst eine YAML-Datei (z. B. mit dem Namen „CreateBackupPVC.yml“), die die unten stehende Beschreibung des Sicherungs-PVC (Persistent Volume Claim) enthält:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Erstellen Sie anschließend einen PVC mit der in der YAML-Datei gespeicherten Definition:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Erstellen Sie als Nächstes die Servergruppe:

```azurecli
az postgres arc-server create -n postgres01 --workers 2 --volume-claim-mounts backup-pvc:backup --k8s-namespace <namespace> --use-k8s
```

> [!IMPORTANT]
> - Lesen Sie die [aktuellen Einschränkungen im Zusammenhang mit der Sicherung und Wiederherstellung](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Wenn Sie den Datencontroller mithilfe der Sitzungsumgebungsvariablen `AZDATA_USERNAME` und `AZDATA_PASSWORD` in der gleichen Terminalsitzung bereitgestellt haben, werden die Werte für `AZDATA_PASSWORD` auch verwendet, um die PostgreSQL Hyperscale-Servergruppe bereitzustellen. Wenn Sie lieber ein anderes Kennwort verwenden möchten, können Sie entweder (1) den Wert für `AZDATA_PASSWORD` aktualisieren, (2) die Umgebungsvariable `AZDATA_PASSWORD` löschen oder (3) deren Wert löschen. Im letzteren Fall werden Sie beim Erstellen einer Servergruppe interaktiv zur Eingabe eines Kennworts aufgefordert.
> - Durch das Erstellen einer PostgreSQL Hyperscale-Servergruppe werden nicht sofort Ressourcen in Azure registriert. Im Rahmen des Uploads des [Ressourcenbestands](upload-metrics-and-logs-to-azure-monitor.md) oder der [Nutzungsdaten](view-billing-data-in-azure.md) in Azure, werden die Ressourcen in Azure erstellt, und Sie können Ihre Ressourcen im Azure-Portal anzeigen.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Auflisten der in Ihrem Arc-Datencontroller bereitgestellten PostgreSQL Hyperscale-Servergruppen

Führen Sie den folgenden Befehl aus, um die in Ihrem Arc-Datencontroller bereitgestellten PostgreSQL Hyperscale-Servergruppen aufzulisten:

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Abrufen der Endpunkte zum Herstellen einer Verbindung mit den PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung

Führen Sie den folgenden Befehl aus, um die Endpunkte für eine PostgreSQL-Servergruppe anzuzeigen:

```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Beispiel:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Sie können den Endpunkt der PostgreSQL-Instanz verwenden, um über Ihr bevorzugtes Tool eine Verbindung mit der PostgreSQL Hyperscale-Servergruppe herzustellen: [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin usw. In diesem Fall stellen Sie eine Verbindung mit dem Koordinatorknoten (Koordinatorinstanz) her, der die Abfrage an die entsprechenden Workerknoten (Workerinstanzen) weiterleitet, wenn Sie verteilte Tabellen erstellt haben. Weitere Informationen finden Sie im Thema zu den [Konzepten von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](concepts-distributed-postgres-hyperscale.md).

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Besonderer Hinweis zu Azure-VM-Bereitstellungen

Wenn Sie eine Azure-VM verwenden, zeigt die Endpunkt-IP-Adresse die _öffentliche_ IP-Adresse nicht an. Verwenden Sie den folgenden Befehl, um die öffentliche IP-Adresse zu ermitteln:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sie können die öffentliche IP-Adresse mit dem Port kombinieren, um Ihre Verbindung herzustellen.

Möglicherweise müssen Sie den Port der PostgreSQL Hyperscale-Servergruppe über das Netzwerksicherheitsgateway zur Verfügung stellen. Sie müssen eine Regel hinzufügen, um Datenverkehr über das Netzwerksicherheitsgateway zuzulassen. Hierzu können Sie den folgenden Befehl verwenden:

Zum Festlegen einer Regel müssen Sie den Namen Ihres Netzwerksicherheitsgateways kennen. Sie bestimmen das Netzwerksicherheitsgateway mithilfe des folgenden Befehls:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Sobald Sie den Namen des Netzwerksicherheitsgateways kennen, können Sie mithilfe des folgenden Befehls eine Firewallregel hinzufügen. Mit den hier gezeigten Beispielwerten wird eine Netzwerksicherheitsgateway-Regel für Port 30655 erstellt und das Herstellen von Verbindungen über **beliebige** Quell-IP-Adressen zugelassen.  Dies ist keine bewährte Methode hinsichtlich der Sicherheit!  Sie können mehr Sicherheit gewährleisten, indem Sie einen Wert „-source-address-prefixes“ festlegen, der spezifisch für Ihre Client-IP-Adresse oder einen IP-Adressbereich gilt, der die IP-Adressen Ihres Teams oder Ihrer Organisation umfasst.

Ersetzen Sie den Wert des Parameters „--destination-port-ranges“ unten durch die Portnummer, die Sie zuvor mithilfe des Befehls „az postgres arc-server list“ abgerufen haben.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Herstellen einer Verbindung mit Azure Data Studio

Öffnen Sie Azure Data Studio, und stellen Sie mit der Endpunkt-IP-Adresse und der Portnummer oben eine Verbindung mit Ihrer Instanz her. Verwenden Sie hierzu das Kennwort, das Sie beim Erstellen der Instanz festgelegt haben.  Wenn PostgreSQL nicht im Dropdownmenü *Verbindungstyp* verfügbar ist, können Sie die PostgreSQL-Erweiterung installieren, indem Sie in der Registerkarte „Erweiterungen“ nach „PostgreSQL“ suchen.

> [!NOTE]
> Sie müssen im Verbindungsbereich auf die Schaltfläche „[Advanced]“ (Erweitert) klicken, um die Portnummer einzugeben.

Denken Sie daran, dass Sie die _öffentliche_ IP-Adresse benötigen, auf die Sie mithilfe des folgenden Befehls zugreifen können, wenn Sie eine Azure-VM verwenden:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Herstellen einer Verbindung mit psql

Übergeben Sie den externen Endpunkt der PostgreSQL Hyperscale-Servergruppe, den Sie oben abgerufen haben, um auf Ihre PostgreSQL Hyperscale-Servergruppe zuzugreifen:

Sie können nun eine Verbindung mit psql herstellen:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Nächste Schritte

- Verbinden mit Ihrem PostgreSQL Hyperscale mit Azure Arc-Unterstützung: Lesen Sie hierzu [Verbindungsendpunkte und Verbindungsstrings abrufen](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und so von einer besseren Leistung zu profitieren:
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

- [Aufskalieren der PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung](scale-out-in-postgresql-hyperscale-server-group.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Erweitern von Ansprüchen persistenter Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
