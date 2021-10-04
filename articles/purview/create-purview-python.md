---
title: 'Schnellstart: Erstellen eines Purview-Kontos mithilfe von Python'
description: Erstellen Sie ein Azure Purview-Konto mithilfe von Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 7a6d13da2ee3138e6dfc5eca4a5b75f454f90457
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212480"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Schnellstart: Erstellen eines Purview-Kontos mithilfe von Python

In dieser Schnellstartanleitung erstellen Sie programmgesteuert mithilfe von Python ein Purview-Konto. Die [Python-Referenz für Purview](/python/api/azure-mgmt-purview/) ist verfügbar, in diesem Artikel werden aber alle Schritte erläutert, die zum Erstellen eines Kontos mit Python erforderlich sind.

Azure Purview ist ein Data Governance-Dienst, der Sie bei der Verwaltung Ihrer Datenlandschaft unterstützt. Durch Herstellen einer Verbindung zu Daten in Ihren lokalen Quellen, mehreren Clouds und SaaS-Quellen (Software-as-a-Service) erstellt Purview eine aktuelle Zuordnung Ihrer Informationen. Purview identifiziert und klassifiziert sensible Daten und gewährleistet eine End-to-End-Herkunft. Datenconsumer können Daten in Ihrer Organisation auffinden, und Datenadministratoren können Ihre Daten überwachen, schützen und die richtige Verwendung ihrer Daten sicherstellen.

Weitere Informationen zu Purview [finden Sie auf unserer Übersichtsseite](overview.md). Weitere Informationen zum Bereitstellen von Purview in Ihrer Organisation [finden Sie in unseren bewährten Methoden für die Bereitstellung](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="install-the-python-package"></a>Installieren des Python-Pakets

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung mit Administratorberechtigungen.
2. Installieren Sie zunächst das Python-Paket für Azure-Verwaltungsressourcen:

    ```python
    pip install azure-mgmt-resource
    ```

3. Führen Sie zum Installieren des Python-Pakets für Purview den folgenden Befehl aus:

    ```python
    pip install azure-mgmt-purview
    ```

    Das [Python SDK für Purview](https://github.com/Azure/azure-sdk-for-python) unterstützt Python 2.7, 3.3, 3.4, 3.5, 3.6 und 3.7.

4. Führen Sie zum Installieren des Python-Pakets für die Azure-Identitätsauthentifizierung den folgenden Befehl aus:

    ```python
    pip install azure-identity
    ```

    > [!NOTE]
    > Das Paket „azure-identity“ steht bei einigen gemeinsamen Abhängigkeiten unter Umständen in Konflikt mit „azure-cli“. Wenn ein Authentifizierungsproblem auftritt, entfernen Sie „azure-cli“ und die zugehörigen Abhängigkeiten, oder verwenden Sie einen neu installierten Computer ohne Installation des Pakets „azure-cli“.

## <a name="create-a-purview-client"></a>Erstellen eines Purview-Clients

1. Erstellen Sie eine Datei mit dem Namen **purview.py**. Fügen Sie die folgenden Anweisungen ein, um Verweise auf Namespaces hinzuzufügen.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Fügen Sie zur Methode **Main** den folgenden Code hinzu, mit dem eine Instanz der DataFactoryManagementClient-Klasse erstellt wird. Sie verwenden dieses Objekt, um ein Purview-Konto zu erstellen oder zu löschen, um die Verfügbarkeit eines Namens zu prüfen und um andere Vorgänge des Ressourcenanbieters durchzuführen.

    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Erstellen eines Purview-Kontos

1. Fügen Sie zur Methode **Main** den folgenden Code hinzu, mit dem eine **Data Factory** erstellt wird. Wenn die Ressourcengruppe bereits vorhanden ist, kommentieren Sie die erste `create_or_update`-Anweisung aus.

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
      ```

2. Fügen Sie nun die folgende Anweisung hinzu, um die **main**-Methode bei Ausführung des Programms aufzurufen:

   ```python
   # Start the main method
   main()
   ```

## <a name="full-script"></a>Vollständiges Skript

Hier sehen Sie den vollständigen Python-Code:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Ausführen des Codes

Erstellen und starten Sie die Anwendung. Die Konsole gibt den Fortschritt der Purview-Kontoerstellung aus. Warten Sie, bis der Vorgang abgeschlossen ist.
Hier ist die Beispielausgabe:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Überprüfen der Ausgabe

Wechseln Sie im Azure-Portal zur Seite mit den **Purview-Konten**, und überprüfen Sie das mit dem obigen Code erstellte Konto.

## <a name="delete-purview-account"></a>Löschen eines Purview-Kontos

Um ein Purview-Konto zu löschen, fügen Sie den folgenden Code in das Programm ein, und führen Sie dann das Programm aus:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Nächste Schritte

Mit dem Code in diesem Tutorial wird zuerst ein Purview-Konto erstellt und dann ein Purview-Konto gelöscht. Sie können jetzt das Python SDK herunterladen und sich über andere Aktionen des Ressourcenanbieters informieren, die Sie für ein Purview-Konto ausführen können.

Lesen Sie diese nächsten Artikel, um zu erfahren, wie Sie in Purview Studio navigieren, eine Sammlung erstellen und Zugriff auf Purview gewähren.

* [Verwenden von Purview Studio](use-purview-studio.md)
* [Erstellen einer Sammlung](quickstart-create-collection.md)
* [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)