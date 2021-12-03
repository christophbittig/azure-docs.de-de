---
title: Erstellen Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe vom CLI
description: Erstellen Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe vom CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: c004ce9854d3a4397fed9064f90c345df5c04189
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561250"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-cli"></a>Erstellen Sie eine Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe vom CLI

In diesem Dokument werden die Schritte zum Erstellen einer PostgreSQL Hyperscale-Servergruppe in Azure Arc sowie das Herstellen einer Verbindung mit ihr beschrieben.

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

Weitere Informationen zu Sicherheitskontexteinschränkungen in OpenShift finden Sie in der [OpenShift-Dokumentation](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Fahren Sie mit dem nächsten Schritt fort.


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



|Sie benötigen Folgendes:   |Form der bereitzustellenden Servergruppe   |Zu verwendender `-w`-Parameter   |Hinweis   |
|---|---|---|---|
|Eine horizontal aufskalierte Form von Postgres, um die Skalierbarkeitsanforderungen Ihrer Anwendungen zu erfüllen.   |Drei oder mehr Postgres-Instanzen, eine ist Koordinator, n sind Worker, mit n >= 2.   |Verwenden Sie `-w n`, mit n >= 2.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine grundlegende Form von Postgres Hyperscale, mit der Sie Ihre Anwendung zu minimalen Kosten funktional überprüfen können. Es ist keine gültige Leistungs- und Skalierbarkeitsvalidierung damit möglich. Hierfür müssen Sie den oben beschriebenen Bereitstellungstyp verwenden.   |Eine Postgres-Instanz, die sowohl Koordinator als auch Worker ist.   |Verwenden Sie `-w 0`, und laden Sie die Citus-Erweiterung. Verwenden Sie bei der Bereitstellung über die Befehlszeile die folgenden Parameter: `-w 0` --extensions Citus.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine einfache Instanz von Postgres, die bei Bedarf horizontal aufskaliert werden kann.   |Eine Postgres-Instanz. Die Semantik für Koordinator und Worker ist ihr noch nicht bekannt. Um sie nach der Bereitstellung aufzuskalieren, bearbeiten Sie die Konfiguration, erhöhen Sie die Anzahl der Workerknoten und verteilen Sie die Daten.   |Verwenden Sie `-w 0`, oder geben Sie `-w` nicht an.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, ist in Ihrer Bereitstellung vorhanden, aber noch nicht geladen.   |
|   |   |   |   |

Auch wenn die Verwendung von `-w 1` funktioniert, empfehlen wir dies nicht. Diese Bereitstellung bietet Ihnen nicht viel Nutzen. Mit ihr erhalten Sie zwei Instanzen von Postgres: einen Koordinator und einen Worker. Mit diesem Setup werden die Daten tatsächlich nicht aufskaliert, da Sie einen einzelnen Worker bereitstellen. Daher erhalten Sie kein höheres Maß an Leistung und Skalierbarkeit. Diese Unterstützung für solche Bereitstellungen wird in einer zukünftigen Version entfernt.

- Die **Speicherklassen**, die von der Servergruppe verwendet werden sollen. Es ist wichtig, dass Sie die Speicherklasse direkt beim Bereitstellen einer Servergruppe festlegen, da diese nach der Bereitstellung nicht mehr geändert werden kann. Sie können die Speicherklassen angeben, die für die Daten, Protokolle und Sicherungen verwendet werden sollen. Wenn Sie keine Speicherklassen angeben, werden standardmäßig die Speicherklassen des Datencontrollers verwendet.
    - Um die Speicherklasse für die Daten festzulegen, geben Sie den Parameter `--storage-class-data` oder `-scd`, gefolgt vom Namen der Speicherklasse, an.
    - Um die Speicherklasse für die Protokolle festzulegen, geben Sie den Parameter `--storage-class-logs` oder `-scl`, gefolgt vom Namen der Speicherklasse, an.
    - Die Unterstützung für das Festlegen von Speicherklassen für die Sicherungen wurde vorübergehend entfernt, da wir die Sicherungs-/Wiederherstellungsfunktionen vorübergehend entfernt haben, während wir Entwürfe und Erfahrungen fertig stellen.

   > [!IMPORTANT]
   > Wenn Sie die Speicherklasse nach der Bereitstellung ändern müssen, extrahieren Sie die Daten, löschen Sie Ihre Servergruppe, erstellen Sie eine neue Servergruppe, und importieren Sie die Daten. 

Beim Ausführen des Erstellungsbefehls (create) werden Sie zur Eingabe des Kennworts des `postgres`-Standardadministrators aufgefordert. Der Name dieses Benutzers kann in dieser Vorschauversion nicht geändert werden. Sie können die interaktive Eingabeaufforderung überspringen, indem Sie die Sitzungsumgebungsvariable `AZDATA_PASSWORD` festlegen, bevor Sie den Erstellungsbefehl ausführen.

### <a name="examples"></a>Beispiele

**Führen Sie den folgenden Befehl aus, um eine Servergruppe mit Postgres-Version 12 namens „postgres01“ mit zwei Workerknoten bereitzustellen, die die gleichen Speicherklassen wie der Datencontroller verwendet**:

```azurecli
az postgres arc-server create -n postgres01 --workers 2 --k8s-namespace <namespace> --use-k8s
```

> [!NOTE]  
> - Wenn Sie den Datencontroller mithilfe der Sitzungsumgebungsvariablen `AZDATA_USERNAME` und `AZDATA_PASSWORD` in der gleichen Terminalsitzung bereitgestellt haben, werden die Werte für `AZDATA_PASSWORD` auch verwendet, um die PostgreSQL Hyperscale-Servergruppe bereitzustellen. Wenn Sie lieber ein anderes Kennwort verwenden möchten, können Sie entweder (1) den Wert für `AZDATA_PASSWORD` aktualisieren, (2) die Umgebungsvariable `AZDATA_PASSWORD` löschen oder (3) deren Wert löschen. Im letzteren Fall werden Sie beim Erstellen einer Servergruppe interaktiv zur Eingabe eines Kennworts aufgefordert.
> - Durch das Erstellen einer PostgreSQL Hyperscale-Servergruppe werden nicht sofort Ressourcen in Azure registriert. Im Rahmen des Uploads des [Ressourcenbestands](upload-metrics-and-logs-to-azure-monitor.md) oder der [Nutzungsdaten](view-billing-data-in-azure.md) in Azure, werden die Ressourcen in Azure erstellt, und Sie können Ihre Ressourcen im Azure-Portal anzeigen.


## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Auflisten der in Ihrem Arc-Datencontroller bereitgestellten PostgreSQL Hyperscale-Servergruppen

Führen Sie den folgenden Befehl aus, um die in Ihrem Arc-Datencontroller bereitgestellten PostgreSQL Hyperscale-Servergruppen aufzulisten:

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```


```output
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 2
  }
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Abrufen der Endpunkte zum Herstellen einer Verbindung mit den PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung

