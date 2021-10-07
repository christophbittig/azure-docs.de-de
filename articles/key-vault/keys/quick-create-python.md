---
title: 'Schnellstart: Python-Clientbibliothek in Azure Key Vault – Verwalten von Schlüsseln'
description: Hier erfahren Sie, wie Sie mithilfe der Python-Clientbibliothek Schlüssel in einer Azure Key Vault-Instanz erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 6375d522089f7ff4d77761f941a8b754cfa528bc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748682"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Schnellstart: Azure Key Vault-Schlüsselclientbibliothek für Python

Hier finden Sie Informationen zu den ersten Schritten mit der Azure Key Vault-Clientbibliothek für Python. Führen Sie die weiter unten beschriebenen Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben auszuprobieren. Wenn Sie Key Vault zum Speichern von Kryptografieschlüsseln verwenden, vermeiden Sie das Speichern solcher Schlüssel im Code, was die Sicherheit Ihrer App erhöht.

[API-Referenzdokumentation](/python/api/overview/azure/keyvault-keys-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Paket (Python-Paketindex)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- [Python 2.7 oder höher bzw. 3.6 oder höher](/azure/developer/python/configure-local-development-environment)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

In diesem Schnellstart wird davon ausgegangen, dass Sie die [Azure CLI](/cli/azure/install-azure-cli) in einem Linux-Terminalfenster ausführen.

## <a name="set-up-your-local-environment"></a>Einrichten Ihrer lokalen Umgebung

In dieser Schnellstartanleitung wird die Azure Identity-Bibliothek mit der Azure CLI verwendet, um den Benutzer bei Azure-Diensten zu authentifizieren. Entwickler können auch Visual Studio oder Visual Studio Code verwenden, um ihre Aufrufe zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren des Clients mit der Azure Identity-Clientbibliothek](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Führen Sie den Befehl `login` aus.

    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

    Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

2. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

### <a name="install-the-packages"></a>Installieren der Pakete

1. Erstellen Sie in einem Terminal oder an einer Eingabeaufforderung einen geeigneten Projektordner, und erstellen und aktivieren Sie dann eine virtuelle Python-Umgebung, wie unter [Verwenden von virtuellen Python-Umgebungen](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) beschrieben.

1. Installieren der Azure Active Directory-Identitätsbibliothek:

    ```terminal
    pip install azure.identity
    ```


1. Installieren der Key Vault-Schlüsselclientbibliothek:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erstellen einer Ressourcengruppe und eines Schlüsseltresors

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Erstellen Sie für Ihren Schlüsseltresor eine Zugriffsrichtlinie, mit der Ihrem Benutzerkonto die Geheimnisberechtigung erteilt wird.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
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

## <a name="create-the-sample-code"></a>Erstellen des Beispielcodes

Mit der Azure Key Vault-Schlüsselclientbibliothek für Python können Sie kryptografische Schlüssel verwalten. Im folgenden Codebeispiel wird gezeigt, wie Sie einen Client erstellen und einen Schlüssel festlegen, abrufen und löschen.

Erstellen Sie eine Datei mit dem Namen *kv_keys.py*, die diesen Code enthält.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Ausführen des Codes

Stellen Sie sicher, dass sich der Code aus dem vorherigen Abschnitt in einer Datei namens *kv_keys.py* befindet. Führen Sie den Code dann mithilfe des folgenden Befehls aus:

```terminal
python kv_keys.py
```

- Wenn Berechtigungsfehler auftreten, stellen Sie sicher, dass Sie den [`az keyvault set-policy`-Befehl](#grant-access-to-your-key-vault) ausgeführt haben.
- Wenn Sie den Code mit dem gleichen Schlüsselnamen erneut ausführen, wird möglicherweise der Fehler „(Konflikt) Schlüssel \<name\> befindet sich derzeit in einem gelöschten, aber wiederherstellbaren Zustand“ angezeigt. Verwenden Sie einen anderen Schlüsselnamen.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](../../active-directory/managed-identities-azure-resources/overview.md).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format `https://\<your-key-vault-name\>.vault.azure.net` erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/python/api/azure-identity/azure.identity.defaultazurecredential) verwendet, mit der derselbe Code in verschiedenen Umgebungen mit verschiedenen Optionen zum Bereitstellen von Identitäten verwendet werden kann. Weitere Informationen finden Sie unter der [DefaultAzureCredential-Authentifizierung](/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Speichern eines Schlüssels

Nachdem Sie das Clientobjekt für den Schlüsseltresor abgerufen haben, können Sie einen Schlüssel mithilfe der [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-)-Methode speichern: 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Sie können auch [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) oder [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-) verwenden.

Beim Aufrufen einer `create`-Methode wird ein Aufruf der Azure-REST-API für den Schlüsseltresor generiert.

Bei der Verarbeitung der Anforderung authentifiziert Azure die Identität des Aufrufers (Dienstprinzipal) mithilfe des Anmeldeinformationenobjekts, das Sie für den Client bereitgestellt haben.

## <a name="retrieve-a-key"></a>Abrufen eines Schlüssels

Um einen Schlüssel aus Key Vault zu lesen, verwenden Sie die [get_Key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-)-Methode:

```python
retrieved_key = client.get_key(keyName)
 ```

Mithilfe des Azure CLI-Befehls [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) können Sie auch sich vergewissern, dass der Schlüssel festgelegt wurde.

### <a name="delete-a-key"></a>Löschen eines Schlüssels

Verwenden Sie die [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-)-Methode, um einen Schlüssel zu löschen:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Die `begin_delete_key`-Methode ist asynchron und gibt ein Pollerobjekt zurück. Wenn die `result`-Methode des Pollers aufgerufen wird, wird auf ihren Abschluss gewartet.

Mithilfe des Azure CLI-Befehls [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) können Sie sich vergewissern, dass der Schlüssel gelöscht wurde.

Nach dem Löschen verbleibt ein Schlüssel für einen bestimmten Zeitraum in einem gelöschten, aber wiederherstellbaren Zustand. Wenn Sie den Code erneut ausführen, verwenden Sie einen anderen Schlüsselnamen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie auch mit [Zertifikaten](../certificates/quick-create-python.md) und [Geheimnissen](../secrets/quick-create-python.md) experimentieren möchten, können Sie den in diesem Artikel erstellten Key Vault wiederverwenden.

Verwenden Sie andernfalls den folgenden Befehl, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen, wenn Sie mit die in diesem Artikel erstellten Ressourcen nicht mehr benötigen:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über den Azure-Schlüsseltresor](../general/overview.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/security-features.md)
- [Entwicklerhandbuch zu Azure Key Vault](../general/developers-guide.md)
- [Key Vault-Sicherheitsübersicht](../general/security-features.md)
- [Authentifizieren mit Key Vault](../general/authentication.md)
