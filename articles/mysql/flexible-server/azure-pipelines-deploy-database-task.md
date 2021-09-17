---
title: Azure Pipelines-Aufgabe für Azure Database for MySQL Flexible Server
description: Aktivieren der Azure Database for MySQL Flexible Server-CLI-Aufgabe für die Verwendung mit Azure Pipelines
ms.topic: how-to
ms.service: mysql
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.date: 08/09/2021
ms.openlocfilehash: db8d8e5faaca0941e8f009a2193301cd2207669a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515969"
---
# <a name="azure-pipelines-for-azure-database-for-mysql-flexible-server"></a>Azure Pipelines for Azure Database for MySQL Flexible Server

Sie können Ihre Datenbankupdates nach jedem erfolgreichen Build mit **Azure Pipelines** automatisch in Azure Database for MySQL Flexible Server bereitstellen.  Sie können eine Azure CLI-Aufgabe verwenden, um die Datenbank entweder mit einer SQL-Datei oder einem Inline-SQL-Skript für die Datenbank zu aktualisieren. Diese Aufgabe kann auf plattformübergreifenden Agents ausgeführt werden, die unter Linux, macOS oder Windows ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.

- [Azure Resource Manager-Dienstverbindung](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) zu Ihrem Azure-Konto
- Bei von Microsoft gehosteten Agents ist Azure CLI vorinstalliert. Wenn Sie jedoch private Agents verwenden, [installieren Sie Azure CLI](/cli/azure/install-azure-cli) auf den Computern, auf denen der Build- und Release-Agent ausgeführt wird. Wenn ein Agent bereits auf dem Computer ausgeführt wird, auf dem die Azure CLI installiert ist, starten Sie den Agent neu, um sicherzustellen, dass alle relevanten Phasenvariablen aktualisiert werden.
  
In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- Erstellen einer Azure Database for MySQL Flexible Server-Instanz mit dem [Azure-Portal](./quickstart-create-server-portal.md) oder [Azure CLI](./quickstart-create-server-cli.md)


## <a name="use-sql-file"></a>Verwenden einer SQL-Datei

Im folgenden Beispiel wird veranschaulicht, wie Datenbankargumente übergeben und der Befehl ```execute``` ausgeführt wird.  

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
      az mysql flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) \
      --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>Verwenden des Inline-SQL-Skripts

Im folgenden Beispiel wird veranschaulicht, wie ein Inline-SQL Skript mit dem Befehl ```execute``` ausgeführt wird. 

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
      az mysql flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>Aufgabeneingaben

Sie können die vollständige Liste aller Aufgabeneingaben anzeigen, wenn Sie Azure CLI Aufgabe mit Azure Pipelines verwenden. 

