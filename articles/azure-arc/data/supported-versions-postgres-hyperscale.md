---
title: Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale
description: Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 180cb275b2b4a08dc9463eef4432006c5c0ee714
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290607"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale
Die Liste der unterstützten Versionen wird im Laufe der Zeit erweitert, während wir die Parität mit den verwalteten Postgres-Diensten in Azure PaaS sicherstellen. Aktuell werden die folgenden Hauptversionen unterstützt:
- Postgres 12 (Standard)
- Postgres 11


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Wie wird die geeignete Version ausgewählt?
Es wird empfohlen, zu ermitteln, für welche Versionen Ihre Anwendungen ausgelegt sind und welche Funktionen die einzelnen Versionen aufweisen. Auf der offiziellen Postgres-Website finden Sie weitere Informationen zu den einzelnen Versionen:
- [Postgres 12 (Standard)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Wie wird eine bestimmte Version in Azure Arc-fähigem PostgreSQL Hyperscale erstellt?
Zum Zeitpunkt der Erstellung können Sie angeben, welche Version erstellt werden soll, indem Sie den Parameter _--engine-version_ übergeben. Wenn Sie keine Versionsinformationen angeben, wird standardmäßig eine Servergruppe mit Postgres-Version 12 erstellt.

Beachten Sie, dass es in Ihrem Kubernetes-Cluster unabhängig von den unterstützten Versionen nur eine benutzerdefinierte Postgres-Ressourcendefinition (Custom Resource Definition, CRD) gibt.
Führen Sie beispielsweise den folgenden Befehl aus:
```console
kubectl get crds
```

Dabei wird eine Ausgabe wie die folgende zurückgegeben:
```console
NAME                                                             CREATED AT
dags.sql.arcdata.microsoft.com                                   2021-10-12T23:53:40Z
datacontrollers.arcdata.microsoft.com                            2021-10-13T01:00:27Z
exporttasks.tasks.arcdata.microsoft.com                          2021-10-12T23:53:39Z
healthstates.azmon.container.insights                            2021-10-12T19:04:44Z
monitors.arcdata.microsoft.com                                   2021-10-13T01:00:26Z
postgresqls.arcdata.microsoft.com                                2021-10-12T23:53:37Z
sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com   2021-10-12T23:53:38Z
sqlmanagedinstances.sql.arcdata.microsoft.com                    2021-10-12T23:53:37Z
```

In diesem Beispiel gibt diese Ausgabe an, dass es eine CRD im Zusammenhang mit Postgres gibt: postgresqls.arcdata.microsoft.com, shortname postgresqls. Die CRD ist keine Postgres-Instanz oder Postgres-Servergruppe. Das Vorhandensein einer CRD ist kein Hinweis darauf, dass Sie eine Servergruppe erstellt haben oder nicht. Die CRD gibt Aufschluss darüber, welche Art von Ressourcen im Kubernetes-Cluster erstellt werden können.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>Wie werde ich benachrichtigt, wenn andere Versionen verfügbar sind?
Sie sollten diesen Artikel häufiger prüfen, da er ggf. aktualisiert wird.


## <a name="next-steps"></a>Nächste Schritte:
- [Informationen zum Erstellen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](create-postgresql-hyperscale-server-group.md)
- [Informationen zum Abrufen einer Liste Azure Arc-fähiger PostgreSQL Hyperscale-Servergruppen, die in Ihrem Arc-Datencontroller erstellt wurden](list-server-groups-postgres-hyperscale.md)
