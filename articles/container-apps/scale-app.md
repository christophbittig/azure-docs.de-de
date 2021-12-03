---
title: Skalierung in Azure Container Apps
description: Erfahren Sie, wie Anwendungen in Azure Container Apps horizontal ab- und hochskaliert werden.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 683545b892db4830e01f71faa2f77a097f9e8a9a
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061171"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Festlegen von Skalierungsregeln in Azure Container Apps

Azure Container Apps verwaltet die automatische horizontale Skalierung mithilfe einer Reihe deklarativer Skalierungsregeln. Wenn eine Container-App horizontal hochskaliert wird, werden bei Bedarf neue Instanzen der Container-App erstellt. Diese Instanzen werden als Replikate bezeichnet.

Die Skalierungsregeln sind im Abschnitt `resources.properties.template.scale` der [Konfiguration](overview.md) definiert. Es gibt zwei Skalierungseigenschaften, die für alle Regeln in Ihrer Container-App gelten.

| Skalierungseigenschaft | BESCHREIBUNG | Standardwert | Mindestwert | Höchstwert |
|---|---|---|---|---|
| `minReplicas` | Mindestanzahl von Replikaten, die für Ihre Container-App ausgeführt werden. | 0 | 1 | 25 |
| `maxReplicas` | Höchstanzahl von Replikaten, die für Ihre Container-App ausgeführt werden. | – | 1 | 25 |

- Wenn Ihre Container-App auf 0 (null) skaliert wird, werden Ihnen keine Kosten in Rechnung gestellt.
- Einzelne Skalierungsregeln sind im `rules`-Array definiert.
- Wenn Sie sicherstellen möchten, dass jederzeit eine Instanz Ihrer Anwendung ausgeführt wird, legen Sie `minReplicas` auf 1 oder höher fest.
- Replikate, die keine Verarbeitung ausführen, die aber im Arbeitsspeicher verbleiben, werden in der Kategorie „Leerlaufgebühren“ abgerechnet.
- Änderungen an Skalierungsregeln stellen eine Änderung des [Revisionsbereichs](overview.md) dar.
- Wenn Sie Nicht-HTTP-Ereignisskalierungsregeln verwenden, wird das Festlegen von `activeRevisionMode` auf `single` empfohlen.

> [!IMPORTANT]
> Replikatmengen sind eine Zielmenge, keine Garantie. Selbst wenn Sie `maxReplicas` auf `1` festlegen, gibt es keine Zusicherung der Threadsicherheit.

## <a name="scale-triggers"></a>Skalierungstrigger

Container Apps unterstützt eine große Anzahl von Skalierungstriggern. Weitere Informationen zu den unterstützten Skalierungstriggern finden Sie unter [KEDA Scalers](https://keda.sh/docs/scalers/).

In der KEDA-Dokumentation werden Codebeispiele in YAML angegeben, während die ARM-Vorlage für Container Apps im JSON-Format vorliegt. Wenn Sie Beispiele aus KEDA für Ihre Anforderungen transformieren, achten Sie darauf, Eigenschaftennamen von der [Kebab](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words)-Schreibweise auf die [Camel](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words)-Schreibweise umzustellen.

## <a name="http"></a>HTTP

Mit einer HTTP-Skalierungsregel haben Sie die Kontrolle über den Schwellenwert, der bestimmt, wann horizontal hochskaliert werden soll.

| Skalierungseigenschaft | BESCHREIBUNG | Standardwert | Mindestwert | Höchstwert |
|---|---|---|---|---|
| `concurrentRequests`| Sobald die Anzahl der Anforderungen diesen Wert überschreitet, werden weitere Replikate bis zum Erreichen der Anzahl `maxReplicas` hinzugefügt. | 50 | 1 | – |

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": 0,
          "maxReplicas": 5,
          "rules": [{
            "name": "http-rule",
            "http": {
              "metadata": {
                  "concurrentRequests": 100
              }
            }
          }]
        }
      }
    }
  }
}
```

In diesem Beispiel wird die Container-App auf bis zu fünf Replikate hochskaliert und kann auf null Instanzen herunterskaliert werden. Der Skalierungsschwellenwert ist auf 100 gleichzeitige Anforderungen pro Sekunde festgelegt.

## <a name="event-driven"></a>Ereignisgesteuert

Container-Apps können basierend auf einer Vielzahl von Ereignistypen skaliert werden. Alle von [KEDA](https://keda.sh/docs/scalers/)unterstützten Ereignisse werden in Container Apps unterstützt.

Jeder Ereignistyp weist unterschiedliche Eigenschaften im Abschnitt `metadata` der KEDA-Definition auf. Verwenden Sie diese Eigenschaften, um eine Skalierungsregel in Container Apps zu definieren.

Das folgende Beispiel zeigt, wie Sie eine Skalierungsregel auf der Grundlage eines [Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/)-Triggers erstellen.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      "configuration": {
        "secrets": [{
          "name": "servicebusconnectionstring",
          "value": "<MY-CONNECTION-STRING-VALUE>"
        }],
      },
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [
          {
            "name": "queue-based-autoscaling",
            "custom": {
              "type": "azure-servicebus",
              "metadata": {
                "queueName": "myServiceBusQueue",
                "messageCount": "20"
              },
              "auth": [{
                "secretRef": "servicebusconnectionstring",
                "triggerParameter": "connection"
              }]
        }
    }]
}
```