| Parameter            | BESCHREIBUNG         | 
| :------------------- | :-------------------|
| azureSubscription| (Erforderlich) Wählen Sie das Azure Resource Manager-Abonnement für die Bereitstellung aus. Dieser Parameter wird nur angezeigt, wenn die ausgewählte Taskversion 0.* ist, da die Azure CLI-Aufgabe v1.0 nur Azure Resource Manager-Abonnements unterstützt. |
|scriptType| (Erforderlich) Geben Sie den Typ des Skripts an. Unterstützte Skripts sind PowerShell, PowerShell Core, Bat, Shell und Skript. Wenn Sie auf einem **Linux-Agenten** arbeiten, wählen Sie eine der folgenden Optionen: ```bash``` oder ```pscore```. Wenn Sie auf einem **Windows-Agenten** arbeiten, wählen Sie eine der folgenden Optionen: ```batch```, ```ps``` oder ```pscore```. |
|sriptLocation| (Erforderlich) Geben Sie den Pfad zum Skript an, z. B. den tatsächlichen Dateipfad, oder verwenden Sie ```Inline script```, wenn Sie die Skripts inline bereitstellen. Der Standardwert ist ```scriptPath```. |
|scriptPath| (Erforderlich) Vollqualifizierter Pfad des Skripts (PS1 oder BAT oder CMD bei Verwendung des Windows-basierten Agents, <code>.ps1 </code> oder <code>.sh </code> bei Verwendung eines Linux-basierten Agents) oder ein Pfad relativ zum Standardarbeitsverzeichnis |
|inlineScript|(Erforderlich) Sie können Ihre Skripts hier inline schreiben. Wenn Sie einen Windows-Agent verwenden, verwenden Sie PowerShell oder PowerShell Core oder Batchskripts, während Sie bei Verwendung von Linux-basierten Agents PowerShell Core oder Shell-Skripts verwenden. Verwenden Sie für Batchdateien das Präfix \"call\" vor jedem Azure-Befehl. Sie können auch vordefinierte und benutzerdefinierte Variablen mithilfe von Argumenten an dieses Skript übergeben. <br/>Beispiel für PowerShell/PowerShellCore/shell:``` az --version az account show``` <br/>Beispiel für batch: ``` call az --version call az account show```. |
| Argumente| (Optional) Geben Sie alle Argumente an, die an das Skript übergeben werden. Beispiele: ```-SERVERNAME mydemoserver``` |
|powerShellErrorActionPreference| (Optional) Stellt oben in Ihrem PowerShell-/PowerShell Core-Skript die Zeile <b>$ErrorActionPreference = 'VALUE'</b> voran. Der Standardwert ist „stop“. Unterstützte Werte sind stop, continue und silentlyContinue. |
|addSpnToEnvironment|(Optional) Fügt der Ausführungsumgebung des Skripts die Dienstprinzipal-ID und den Schlüssel des ausgewählten Azure-Endpunkts hinzu. Sie können diese Variablen in Ihrem Skript verwenden: <b>$env:servicePrincipalId, $env:servicePrincipalKey und $env:tenantId</b>. Dies wird nur berücksichtigt, wenn der Azure-Endpunkt über ein Dienstprinzipal-Authentifizierungsschema verfügt. Der Standardwert ist „FALSE“.|
|useGlobalConfig|(Optional) Wenn dies „FALSE“ ist, verwendet dieser Task ein eigenes separates <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">Azure CLI Konfigurationsverzeichnis</a>. Dies kann verwendet werden, um Azure CLI-Aufgaben in <b>parallelen</b> Releases auszuführen. <br/>Standardwert: False</td>
|workingDirectory| (Optional) Aktuelles Arbeitsverzeichnis, in dem das Skript ausgeführt wird.  „Leer“ ist der Stamm des Repositorys (Build) oder der Artefakte (Release), bei dem es sich um $(System.DefaultWorkingDirectory) handelt. |
|failOnStandardError|(Optional) Wenn dies „TRUE“ ist, tritt bei dieser Aufgabe ein Fehler auf, wenn Fehler in den StandardError-Stream geschrieben werden. Deaktivieren Sie das Kontrollkästchen, um Standardfehler zu ignorieren, und verwenden Sie Exitcodes, um den Status zu bestimmen. Der Standardwert ist „FALSE“.|
|powerShellIgnoreLASTEXITCODE| (Optional) Wenn dies „FALSE“ ist, wird die Zeile <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> am Ende des Skripts angefügt. Dadurch wird der letzte Exitcode eines externen Befehls als Exitcode von PowerShell weitergegeben. Andernfalls wird die Zeile nicht am Ende des Skripts angefügt. Der Standardwert ist „FALSE“. |


Bei Problemen mit dem CLI-Task finden Sie Informationen unter [Problembehandlung bei Build und Release.](/azure/devops/pipelines/troubleshooting/troubleshooting?view=azure-devops)

## <a name="next-steps"></a>Nächste Schritte 
Im Folgenden finden Sie einige verwandte Aufgaben für die Bereitstellung in Azure. 

- [Azure-Ressourcengruppenbereitstellung](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)
- [Azure-Web-App-Bereitstellung](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment?view=azure-devops)

