---
title: Azure Pipelines-Aufgabe – Azure Database for PostgreSQL Flexible Server
description: Aktivieren der Azure Database for PostgreSQL Flexible Server-CLI-Aufgabe für die Verwendung bei Azure Pipelines
ms.topic: quickstart
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.service: postgresql
ms.date: 08/09/2021
ms.openlocfilehash: 747a1c59d00ac4127fab4b6c28d02c837e540133
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122516003"
---
# <a name="azure-pipelines-task-for-azure-database-for-postgresql-flexible-server"></a>Azure Pipelines-Aufgabe für Azure Database for PostgreSQL Flexible Server

Sie können Ihre Datenbankupdates nach jedem erfolgreichen Build mit **Azure Pipelines** in Azure Database for PostgreSQL Flexible Server automatisch bereitstellen.  Mithilfe einer Azure CLI-Aufgabe können Sie die Datenbank entweder mit einer SQL-Datei oder einem Inline-SQL-Skript für die Datenbank aktualisieren. Diese Aufgabe kann auf plattformübergreifenden Agents unter den Betriebssystemen Linux, macOS oder Windows ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.
- Eine [Azure Resource Manager-Dienstverbindung](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) zu Ihrem Azure-Konto
- Bei von Microsoft gehosteten Agents ist Azure CLI vorinstalliert. Wenn Sie aber private Agents verwenden, [installieren Sie Azure CLI](/cli/azure/install-azure-cli) auf dem/den Computer(n), auf dem/denen der Build- und Release-Agent ausgeführt wird. Wenn ein Agent auf dem Computer bereits ausgeführt wird, auf dem die Azure CLI installiert ist, starten Sie ihn neu, um sicherzustellen, dass alle relevanten Stufenvariablen aktualisiert werden.
- Erstellen einer Instanz von Azure Database for PostgreSQL Flexible Server im [Azure-Portal](./quickstart-create-server-portal.md) oder in [Azure CLI](./quickstart-create-server-cli.md)


## <a name="use-sql-file"></a>Verwenden einer SQL-Datei

Im folgenden Beispiel wird gezeigt, wie Datenbankargumente übergeben und der Befehl ```execute``` ausgeführt wird.  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az postgres flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>Verwenden eines Inline-SQL-Skripts

Im folgenden Beispiel wird gezeigt, wie ein Inline-SQL-Skript mit dem Befehl ```execute``` ausgeführt wird.

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription 
      az postgres flexible-server execute --name $(SERVERNAME) --admin-user $(DBUSER) \
      --admin-password '$(DBPASSWORD)'  --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>Aufgabeneingaben

Sie können die vollständige Liste aller Aufgabeneingaben anzeigen, wenn Sie die Azure CLI-Aufgabe bei Azure Pipelines verwenden. 

