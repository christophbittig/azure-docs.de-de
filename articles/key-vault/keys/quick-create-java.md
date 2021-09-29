---
title: 'Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für Java'
description: Hier finden Sie eine Schnellstartanleitung zur Azure Key Vault-Schlüsselclientbibliothek für Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 79668f9055d9e5715ed5deb04506c1c0b53e5563
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771439"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für Java
Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Schlüsselclientbibliothek für Java. Führen Sie die weiter unten beschriebenen Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben auszuprobieren.

Zusätzliche Ressourcen:

* [Quellcode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [API-Referenzdokumentation](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktdokumentation](index.yml)
* [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

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
Erstellen Sie in einem Konsolenfenster mithilfe des Befehls `mvn` eine neue Java-Konsolen-App mit dem Namen `akv-keys-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Die Ausgabe der Erstellung des Projekts sieht in etwa wie folgt aus:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wechseln Sie zum neu erstellten Ordner `akv-keys-java/`.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Installieren des Pakets
Öffnen Sie die Datei *pom.xml* in Ihrem Text-Editor. Fügen Sie der Gruppe der Abhängigkeiten das folgende Abhängigkeitselement hinzu:

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
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
Erstellen Sie eine Zugriffsrichtlinie für Ihren Schlüsseltresor, die Ihrem Benutzerkonto Schlüsselberechtigungen erteilt.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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
Mit der Azure Key Vault-Schlüsselclientbibliothek für Java können Sie Schlüssel verwalten. Im Abschnitt [Codebeispiele](#code-examples) wird gezeigt, wie ein Client erstellt und ein Schlüssel erstellt, abgerufen und gelöscht wird.

Die vollständige Konsolen-App finden Sie [weiter unten](#sample-code).

## <a name="code-examples"></a>Codebeispiele
### <a name="add-directives"></a>Hinzufügen von Anweisungen
Fügen Sie am Anfang Ihres Codes die folgenden Anweisungen hinzu:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients
In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren bei Key Vault verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/overview.md).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/java/api/com.azure.identity.defaultazurecredential) verwendet, mit der derselbe Code in verschiedenen Umgebungen mit unterschiedlichen Optionen zum Bereitstellen von Identitäten verwendet werden kann. Weitere Informationen finden Sie unter der [DefaultAzureCredential-Authentifizierung](/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Erstellen eines Schlüssels
Nachdem Ihre Anwendung authentifiziert wurde, können Sie in Ihrem Schlüsseltresor mithilfe der Methode `keyClient.createKey` einen Schlüssel erstellen. Hierfür sind ein Name für den Schlüssel und ein Schlüsseltyp erforderlich. In diesem Beispiel wurde der Variablen `keyName` der Wert „myKey“ zugewiesen und ein RSA-Element vom Typ `KeyType` verwendet.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Mithilfe des Befehls [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) können Sie sich vergewissern, dass der Schlüssel festgelegt wurde:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Abrufen eines Schlüssels
Nun können Sie den zuvor erstellten Schlüssel mithilfe der `keyClient.getKey`-Methode abrufen.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Sie können nun auf die Details des abgerufenen Schlüssels mit Vorgängen wie `retrievedKey.getProperties`, `retrievedKey.getKeyOperations` usw. zugreifen.

### <a name="delete-a-key"></a>Löschen eines Schlüssels
Abschließend löschen Sie den Schlüssel mithilfe der Methode `keyClient.beginDeleteKey` aus dem Schlüsseltresor.

Die Löschung eines Schlüssels ist ein zeitintensiver Vorgang. Sie können seinen Status abrufen oder auf seinen Abschluss warten.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Mithilfe des Befehls [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) können Sie sich vergewissern, dass der Schlüssel gelöscht wurde:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
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
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt sowie einen Schlüssel erstellt, abgerufen und gelöscht. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Azure Key Vault-Sicherheitsübersicht](../general/security-features.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/security-features.md)
