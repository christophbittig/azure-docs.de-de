---
title: 'Schnellstart: Microsoft Azure Confidential Ledger-Python-Clientbibliothek'
description: Hier erfahren Sie, wie Sie die Microsoft Azure Confidential Ledger-Clientbibliothek für Python verwenden.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/27/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 292c298f3af1b693574ecf9e053de14373d4d597
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476317"
---
# <a name="quickstart-microsoft-azure-confidential-ledger-client-library-for-python"></a>Schnellstart: Microsoft Azure Confidential Ledger-Clientbibliothek für Python

Führen Sie erste Schritte mit der Microsoft Azure Confidential Ledger-Clientbibliothek für Python aus. Führen Sie die weiter unten beschriebenen Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben auszuprobieren.

Microsoft Azure Confidential Ledger ist ein neuer und äußerst sicherer Dienst für die Verwaltung vertraulicher Datensätze. Basierend auf einem berechtigungsbasierten Block Chain-Modell bietet Azure Confidential Ledger einzigartige Vorteile bei der Datenintegrität, z. B. Unveränderlichkeit (der Ledger unterstützt nur Anfügevorgänge) und Manipulationsschutz (um sicherzustellen, dass alle Datensätze intakt bleiben).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[API-Referenzdokumentation](/python/api/overview/azure/keyvault-secrets-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/confidentialledger) | [Paketverwaltungsbibliothek (Python-Paketindex)](https://pypi.org/project/azure-mgmt-confidentialledger/)| [Paketclientbibliothek (Python-Paketindex)](https://pypi.org/project/azure-confidentialledger/)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- [Python 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli) oder [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="set-up"></a>Einrichten

In dieser Schnellstartanleitung wird die Azure Identity-Bibliothek mit der Azure CLI oder Azure PowerShell verwendet, um den Benutzer bei Azure-Diensten zu authentifizieren. Entwickler können auch Visual Studio oder Visual Studio Code verwenden, um ihre Aufrufe zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren des Clients mit der Azure Identity-Clientbibliothek](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [Sign in to Azure](../../includes/confidential-ledger-sign-in-azure.md)]

### <a name="install-the-packages"></a>Installieren der Pakete

Erstellen Sie in einem Terminal oder an einer Eingabeaufforderung einen geeigneten Projektordner, und erstellen und aktivieren Sie dann eine virtuelle Python-Umgebung, wie unter [Verwenden von virtuellen Python-Umgebungen](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) beschrieben.

Installieren der Azure Active Directory-Identitätsbibliothek:

```terminal
pip install azure-identity
```

Installieren Sie die Azure Confidential Ledger-Clientbibliothek für die Steuerungsebene:

```terminal
pip install azure.mgmt.confidentialledger
```

Installieren Sie die Azure Confidential Ledger-Clientbibliothek für die Datenebene:

```terminal
pip install azure.confidentialledger 
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../includes/confidential-ledger-rg-create.md)]

### <a name="register-the-microsoftconfidentialledger-resource-provider"></a>Registrieren Sie microsoft.ConfidentialLedger-Ressourcenanbieter

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

## <a name="create-your-python-app"></a>Erstellen Ihrer Python-App

### <a name="initialization"></a>Initialisierung

Wir können jetzt damit beginnen, unsere Python-Anwendung zu schreiben.  Importieren Sie zunächst die erforderlichen Pakete.

```python
# Import the Azure authentication library

from azure.identity import DefaultAzureCredential

