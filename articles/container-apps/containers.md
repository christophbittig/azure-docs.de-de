---
title: Container in Azure Container Apps (Vorschauversion)
description: Erfahren Sie, wie Container in Azure Container Apps verwaltet und konfiguriert werden.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdebbbae4cba1577b8f92e0f54821eb72737c2c3
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026765"
---
# <a name="containers-in-azure-container-apps-preview"></a>Container in Azure Container Apps (Vorschauversion)

Azure Container Apps verwaltet die Details von Kubernetes und Containerorchestrierungen für Sie. Container in Azure Container Apps können eine beliebige Laufzeit, Programmiersprache oder einen Entwicklungsstapel Ihrer Wahl verwenden.

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure Container Apps: Container":::

Azure Container Apps unterstützt Folgendes:

- Linux-basierte Containerimages
- Container aus einer öffentlichen oder privaten Containerregistrierung

Folgende zusätzlichen Funktionen sind verfügbar:

- Es gibt kein erforderliches Basiscontainerimage.
- Änderungen am Abschnitt `template` der ARM-Konfiguration lösen eine neue [Container-App-Revision](application-lifecycle-management.md) aus.
- Wenn ein Container abstürzt, wird er automatisch neu gestartet.

## <a name="configuration"></a>Konfiguration

Die folgende Beispielkonfiguration zeigt die beim Einrichten eines Containers verfügbaren Optionen.

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| Einstellung | BESCHREIBUNG | Bemerkungen |
|---|---|---|
| `image` | Der Name des Containerimages für Ihre Container-App. | Dieser Wert hat das Format `repository/image-name:tag`. |
| `name` | Anzeigename des Containers. | Dient zur Berichterstellung und Identifizierung. |
| `command` | Der Startbefehl des Containers. | Entspricht dem Feld [entrypoint](https://docs.docker.com/engine/reference/builder/) von Docker.  |
| `args` | Startbefehlsargumente. | Einträge im Array werden miteinander so verbunden, dass eine Parameterliste erstellt wird, die an den Startbefehl übergeben wird. |
| `env` | Ein Array von Schlüssel-Wert-Paaren, die Umgebungsvariablen festlegen. | Verwenden Sie `secretRef` anstelle des Felds `value`, um auf ein Geheimnis zu verweisen. |
| `resources.cpu` | Die Anzahl der CPUs, die dem Container zugeordnet sind. | Werte müssen den folgenden Regeln entsprechen: Der Wert muss größer als 0 und kleiner als 2 sein und kann eine beliebige Dezimalzahl mit maximal einer Dezimalstelle sein. Beispiel: `1.1` ist gültig, `1.55` jedoch ungültig. Der Standardwert ist 0,5 CPU pro Container. |
| `resources.memory` | Die Größe des dem Container zugeteilten RAM. | Dieser Wert kann bis zu `4Gi` betragen. Die einzigen zulässigen Einheiten sind [Gibibytes](https://simple.wikipedia.org/wiki/Gibibyte) (`Gi`). Werte müssen den folgenden Regeln entsprechen: Der Wert muss größer als 0 und kleiner als `4Gi` sein und kann eine beliebige Dezimalzahl mit maximal zwei Dezimalstellen sein. Beispiel: `1.25Gi` ist gültig, `1.555Gi` jedoch ungültig. Der Standardwert ist `1Gi` pro Container.  |

Die Gesamtmenge der CPUs und des RAM, die für alle Container in einer Container-App angefordert werden, muss sich zu einer der folgenden Kombinationen summieren.

| vCPUs | Arbeitsspeicher in Gi |
|---|---|
| 0,5 | 1.0 |
| 1.0 | 2.0 |
| 1.5 | 3.0 |
| 2.0 | 4.0 |

- Alle CPU-Anforderungen in allen Containern müssen mit einem der Werte in der Spalte „vCPUs“ übereinstimmen.
- Alle Arbeitsspeicheranforderungen in allen Ihren Containern müssen mit dem Wert für „Arbeitsspeicher“ in der Spalte „Arbeitsspeicher“ in derselben Zeile wie in der Spalte „CPU“ übereinstimmen.

## <a name="multiple-containers"></a>Mehrere Container

Sie können in einer einzelnen Container-App mehrere Container definieren. Containergruppen werden als [Pods](https://kubernetes.io/docs/concepts/workloads/pods) bezeichnet. Die Container in einem Pod teilen sich Festplatten- und Netzwerkressourcen und durchlaufen den gleichen [Anwendungslebenszyklus](application-lifecycle-management.md).

Sie führen mehrere Container zusammen aus, indem Sie mehrere Container in der Konfiguration des Arrays `containers` definieren.

Gründe für die gemeinsame Ausführung von Containern in einem Pod sind u. a.:

- Nutzen eines Containers als Begleiter Ihrer primären App
- Verwenden von freigegebenem Speicherplatz und eines virtuellen Netzwerks
- Freigeben von Skalierungsregeln für Container
- Gruppieren mehrerer Container, die stets zusammen ausgeführt werden müssen
- Aktivieren direkter Kommunikation zwischen Containern auf demselben Host

## <a name="container-registries"></a>Containerregistrierungen

Sie können Images bereitstellen, die auf privaten Hosts gehostet werden, bei denen die Anmeldeinformationen über die Container Apps-Konfiguration bereitgestellt werden.

Um eine Containerregistrierung zu verwenden, definieren Sie zunächst die erforderlichen Felder im Abschnitt `registries` der [Konfiguration](azure-resource-manager-api-spec.md).

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

Nach dieser Einrichtung können die gespeicherten Anmeldeinformationen verwendet werden, wenn Sie in einem `image`-Element im Array `containers` auf ein Containerimage verweisen.

Das folgende Beispiel zeigt, wie Sie eine App aus Azure Container Registry bereitstellen.

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

## <a name="limitations"></a>Einschränkungen

Für Azure Container Apps gelten die folgenden Einschränkungen:

- **Privilegierte Container**: Mit Azure Container Apps lassen sich keine privilegierten Container ausführen. Wenn Ihr Programm versucht, einen Prozess auszuführen, der Root-Zugriff erfordert, tritt bei der Anwendung innerhalb des Containers ein Laufzeitfehler auf.

- **Betriebssystem**: Linux-basierte Containerimages sind erforderlich.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Umgebung](environment.md)
