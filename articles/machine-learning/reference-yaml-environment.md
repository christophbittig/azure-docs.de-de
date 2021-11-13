---
title: 'CLI-Umgebung (v2): YAML-Schema'
titleSuffix: Azure Machine Learning
description: Referenzdokumentation zum YAML-Schema für die CLI-Umgebung (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 8f812b9c02ef14d7e098c7f50e3a1c3cfabe97a6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058352"
---
# <a name="cli-v2-environment-yaml-schema"></a>CLI-Umgebung (v2): YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/environment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | Zeichenfolge | Das YAML-Schema. Wenn Sie die Azure Machine Learning VS Code-Erweiterung verwenden, um die YAML-Datei zu erstellen, können Sie durch Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Der Name der Umgebung. | | |
| `version` | Zeichenfolge | Die Version der Umgebung. Wird diese Angabe weggelassen, generiert Azure ML automatisch eine Version. | | |
| `description` | Zeichenfolge | Die Beschreibung der Umgebung. | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für die Umwelt. | | |
| `image` | Zeichenfolge | Das Docker-Image, das für die Umgebung verwendet werden soll. **Eines von `image` oder `build` ist erforderlich.** | | |
| `conda_file` | Zeichenfolge oder Objekt | Die Standard-Conda-YAML-Konfigurationsdatei mit den Abhängigkeiten für eine Conda-Umgebung. Siehe https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually. <br> <br> Falls angegeben, muss auch `image` angegeben werden. Azure ML wird die conda-Umgebung auf dem bereitgestellten Docker-Image aufbauen. | | |
| `build` | Objekt (object) | Die für die Umgebung zu verwendende Docker-Build-Kontext-Konfiguration. **Eines von `image` oder `build` ist erforderlich.** | | |
| `build.local_path` | Zeichenfolge | Lokaler Pfad zu dem Verzeichnis, das als Build-Kontext verwendet werden soll. | | |
| `build.dockerfile_path` | Zeichenfolge | Relativer Pfad zum Dockerfile innerhalb des Build-Kontextes. | | `Dockerfile` |
| `os_type` | Zeichenfolge | Die Art des Betriebssystems. | `linux`, `windows` | `linux` |  
| `inference_config` | Objekt (object) | Konfigurationen der Rückschließen behälter. Nur anwendbar, wenn die Umgebung zur Erstellung eines Serving-Containers für Online-Bereitstellungen verwendet wird. Siehe [Attribute der `inference_config` Schlüssel](#attributes-of-the-inference_config-key). | | |

### <a name="attributes-of-the-inference_config-key"></a>Attribute des `inference_config`-Schlüssels

| Schlüssel | type | BESCHREIBUNG |
| --- | ---- | ----------- |
| `liveness_route` | Objekt (object) | Die Liveness-Route für den Serving-Container. |
| `liveness_route.path` | Zeichenfolge | Der Pfad, an den Liveness-Anforderung weitergeleitet werden. |
| `liveness_route.port` | integer | Der Port, an den Liveness-Anforderung weitergeleitet werden. |
| `readiness_route` | Objekt (object) | Die Bereitschaftsroute für den Serviercontainer. |
| `readiness_route.path` | Zeichenfolge | Der Pfad, an den Bereitschafts Anforderung weitergeleitet werden sollen. |
| `readiness_route.port` | integer | Der Anschluss, an den Bereitschafts Anforderung weitergeleitet werden sollen. |
| `scoring_route` | Objekt (object) | Die Wertungsstrecke für den Servierbehälter. |
| `scoring_route.path` | Zeichenfolge | Der Pfad, an den Scoring-Anforderung weitergeleitet werden. |
| `scoring_route.port` | integer | Der Anschluss, an den Scoring-Anforderung weitergeleitet werden. |

## <a name="remarks"></a>Hinweise

Mit dem Befehl `az ml environment` können Sie Azure Machine Learning-Umgebungen verwalten.

## <a name="examples"></a>Beispiele

Beispiele sind im [Beispiele GitHub-Repository](https://github.com/Azure/azureml-examples/tree/main/cli/assets/environment) verfügbar. Einige davon sind unten aufgeführt.

## <a name="yaml-local-docker-build-context"></a>YAML: lokaler Docker-Baukontext

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-context.yml":::

## <a name="yaml-docker-image"></a>YAML: Docker-Image

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image.yml":::

## <a name="yaml-docker-image-plus-conda-file"></a>YAML: Docker-Image plus Conda-Datei

:::code language="yaml" source="~/azureml-examples-main/cli/assets/environment/docker-image-plus-conda.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
