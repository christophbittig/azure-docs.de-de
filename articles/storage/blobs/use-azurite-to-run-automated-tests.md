---
title: Ausführen automatisierter Tests mithilfe von Azurite
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie mithilfe von Azurite automatisierte Tests für private Endpunkte für Azure BLOB Storage schreiben.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: 21a0d2157cdb8b6b2c31520596327b3b433ef93a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674545"
---
# <a name="run-automated-tests-by-using-azurite"></a>Ausführen automatisierter Tests mithilfe von Azurite

Erfahren Sie, wie Sie mithilfe von Azurite automatisierte Tests für private Endpunkte für Azure BLOB Storage Emulator schreiben.

## <a name="run-tests-on-your-local-machine"></a>Ausführen von Tests auf Ihrem lokalen Computer aus

1. Installieren der aktuellen Version von [Python](https://www.python.org/)

1. Installieren von [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)

1. Installieren und Ausführen von [Azurite](../common/storage-use-azurite.md):

   **Option 1:** Verwenden von npm zum Installieren und anschließendes Ausführen von Azurite

   ```bash
   # Install Azurite
   npm install -g azurite

   # Create an Azurite directory
   mkdir c:\azurite

   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Option 2:** Verwenden von Docker zum Ausführen von Azurite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. Wählen Sie im Azure Storage Explorer die Option **Anhängen an einen lokalen Emulator**

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Screenshot der Azure Storage-Explorer, Herstellen einer Verbindung mit Azure Storage Quelle.":::

1. Geben Sie einen **Anzeigenamen** und eine **BLOB-Port**-Nummer an, um eine Verbindung mit Azurite herzustellen, und verwenden Sie Azure Storage-Explorer, um den lokalen BLOB-Speicher zu verwalten.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Screenshot des Azure Storage-Explorer, angehängt an einen lokalen Emulator.":::

1. Erstellen einer virtuellen Python-Umgebung

   ```bash
   python -m venv .venv
   ```

1. Erstellen Sie einen Container, und initialisieren Sie Umgebungsvariablen. Verwenden Sie eine [Pytest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/latest/how-to/writing_plugins.html#conftest-py-plugins)-Datei, um Tests zu generieren. Hier ein Beispiel für eine conftest.py-Datei:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > Der für angezeigte Wert `AZURE_STORAGE_CONNECTION_STRING` ist der Standardwert für Azurite, es handelt sich nicht um einen privaten Schlüssel.

1. Installieren von Abhängigkeiten, die in einer [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt)-Datei aufgelistet sind

   ```bash
   pip install -r requirements.txt
   ```

1. Ausführen von Tests:

   ```bash
   python -m pytest ./tests
   ```

Nachdem Sie Tests ausgeführt haben, können Sie die Dateien im Azurite BLOB-Speicher mit Azure Storage-Explorer anzeigen.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Screenshot von Azure Storage-Explorer, der von den Tests generierte Dateien anzeigt.":::

## <a name="run-tests-on-azure-pipelines"></a>Ausführen von Tests auf Azure Pipelines

Stellen Sie nach dem lokalen Ausführen von Tests sicher, dass die Tests auf [Azure Pipelines](/azure/devops/pipelines)bestanden werden. Verwenden Sie ein Docker Azurite-Image als gehosteten Agent in Azure, oder verwenden Sie npm zum Installieren von Azurite. Im folgenden Azure Pipelines-Beispiel wird npm zum Installieren von Azurite verwendet.

```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'

- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Nachdem Sie die Azure Pipelines-Tests ausgeführt haben, sollte eine Ausgabe ähnlich der folgenden angezeigt werden:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Screenshot der Azure Pipelines-Testergebnisse.":::

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden des Azurite-Emulators für die lokale Azure Storage-Entwicklung](../common/storage-use-azurite.md)
- [Beispiel: Verwenden von Azurite zum Ausführen von BLOB Storage-Tests in der Azure DevOps Pipeline](https://github.com/Azure-Samples/automated-testing-with-azurite)
