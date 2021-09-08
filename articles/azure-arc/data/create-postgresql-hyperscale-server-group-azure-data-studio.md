---
title: Erstellen von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung mit Azure Data Studio
description: Erstellen von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung mit Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 3e71dc89ccf94462f83ce07e69bc57df86ae0a72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340336"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Erstellen von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung mit Azure Data Studio

In diesem Dokument werden die Schritte zum Verwenden von Azure Data Studio zum Bereitstellen von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung beschrieben.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Vorläufiger und temporärer Schritt nur für OpenShift-Benutzer

Implementieren Sie diesen Schritt, bevor Sie mit dem nächsten fortfahren. Sie müssen die folgenden Befehle zum Updaten der Sicherheitseinschränkungen für Ihren Cluster ausführen, um eine PostgreSQL Hyperscale-Servergruppe in Red Hat OpenShift in einem Projekt bereitzustellen, das nicht dem Standardprojekt entspricht. Dieser Befehl gewährt den Dienstkonten, unter denen Ihre PostgreSQL Hyperscale-Servergruppe ausgeführt wird, die erforderlichen Berechtigungen. Die Sicherheitskontexteinschränkung **_arc-data-scc_** haben Sie hinzugefügt, als Sie den Azure Arc-Datencontroller bereitgestellt haben.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-group-name** entspricht dem Namen der Servergruppe, die Sie während des nächsten Schritts bereitstellen._
   
