---
title: Einrichten von eingehendem HTTPS-Datenverkehr in Azure Container Apps-Vorschau
description: Aktivieren von öffentlichen und privaten Endpunkten in Ihrer App mit Azure Container Apps
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: how-to
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5979588ec977ef7655fab21ef30e4fd233c12b43
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026423"
---
# <a name="set-up-https-ingress-in-azure-container-apps-preview"></a>Einrichten von eingehendem HTTPS-Datenverkehr in Azure Container Apps-Vorschau

Mit Azure Container Apps können Sie Ihre Container-App durch das Aktivieren von eingehendem Datenverkehr im öffentlichen Web verfügbar machen. Wenn Sie den eingehenden Datenverkehr aktivieren, ist kein Erstellen von Azure Load Balancer, einer öffentlichen IP-Adresse oder anderen Azure-Ressourcen notwendig, um eingehende HTTP-Anforderungen zu aktiveren.

Wenn der eingehende Datenverkehr aktiviert ist, hat Ihre Container-App die folgenden Eigenschaften:

- Unterstützt TLS-Abschluss
- Unterstützt HTTP/1.1 und HTTP/2
- Endpunkte verwenden immer TLS 1.2, beendet am Eingangspunkt
- Endpunkte machen die Ports 80 (für HTTP) und 443 (für HTTPS) immer verfügbar.
  - HTTP-Anforderungen an den Port 80 werden standardmäßig an HTTPS auf 443 umgeleitet.

## <a name="configuration"></a>Konfiguration

Die Einstellung zum eingehenden Datenverkehr bezieht sich auf die gesamte Anwendung. Veränderungen, die an den Einstellungen zum eingehenden Datenverkehr vorgenommen werden, werden gleichzeitig auf alle Überarbeitungen angewendet und generieren keine neuen Überarbeitungen.

Der Abschnitt mit der Konfiguration des eingehenden Datenverkehrs hat das folgende Format:

```json
{
  ...
  "configuration": {
      "ingress": {
          "external": true,
          "targetPort": 80,
          "transport": auto
      }
  }
}
```

Die folgenden Eigenschaften sind beim Konfigurieren des eingehenden Datenverkehrs verfügbar:

| Eigenschaft | BESCHREIBUNG | Werte | Erforderlich |
|---|---|---|---|
| `external` | Ihre Eingangs-IP und Ihr vollqualifizierter Domänenname können entweder extern im Internet oder intern in VNet sichtbar sein. |`true` für externe Sichtbarkeit, `false` für interne Sichtbarkeit (Standard) | Ja |
| `targetPort` | Der Port, dem Ihr Container für eingehende Anforderungen lauscht | Geben Sie für diesen Wert die Portnummer ein, die Ihr Container verwendet. Der Eingangsendpunkt Ihrer Anwendung ist im Port `443` immer verfügbar. | Ja |
| `transport` | Sie können entweder HTTP/1.1 oder HTTP/2 verwenden, oder Sie können einstellen, dass der Transporttyp automatisch erkannt wird. | `http` für HTTP/1, `http2` für HTTP/2, `auto`, um den Transporttyp automatisch zu erkennen (Standard) | Nein |
| `allowInsecure` | Erlaubt unsicheren Datenverkehr zu Ihrer Container-App | `false` (Standard), `true`<br><br>Wenn `true` eingestellt ist, werden HTTP-Anforderungen an den Port 80 nicht automatisch mithilfe von HTTPS an den Port 443 umgeleitet, wodurch unsichere Verbindungen erlaubt werden. | Nein |

> [!NOTE]
> Um den eingehenden Datenverkehr für Ihre Anwendung zu deaktivieren, können Sie die Konfigurationseigenschaft `ingress` komplett auslassen.

## <a name="ip-addresses-and-domain-names"></a>IP-Adressen und Domänennamen

Wenn der eingehende Datenverkehr aktiviert ist, wird Ihrer Anwendung ein vollqualifizierter Domänenname (FQDN) zugewiesen. Der Domänenname nimmt die folgenden Formen an:

|Eingangssichtbarkeitseinstellung | Vollqualifizierter Domänenname |
|---|---|
| Extern | `<APP_NAME>.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io`|
| Intern | `<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io` |

Ihre Container Apps-Umgebung hat eine einzelne öffentliche IP-Adresse für Anwendungen mit `external`-Eingangssichtbarkeit und eine einzelne interne IP-Adresse für Anwendungen mit `internal`-Eingangssichtbarkeit. Daher teilen alle Anwendungen innerhalb einer Container Apps-Umgebung mit `external`-Eingangssichtbarkeit eine einzelne öffentliche IP-Adresse. Ähnlich dazu teilen alle Anwendungen innerhalb einer Container App-Umgebung mit `internal`-Eingangssichtbarkeit eine einzelne interne IP-Adresse. HTTP-Datenverkehr wird basierend auf dem FQDN im Hostheader an individuelle Anwendungen weitergeleitet.

Sie können Zugriff auf den eindeutigen Bezeichner der Umgebung erhalten, indem Sie die Umgebungseinstellungen abfragen.

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

> [!div class="nextstepaction"]
> [Verwalten der Skalierung](scale-app.md)
