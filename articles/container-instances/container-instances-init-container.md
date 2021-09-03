---
title: Ausführen von Init-Containern
description: Führen Sie Init-Container in Azure Container Instances aus, um vor der Ausführung der Anwendungscontainer Einrichtungsaufgaben in einer Containergruppe auszuführen.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: a108e76f76fb773d0f982a38b6415f9cd9937001
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342915"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Ausführen eines Init-Containers für Einrichtungsaufgaben in einer Containergruppe

Azure Container Instances unterstützt *Init-Container* in einer Container Gruppe. Init-Container werden vollständig ausgeführt, bevor der oder die Anwendungscontainer gestartet werden. Ähnlich wie bei [Kubernetes-Init-Containern](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) verwenden Sie mindestens einen Init-Container, um die Initialisierungslogik für Ihre App-Container auszuführen, z. B. das Festlegen von Konten, das Ausführen von Setupskripts oder die Konfiguration von Datenbanken.

In diesem Artikel erfahren Sie, wie Sie eine Containergruppe in einem Init-Container mit einer Azure Resource Manager-Vorlage konfigurieren.

## <a name="things-to-know"></a>Wichtige Hinweise

* **API-Version**: Sie benötigen mindestens Version 2019-12-01 der Azure Container Instances API, um Init-Container bereitzustellen. Verwenden Sie eine `initContainers`-Eigenschaft in einer [YAML-Datei](container-instances-multi-container-yaml.md) oder eine [Resource Manager-Vorlage](container-instances-multi-container-group.md) für die Bereitstellung.
* **Ausführungsreihenfolge**: Init-Container werden in der in der Vorlage angegebenen Reihenfolge und vor anderen Containern ausgeführt. Standardmäßig können Sie maximal 59 Init-Container pro Containergruppe angeben. Die Gruppe muss mindestens einen Nicht-Init-Container enthalten.
* **Hostumgebung**: Init-Container werden auf derselben Hardware wie die anderen Container in der Gruppe ausgeführt.
* **Ressourcen**: Für Init-Container werden keine Ressourcen angegeben. Sie haben Zugriff auf sämtliche Ressourcen wie CPUs und Arbeitsspeicher, die für die Containergruppe verfügbar sind. Während ein Init-Container ausgeführt wird, werden keine anderen Container in der Gruppe ausgeführt.
* **Unterstützte Eigenschaften**: Init-Container können Gruppeneigenschaften wie Volumes, Geheimnisse und verwaltete Identitäten verwenden. Allerdings können sie keine Ports oder eine IP-Adresse verwenden, wenn diese für die Containergruppe konfiguriert sind. 
* **Neustartrichtlinie**: Jeder Init-Container muss erfolgreich beendet werden, bevor der nächste Container in der Gruppe gestartet wird. Wenn ein Init-Container nicht erfolgreich beendet wird, richtet sich seine Neustartaktion nach der [Neustartrichtlinie](container-instances-restart-policy.md), die für die Gruppe konfiguriert ist:

    |Richtlinie in Gruppe  |Richtlinie in Init  |
    |---------|---------|
    |Always     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Nie     |Nie         |
* **Gebühren**: Für die Containergruppe fallen Gebühren für die erste Bereitstellung eines Init-Containers an.

## <a name="resource-manager-template-example"></a>Beispiel mit Resource Manager-Vorlage

Beginnen Sie, indem Sie den folgenden JSON-Code in eine neue Datei mit dem Namen `azuredeploy.json` kopieren. Durch die Vorlage wird eine Containergruppe mit einem Init-Container und zwei Anwendungscontainern eingerichtet:

* Der Container *init1* führt das Image [busybox](https://hub.docker.com/_/busybox) aus. Er wechselt 60 Sekunden lang in den Ruhezustand und schreibt dann eine Befehlszeilenzeichenfolge in eine Datei auf einem [emptyDir-Volume](container-instances-volume-emptydir.md).
* Beide Anwendungscontainer führen das Microsoft-Containerimage `aci-wordcount` aus:
    * Der *hamlet*-Container führt in der Standardkonfiguration die WordCount-App aus, mit der die Häufigkeit von Wörtern in Shakespeares *Hamlet* berechnet wird.
    * Der *juliet*-App-Container liest die Befehlszeilenzeichenfolge aus dem emptDir-Volume, um die WordCount-App stattdessen für Shakespeares *Romeo und Julia* auszuführen.

Weitere Informationen und Beispiele zur Verwendung des `aci-wordcount`-Image finden Sie unter [Festlegen von Umgebungsvariablen in Container Instances](container-instances-environment-variables.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "mcr.microsoft.com/aks/e2e/library-busybox:master.210714.1",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe.

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie die Vorlage mit dem Befehl [az deployment group create][az-deployment-group-create] bereit.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

In einer Gruppe mit einem Init-Container dauert die Bereitstellung länger, da die Ausführung des oder der Init-Container eine gewisse Zeit in Anspruch nimmt.


## <a name="view-container-logs"></a>Containerprotokolle anzeigen

Um zu überprüfen, ob der Init-Container erfolgreich ausgeführt wurde, können Sie die Protokollausgabe der App-Container mithilfe des Befehls [az container logs][az-container-logs] anzeigen. Das `--container-name`-Argument gibt den Container an, aus dem Protokolle erhalten werden können. In diesem Beispiel rufen Sie die Protokolle für die Container *hamlet* und *juliet* ab, die zu unterschiedlichen Befehlsausgaben führen:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Ausgabe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Ausgabe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nächste Schritte

Init-Container unterstützen Sie beim Ausführen von Einrichtungs- und Initialisierungsaufgaben für Ihre Anwendungscontainer. Weitere Informationen zum Ausführen aufgabenbasierter Container finden Sie unter [Ausführen von Aufgaben in Containern mit Neustartrichtlinien](container-instances-restart-policy.md).

Azure Container Instances bietet weitere Optionen zum Ändern des Verhaltens von Anwendungscontainern. Beispiele:

* [Festlegen von Umgebungsvariablen in Container Instances](container-instances-environment-variables.md)
* [Festlegen der Befehlszeile in einer Containerinstanz zur Außerkraftsetzung der Standard-Befehlszeilenoperation](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
