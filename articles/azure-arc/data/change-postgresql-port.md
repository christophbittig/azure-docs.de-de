---
title: Ändern des PostgreSQL-Ports
description: Ändern des Ports, an dem die Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe lauscht.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 516c296bc18a3a38b0a6bbe303313119fe2bce1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561800"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Ändern des Ports, an dem die Servergruppe lauscht 

Bearbeiten Sie die Servergruppe, um den Port zu ändern. Führen Sie beispielsweise den folgenden Befehl aus:

```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

Wenn der Name Ihrer Servergruppe _postgres01_ lautet, und Sie an Port _866_ lauschen möchten. Führen Sie den folgenden Befehl aus:

```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace arc --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>Überprüfen, ob der Port geändert wurde

Um zu überprüfen, ob der Port geändert wurde, führen Sie den folgenden Befehl aus, um die Konfiguration Ihrer Servergruppe anzuzeigen:

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

Sehen Sie sich in der Ausgabe dieses Befehls die für das Element „Port“ im Abschnitt „Dienst“ der Spezifikationen Ihrer Servergruppe angezeigte Portnummer an.

Alternativ können Sie im Element `externalEndpoint` des Statusabschnitts der Spezifikationen Ihrer Servergruppe überprüfen, ob auf die IP-Adresse die von Ihnen konfigurierte Portnummer folgt.

Um das obige Beispiel fortzufahren, führen Sie zur Veranschaulichung den Befehl aus:

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

Der Befehl gibt Port 866 zurück:

```output
"services": {
      "primary": {
        "port": 866,
        "type": "LoadBalancer"
      }
    }
```

Beachten Sie außerdem den Wert für `primaryEndpoint`.

```output
"primaryEndpoint": "12.345.67.890:866",
```

## <a name="next-steps"></a>Nächste Schritte
- Informationen zum [Herstellen einer Verbindung mit Ihrer Servergruppe](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Informieren Sie sich darüber, wie Sie andere Aspekte Ihrer Servergruppe im Abschnitt „Vorgehensweise\Verwalten\Konfigurieren & Skalieren“ der Dokumentation konfigurieren können.
