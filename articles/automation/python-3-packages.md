---
title: Verwalten von Python 3-Paketen in Azure Automation
description: In diesem Artikel wird erläutert, wie Sie Python 3-Pakete (Vorschau) in Azure Automation verwalten.
services: automation
ms.subservice: process-automation
ms.date: 08/25/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: d3ec338b6d6edac2c56c8b42f877a1095aace2a0
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349605"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Verwalten von Python 3-Paketen (Vorschau) in Azure Automation

Mit Azure Automation können Sie Python 3-Runbooks (Vorschau) in einer Azure Sandbox-Umgebung und auf Hybrid Runbook Workern unter Linux ausführen. Um die Runbooks zu vereinfachen, können Sie mithilfe von Python-Paketen die erforderlichen Module importieren. Das Azure-Paket 4.0.0 wird standardmäßig in der Automation-Umgebung von Python 3 installiert. Die Standardversion kann überschrieben werden, indem die Python-Pakete in das Automation-Konto importiert werden. Die importierte Version im Automation-Konto wird bevorzugt. Informationen zum Importieren eines einzelnen Pakets finden Sie unter [Importieren eines Pakets](#import-a-package). Informationen zum Importieren eines Pakets mit mehreren Paketen finden Sie unter [Importieren eines Pakets mit Abhängigkeiten](#import-a-package-with-dependencies). In diesem Artikel wird beschrieben, wie Sie Python 3-Pakete (Vorschau) in Azure Automation verwalten und verwenden.

## <a name="packages-as-source-files"></a>Pakete als Quelldateien

Azure Automation unterstützt nur Python-Pakete, die ausschließlich Python-Code enthalten und weder Erweiterungen noch Code in einer anderen Sprache umfassen. Die Azure Sandbox verfügt jedoch möglicherweise nicht über die erforderlichen Compiler für C/C++-Binärdateien, daher empfiehlt es sich, stattdessen [wheel-Dateien](https://pythonwheels.com/) zu verwenden. Der [Python Package Index](https://pypi.org/) (PyPI) ist ein Repository mit Software für die Programmiersprache Python. Wenn Sie in PyPI ein Python 3-Paket für den Import in Ihr Automation-Konto auswählen, beachten Sie die folgenden Namensbestandteile:

| Bestandteil des Dateinamens | BESCHREIBUNG |
|---|---|
|cp38|Automation unterstützt **Python 3.8.x** für Cloudaufträge.|
|amd64|Azure Sandbox-Prozesse gehören zur **Windows 64-Bit**-Architektur.|

Wenn Sie beispielsweise pandas importieren möchten, können Sie eine wheel-Datei mit einem Namen ähnlich wie `pandas-1.2.3-cp38-win_amd64.whl` auswählen.

Einige in PyPI verfügbare Python-Pakete enthalten keine wheel-Datei. Laden Sie in diesem Fall die Quelldatei (ZIP oder TAR.GZ) herunter, und generieren Sie die wheel-Datei mithilfe von `pip`. Führen Sie beispielsweise die folgenden Schritte auf einem 64-Bit-Computer mit Python 3.8.x und installiertem wheel-Paket aus:

1. Laden Sie die Quelldatei `pandas-1.2.4.tar.gz` herunter.
1. Führen Sie pip mit dem folgenden Befehl aus, um die wheel-Datei abzurufen: `pip wheel --no-deps pandas-1.2.4.tar.gz`.

## <a name="import-a-package"></a>Importieren eines Pakets

Wählen Sie in Ihrem Azure Automation-Konto unter **Python-Pakete** die Option **Freigegebene Ressourcen** aus. Wählen Sie **+ Python-Paket hinzufügen** aus.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Screenshot der Seite „Python 3-Pakete“ mit Python 3-Paketen im linken Menü und Hervorhebung von „Python-Paket hinzufügen“.":::

Wählen Sie auf der Seite **Python-Paket hinzufügen** unter **Version** die Option **Python 3** sowie ein hochzuladendes lokales Paket aus. Das Paket kann eine Datei im Format **.whl** oder **.tar.gz** sein. Wählen Sie nach Auswahl des Pakets **OK** aus, um es hochzuladen.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Screenshot der Seite „Python 3-Paket hinzufügen“ mit Auswahl der hochgeladenen Datei „tar.gz“.":::

Nach dem Importieren eines Pakets wird es in Ihrem Automation-Konto auf der Seite mit den Python-Paketen auf der Registerkarte **Python 3-Pakete (Vorschau)** aufgelistet. Wenn Sie ein Paket entfernen möchten, wählen Sie das Paket und dann **Löschen** aus.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Screenshot der Seite „Python 3-Pakete“ nach Import eines Pakets.":::

### <a name="import-a-package-with-dependencies"></a>Importieren eines Pakets mit Abhängigkeiten

Sie können ein Python 3-Paket und die zugehörigen Abhängigkeiten importieren, indem Sie das folgende Python-Skript in ein Python 3-Runbook importieren und es anschließend ausführen.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importieren des Skripts in ein Runbook
Informationen zum Importieren des Runbooks finden Sie unter [Importieren eines Runbooks im Azure-Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Kopieren Sie die Datei aus GitHub an einen Speicherort, auf den das Portal zugreifen kann, bevor Sie den Import ausführen.

Auf der Seite zum **Importieren eines Runbooks** wird als Name des Runbooks standardmäßig der Name des Skripts verwendet. Wenn Sie Zugriff auf das Feld haben, können Sie den Namen ändern. Der **Runbooktyp** ist möglicherweise standardmäßig auf **Python 2** festgelegt. Falls ja, ändern Sie ihn in **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Screenshot: Seite zum Importieren eines Python 3-Runbooks":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Ausführen des Runbooks zum Importieren des Pakets und der Abhängigkeiten

Nachdem Sie das Runbook erstellt und veröffentlicht haben, können Sie es ausführen, um das Paket zu importieren. Ausführliche Informationen zum Ausführen des Runbooks finden Sie unter [Starten eines Runbooks in Azure Automation](start-runbooks.md).

Für das Skript `import_py3package_from_pypi.py` sind folgende Parameter erforderlich:

| Parameter | BESCHREIBUNG |
|---------------|-----------------|
|subscription_id | Abonnement-ID des Automation-Kontos |
| resource_group | Name der Ressourcengruppe, in der das Automation-Konto definiert ist |
| automation_account | Name des Automation-Kontos |
| module_name | Name des Moduls für den Import aus `pypi.org` |

Weitere Informationen zur Verwendung von Parametern mit Runbooks finden Sie unter [Verwenden von Runbookparametern](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Verwenden eines Pakets in einem Runbook

Nachdem Sie das Paket importiert haben, können Sie es in einem Runbook verwenden. Fügen Sie den folgenden Code hinzu, um alle Ressourcengruppen in einem Azure-Abonnement aufzulisten.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

> [!NOTE]
> Da das Python-Paket `automationassets` auf pypi.org nicht verfügbar ist, kann es nicht auf einen Windows-Computer importiert werden.

## <a name="identify-available-packages-in-sandbox"></a>Identifizieren verfügbarer Pakete in der Sandbox

Verwenden Sie den folgenden Code, um die installierten Standardmodule aufzulisten:

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Vorbereiten eines Python-Runbooks finden Sie unter [Tutorial: Erstellen eines Python-Runbooks](learn/automation-tutorial-runbook-textual-python-3.md).