## Import the control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import the data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient
```

Als Nächstes verwenden wir die [DefaultAzureCredential-Klasse](/python/api/azure-identity/azure.identity.defaultazurecredential), um die App zu authentifizieren.

```python
credential = DefaultAzureCredential()
```

Wir schließen das Setup ab, indem wir einige Variablen für die Verwendung in Ihrer Anwendung festlegen: die Ressourcengruppe (myResourceGroup), den Namen des Ledgers, den Sie erstellen möchten, und zwei URLs, die von der Clientbibliothek der Datenebene verwendet werden sollen.

  > [!Important]
  > Jedes Ledger muss einen weltweit eindeutigen Namen haben. Ersetzen Sie \<your-unique-keyvault-name\> im folgenden Beispiel durch den Namen Ihres Ledgers.

```python
resource_group = "myResourceGroup"
ledger_name = "<your-unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".confidential-ledger.azure.com"
```

### <a name="use-the-control-plane-client-library"></a>Verwenden der Steuerungsebene-Clientbibliothek

Die Steuerungsebene-Clientbibliothek der (azure.mgmt.confidentialledger) ermöglicht Vorgänge für Ledger, z. B. Erstellen, Ändern und Löschen, Auflisten der einem Abonnement zugeordneten Ledger und Abrufen der Details eines bestimmten Ledgers.

In unserem Code erstellen wir zunächst einen Steuerungsebenen-Client, indem wir der ConfidentialLedgerAPI die Anmeldeinformationsvariable und Ihre Azure-Abonnement-ID übergeben (die beide oben festgelegt sind).  

```python
confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, subscription_id
)
```

Wir können jetzt mit `begin_create` einen Ledger erstellen. Die `begin_create`-Funktion erfordert drei Parameter: Ihre Ressourcengruppe, einen Namen für den Ledger und ein „Eigenschaften“-Objekt.  

Erstellen Sie ein `properties`-Wörterbuch mit den folgenden Schlüsseln und Werten, und weisen Sie es einer Variablen zu.

```python
properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)
```

Übergeben Sie nun die Ressourcengruppe, den Namen Ihres Ledgers und das Eigenschaftsobjekt an `begin_create`.

```python
confidential_ledger_mgmt.ledger.begin_create(resource_group, ledger_name, ledger_properties)
```

Um zu überprüfen, ob Ihr Ledger erfolgreich erstellt wurde, zeigen Sie seine Details mithilfe der `get`-Funktion an.

```python
myledger = confidential_ledger_mgmt.ledger.get(resource_group, ledger_name)

print("Here are the details of your newly created ledger:")
print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

```

### <a name="use-the-data-plane-client-library"></a>Verwenden der Datenebenen-Clientbibliothek

Nachdem wir nun über einen Ledger verfügen, interagieren wir mit der Datenebene-Clientbibliothek (azure.confidentialledger). 

Zunächst generieren und speichern Sie ein Confidential Ledger-Zertifikat:  

```python
identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)
```

Jetzt können Sie das Netzwerkzertifikat zusammen mit der Ledger-URL und den Anmeldeinformationen verwenden, um einen Confidential Ledger-Client zu erstellen:

```python
ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)
```

Wir sind bereit, in den Ledger zu schreiben.  Dazu verwenden wir die `append_to_ledger`-Funktion.

```python
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)
```

Die print-Funktion gibt die Transaktions-ID Ihres Schreibvorgangs an den Ledger zurück, der zum Abrufen der Nachricht verwendet werden kann, die Sie in den Ledger geschrieben haben.

```python
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

Die print-Funktion gibt „Hello World!“ zurück, da dies die Meldung im Ledger ist, die der Transaktions-ID entspricht.

## <a name="full-sample-code"></a>Vollständiger Beispielcode

```python
from azure.identity import DefaultAzureCredential

## Import control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

# Set variables

rg = "myResourceGroup"
ledger_name = "<unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".eastus.cloudapp.azure.com"

# Authentication

# Need to do az login to get default credential to work

credential = DefaultAzureCredential()

# Control plane (azure.mgmt.confidentialledger)
# 
# initialize endpoint with credential and subscription

confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, "<subscription-id>"
)

# Create properties dictionary for begin_create call 

properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)

# Create a ledger

foo = confidential_ledger_mgmt.ledger.begin_create(rg, ledger_name, ledger_properties)

# Get the details of the ledger you just created

print(f"{rg} / {ledger_name}")
 
print("Here are the details of your newly created ledger:")
myledger = confidential_ledger_mgmt.ledger.get(rg, ledger_name)

print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

# Data plane (azure.confidentialledger)
#
# Create a CL client

identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)


ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)

# Write to the ledger
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)

# Read from the ledger
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Weitere Artikel zu Azure Confidential Ledger können auf dieser Schnellstartanleitung aufbauen. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.

Andernfalls, wenn Sie mit den in diesem Artikel erstellten Ressourcen fertig sind, verwenden Sie den Azure CLI-Befehl [az group delete](/cli/azure/group?#az_group_delete), um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen:

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Confidential Ledger](overview.md)