Führen Sie den folgenden Befehl aus, um die Endpunkte für eine PostgreSQL-Servergruppe anzuzeigen:

```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Beispiel:
```console
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@123.456.78.912:5432"
        },
        {
          "description": "Log Search Dashboard",
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://98.765.432.11:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

Sie können den Endpunkt der PostgreSQL-Instanz verwenden, um über Ihr bevorzugtes Tool eine Verbindung mit der PostgreSQL Hyperscale-Servergruppe herzustellen: [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin usw. In diesem Fall stellen Sie eine Verbindung mit dem Koordinatorknoten (der Koordinatorinstanz) her, der die Abfrage an die entsprechenden Workerknoten (Workerinstanzen) weiterleitet, wenn Sie verteilte Tabellen erstellt haben. Weitere Informationen finden Sie im Thema zu den [Konzepten von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](concepts-distributed-postgres-hyperscale.md).

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Besonderer Hinweis zu Azure-VM-Bereitstellungen

Wenn Sie eine Azure-VM verwenden, zeigt die Endpunkt-IP-Adresse die _öffentliche_ IP-Adresse nicht an. Verwenden Sie den folgenden Befehl, um die öffentliche IP-Adresse zu ermitteln:
```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```
Sie können die öffentliche IP-Adresse mit dem Port kombinieren, um Ihre Verbindung herzustellen.

Möglicherweise müssen Sie den Port der PostgreSQL Hyperscale-Servergruppe über das Netzwerksicherheitsgateway zur Verfügung stellen. Um Datenverkehr über das Netzwerksicherheitsgateway (NSG) zuzulassen, legen Sie eine Regel fest. Zum Festlegen einer Regel müssen Sie den Namen Ihres Netzwerksicherheitsgateways kennen. Sie bestimmen das Netzwerksicherheitsgateway mithilfe des folgenden Befehls:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Sobald Sie den Namen des Netzwerksicherheitsgateways kennen, können Sie mithilfe des folgenden Befehls eine Firewallregel hinzufügen. Mit den hier gezeigten Beispielwerten wird eine Netzwerksicherheitsgateway-Regel für Port 30655 erstellt und das Herstellen von Verbindungen über **beliebige** Quell-IP-Adressen zugelassen. 

> [!WARNING]
> Es wird nicht empfohlen, eine Regel festzulegen, um Verbindungen von einer beliebigen Quell-IP-Adresse zuzulassen. Sie können mehr Sicherheit gewährleisten, indem Sie einen `-source-address-prefixes`-Wert festlegen, der spezifisch für Ihre Client-IP-Adresse oder einen IP-Adressbereich gilt, der die IP-Adressen Ihres Teams oder Ihrer Organisation umfasst.

Ersetzen Sie den Wert des `--destination-port-ranges`-Parameters unten durch die Portnummer, die Sie aus dem Befehl `az postgres arc-server list` oben abgerufen haben.

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