Weitere Informationen zu Sicherheitskontexteinschränkungen in OpenShift finden Sie in der [OpenShift-Dokumentation](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Sie können nun mit dem nächsten Schritt fortfahren.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Erstellen einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung

1. Starten von Azure Data Studio
1. Klicken Sie auf der Registerkarte „Verbindungen“ auf die drei Punkte oben links, und wählen Sie dann „Neue Bereitstellung“ aus.
1. Wählen Sie in den Bereitstellungsoptionen **PostgreSQL Hyperscale-Servergruppe - Azure Arc** aus.
    >[!NOTE]
    > Möglicherweise werden Sie hier dazu aufgefordert, die [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] zu installieren, wenn dies noch nicht geschehen ist.
1. Akzeptieren Sie die Datenschutz- und Lizenzbedingungen, und klicken Sie unten auf **Auswählen**.
1. Geben Sie auf dem Blatt „PostgreSQL Hyperscale-Servergruppe - Azure Arc“ die folgenden Informationen ein:
   - Geben Sie einen Namen für die Servergruppe ein.
   - Die Anzahl an Workerknoten:
   - Geben Sie ein Kennwort für den _Postgres_-Administratorbenutzer der Servergruppe ein, und bestätigen Sie es.
   - Wählen Sie die Speicherklasse aus, die für die Daten geeignet ist.
   - Wählen Sie die Speicherklasse aus, die für Protokolle geeignet ist.
   - Wählen Sie die Speicherklasse aus, die für Sicherungen geeignet ist.
   - Wählen Sie die Anzahl der bereitzustellenden Workerknoten aus.
1. Klicken Sie auf die Schaltfläche **Bereitstellen**.

Dadurch wird die Erstellung der Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung für den Datencontroller gestartet.

Nach einigen Minuten sollte die Erstellung erfolgreich abgeschlossen werden.

### <a name="important-parameters-you-should-consider"></a>Wichtige Parameter, die Sie berücksichtigen sollten:

- **Anzahl von Workerknoten**, die Sie zum Aufskalieren und ggf. zum Verbessern der Leistung bereitstellen möchten. Lesen Sie die [Konzepte zu Postgres Hyperscale](concepts-distributed-postgres-hyperscale.md), bevor Sie fortfahren. Die folgende Tabelle gibt den Bereich der unterstützten Werte und die Form der Postgres-Bereitstellung an, die Sie damit jeweils erhalten. Wenn Sie z. B. eine Servergruppe mit zwei Workerknoten bereitstellen möchten, geben Sie „2“ an. Dadurch werden drei Pods erstellt: einer für den Koordinatorknoten/die Koordinatorinstanz und zwei für die Workerknoten/Workerinstanzen (einer für jeden Worker).



|Sie benötigen Folgendes:   |Form der bereitzustellenden Servergruppe   |Anzahl anzugebender Workerknoten   |Hinweis   |
|---|---|---|---|
|Eine horizontal aufskalierte Form von Postgres, um die Skalierbarkeitsanforderungen Ihrer Anwendungen zu erfüllen.   |3 oder mehr Postgres-Instanzen, 1 ist Koordinator, n sind Worker mit n >= 2.   |n, bei n >= 2.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine grundlegende Form von Postgres Hyperscale, mit der Sie Ihre Anwendung zu minimalen Kosten funktional überprüfen können. Es ist keine gültige Leistungs- und Skalierbarkeitsvalidierung damit möglich. Hierfür müssen Sie den oben beschriebenen Bereitstellungstyp verwenden.   |1 Postgres-Instanz, die sowohl Koordinator als auch Worker ist.   |0, und Citus zur Liste der zu ladenden Erweiterungen hinzufügen.   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, wurde geladen.   |
|Eine einfache Instanz von Postgres, die bei Bedarf horizontal aufskaliert werden kann.   |1 Postgres-Instanz. Die Semantik für Koordinator und Worker ist ihr noch nicht bekannt. Um sie nach der Bereitstellung aufzuskalieren, bearbeiten Sie die Konfiguration, erhöhen Sie die Anzahl der Workerknoten und verteilen Sie die Daten.   |0   |Die Citus-Erweiterung, die die Hyperscale-Funktion bereitstellt, ist in Ihrer Bereitstellung vorhanden, aber noch nicht geladen.   |
|   |   |   |   |

1 Worker anzugeben funktioniert zwar, es wird jedoch nicht empfohlen, ihn zu verwenden. Diese Bereitstellung bietet Ihnen nicht viel Nutzen. Sie umfasst 2 Instanzen von Postgres: 1 Koordinator und 1 Worker. In dieser Konfiguration werden die Daten tatsächlich nicht aufskaliert, da Sie einen einzelnen Worker bereitstellen. Daher erhalten Sie kein höheres Maß an Leistung und Skalierbarkeit. Diese Unterstützung für solche Bereitstellungen wird in einer zukünftigen Version entfernt.

- **Speicherklassen**, die von der Servergruppe verwendet werden sollen. Es ist wichtig, dass Sie die Speicherklasse direkt beim Bereitstellen einer Servergruppe festlegen, da diese nach der Bereitstellung nicht mehr geändert werden kann. Um die Speicherklasse nach der Bereitstellung zu ändern, müssten Sie die Daten extrahieren, Ihre Servergruppe löschen, eine neue Servergruppe erstellen und die Daten importieren. Sie können die Speicherklassen angeben, die für die Daten, Protokolle und Sicherungen verwendet werden sollen. Wenn Sie keine Speicherklassen angeben, werden standardmäßig die Speicherklassen des Datencontrollers verwendet.
    - Um die Speicherklasse für die Daten festzulegen, geben Sie den Parameter `--storage-class-data` oder `-scd` gefolgt vom Namen der Speicherklasse an.
    - Um die Speicherklasse für die Protokolle festzulegen, geben Sie den Parameter `--storage-class-logs` oder `-scl` gefolgt vom Namen der Speicherklasse an.
    - Um die Speicherklasse für die Sicherungen festzulegen: In dieser Vorschauversion von PostgreSQL Hyperscale mit Azure Arc-Unterstützung gibt es je nach Sicherungs-/Wiederherstellungsvorgängen, die Sie ausführen möchten, zwei Möglichkeiten zum Festlegen von Speicherklassen. Wir arbeiten daran, diese Funktion zu vereinfachen. Sie geben entweder eine Speicherklasse oder eine Volumeanspruchseinbindung an. Bei einer Volumeanspruchseinbindung handelt es sich um ein durch einen Doppelpunkt getrenntes Paar, das aus einem Anspruch auf ein vorhandenes persistentes Volume (Persistent Volume Claim, PVC) im selben Namespace und dem Volumetyp (sowie je nach Volumetyp optionalen Metadaten) besteht. Das persistente Volume wird in jedem Pod für die PostgreSQL-Servergruppe eingebunden.
        - Wenn Sie nur vollständige Datenbankwiederherstellungen ausführen möchten, geben Sie den Parameter `--storage-class-backups` oder `-scb` gefolgt vom Namen der Speicherklasse an.
        - Wenn Sie sowohl vollständige Datenbankwiederherstellungen als auch Zeitpunktwiederherstellungen ausführen möchten, geben Sie den Parameter `--volume-claim-mounts` oder `--volume-claim-mounts` gefolgt vom Namen eines Volumeanspruchs und eines Volumetyps an.


## <a name="next-steps"></a>Nächste Schritte
- [Verwalten der Servergruppe mit Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Überwachen der Servergruppe](monitor-grafana-kibana.md)
- Lesen Sie die Konzepte und Schrittanleitungen zu Azure Database for PostgreSQL Hyperscale, um Ihre Daten auf mehrere PostgreSQL Hyperscale-Knoten zu verteilen und von der gesamten Leistung von Azure Database for Postgres Hyperscale zu profitieren. :
    * [Knoten und Tabellen](../../postgresql/concepts-hyperscale-nodes.md)
    * [Festlegen des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md)
    * [Auswählen einer Verteilungsspalte](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellenzusammenstellung](../../postgresql/concepts-hyperscale-colocation.md)
    * [Verteilen und Ändern von Tabellen](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Entwerfen einer Datenbank mit mehreren Mandanten](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Entwerfen eines Dashboards für Echtzeitanalysen](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Überspringen Sie die Abschnitte **Anmelden am Azure-Portal** und **Erstellen einer Azure Database for PostgreSQL-Instanz für Hyperscale (Citus)** in den oben aufgeführten Dokumenten. Implementieren Sie die restlichen Schritte in Ihrer Azure Arc-Bereitstellung. Diese Abschnitte sind speziell für den PaaS-Dienst „Azure Database for PostgreSQL Hyperscale (Citus)“ in der Azure-Cloud vorgesehen. Die anderen Abschnitte der Dokumente sind jedoch direkt auf Ihre Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung übertragbar.

- [Skalieren der Azure Database for PostgreSQL Hyperscale-Servergruppe](scale-out-in-postgresql-hyperscale-server-group.md)
- [Speicherkonfiguration und Kubernetes-Speicherkonzepte](storage-configuration.md)
- [Kubernetes-Ressourcenmodell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

