---
title: Problembehandlung bei CLI-Fehlern in Azure Database for PostgreSQL Flexible Server
description: Dieses Thema enthält einen Leitfaden zur Behandlung häufiger Probleme mit der Azure-Befehlszeilenschnittstelle bei Verwendung von PostgreSQL Flexible Server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 08/24/2021
ms.openlocfilehash: dd44e0bf0ffd7ae70cdb2b715561517d5b92a049
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440660"
---
# <a name="troubleshoot-azure-database-for-postgresql-flexible-server-cli-errors"></a>Problembehandlung bei CLI-Fehlern in Azure Database for PostgreSQL Flexible Server

Dieses Dokument bietet Hilfe bei der Behandlung häufiger Probleme mit der Azure-Befehlszeilenschnittstelle bei Verwendung von PostgreSQL Flexible Server.

## <a name="command-not-found"></a>Der Befehl wurde nicht gefunden.

 Sie erhalten eine Fehlermeldung, dass ein Befehl **falsch geschrieben oder vom System nicht erkannt wurde**. Dies kann bedeuten, dass die Version der Befehlszeilenschnittstelle auf Ihrem Clientcomputer möglicherweise nicht auf dem neuesten Stand ist. Führen Sie ```az upgrade``` aus, um ein Upgrade auf die aktuelle Version durchzuführen. Wenn Sie ein Upgrade Ihrer CLI-Version durchführen, werden damit möglicherweise auch Probleme mit Inkompatibilitäten einzelner Befehle aufgrund von API-Änderungen behoben.
 
## <a name="debug-deployment-failures"></a>Debuggen von Bereitstellungsfehlern 
Derzeit unterstützt die Azure-Befehlszeilenschnittstelle das Aktivieren der Debugprotokollierung nicht, Sie können diese jedoch anhand der folgenden Schritte abrufen.

>[!NOTE]
> - Ersetzen Sie ```examplegroup``` und ```exampledeployment``` durch die richtige Ressourcengruppe und den richtigen Bereitstellungsnamen für Ihren Datenbankserver. 
> - Der Bereitstellungsname wird auf der Seite „Bereitstellungen“ in Ihrer Ressourcengruppe angezeigt. Weitere Informationen finden Sie unter [Ermitteln des Bereitstellungsnamens](../../azure-resource-manager/templates/deployment-history.md?tabs=azure-portal)


1. Auflisten der Bereitstellungen in der Ressourcengruppe zum Identifizieren der PostgreSQL-Serverbereitstellung 
    ```azurecli

        az deployment operation group list \
          --resource-group examplegroup \
          --name exampledeployment
    ```

2. Abrufen des Anforderungsinhalts der PostgreSQL-Serverbereitstellung 
    ```azurecli

        az deployment operation group list \
          --name exampledeployment \
          -g examplegroup \
          --query [].properties.request
    ```
3. Untersuchen des Antwortinhalts 
    ```azurecli
    az deployment operation group list \
      --name exampledeployment \
      -g examplegroup \
      --query [].properties.response
    ```

## <a name="error-codes"></a>Fehlercodes

| Fehlercode | Minderung |
| ---------- | ---------- | 
|MissingSubscriptionRegistration|Registrieren Sie Ihr Abonnement beim Ressourcenanbieter. Führen Sie zum Beheben dieses Problems den Befehl ```az provider register --namespace Microsoft.DBPostgreSQL``` aus.|
|InternalServerError| Versuchen Sie, in den Aktivitätsprotokollen für Ihren Server weitere Informationen zu finden. Führen Sie den Befehl ```az monitor activity-log list --correlation-id <enter correlation-id>``` aus. Sie können denselben CLI-Befehl nach einigen Minuten erneut ausprobieren. Wenn das Problem weiterhin besteht, [melden Sie es](https://github.com/Azure/azure-cli/issues), oder wenden Sie sich an den Microsoft-Support.|
|ResourceNotFound| Die Ressource, auf die verwiesen wird, wurde nicht gefunden.  Überprüfen Sie die Ressourceneigenschaften oder, ob die Ressource gelöscht wurde oder sich in einem anderen Abonnement befindet. |
|LocationNotAvailableForResourceType| - Überprüfen Sie die Verfügbarkeit von Azure Database for Postgres Flexible Server in den einzelnen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). <br>- Überprüfen Sie, ob Azure DB for PostgreSQL-Ressourcentypen bei Ihrem Abonnement registriert sind. |
|ResourceGroupBeingDeleted| Die Ressourcengruppe wird gelöscht. Warten Sie, bis der Löschvorgang abgeschlossen ist.|
|PasswordTooLong| Das angegebene Kennwort ist zu lang. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).|
|PasswordNotComplex| Das angegebene Kennwort ist nicht komplex genug.  Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).|
|PasswordTooShort| Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).|
|SubscriptionNotFound| Das angeforderte Abonnement wurde nicht gefunden. Führen Sie ```az account list all``` aus, um alle Ihre aktuellen Abonnements anzuzeigen.|
|InvalidParameterValue| Für einen Parameter wurde ein ungültiger Wert angegeben. Überprüfen Sie in den [Referenzdokumenten zur Befehlszeilenschnittstelle](/cli/azure/postgres/flexible-server?view=azure-cli-latest&preserve-view=true), welche Werte für die Argumente unterstützt werden.|
|InvalidLocation| Es wurde ein ungültiger Standort angegeben. Überprüfen der Verfügbarkeit von Azure Database for Postgres Flexible Server in den einzelnen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) |
|InvalidServerName|Es wurde ein ungültiger Servername ermittelt. Überprüfen Sie den Servernamen. Führen Sie den Befehl [az mysql flexible-server list](/cli/azure/mysql/flexible-server?view=azure-cli-latest#az_mysql_flexible_server_list&preserve-view=true) aus, um die Liste der verfügbaren flexiblen Server anzuzeigen.|
|InvalidResourceIdSegment| In Ihrer Azure Resource Manager-Vorlage wurde ein Syntaxfehler gefunden. Verwenden Sie ein JSON-Formatierungstool, um den JSON-Code zu überprüfen und den Syntaxfehler zu finden.|
|InvalidUserName| Geben Sie einen gültigen Benutzernamen ein. Der Administratorbenutzername darf nicht „azure_superuser“, „azure_pg_admin“, „admin“, „administrator“, „root“, „guest“ oder „public“ lauten. Er darf auch nicht mit „pg_“ beginnen.|
|BlockedUserName| Der Administratorbenutzername darf nicht „azure_superuser“, „azure_pg_admin“, „admin“, „administrator“, „root“, „guest“ oder „public“ lauten. Er darf auch nicht mit „pg_“ beginnen. Vermeiden Sie die Verwendung dieser Muster im Administratornamen.|

## <a name="next-steps"></a>Nächste Schritte

- Wenn weiterhin Probleme auftreten, [melden Sie das Problem](https://github.com/Azure/azure-cli/issues). 
- Falls Sie Fragen haben, besuchen Sie unsere Stack Overflow-Seite: https://aka.ms/azcli/questions. 
- Teilen Sie uns Ihre Meinung in dieser Umfrage mit: https://aka.ms/azureclihats. 
