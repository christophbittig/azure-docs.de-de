---
title: Azure Functions in Kubernetes mit KEDA
description: Erfahren Sie, wie Sie Azure Functions in Kubernetes mithilfe von KEDA (Kubernetes-based Event Driven Autoscaling, [Kubernetes-basierte ereignisgesteuerte automatische Skalierung]) in der Cloud oder lokal ausführen.
author: eamonoreilly
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: eamono
ms.openlocfilehash: 736945109cd18bd7c6f3a6b9a16b6549f7c65652
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346823"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions in Kubernetes mit KEDA

Die Azure Functions-Runtime bietet Flexibilität im Hinblick auf Art und Ort des Hostings.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling, [Kubernetes-basierte ereignisgesteuerte automatische Skalierung]) lässt sich nahtlos an die Azure Functions-Runtime und -Tools koppeln, um eine ereignisgesteuerte Skalierung in Kubernetes bereitzustellen.

## <a name="how-kubernetes-based-functions-work"></a>Funktionsweise Kubernetes-basierter Funktionen

Der Azure Functions-Dienst besteht aus zwei Komponenten: einer Runtime und einem Skalierungscontroller.  Die Functions-Runtime führt Ihren Code aus.  Die Runtime enthält Logik zum Auslösen, Protokollieren und Verwalten von Funktionsausführungen.  Die Azure Functions-Runtime kann *überall* ausgeführt werden.  Die andere Komponente ist ein Skalierungscontroller.  Der Skalierungscontroller überwacht die Rate der Ereignisse, die Ihre Funktion nutzen möchten, und skaliert proaktiv die Anzahl von Instanzen, die Ihre App ausführen.  Weitere Informationen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md).

Die Kubernetes-basierte Version stellt die Functions-Runtime in einem [Docker-Container](functions-create-function-linux-custom-image.md) bereit, während die ereignisgesteuerte Skalierung über KEDA erfolgt.  KEDA kann eine horizontale Herunterskalierung auf 0 Instanzen (wenn keine Ereignisse stattfinden) und eine horizontale Hochskalierung auf bis zu *n* Instanzen durchführen. Zu diesem Zweck macht KEDA benutzerdefinierte Metriken für die automatische Kubernetes-Skalierung (automatische horizontale Podskalierung) verfügbar.  Durch Verwendung von Functions-Container mit KEDA können serverlose Funktionen in jedem Kubernetes-Cluster repliziert werden.  Diese Funktionen können auch mithilfe des Features für [virtuelle AKS-Knoten (Azure Kubernetes Service)](../aks/virtual-nodes-cli.md) für serverlose Infrastrukturen bereitgestellt werden.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Verwalten von KEDA und Functions in Kubernetes

Um Functions in Ihrem Kubernetes-Cluster ausführen zu können, müssen Sie die KEDA-Komponenten installieren. Sie können die Komponente auf eine der folgenden Arten installieren:

+ Azure Functions Core Tools: mithilfe des [`func kubernetes install`-Befehls](functions-core-tools-reference.md#func-kubernetes-install)

+ Helm: Es gibt verschiedene Möglichkeiten, KEDA in einem Kubernetes-Cluster, einschließlich Helm, zu installieren.  Bereitstellungsoptionen sind auf der [KEDA-Site ](https://keda.sh/docs/deploy/) dokumentiert.

## <a name="deploying-a-function-app-to-kubernetes"></a>Bereitstellen einer Funktions-App in Kubernetes

Sie können jede beliebige Funktions-App in einem Kubernetes-Cluster bereitstellen, in dem KEDA ausgeführt wird.  Da Ihre Funktionen in einem Docker-Container ausgeführt werden, benötigt Ihr Projekt ein Dockerfile.  Sie können das Dockerfile mithilfe der [`--docker`-Option][func init] erstellen, wenn Sie `func init` aufrufen, um das Projekt zu erstellen. Wenn Sie dies vergessen haben, können Sie `func init` erneut aus dem Stammverzeichnis Ihres Functions-Projekts aufrufen und dieses Mal die [`--docker-only`-Option][func init] verwenden (wie im folgenden Beispiel dargestellt). 

```command
func init --docker-only
```

Weitere Informationen zur Generierung von Dockerfiles finden Sie in der [`func init`][func init]-Referenz. 

Um ein Image zu erstellen und Ihre Funktionen in Kubernetes bereitzustellen, führen Sie den folgenden Befehl aus:

```command
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

In diesem Beispiel ersetzen Sie `<name-of-function-deployment>` durch den Namen Ihrer Funktions-App.

Der Bereitstellungsbefehl bewirkt Folgendes:

1. Das zuvor erstellte Dockerfile wird verwendet, um ein lokales Image für die Funktions-App zu erstellen.
1. Das lokale Image wird markiert und an die Containerregistrierung gepusht, bei der der Benutzer angemeldet ist.
1. Es wird ein Manifest erstellt und auf den Cluster angewandt. Dieses definiert eine Kubernetes-`Deployment`-Ressource, eine `ScaledObject`-Ressource und `Secrets`, einschließlich aus Ihrer Datei `local.settings.json` importierter Umgebungsvariablen.

Weitere Informationen finden Sie unter dem [`func kubernetes deploy`-Befehl](functions-core-tools-reference.md#func-kubernetes-deploy).

### <a name="deploying-a-function-app-from-a-private-registry"></a>Bereitstellen einer Funktions-App aus einer privaten Registrierung

Der obige Flow funktioniert auch für private Registrierungen.  Wenn Sie Ihr Containerimage aus einer privaten Registrierung abrufen, schließen Sie das `--pull-secret`-Flag ein. Dieses verweist auf den geheimen Kubernetes-Schlüssel, der die Anmeldeinformationen für die private Registrierung beim Ausführen von `func kubernetes deploy` enthält.

## <a name="removing-a-function-app-from-kubernetes"></a>Entfernen einer Funktions-App aus Kubernetes

Nach dem Bereitstellen können Sie eine Funktion entfernen, indem Sie die zugehörigen erstellten `Deployment`-, `ScaledObject`- und `Secrets`-Elemente entfernen.

```command
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Deinstallieren von KEDA aus Kubernetes

Sie können KEDA auf eine der folgenden Arten aus Ihrem Cluster entfernen:

+ Azure Functions Core Tools: mithilfe des [`func kubernetes remove`-Befehls](functions-core-tools-reference.md#func-kubernetes-remove).

+ Helm: Befolgen Sie die Schritte zur Deinstallation [auf der KEDA-Seite](https://keda.sh/docs/deploy/).

## <a name="supported-triggers-in-keda"></a>Unterstützte Trigger in KEDA

KEDA bietet Unterstützung für die folgenden Azure Functions-Trigger:

* [Azure Storage-Warteschlangen](functions-bindings-storage-queue.md)
* [Azure Service Bus-Warteschlangen](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-Warteschlange](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>HTTP-Triggerunterstützung

Sie können Azure Functions verwenden, die HTTP-Trigger verfügbar machen, KEDA verwaltet diese jedoch nicht direkt.  Sie können den prometheus-Trigger von KEDA nutzen, um die [HTTP-Azure Functions von 1 bis *n* Instanzen zu skalieren](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Erstellen einer Funktion mit einem benutzerdefinierten Image](functions-create-function-linux-custom-image.md)
* [Lokales Codieren und Testen von Azure Functions](functions-develop-local.md)
* [So funktioniert der Azure Functions-Verbrauchstarif](functions-scale.md)

[func init]: functions-core-tools-reference.md#func-init