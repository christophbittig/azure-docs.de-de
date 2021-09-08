---
title: YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die YAML-Dateien, die zum Bereitstellen von Modellen als verwaltete Onlineendpunkte verwendet werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: ebb4db34c802c1cd82e0065579cc97ffeb3fe642
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346229"
---
# <a name="cli-v2-managed-online-endpoint-yaml-schema"></a>CLI (v2) verwalteter Onlineendpunkt: YAML-Schema

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Ein vollständig angegebener YAML-Beispielcode für verwaltete Onlineendpunkte steht als [Referenz](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml) zur Verfügung.

## <a name="schema"></a>Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json. Das Schema wird der Einfachheit halber nachstehend im JSON- und YAML-Format bereitgestellt.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/managedOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/managedOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>Hinweise

| Key | BESCHREIBUNG |
| --- | --- |
| $schema    | \[__Optional__\] Das YAML-Schema Sie können das Schema aus dem obigen Beispiel in einem Browser betrachten, um alle verfügbaren Optionen in der YAML-Datei anzuzeigen.|
| name       | Name des Endpunkts. Muss auf Azure-Regionsebene eindeutig sein.|
| traffic | Prozentsatz des Datenverkehrs vom Endpunkt, der zu den einzelnen Bereitstellungen umgeleitet wird. Datenverkehrswerte müssen addiert 100 ergeben. |
| auth_mode | Verwenden Sie `key` für die schlüsselbasierte Authentifizierung und `aml_token` für die tokenbasierte Azure Machine Learning-Authentifizierung. `key` läuft nicht ab, dafür aber `aml_token`. Rufen Sie das letzte Token mit dem Befehl `az ml endpoint list-keys` ab. |
| Identität | Wird verwendet, um systemseitig zugewiesene und benutzerseitig zugewiesene verwaltete Identitäten zu konfigurieren. |
| app_insights_enabled | `True`, um die Integration in Azure AppInsights zu aktivieren, die Ihrem Azure Machine Learning-Arbeitsbereich zugeordnet ist. `False` standardmäßig.
| tags | Wörterbuch mit Azure-Tags, die dem Endpunkt zugeordnet werden sollen. |
| description | Eine Beschreibung des Endpunkts. |
| target | Wenn dieser Schlüssel nicht definiert ist, wird der Endpunkt als verwalteter Onlineendpunkt bereitgestellt. Legen Sie den Wert dieses Schlüssels auf den Namen des registrierten Computeziels fest, z. B. `target:azureml:my-aks`, um AKS zu verwenden. 
| deployments | Enthält eine Liste der Bereitstellungen, die am Endpunkt erstellt werden sollen. In diesem Fall gibt es nur eine Bereitstellung namens `blue`. |

### <a name="attributes-of-the-deployments-key"></a>Attribute des `deployments`-Schlüssels.
 
