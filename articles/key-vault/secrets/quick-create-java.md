---
title: 'Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für Java'
description: Hier finden Sie eine Schnellstartanleitung zur Azure Key Vault-Geheimnisclientbibliothek für Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 7497db682cd8fac12cd93a83c3765862898455ee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763479"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Schnellstart: Azure Key Vault-Geheimnisclientbibliothek für Java
Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Geheimnisclientbibliothek für Java. Führen Sie die weiter unten beschriebenen Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben auszuprobieren.

Zusätzliche Ressourcen:

* [Quellcode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [API-Referenzdokumentation](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktdokumentation](index.yml)
* [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Voraussetzungen
- Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- [Java Development Kit (JDK)](/java/azure/jdk/), Version 8 oder höher
- [Apache Maven](https://maven.apache.org)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

In diesem Schnellstart wird davon ausgegangen, dass Sie die [Azure CLI](/cli/azure/install-azure-cli) und [Apache Maven](https://maven.apache.org) in einem Linux-Terminalfenster ausführen.

## <a name="setting-up"></a>Einrichten
In dieser Schnellstartanleitung wird die Azure Identity-Bibliothek mit der Azure CLI verwendet, um den Benutzer bei Azure-Diensten zu authentifizieren. Entwickler können auch Visual Studio oder Visual Studio Code verwenden, um ihre Aufrufe zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren des Clients mit der Azure Identity-Clientbibliothek](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Anmelden bei Azure
1. Führen Sie den Befehl `login` aus.

    ```azurecli-interactive
    az login
    ```

   Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

   Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

2. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

### <a name="create-a-new-java-console-app"></a>Erstellen einer neuen Java-Konsolen-App
Erstellen Sie in einem Konsolenfenster mithilfe des Befehls `mvn` eine neue Java-Konsolen-App mit dem Namen `akv-secrets-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Die Ausgabe der Erstellung des Projekts sieht in etwa wie folgt aus:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wechseln Sie zum neu erstellten Ordner `akv-secrets-java/`.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Installieren des Pakets
Öffnen Sie die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie der Gruppe der Abhängigkeiten das folgende Abhängigkeitselement hinzu:

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Erstellen einer Ressourcengruppe und eines Schlüsseltresors
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor
Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Geheimnisberechtigungen erteilt.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen
Diese Anwendung verwendet den Namen des Schlüsseltresors als Umgebungsvariable namens `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS oder Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell
Mit der Azure Key Vault-Geheimnisclientbibliothek für Java können Sie Geheimnisse verwalten. Im Abschnitt [Codebeispiele](#code-examples) wird gezeigt, wie ein Client erstellt und ein Geheimnis festgelegt, abgerufen und gelöscht wird.

Die vollständige Konsolen-App finden Sie [weiter unten](#sample-code).

## <a name="code-examples"></a>Codebeispiele
### <a name="add-directives"></a>Hinzufügen von Anweisungen
Fügen Sie am Anfang Ihres Codes die folgenden Anweisungen hinzu:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients
In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren bei Key Vault verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/overview.md).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format `https://\<your-key-vault-name\>.vault.azure.net` erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/java/api/com.azure.identity.defaultazurecredential) verwendet, mit der derselbe Code in verschiedenen Umgebungen mit unterschiedlichen Optionen zum Bereitstellen von Identitäten verwendet werden kann. Weitere Informationen finden Sie unter der [DefaultAzureCredential-Authentifizierung](/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Speichern eines Geheimnisses
Nachdem Ihre Anwendung authentifiziert wurde, können Sie Ihrem Schlüsseltresor mithilfe der Methode `secretClient.setSecret` ein Geheimnis hinzufügen Hierzu benötigen Sie einen Namen für das Geheimnis. In diesem Beispiel haben wir der Variable `secretName` den Wert „mySecret“ zugewiesen.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Mithilfe des Befehls [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) können Sie sich vergewissern, dass das Geheimnis festgelegt wurde:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Abrufen eines Geheimnisses
Nun können Sie das zuvor festgelegte Geheimnis mithilfe der Methode `secretClient.getSecret` abrufen.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Sie können jetzt mit `retrievedSecret.getValue()` auf den Wert des abgerufenen Geheimnisses zugreifen.

### <a name="delete-a-secret"></a>Löschen eines Geheimnisses
Abschließend löschen wir das Geheimnis mithilfe der `secretClient.beginDeleteSecret`-Methode aus dem Schlüsseltresor.

Die Löschung eines Geheimnisses ist ein zeitintensiver Vorgang. Sie können seinen Status abrufen oder auf seinen Abschluss warten.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

Mithilfe des Befehls [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) können Sie sich vergewissern, dass das Geheimnis gelöscht wurde:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die Ressourcen nicht mehr benötigen, können Sie die Azure CLI oder Azure PowerShell verwenden, um Ihren Schlüsseltresor und die zugehörige Ressourcengruppe zu entfernen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Beispielcode
```java
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt sowie ein Geheimnis erstellt, abgerufen und gelöscht. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/security-features.md)
