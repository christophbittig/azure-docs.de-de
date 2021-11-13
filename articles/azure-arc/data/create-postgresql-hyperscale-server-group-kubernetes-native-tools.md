---
title: Erstellen einer PostgreSQL Hyperscale-Servergruppe mit Kubernetes-Tools
description: Erstellen einer PostgreSQL Hyperscale-Servergruppe mit Kubernetes-Tools
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 20214832a79dcf22bd14f6c6594f37a7036669fe
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558856"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Erstellen einer PostgreSQL Hyperscale-Servergruppe mit Kubernetes-Tools

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

Einen [Azure Arc-Datencontroller](./create-data-controller.md) sollten Sie bereits erstellt haben.

Zum Erstellen einer PostgreSQL Hyperscale-Servergruppe mithilfe von Kubernetes-Tools müssen die Kubernetes-Tools installiert sein.  In den Beispielen in diesem Artikel wird `kubectl` verwendet, allerdings können auch ähnliche Ansätze mit anderen Kubernetes-Tools verfolgt werden, z. B. dem Kubernetes-Dashboard, `oc` oder `helm`, wenn Sie mit diesen Tools und Kubernetes-YAML/JSON-Dateien vertraut sind.

[Installieren des kubectl-Tools](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Übersicht

Um eine PostgreSQL-Hyperscale-Servergruppe zu erstellen, müssen Sie ein Kubernetes-Geheimnis erstellen, um Ihr Postgres-Administrator-Login und -Passwort sicher zu speichern, sowie eine benutzerdefinierte PostgreSQL-Hyperscale-Servergruppen-Ressource, die auf den benutzerdefinierten Ressourcendefinitionen _postgresqls_ basiert.

## <a name="create-a-yaml-file"></a>Erstellen einer YAML-Datei

Sie können die [YAML-Vorlagedatei](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) als Ausgangspunkt verwenden, um Ihre eigene benutzerdefinierte YAML-Datei für die PostgreSQL Hyperscale-Servergruppe zu erstellen.  Laden Sie diese Datei auf Ihren Computer herunter, und öffnen Sie sie in einem Text-Editor.  Es ist hilfreich, einen Text-Editor wie [VS Code](https://code.visualstudio.com/download) zu verwenden, der Syntaxhervorhebung und Linten für YAML-Dateien unterstützen.

**Beispiel yaml-Datei**:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1beta1
kind: postgresql
metadata:
  name: pg1
spec:
  engine:
    version: 12
    extensions:
    - name: citus
  scale:
    workers: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer # Modify service type based on your Kubernetes environment
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>Anpassen des Anmeldenamens und des Kennworts.
Ein Kubernetes-Geheimnis wird als Base64-codierte Zeichenfolge gespeichert – eine für den Benutzernamen und eine für das Kennwort.  Sie müssen einen Administrator-Login und ein Passwort mit base64 kodieren und in den Platzhaltern `data.password` und `data.username` unterbringen.  Nehmen Sie nicht die Symbole `<` und `>` mit auf, die in der Vorlage vorhanden sind.

Sie können ein Onlinetool verwenden, um den gewünschten Benutzernamen und das zugehörige Kennwort mit einer Base64-Codierung zu versehen, oder Sie können abhängig von Ihrer Plattform integrierte CLI-Tools verwenden.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>Anpassen des Namens

Die Vorlage hat einen Wert von `pg1` für das Attribut name.  Sie können diesen Wert ändern, aber es müssen Zeichen sein, die den DNS-Namensstandards entsprechen. Wenn Sie den Namen ändern, müssen Sie auch den Namen des Geheimnisses entsprechend ändern.  Wenn Sie zum Beispiel den Namen der PostgreSQL Hyperscale-Servergruppe in `pg2` ändern, müssen Sie den Namen des Geheimnisses von `pg1-login-secret` in `pg2-login-secret` ändern.

### <a name="customizing-the-engine-version"></a>Anpassen der Engine-Version

Sie können die Engine-Version in postgresql-11 oder postgresql-12 ändern, indem Sie das `kind`-Attribut bearbeiten.

### <a name="customizing-the-resource-requirements"></a>Anpassen der Ressourcenanforderungen

Sie können die Ressourcenanforderungen (Limits und Anforderungen für RAM und Kerne) je nach Bedarf ändern.  

> [!NOTE]
> Weitere Informationen zur [Kubernetes-Ressourcengovernance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Anforderungen für Ressourcenlimits und -anforderungen:
- Der Grenzwert für die Anzahl von Kernen ist aus Abrechnungsgründen **obligatorisch**.
- Der Rest der Ressourcenanforderungen und -limits ist optional.
- Das Limit und die Anforderung für Kerne müssen ein positiver ganzzahliger Wert sein, falls angegeben.
- Für die Anforderung von Kernen ist mindestens ein Kern erforderlich, sofern angegeben.
- Das Format des Arbeitsspeicherwerts folgt der Kubernetes-Notation.  

### <a name="customizing-service-type"></a>Anpassen des Diensttyps

Der Diensttyp kann bei Bedarf in „NodePort“ geändert werden.  Es wird eine zufällige Portnummer zugewiesen.

### <a name="customizing-storage"></a>Anpassen von Speicher

Sie können die Speicherklassen für Speicher so anpassen, dass sie Ihrer Umgebung entsprechen.  Wenn Sie nicht sicher sind, welche Speicherklassen verfügbar sind, führen Sie den Befehl `kubectl get storageclass` aus, um sie anzuzeigen.  Die Vorlage hat einen Standardwert von `default`.  Dieser Wert bedeutet, dass es eine Speicherklasse _mit dem Namen_  gibt `default` und nicht, dass es eine Speicherklasse gibt, die _der Standard ist_.  Sie können die Größe Ihres Speichers auch optional ändern.  Weitere Informationen zur [Speicherkonfiguration](./storage-configuration.md).

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Erstellen der PostgreSQL Hyperscale-Servergruppe

Nachdem Sie die YAML-Datei für die PostgreSQL Hyperscale-Servergruppe angepasst haben, können Sie die PostgreSQL Hyperscale-Servergruppe erstellen, indem Sie den folgenden Befehl ausführen:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Überwachen des Erstellungsstatus

Es dauert einige Minuten, bis die PostgreSQL Hyperscale-Servergruppe erstellt ist. Mithilfe der folgenden Befehle können Sie den Status in einem anderen Terminalfenster überwachen:

> [!NOTE]
>  Die folgenden Beispielbefehle gehen davon aus, dass Sie eine PostgreSQL Hyperscale-Servergruppe mit dem Namen `pg1` und einen Kubernetes-Namespace mit dem Namen `arc` erstellt haben.  Wenn Sie einen anderen Namespace/PostgreSQL Hyperscale Server Gruppennamen verwendet haben, können Sie `arc` und `pg1` durch Ihre Namen ersetzen.

```console
kubectl get postgresqls/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Sie können auch den Erstellungsstatus eines bestimmten Pods überprüfen, indem Sie den Befehl `kubectl describe` ausführen.  Der Befehl `describe` ist besonders nützlich bei der Fehlersuche. Zum Beispiel:

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Beheben von Problemen bei der Erstellung

Wenn Probleme bei der Erstellung auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).
