---
title: 'Tutorial: Verarbeiten gespeicherter Dokumente mithilfe einer Azure-Funktion'
titleSuffix: Azure Applied AI Services
description: In diesem Leitfaden erfahren Sie, wie Sie eine Azure-Funktion verwenden, um die Verarbeitung von Dokumenten auszulösen, die in einen Azure Blob Storage-Container hochgeladen werden.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: ae69ae943de5d5e83b08a0d654bd102c4bf95bcf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026579"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Tutorial: Verarbeiten gespeicherter Dokumente mithilfe einer Azure-Funktion

Sie können die Formularerkennung als Teil einer automatisierten, mit Azure Functions erstellten Datenverarbeitungspipeline verwenden. In diesem Leitfaden erfahren Sie, wie Sie eine Azure-Funktion verwenden, um Dokumente zu verarbeiten, die in einen Azure Blob Storage-Container hochgeladen werden. Im Rahmen dieses Workflows werden mithilfe des Layoutdiensts für die Formularerkennung Tabellendaten aus gespeicherten Dokumenten extrahiert und in einer CSV-Datei in Azure gespeichert. Die Daten können dann mithilfe von Microsoft Power BI angezeigt werden (wird hier nicht behandelt).

> [!div class="mx-imgBorder"]
> ![Diagramm: Workflow des Azure-Diensts](./media/tutorial-azure-function/workflow-diagram.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure-Speicherkontos
> * Erstellen eines Azure Functions-Projekts
> * Extrahieren von Layoutdaten aus hochgeladenen Formularen
> * Hochladen von Layoutdaten in Azure Storage

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Erstellen einer Formularerkennungsressource"  target="_blank">Erstellen Sie eine Formularerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt für die Formularerkennung abzurufen. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
  * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Ein lokales PDF-Dokument für die Analyse. Hierzu können Sie [dieses Beispieldokument](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) herunterladen und verwenden.
* Installation von [Python 3.8.x](https://www.python.org/downloads/)
* Installation des [Azure Storage-Explorers](https://azure.microsoft.com/features/storage-explorer/)
* Installation von [Azure Functions Core Tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools)
* Installation von Visual Studio Code mit folgenden Erweiterungen:
  * [Azure Functions-Erweiterung](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python-Erweiterung](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

[Erstellen Sie ein Azure-Speicherkonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) über das Azure-Portal. Wählen Sie als Kontoart die Option **StorageV2** aus.

Wählen Sie im linken Bereich die Registerkarte **CORS** aus, und entfernen Sie die ggf. vorhandene CORS-Richtlinie.

Erstellen Sie nach der Bereitstellung zwei leere Blob Storage-Container namens **test** (Test) und **output** (Ausgabe).

## <a name="create-an-azure-functions-project"></a>Erstellen eines Azure Functions-Projekts

Öffnen Sie Visual Studio Code. Wenn Sie die Azure Functions-Erweiterung installiert haben, sollte im linken Navigationsbereich ein Azure-Logo angezeigt werden. Wählen Sie es aus. Erstellen Sie ein neues Projekt, und erstellen Sie bei entsprechender Aufforderung einen lokalen Ordner namens **coa_new** für das Projekt.

![VS Code: Schaltfläche zum Erstellen einer Funktion](./media/tutorial-azure-function/vs-code-create-function.png)


Sie werden aufgefordert, eine Reihe von Einstellungen zu konfigurieren:
* Wählen Sie als Sprache die Option **Python** aus.
* Wählen Sie als **Vorlage den** Azure Blob Storage-Trigger aus. Legen Sie dann einen Namen für den Standardtrigger fest.
* Geben Sie bei der Aufforderung zum Auswählen der **Einstellungen** an, dass Sie neue lokale App-Einstellungen erstellen möchten.
* Wählen Sie Ihr Azure-Abonnement mit dem von Ihnen erstellten Speicherkonto aus. Geben Sie anschließend den Namen des Speichercontainers (in diesem Fall: `test/{name}`) ein.
* Geben Sie an, dass das Projekt im aktuellen Fenster geöffnet werden soll. 

![VS Code: Beispiel für Erstellungsaufforderung](./media/tutorial-azure-function/vs-code-prompt.png)

Nach Abschluss dieser Schritte wird von VS Code ein neues Azure-Funktionsprojekt mit einem Python-Skript namens *\_\_init\_\_.py* hinzugefügt. Dieses Skript wird ausgelöst, wenn eine Datei in den Speichercontainer **test** hochgeladen wird, führt aber keine Aktionen aus.

## <a name="test-the-function"></a>Testen der Funktion

Drücken Sie F5, um die grundlegende Funktion auszuführen. Daraufhin werden Sie von VS Code aufgefordert, ein zu verwendendes Speicherkonto auszuwählen. Wählen Sie das von Ihnen erstellte Speicherkonto aus, und setzen Sie den Vorgang fort.

Öffnen Sie den Azure Storage-Explorer, und laden Sie ein PDF-Beispieldokument in den Container **test** hoch. Überprüfen Sie dann das VS Code-Terminal. Das Skript sollte protokollieren, dass es durch den PDF-Upload ausgelöst wurde.

![VS Code: Terminaltest](./media/tutorial-azure-function/vs-code-terminal-test.png)


Beenden Sie das Skript, bevor Sie fortfahren.

## <a name="add-form-processing-code"></a>Hinzufügen von Formularverarbeitungscode

Fügen Sie als Nächstes dem Python-Skript Ihren eigenen Code hinzu, um den Formularerkennungsdienst aufzurufen und die hochgeladenen Dokumente mithilfe der [Layout-API](concept-layout.md) für die Formularerkennung zu analysieren.

Navigieren Sie in VS Code zur Datei *requirements.txt* der Funktion. Sie dient zum Definieren der Abhängigkeiten für Ihr Skript. Fügen Sie der Datei die folgenden Python-Pakete hinzu:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Öffnen Sie dann das Skript *\_\_init\_\_.py*. Fügen Sie die folgenden `import` -Anweisungen ein:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Die generierte Funktion `main` muss nicht geändert werden. Ihr benutzerdefinierter Code wird in diese Funktion eingefügt.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

Durch den folgenden Codeblock wird die Formularerkennungs-API zum [Analysieren des Layouts](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) für das hochgeladene Dokument aufgerufen. Geben Sie Ihre Endpunkt- und Schlüsselwerte an. 

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Formularerkennungsressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Schlüssel und Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. 
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im [Artikel zur Sicherheit von Cognitive Services](../../cognitive-services/cognitive-services-security.md).

Fügen Sie als Nächstes Code hinzu, um den Dienst abzufragen und die zurückgegebenen Daten abzurufen. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Fügen Sie dann den folgenden Code hinzu, um eine Verbindung mit dem Azure Storage-Container **output** herzustellen. Geben Sie Ihre eigenen Werte für Speicherkontoname und -schlüssel ein. Den Schlüssel können Sie im Azure-Portal auf der Registerkarte **Zugriffsschlüssel** Ihrer Speicherressource abrufen.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

Mit dem folgenden Code wird die zurückgegebene Antwort der Formularerkennung analysiert sowie eine CSV-Datei erstellt und in den Container **output** hochgeladen. 


> [!IMPORTANT]
> Dieser Code muss wahrscheinlich bearbeitet werden, um der Struktur Ihrer eigenen Formulardokumente zu entsprechen.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Durch den letzten Codeblock werden schließlich die extrahierten Tabellen- und Textdaten in Ihr Blob Storage-Element hochgeladen.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Ausführen der Funktion

Drücken Sie F5, um die Funktion erneut auszuführen. Laden Sie über den Azure Storage-Explorer ein PDF-Beispielformular in den Speichercontainer **test** hoch. Durch diese Aktion sollte die Ausführung des Skripts ausgelöst werden, und die resultierende CSV-Datei sollte als Tabelle im Container **output** angezeigt werden.

Sie können diesen Container mit Power BI verbinden, um umfangreiche Visualisierungen der darin enthaltenen Daten zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine in Python geschriebene Azure-Funktion verwenden, um hochgeladene PDF-Dokumente automatisch zu verarbeiten und ihren Inhalt in einem datenfreundlicheren Format auszugeben. Als Nächstes erfahren Sie, wie Sie die Daten mithilfe von Power BI anzeigen.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Was ist die Formularerkennung?](overview.md)
* [Layoutdienst für die Formularerkennung](concept-layout.md)