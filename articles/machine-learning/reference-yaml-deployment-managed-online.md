---
title: 'CLI (v2) verwaltete Onlinebereitstellung: YAML-Schema'
titleSuffix: Azure Machine Learning
description: Referenzdokumentation für das YAML-Schema der CLI (v2) verwalteten Onlinebereitstellung.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: 82c215dd0b7bccfcb53da0952cba82f89105c38a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137847"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>CLI (v2) verwaltete Onlinebereitstellung: YAML-Schema

Das JSON-Quellschema finden Sie unter https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>YAML-Syntax

| Schlüssel | type | BESCHREIBUNG | Zulässige Werte | Standardwert |
| --- | ---- | ----------- | -------------- | ------------- |
| `$schema` | Zeichenfolge | Das YAML-Schema Wenn Sie die VS Code-Erweiterung für Azure Machine Learning zum Erstellen einer YAML-Datei verwenden, können Sie durch das Einfügen von `$schema` am Anfang der Datei Schema- und Ressourcenvervollständigungen aufrufen. | | |
| `name` | Zeichenfolge | **Erforderlich.** Name der Bereitstellung <br><br> Benennungsregeln sind [hier](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview) definiert.| | |
| `description` | Zeichenfolge | Beschreibung des Bereitstellung. | | |
| `tags` | Objekt (object) | Wörterbuch der Tags für die Bereitstellung. | | |
| `endpoint_name` | Zeichenfolge | **Erforderlich.** Name des Endpunkts, unter dem die Bereitstellung erstellt werden soll. | | |
| `model` | Zeichenfolge oder Objekt | Das für die Bereitstellung zu verwendende Modell. Dieser Wert kann entweder ein Verweis auf ein vorhandenes versioniertes Modell im Arbeitsbereich oder eine Inline-Modellspezifikation sein. <br><br> Um auf ein bestehendes Modell zu verweisen, verwenden Sie die Syntax `azureml:<model-name>:<model-version>`. <br><br> Um ein Modell inline zu definieren, folgen Sie dem [Modellschema](reference-yaml-model.md#yaml-syntax). <br><br> Als bewährtes Verfahren für Produktionsszenarien sollten Sie das Modell separat erstellen und hier darauf verweisen. <br><br> Dieses Feld ist für Szenarien mit [benutzerdefinierter Containerbereitstellung](how-to-deploy-custom-container.md) optional.| | |
| `model_mount_path` | Zeichenfolge | Der Pfad zum Einbinden des Modells in einen benutzerdefinierten Container. Gilt nur für Szenarien mit [benutzerdefinierter Containerbereitstellung](how-to-deploy-custom-container.md). Wenn das Feld `model` angegeben ist, wird es unter diesem Pfad im Container eingebunden. | | |
| `code_configuration` | Objekt (object) | Konfiguration für die Logik des Bewertungscodes. <br><br> Dieses Feld ist für Szenarien mit [benutzerdefinierter Containerbereitstellung](how-to-deploy-custom-container.md) optional. | | |
| `code_configuration.code.local_path` | Zeichenfolge | Lokaler Pfad zum Quellcodeverzeichnis für die Bewertung des Modells. | | |
| `code_configuration.scoring_script` | Zeichenfolge | Relativer Pfad zur Bewertungsdatei im Quellcodeverzeichnis. | | |
| `environment` | Zeichenfolge oder Objekt | **Erforderlich.** Die für die Bereitstellung zu verwendende Umgebung. Dieser Wert kann entweder ein Verweis auf eine vorhandene versionierte Umgebung im Arbeitsbereich oder eine Inline-Umgebungsspezifikation sein. <br><br> Verwenden Sie die Syntax `azureml:<environment-name>:<environment-version>`, um auf eine vorhandene Umgebung zu verweisen. <br><br> Um eine Umgebung inline zu definieren, folgen Sie dem [Umgebungsschema](reference-yaml-environment.md#yaml-syntax). <br><br> Als bewährtes Verfahren für Produktionsszenarien sollten Sie die Umgebung separat erstellen und hier referenzieren. | | |
| `instance_type` | Zeichenfolge | **Erforderlich.** Die VM-Größe, die für die Bereitstellung verwendet werden soll. Eine Liste der unterstützten Größen finden Sie unter [SKU-Liste für verwaltete Onlineendpunkte](reference-managed-online-endpoints-vm-sku-list.md). | | |
| `instance_count` | integer | **Erforderlich.** Die Anzahl der Instanzen, die für die Bereitstellung verwendet werden sollen. Geben Sie den Wert basierend auf der erwarteten Workload an. Für die Hochverfügbarkeit empfiehlt Microsoft, den Wert mindestens auf `3` festzulegen. <br><br> `instance_count` kann nach der Erstellung der Bereitstellung mit dem Befehl `az ml online-deployment update` aktualisiert werden. | | |
| `app_insights_enabled` | boolean | Gibt an, ob die Integration mit der Azure Application Insights-Instanz, die Ihrem Arbeitsbereich zugeordnet ist, aktiviert werden soll. | | `false` |
| `scale_settings` | Objekt (object) | Die Skalierungseinstellungen für die Bereitstellung. Derzeit wird nur der Skalierungstyp `default` unterstützt, sodass Sie diese Eigenschaft nicht angeben müssen. <br><br> Mit diesem `default`-Skalierungstyp können Sie entweder 1) die Anzahl der Instanzen nach der Erstellung der Bereitstellung manuell hoch- und herunterskalieren, indem Sie die `instance_count`-Eigenschaft aktualisieren, oder 2) eine [Richtlinie für die automatische Skalierung]() erstellen. | | |
| `scale_settings.type` | Zeichenfolge | Der Skalierungstyp. | `default` | `default` |
| `request_settings` | Objekt (object) | Einstellungen für die Bewertungsanforderung für die Bereitstellung. Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [RequestSettings](#requestsettings). | | |
| `liveness_probe` | Objekt (object) | Livetesteinstellungen zum regelmäßigen Überwachen der Integrität des Containers. Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [ProbeSettings](#probesettings). | | |
| `readiness_probe` | Objekt (object) | Bereitschaftstesteinstellungen zum Überprüfen, ob der Container für die Bereitstellung von Datenverkehr bereit ist. Informationen zu den konfigurierbaren Eigenschaften finden Sie unter [ProbeSettings](#probesettings). | | |

### <a name="requestsettings"></a>RequestSettings

| Schlüssel | type | Beschreibung | Standardwert |
| --- | ---- | ----------- | ------------- |
| `request_timeout_ms` | integer | Das Bewertungstimeout in Millisekunden | `5000` |
| `max_concurrent_requests_per_instance` | integer | Die maximale Anzahl gleichzeitiger Anforderungen pro Instanz, die für die Bereitstellung zulässig sind. <br><br> **Ändern Sie für diese Einstellung nicht den Standardwert, es sei denn, dies wird vom technischen Support von Microsoft oder einem Mitglied des Azure ML-Teams angeordnet.** | `1` |
| `max_queue_wait_ms` | integer | Die maximale Zeitdauer in Millisekunden, die eine Anforderung in der Warteschlange bleibt. | `500` |

### <a name="probesettings"></a>ProbeSettings

| Schlüssel | type | Beschreibung | Standardwert |
| --- | ---- | ----------- | ------------- |
| `period` | integer | Gibt an, wie häufig (in Sekunden) ein Test durchgeführt werden soll. | `10` |
| `initial_delay` | integer | Die Anzahl der Sekunden, die nach dem Start des Containers vergehen, bevor der Test initiiert wird. Der Mindestwert ist `1`. | `10` |
| `timeout` | integer | Die Anzahl von Sekunden, nach denen der Timeout für den Test auftritt. Der Mindestwert ist `1`. | `2` |
| `success_threshold` | integer | Die Anzahl der Erfolge, die mindestens aufeinander folgen müssen, damit ein Test nach einem Fehler wieder als erfolgreich betrachtet wird. Der Mindestwert ist `1`. | `1` |
| `failure_threshold` | integer | Wenn ein Test fehlschlägt, versucht das System den Vorgang `failure_threshold` Mal, bevor es aufgibt. Für einen Livetest bedeutet das Aufgeben einen Neustart des Containers. Im Falle eines Bereitschaftstests wird der Container als „Nicht bereit“ markiert. Der Mindestwert ist `1`. | `30` |

## <a name="remarks"></a>Hinweise

Die `az ml online-deployment`-Befehle können für die Verwaltung von verwalteten Azure Machine Learning-Onlinebereitstellungen verwendet werden.

## <a name="examples"></a>Beispiele

Beispiele finden Sie im [GitHub-Beispielrepository](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online). Im Folgenden sind mehrere aufgeführt.

## <a name="yaml-basic"></a>YAML: Grundlagen

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml":::

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/green-deployment.yml":::

## <a name="yaml-system-assigned-identity"></a>YAML: Systemseitig zugewiesene Identität

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

## <a name="yaml-user-assigned-identity"></a>YAML: Benutzerseitig zugewiesene Identität

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

## <a name="next-steps"></a>Nächste Schritte

- [Installieren und Verwenden der CLI (V2)](how-to-configure-cli.md)