| Key | BESCHREIBUNG |
| --- | --- |
| name  | Der Name der Bereitstellung |
| model | Der Name der registrierten Modellversion im Formular `model: azureml:my-model:1`. Sie können Modelleigenschaften auch inline angeben: `name`, `version` und `local_path`. Die Modelldateien werden automatisch hochgeladen und registriert. Ein Nachteil der Inlinespezifikation ist, dass Sie die Version manuell erhöhen müssen, wenn Sie die Modelldateien aktualisieren möchten.|
| code_configuration.code.local_path | Das Verzeichnis, das den gesamten Python-Quellcode für die Bewertung des Modells enthält. Geschachtelte Verzeichnisse/Pakete werden unterstützt. |
| code_configuration.scoring_script | Die Python-Datei im obigen Bewertungsverzeichnis. Dieser Python-Code muss über eine `init()`- und eine `run()`-Funktion verfügen. Die Funktion `init()` wird aufgerufen, nachdem das Modell erstellt oder aktualisiert wurde (Sie können es verwenden, um das Modell im Arbeitsspeicher zwischenzuspeichern usw.). Die Funktion `run()` wird bei jedem Aufruf des Endpunkts aufgerufen, um die tatsächliche Bewertung/Vorhersage auszuführen. |
| Environment | Enthält die Details der Azure Machine Learning-Umgebung zum Hosten des Modells und des Codes. Als bewährte Methode für die Produktion sollten Sie das Modell und die Umgebung separat registrieren und den registrierten Namen sowie die Version im YAML-Code angeben. Beispiel: `environment: azureml:my-env:1`. |
| instance_type | Die VM-SKU zum Hosten Ihrer Bereitstellungsinstanzen. Weitere Informationen finden Sie unter [Unterstützte VM-SKUs für verwaltete Onlineendpunkte](reference-managed-online-endpoints-vm-sku-list.md).|
| scale_settings.scale_type | Zurzeit muss dieser Wert auf `manual` festgelegt werden. Um nach dem Erstellen des Endpunkts und der Bereitstellung hoch- oder herunterskalieren zu können, aktualisieren Sie `instance_count` im YAML-Code, und führen Sie den Befehl `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` aus. |
| scale_settings.instance_count | Die Anzahl der Instanzen in der Bereitstellung. Richten Sie den Wert nach der zu erwartenden Workload. Für die Hochverfügbarkeit empfiehlt Microsoft, den Wert mindestens auf `3` festzulegen. |
| scale_settings.min_instances | Die Mindestanzahl von Instanzen, die immer vorhanden sein sollen. |
| scale_settings.max_instances | Die maximale Anzahl der Instanzen, auf die die Bereitstellung skaliert werden kann. Das Kontingent wird für max_instances reserviert. |
| request_settings.request_timeout_ms | Das Bewertungstimeout in Millisekunden. Der Standardwert für verwaltete Onlineendpunkte beträgt 5000. |
| request_settings.max_concurrent_requests_per_instance | Die Anzahl der maximal zulässigen gleichzeitigen Anforderungen pro Knoten pro Bereitstellung. Der Standardwert lautet 1. __Ändern Sie für diese Einstellung nicht den Standardwert 1, es sei denn, dies wird vom technischen Support von Microsoft oder einem Mitglied des Azure Machine Learning-Teams angeordnet.__ |
| request_settings.max_queue_wait_ms | Die maximale Zeitdauer, die eine Anforderung in der Warteschlange bleibt (in Millisekunden). Standardwerte auf 500. |
| liveness_probe | Der Livetest überwacht regelmäßig die Integrität des Containers. |
| liveness_probe.period | Gibt an, wie häufig (in Sekunden) ein Livetest durchgeführt werden soll. Der Standardwert ist 10 Sekunden. Der Mindestwert ist 1. |
| liveness_probe.initial_delay | Die Anzahl der Sekunden, die nach dem Start des Containers vergehen, bevor Livetests initiiert werden. Der Standardwert ist 10. |
| liveness_probe.timeout | Die Anzahl der Sekunden, nach denen das Zeitlimit für den Livetest überschritten ist. Der Standardwert ist 2 Sekunden. Der Mindestwert ist 1. |
| liveness_probe.failure_threshold | Das System versucht, failure_threshold mal zu testen, bevor es abbricht. Der Standardwert ist 30. Der Mindestwert ist 1. |
| liveness_probe.success_threshold | Die Anzahl der Erfolge, die mindestens aufeinander folgen müssen, damit ein Livetest nach einem Fehler wieder als erfolgreich betrachtet wird. Der Standardwert lautet 1. Der Mindestwert ist 1. |
| readiness_probe | Der Bereitschaftstest überprüft, ob der Container bereit ist, Datenverkehr auszuliefern. Die Eigenschaften und Standardwerte sind identisch mit dem Livetest. |
| tags | Ein Wörterbuch mit Azure-Tags, das der Bereitstellung zugeordnet werden soll. |
| description | Eine Beschreibung der Bereitstellung. |

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (v2)](how-to-configure-cli.md)
- Erfahren Sie, wie Sie ein [Modell mit einem verwalteten Onlineendpunkt bereitstellen](how-to-deploy-managed-online-endpoints.md).
- [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](how-to-troubleshoot-managed-online-endpoints.md)
