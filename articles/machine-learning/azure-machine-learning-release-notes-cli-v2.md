---
title: CLI (v2) Versionshinweise
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die neuesten Updates für Azure Machine Learning CLI (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: 9619b98ad824de757273cbe38567b5f9418686a3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566695"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Azure Machine Learning CLI (v2) Versionshinweise

In diesem Artikel erfahren Sie mehr über Azure Machine Learning CLI (v2) Releases.

__RSS-Feed__: Lassen Sie sich benachrichtigen, wenn diese Seite aktualisiert wird, indem Sie die folgende URL kopieren und in Ihren Feedreader einfügen: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v201a6"></a>Azure Machine Learning CLI (v2) v2.0.1a6

- `az ml workspace`
  - Aktualisiertes [Arbeitsbereich-YAML-Schema](reference-yaml-workspace.md)
- `az ml compute`
  - Aktualisierte YAML-Schemata für [AmlCompute](reference-yaml-compute-aml.md) und [Compute Instance](reference-yaml-compute-instance.md)
  - Die Unterstützung für AKS-Anhänge über `az ml compute attach` wurde entfernt. Azure Arc-fähige Kubernetes-Anhänge werden in der nächsten Version unterstützt
- `az ml datastore`
  - Aktualisierte YAML-Schemata für [Azure Blob](reference-yaml-datastore-blob.md), [Azure File](reference-yaml-datastore-files.md), [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) und [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) Datenspeicher
  - Unterstützung für die Erstellung von Azure Data Lake Storage Gen1 und Gen2 Datastores hinzugefügt
- `az ml job`
  - Aktualisierte YAML-Schemata für [Befehlsjob](reference-yaml-job-command.md) und [Sweepjob](reference-yaml-job-sweep.md)
  - Unterstützung für die Ausführung von Pipeline-Jobs hinzugefügt ([Pipeline-Job-YAML-Schema](reference-yaml-job-pipeline.md))
  - Unterstützung für Job-Eingabeliterale und Eingabedaten-URIs für alle Jobtypen hinzugefügt
  - Unterstützung für Auftragsausgaben für alle Auftragstypen hinzugefügt
  - Die Syntax des Ausdrucks wurde von `{ <expression> }` auf `${{ <expression> }}` geändert. Weitere Informationen finden Sie unter [Ausdrucksyntax für die Konfiguration von Azure ML-Jobs](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components)
- `az ml environment`
  - Aktualisiertes [Umgebungs-YAML-Schema](reference-yaml-environment.md)
  - Unterstützung für die Erstellung von Umgebungen aus dem Docker-Build-Kontext hinzugefügt
- `az ml model`
  - Aktualisiertes [Modell YAML-Schema](reference-yaml-model.md)
  - Neue Eigenschaft `model_format` zu Model für no-code Bereitstellung Szenarien hinzugefügt
- `az ml dataset`
  - Umbenennung der Untergruppe `az ml data` in `az ml dataset`
  - Aktualisiertes [Datensatz-YAML-Schema](reference-yaml-dataset.md)
- `az ml component`
  - Hinzufügen der `az ml component` Befehle zur Verwaltung von Azure ML-Komponenten
  - Unterstützung für Befehlskomponenten hinzugefügt ([Befehlskomponente YAML-Schema](reference-yaml-component-command.md))
- `az ml online-endpoint`
  - Die Untergruppe `az ml endpoint` wurde in zwei separate Gruppen aufgeteilt: `az ml online-endpoint` und `az ml batch-endpoint`
  - Aktualisiertes [Online-Endpunkt-YAML-Schema](reference-yaml-endpoint-managed-online.md)
  - Unterstützung für lokale Endpunkte für Entwicklungs-/Testszenarien hinzugefügt
  - Interaktive VSCode-Debugging-Unterstützung für lokale Endpunkte hinzugefügt (das `--vscode-debug`-Flag wurde zu `az ml batch-endpoint create/update` hinzugefügt)
- `az ml online-deployment`
  - Die Untergruppe `az ml deployment` wurde in zwei separate Gruppen aufgeteilt: `az ml online-deployment` und `az ml batch-deployment`
  - Aktualisiertes [verwaltetes Online-Bereitstellungs-YAML-Schema](reference-yaml-endpoint-managed-online.md)
  - Unterstützung für automatische Skalierung durch Integration mit Azure Monitor Autoscale hinzugefügt
  - Unterstützung für die Aktualisierung mehrerer Online-Bereitstellungseigenschaften im selben Aktualisierungsvorgang hinzugefügt
  - Unterstützung für die Durchführung gleichzeitiger Operationen auf Bereitstellungen unter demselben Endpunkt hinzugefügt
- `az ml batch-endpoint`
  - Die Untergruppe `az ml endpoint` wurde in zwei separate Gruppen aufgeteilt: `az ml online-endpoint` und `az ml batch-endpoint`
  - Aktualisiertes [Batch-Endpunkt-YAML-Schema](reference-yaml-endpoint-batch.md)
  - Die Eigenschaft `traffic` wurde entfernt und durch eine konfigurierbare Standard-Eigenschaft ersetzt
  - Unterstützung für Eingabedaten-URIs für `az ml batch-endpoint invoke` hinzugefügt
  - Unterstützung für VNet eingehend (private Verbindung) hinzugefügt
- `az ml batch-deployment`
  - Die Untergruppe `az ml deployment` wurde in zwei separate Gruppen aufgeteilt: `az ml online-deployment` und `az ml batch-deployment`
  - Aktualisiertes [Batch Bereitstellung YAML Schema](reference-yaml-deployment-batch.md)

## <a name="2021-05-25"></a>25.05.2021

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Ankündigung der CLI (v2) (Vorschauversion) für Azure Machine Learning

Die Erweiterung `ml` für die Azure CLI ist die Benutzeroberfläche der nächsten Generation für Azure Machine Learning. Hiermit können Sie Modelle über die Befehlszeile trainieren und bereitstellen und Features nutzen, mit denen beim Nachverfolgen des Modelllebenszyklus das Hoch- und Aufskalieren von Data Science-Einheiten beschleunigt werden kann. [Führen Sie die Installation durch und die ersten Schritte aus](how-to-configure-cli.md).