In diesem Beispiel wird die Container-App gemäß dem folgenden Verhalten skaliert:

- Wenn die Anzahl der Nachrichten in der Warteschlange 20 überschreitet, werden neue Replikate erstellt.
- Die Verbindungszeichenfolge für die Warteschlange wird der Konfigurationsdatei als Parameter übergeben, und es wird in der `secretRef`-Eigenschaft auf sie verwiesen.

## <a name="cpu"></a>CPU

Mithilfe von CPU-Skalierung kann Ihre App je nach Auslastung der CPU horizontal hoch- oder abskalieren. Bei der CPU-Skalierung kann Ihre Container-App nicht auf 0 skaliert werden. Weitere Informationen zu diesem Trigger finden Sie unter [KEDA CPU scale trigger](https://keda.sh/docs/scalers/cpu/) (KEDA CPU-Skalierungstrigger).

Das folgende Beispiel zeigt das Erstellen einer CPU-Skalierungsregel.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "cpuScalingRule",
            "custom": {
              "type": "cpu",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- In diesem Beispiel wird die Container-App skaliert, wenn die CPU-Auslastung 50 % überschreitet.
- Für Apps, die basierend auf der CPU-Auslastung skaliert werden, bleibt mindestens ein einzelnes Replikat im Arbeitsspeicher.

## <a name="memory"></a>Arbeitsspeicher

Mithilfe der Skalierung des Arbeitsspeichers kann Ihre App je nach Auslastung des Arbeitsspeichers horizontal hoch- oder abskalieren. Bei der Skalierung des Arbeitsspeichers kann Ihre Container-App nicht auf 0 skaliert werden. Weitere Informationen zu diesem Skalierer finden Sie unter [KEDA-Speicherskalierer](https://keda.sh/docs/scalers/memory/).

Das folgende Beispiel zeigt das Erstellen einer Arbeitsspeicher-Skalierungsregel.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [{
            "name": "memoryScalingRule",
            "custom": {
              "type": "memory",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- In diesem Beispiel wird die Container-App skaliert, wenn die Arbeitsspeicherauslastung 50 % überschreitet.
- Für Apps, die basierend auf der Arbeitsspeicherauslastung skaliert werden, bleibt mindestens ein einzelnes Replikat im Arbeitsspeicher.


## <a name="considerations"></a>Überlegungen

- Vertikale Skalierung wird nicht unterstützt.
- Replikatmengen sind eine Zielmenge, keine Garantie.
  - Selbst wenn Sie `maxReplicas` auf `1` festlegen, gibt es keine Zusicherung der Threadsicherheit.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen Ihrer Container-App](secure-app.md)
