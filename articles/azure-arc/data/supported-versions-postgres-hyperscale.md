---
title: Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale
description: Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0a00475eacdeb741eca20d4a6c43282df9cfd17d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346829"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Unterstützte Postgres-Versionen mit Azure Arc-fähigem PostgreSQL Hyperscale

In diesem Artikel wird erläutert, welche Versionen von Postgres mit Azure Arc-fähigem PostgreSQL Hyperscale verfügbar sind.
Die Liste der unterstützten Versionen wird im Laufe der Zeit erweitert. Aktuell werden die folgenden Hauptversionen unterstützt:
- Postgres 12 (Standard)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Wie wird die geeignete Version ausgewählt?
Es wird empfohlen, zu ermitteln, für welche Versionen Ihre Anwendungen ausgelegt sind und welche Funktionen die einzelnen Versionen aufweisen. Auf der offiziellen Postgres-Website finden Sie weitere Informationen zu den einzelnen Versionen:
- [Postgres 12 (Standard)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Wie wird eine bestimmte Version in Azure Arc-fähigem PostgreSQL Hyperscale erstellt?
Zum Zeitpunkt der Erstellung können Sie angeben, welche Version erstellt werden soll, indem Sie den Parameter _--engine-version_ übergeben. Wenn Sie keine Versionsinformationen angeben, wird standardmäßig eine Servergruppe mit Postgres-Version 12 erstellt.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>Wie werde ich benachrichtigt, wenn andere Versionen verfügbar sind?
Sie sollten diesen Artikel häufiger prüfen, da er ggf. aktualisiert wird. Sie können auch die Arten von benutzerdefinierten Ressourcendefinitionen (Custom Resource Definitions, CRDs) im Arc-Datencontroller in Ihrem Kubernetes-Cluster auflisten.
Führen Sie den folgenden Befehl aus:
```console
kubectl get crds
```

Dabei wird eine Ausgabe wie die folgende zurückgegeben:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

In diesem Beispiel wird in der Ausgabe angegeben, dass es zwei Arten von Postgres-CRDs gibt:
- postgresql-12s.arcdata.microsoft.com ist die CRD für Postgres 12.
- postgresql-11s.arcdata.microsoft.com ist die CRD für Postgres 11.

Dabei handelt es sich um CRDs, nicht um Servergruppen. Das Vorhandensein einer CRD ist kein Hinweis darauf, dass Sie eine Servergruppe dieser Version erstellt haben oder nicht.
Die CRD gibt Aufschluss darüber, welche Art von Ressourcen erstellt werden können.

## <a name="next-steps"></a>Nächste Schritte:
- [Informationen zum Erstellen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](create-postgresql-hyperscale-server-group.md)
- [Informationen zum Abrufen einer Liste Azure Arc-fähiger PostgreSQL Hyperscale-Servergruppen, die in Ihrem Arc-Datencontroller erstellt wurden](list-server-groups-postgres-hyperscale.md)