| Parameter            | BESCHREIBUNG         | 
| :------------------- | :-------------------|
| azureSubscription| (Erforderlich) Geben Sie das Azure Resource Manager-Abonnement für die Bereitstellung an. Dieser Parameter wird nur angezeigt, wenn die ausgewählte Aufgabenversion „0.*“ ist, weil die Azure CLI-Aufgabe v1.0 nur Azure Resource Manager-Abonnements unterstützt. |
|scriptType| (Erforderlich) Geben Sie den Typ des Skripts an. Unterstützte Skripts sind PowerShell, PowerShell Core, Bat, Shell und Skript. Wenn Sie einen **Linux-Agent** ausführen, wählen Sie eine der folgenden Optionen aus: ```bash``` oder ```pscore```. Wenn Sie einen **Windows-Agent** ausführen, wählen Sie eine der folgenden Optionen aus: ```batch```, ```ps``` oder ```pscore```. |
|sriptLocation| (Erforderlich) Geben Sie den Pfad zum Skript an, z. B. den tatsächlichen Dateipfad, oder verwenden Sie ```Inline script```, wenn Sie die Skripts inline bereitstellen. Der Standardwert ist ```scriptPath```. |
|scriptPath| (Erforderlich) Vollqualifizierter Pfad des Skripts („.ps1“, „.bat“ oder „.cmd“ bei Verwendung eines Windows-basierten Agents, oder aber <code>.ps1 </code> oder <code>.sh </code> bei Verwendung eines Linux-basierten Agents) oder ein Pfad relativ zum Standardarbeitsverzeichnis. |
|inlineScript|(Erforderlich) Sie können Ihre Skripts hier inline schreiben. Wenn Sie einen Windows-Agent verwenden, verwenden Sie PowerShell oder PowerShell Core oder aber Batchskripts, während Sie bei Verwendung von Linux-basierten Agents PowerShell Core oder Shell-Skripts verwenden müssen. Verwenden Sie bei Batchdateien vor jedem Azure-Befehl das Präfix \"call\". Sie können auch vordefinierte und benutzerdefinierte Variablen mithilfe von Argumenten an dieses Skript übergeben. <br/>Beispiel für „PowerShell/PowerShellCore/shell“: ``` az --version az account show``` <br/>Beispiel für „Batch“: ``` call az --version call az account show```. |
| Argumente| (Optional) Geben Sie alle Argumente an, die an das Skript übergeben werden. Beispiele: ```-SERVERNAME mydemoserver```. |
|powerShellErrorActionPreference| (Optional) Stellt dem Anfang Ihres PowerShell-/PowerShell Core-Skripts die Zeile <b>$ErrorActionPreference = 'VALUE'</b> voran. Der Standardwert ist „stop“. Unterstützte Werte sind „stop“, „continue“ und „silentlyContinue“. |
|addSpnToEnvironment|(Optional) Fügt der Ausführungsumgebung des Skripts die Dienstprinzipal-ID und den Schlüssel des von Ihnen ausgewählten Azure-Endpunkts hinzu. Sie können in Ihrem Skript diese Variablen verwenden: <b>„$env:servicePrincipalId“, „$env:servicePrincipalKey2“ und „$env:tenantId“</b>. Dies wird nur berücksichtigt, wenn es beim Azure-Endpunkt ein Dienstprinzipal-Authentifizierungsschema gibt. Der Standardwert ist „FALSE“.|
|useGlobalConfig|(Optional) Wenn der Wert „FALSE“ ist, verwendet diese Aufgabe ein eigenes separates <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">Azure CLI-Konfigurationsverzeichnis</a>. Es kann zur Ausführung von Azure CLI-Aufgaben in <b>parallelen</b> Releases verwendet werden. <br/>Standardwert: False</td>
|workingDirectory| (Optional) Aktuelles Arbeitsverzeichnis, in dem das Skript ausgeführt wird.  „Leer“ ist der Stamm des Repositorys (Build) oder der Artefakte (Release), bei dem es sich um „$(System.DefaultWorkingDirectory)“ handelt. |
|failOnStandardError|(Optional) Wenn der Wert „true“ ist, schlägt diese Aufgabe fehl, wenn Fehler in den „StandardError“-Stream geschrieben werden. Deaktivieren Sie das Kontrollkästchen, damit Standardfehler ignoriert werden, und verwenden Sie Exitcodes, um den Status zu bestimmen. Der Standardwert ist „FALSE“.|
|powerShellIgnoreLASTEXITCODE| (Optional) Wenn der Wert „FALSE“ ist, wird die Zeile <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> am Ende Ihres Skripts angefügt. Dadurch wird der letzte Exitcode aus einem externen Befehl als Exitcode von PowerShell weitergegeben. Andernfalls wird die Zeile am Ende Ihres Skripts nicht angefügt. Der Standardwert ist „FALSE“. |

Wenn Probleme bei der CLI-Aufgabe auftreten, lesen Sie [Problembehandlung bei Build und Release](/azure/devops/pipelines/troubleshooting/troubleshooting?view=azure-devops).

## <a name="next-steps"></a>Nächste Schritte 
Hier sind einige verwandte Aufgaben, die für die Bereitstellung mit Azure Piplelines verwendet werden können.

- [Azure-Ressourcengruppenbereitstellung](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)
- [Azure-Web-App-Bereitstellung](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment?view=azure-devops)

