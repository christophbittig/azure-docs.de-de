---
title: Aktualisieren einer Containergruppe
description: Hier erfahren Sie, wie Sie ausgeführte Container in Ihren Azure Container Instances-Containergruppen aktualisieren.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: f2ec8ed3641fd9e692c89c6fdb29a7bea268b023
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853337"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualisieren von Containern in Azure Container Instances

Während des normalen Betriebs Ihrer Containerinstanzen ist es unter Umständen erforderlich, die in einer [Containergruppe](./container-instances-container-groups.md) ausgeführten Container zu aktualisieren. Beispielsweise möchten Sie möglicherweise eine Eigenschaft aktualisieren, z. B. eine Imageversion, einen DNS-Namen oder eine Umgebungsvariable, oder eine Eigenschaft in einem Container aktualisieren, dessen Anwendung abgestürzt ist.

Aktualisieren Sie die in einer Containergruppe ausgeführten Container, indem Sie eine vorhandene Gruppe mit mindestens einer geänderten Eigenschaft erneut bereitstellen. Wenn Sie eine Containergruppe aktualisieren, werden alle ausgeführten Container in der Gruppe direkt neu gestartet. Dies erfolgt in der Regel auf demselben zugrunde liegenden Containerhost.

> [!NOTE]
> Beendete oder gelöschte Containergruppen können nicht aktualisiert werden. Nachdem eine Containergruppe beendet (Erfolgs- oder Fehlerstatus) oder gelöscht wurde, muss die Gruppe als neue Gruppe bereitgestellt werden. Weitere Informationen finden Sie unter [Einschränkungen](#limitations).

## <a name="update-a-container-group"></a>Aktualisieren einer Containergruppe

Aktualisieren einer vorhandenen Containergruppe:

* Geben Sie den Befehl „create“ ein (oder verwenden Sie das Azure-Portal), und geben Sie den Namen einer vorhandenen Gruppe an. 
* Ändern Sie mindestens eine Eigenschaft der Gruppe, die Updates bei der erneuten Bereitstellung unterstützt, oder fügen Sie eine solche Eigenschaft hinzu. Bestimmte Eigenschaften [unterstützen keine Updates](#properties-that-require-container-delete).
* Legen Sie andere Eigenschaften mit den Werten fest, die Sie zuvor angegeben haben. Wenn Sie keinen Wert für eine Eigenschaft festlegen, wird der Standardwert wiederhergestellt.

> [!TIP]
> Eine [YAML-Datei](./container-instances-container-groups.md#deployment) hilft bei der Verwaltung der Bereitstellungskonfiguration einer Containergruppe und bietet einen Startpunkt für die Bereitstellung einer aktualisierten Gruppe. Wenn Sie eine andere Methode zum Erstellen der Gruppe verwendet haben, können Sie die Konfiguration mithilfe des Befehls [az container export][az-container-export] in eine YAML-Datei exportieren. 

### <a name="example"></a>Beispiel

Das folgende Azure CLI-Beispiel aktualisiert eine Containergruppe mit einer neuen DNS-Namensbezeichnung. Da die Eigenschaft der DNS-Namensbezeichnung der Gruppe geändert werden kann, wird die Containergruppe erneut bereitgestellt, und ihre Container werden neu gestartet.

Erste Bereitstellung mit der DNS-Namensbezeichnung *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aktualisieren Sie die Containergruppe mit der neuen DNS-Namensbezeichnung *myapplication*, und legen Sie die übrigen Eigenschaften mithilfe der zuvor verwendeten Werte fest:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Vorteile der Aktualisierung

Der Hauptvorteil der Aktualisierung einer vorhandenen Containergruppe ist die schnellere Bereitstellung. Wenn Sie eine bereits vorhandene Containergruppe erneut bereitstellen, werden die Containerimageebenen aus der Zwischenspeicherung der vorherigen Bereitstellung abgerufen. Statt wie bei neuen Bereitstellungen alle Imageebenen direkt aus der Registrierung abzurufen, werden nur geänderte Ebenen (sofern vorhanden) abgerufen.

Bei Anwendungen, die auf größeren Containerimages wie Windows Server Core basieren, wird die Bereitstellung erheblich beschleunigt, wenn Sie Container aktualisieren, statt sie zu löschen und neu bereitzustellen.

## <a name="limitations"></a>Einschränkungen

* Nicht alle Eigenschaften einer Containergruppe unterstützen die Aktualisierung. Bei einigen Eigenschaften einer Containergruppe müssen Sie zum Ändern die Gruppe zuerst löschen und anschließend erneut bereitstellen. Weitere Informationen finden Sie unter [Eigenschaften, für die eine Löschung der Container erforderlich ist](#properties-that-require-container-delete).
* Alle Container in einer Containergruppe werden neu gestartet, wenn Sie die Containergruppe aktualisieren. Sie können keine Aktualisierung und keinen direkten Neustart eines bestimmten Containers in einer Gruppe mit mehreren Containern ausführen.
* Die IP-Adresse einer Containergruppe ändert sich bei Updates in der Regel nicht. Es wird jedoch nicht garantiert, dass sie gleich bleibt. Wird die Containergruppe auf dem gleichen zugrunde liegenden Host bereitgestellt, behält die Containergruppe ihre IP-Adresse. Obwohl es selten vorkommt, gibt es einige interne Azure-Ereignisse, die eine erneute Bereitstellung auf einem anderen Host verursachen können. Es wird empfohlen, stets eine DNS-Namensbezeichnung für Ihre Containerinstanzen zu verwenden, um dieses Problem zu umgehen.
* Beendete oder gelöschte Containergruppen können nicht aktualisiert werden. Sobald eine Containergruppe beendet (Status: *Beendet*) oder gelöscht wird, wird die Gruppe als neue Gruppe bereitgestellt.

## <a name="properties-that-require-container-delete"></a>Eigenschaften, für die eine Löschung der Container erforderlich ist

Nicht alle Eigenschaften der Containergruppe können aktualisiert werden. Wenn Sie beispielsweise die Neustartrichtlinie eines Containers ändern möchten, müssen Sie zuerst die Containergruppe löschen und anschließend nochmals erstellen.

Damit Änderungen an diesen Eigenschaften vorgenommen werden können, muss vor der erneuten Bereitstellung die Containergruppe gelöscht werden:

* Betriebssystemtyp
* CPU-, Arbeitsspeicher- oder GPU-Ressourcen
* Neustartrichtlinie
* Netzwerkprofil
* Verfügbarkeitszone

Wenn Sie eine Containergruppe löschen und erneut erstellen, wird sie nicht erneut bereitgestellt, sondern neu erstellt. Alle Imageebenen werden neu aus der Registrierung und nicht aus der Zwischenspeicherung einer vorherigen Bereitstellung abgerufen. Die IP-Adresse des Containers kann sich unter Umständen ebenfalls ändern, weil die erneute Bereitstellung auf einem anderen zugrunde liegenden Host erfolgt.

## <a name="next-steps"></a>Nächste Schritte

Die **Containergruppe** wurde in diesem Artikel bereits mehrmals erwähnt. Jeder Container in Azure Container Instances wird in einer Containergruppe bereitgestellt, und Containergruppen können mehrere Container enthalten.

[Containergruppen in Azure Container Instances](./container-instances-container-groups.md)

[Bereitstellen einer Containergruppe](container-instances-multi-container-group.md)

[Manuelles Beenden oder Starten von Containern in Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
