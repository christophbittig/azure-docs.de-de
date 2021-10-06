---
title: Ereignisbasierte Gesichtsbearbeitung
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie eine ereignisbasierte Lösung in Azure bereitstellen, bei der eingehende Videos basierend auf einem Auftrag in Azure Media Services transformiert werden.
services: media-services
author: harmke
manager: ervandeh
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 5/21/2021
ms.author: inhenkel
ms.openlocfilehash: 198009abab7e1c5f663a454eb23debc55c81d7f3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730800"
---
# <a name="event-based-face-redaction"></a>Ereignisbasierte Gesichtsbearbeitung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Einführung
 
Bei einigen Szenarien oder Anwendungsfällen sollten Videos von Azure Media Services in dem Moment verarbeitet bzw. analysiert werden, in dem sie in einem Datenspeicher eintreffen. Ein Beispiel für einen Anwendungsfall ist, dass ein Team Videos eines Standorts oder Werks analysieren möchte, um zu überprüfen, ob sich die Personen an diesem Ort an die Sicherheitsanweisungen (z. B. das Tragen eines Helms) halten. Für diesen Anwendungsfall kann ein Edgegerät vor Ort die Videos aufzeichnen, wenn eine Bewegung erkannt wird, und diese dann an Azure senden. Zur Einhaltung der Datenschutzstandards sollten die Gesichter von Personen, die in den Videos zu sehen sind, bearbeitet werden, bevor die Analyse durch das Team erfolgen kann. Damit die angereicherten Videos so schnell wie möglich für das Team bereitgestellt werden können, sollte der Schritt für die Gesichtsbearbeitung sofort ausgeführt werden, wenn ein Video in Azure eintrifft. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Azure Media Services für ein ereignisbasiertes Szenario dieser Art in Azure verwenden. Videos, die in ein Speicherkonto hochgeladen werden, werden basierend auf einem Auftrag in Azure Media Services transformiert. Hierbei wird die API „Media Services v3“ verwendet.

Die genutzte spezifische Transformation wird als [Face Redactor](./analyze-face-redaction-concept.md) bezeichnet. Dies ist eine Voreinstellung von Azure Media Analytics, mit der Sie Ihr Video modifizieren können, indem Sie die Gesichter von ausgewählten Personen unscharf darstellen.

Am Ende der Schnellstartanleitung können Sie die in einem Video enthaltenen Gesichter bearbeiten:

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/output-redacted.gif" alt-text="Beispielausgabe":::

## <a name="solution-overview"></a>Übersicht über die Lösungen

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/architecture.png" alt-text="Übersicht über die Architektur der Lösung":::

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie die Lösung bereitstellen, auf die Sie oben in der Lösungsübersicht zugreifen können. Am Anfang steht ein Speicherkonto (Azure Data Lake Storage Gen2), das über eine Verbindung mit einem Ereignislistener verfügt (Event Grid). Der Ereignislistener löst eine Azure-Funktion aus, wenn neue MP4-Dateien in das Speicherkonto hochgeladen werden. Die Azure-Funktion übermittelt einen Auftrag an eine vorkonfigurierte Transformation in Azure Media Services. Das fertige bearbeitete Video wird in einem Blob Storage-Konto gespeichert.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Erstellen Sie eine Ressourcengruppe für diese Schnellstartanleitung.

## <a name="get-the-sample-and-understand-its-deployment"></a>Abrufen des Beispiels und Vertrautmachen mit der Bereitstellung

Erstellen Sie einen Fork des [Repositorys mit den Python-Beispielen](https://github.com/Azure-Samples/media-services-v3-python). In dieser Schnellstartanleitung verwenden wir das Beispiel „FaceRedactorEventBased“.

Die Bereitstellung dieses Beispiels umfasst drei einzelne Schritte: Bereitstellen der Azure-Dienste für die Einrichtung der Gesamtlösung, Bereitstellen der Funktions-App für die Übermittlung eines Auftrags an Azure Media Services, wenn eine neue Datei hochgeladen wird, und Konfigurieren des EventGrid-Triggers. Wir haben einen GitHub Actions-Workflow für die Ausführung dieser Schritte erstellt. Diese Lösung kann daher bereitgestellt werden, indem die erforderlichen Variablen Ihrer GitHub-Umgebung hinzugefügt werden. Dies bedeutet, dass keine lokalen Entwicklungstools benötigt werden.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Bevor der GitHub Actions-Workflow ausgeführt werden kann, muss ein Dienstprinzipal erstellt werden, der in der Ressourcengruppe über die Rollen *Mitwirkender* und *Storage-Blobdatenleser* verfügt. Dieser Dienstprinzipal dient als App, mit der alle Azure-Dienste im Namen von GitHub Actions bereitgestellt und konfiguriert werden. Der Dienstprinzipal wird auch nach der Bereitstellung der Lösung verwendet, um ein SAS-Token für Videos zu generieren, die verarbeitet werden müssen.

Fügen Sie die Variablen im folgenden Bash-Befehl ein, und führen Sie ihn in der Cloud Shell aus, um den Dienstprinzipal zu erstellen und mit den Rollen zu versehen, die in der Ressourcengruppe benötigt werden:
```bash
# Replace <subscription-id>, <name-of-resource-group> and <name-of-app> with the corresponding values. 
# Make sure to use a unique name for the app name parameter.

app_name="<name-of-app>"
resource_group="<name-of-resource-group>"
subscription_id="<subscription-id>"

az ad sp create-for-rbac --name $app_name --role contributor \
                     --scopes /subscriptions/$subscription_id/resourceGroups/$resource_group \
                     --sdk-auth

object_id=$(az ad sp list --display-name $app_name --query [0].objectId -o tsv)

az role assignment create --assignee $object_id --role "Storage Blob Data Reader" \
                      --scope /subscriptions/$subscription_id/resourceGroups/$resource_group
```
  
Der Befehl sollte ein JSON-Objekt ausgeben, das in etwa wie folgt aussieht:
   
```json
{
  "clientId": "<GUID>",
  "clientSecret": "<GUID>",
  "subscriptionId": "<GUID>",
  "tenantId": "<GUID>",
  (...)
}
```
Kopieren Sie die Ausgabe, und bewahren Sie sie so auf, dass Sie im nächsten Schritt darauf zugreifen können.
 
## <a name="add-service-principal-details-to-github-secrets"></a>Hinzufügen von Dienstprinzipaldetails zu GitHub-Geheimnissen 

Die Dienstprinzipaldetails sollten als [GitHub-Geheimnis](https://docs.github.com/en/actions/reference/encrypted-secrets) gespeichert werden, damit die erforderlichen Dienste von GitHub Actions in Azure bereitgestellt und konfiguriert werden können. Navigieren Sie für Ihr geforktes Repository zu „Repo Settings“ > „Secrets“ („Repositoryeinstellungen“ > „Geheimnisse“), und klicken Sie auf „Create New Secrets“ (Neue Geheimnisse erstellen). Erstellen Sie die folgenden Geheimnisse:
 - Erstellen Sie Azure-Anmeldeinformationen (AZURE_CREDENTIALS), und fügen Sie die Ausgabe aus dem vorherigen Schritt (vollständiger JSON-Code) ein. Im GitHub Actions-Workflow wird dieses Geheimnis verwendet, um eine Verbindung mit Azure herzustellen. 
 - Erstellen Sie die Client-ID (CLIENT_ID), und fügen Sie den Wert von „clientId“ aus dem vorherigen Schritt ein.
 - Erstellen Sie das Clientgeheimnis (CLIENT_SECRET), und fügen Sie den Wert von „clientSecret“ aus dem vorherigen Schritt ein.
 - Erstellen Sie die Mandanten-ID (TENANT_ID), und fügen Sie den Wert von „tenantId“ aus dem vorherigen Schritt ein.
 
## <a name="create-the-env-file"></a>Erstellen der ENV-Datei

Kopieren Sie den Inhalt aus der Datei „sample.env“, die in Ihrem geforkten Repository im Ordner „VideoAnalytics/FaceRedactorEventBased“ enthalten ist. Erstellen Sie anschließend Ihre eigene Datei „.env“, indem Sie auf „Datei hinzufügen“ > „Neue Datei erstellen“ klicken. Geben Sie der Datei den Namen *.env*, und fügen Sie die Variablen ein. Klicken Sie auf „Commit new file“ (Neue Datei committen), wenn Sie fertig sind. Jetzt ist alles für die Bereitstellung der Lösung bereit, aber zunächst untersuchen wir die Codedateien, die wir verwenden möchten.

## <a name="examine-the-code-for-provisioning-the-azure-resources"></a>Untersuchen des Codes für die Bereitstellung der Azure-Ressourcen

Mit dem unten angegebenen Bash-Skript werden die Azure-Dienste bereitgestellt, die in dieser Lösung verwendet werden. Das Bash-Skript verwendet die Azure CLI und führt die folgenden Aktionen durch:
- Laden der Umgebungsvariablen in lokale Variablen
- Definieren von Namen für ADLSgen2, ein generisches Azure Storage-Konto, Azure Media Services, Azure-Funktions-App und ein Thema und Abonnement für das Event Grid-System
- Bereitstellen der definierten Azure-Dienste

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/deploy_resources.azcli)]

## <a name="examine-azure-function-code"></a>Untersuchen des Azure-Funktionscodes

Nach der erfolgreichen Bereitstellung der Azure-Ressourcen können wir den Python-Code für unsere Azure-Funktion bereitstellen. Die Datei **/azure-function/EventGrid_AMSJob/__init__.py** enthält die Logik, mit der jeweils ein AMS-Auftrag ausgelöst wird, wenn eine Datei im Azure Data Lake Gen2-Dateisystem eintrifft. Das Skript führt die folgenden Schritte aus:
- Importieren von Abhängigkeiten und Bibliotheken
- Verwenden des Funktionsbinders zum Lauschen für Azure Event Grid
- Abrufen und Definieren von Variablen aus dem Ereignisschema
- Erstellen eines Eingabe/Ausgabe-Objekts für den AMS-Auftrag
- Herstellen einer Verbindung mit Azure Data Lake Gen2 per DataLakeService-Client und Generieren eines SAS-Tokens als Authentifizierungselement für die AMS-Auftragseingabe
- Konfigurieren und Erstellen des Auftrags

[!code-python[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureFunction/EventGrid_AMSJob/__init__.py)]

## <a name="examine-the-code-for-configuring-the-azure-resources"></a>Untersuchen des Codes für die Konfiguration der Azure-Ressourcen 

Das unten angegebene Bash-Skript wird zum Konfigurieren der Ressourcen verwendet, nachdem diese bereitgestellt wurden. Die Ausführung dieses Skripts ist der letzte Schritt zur Bereitstellung der Lösung, nachdem unser Funktionscode bereitgestellt wurde. Das Skript führt die folgenden Schritte aus:
- Konfigurieren der App-Einstellungen für die Funktions-App
- Erstellen eines Azure Event Grid-Systemthemas
- Erstellen des Event-Abonnements, damit die Azure-Funktion ausgelöst wird, wenn im ADLSg2-Ordner „raw“ ein Blob erstellt wird
- Erstellen der Azure Media Services-Transformation per REST-API-Aufruf. Diese Transformation wird in der Azure-Funktion aufgerufen.

> [!NOTE]
> Derzeit wird die Erstellung einer FaceRedaction-Transformation weder vom Azure Media Services v3 Python SDK noch von der Azure CLI unterstützt. Daher verwenden wir die REST-API-Methode zum Erstellen des Transformationsauftrags.

[!code-bash[Main](../../../media-services-v3-python/VideoAnalytics/FaceRedactorEventBased/AzureServicesProvisioning/configure_resources.azcli)]
 
## <a name="enable-github-actions-pipeline"></a>Aktivieren der GitHub Actions-Pipeline
 Die Workflowdatei in diesem Repository enthält die Schritte zum Ausführen der Bereitstellung dieser Lösung. Damit Sie den Workflow starten können, müssen Sie ihn für Ihr eigenes Repository aktivieren. Navigieren Sie zum Aktivieren in Ihrem Repository zur Registerkarte „Actions“ (Aktionen), und wählen Sie die Option „I understand my workflows, go ahead and enable them“ (Ich verstehe meine Workflows, und sie sollen aktiviert werden) aus.
:::image type="content" source="./media/face-redaction-event-based-python-quickstart/activate-workflow.png" alt-text="Workflow aktivieren":::

Nach dem Aktivieren von GitHub Actions finden Sie die Workloaddatei hier: [.github/workflows/main.yml](https://github.com/Azure-Samples/media-services-v3-python/blob/main/.github/workflows/main.yml).  Zusätzlich zu den Triggern ist noch ein Buildauftrag mit einigen Schritten vorhanden. Er enthält die folgenden Schritte:
- **Env**: Hier sind mehrere Umgebungsvariablen definiert, die auf die weiter oben hinzugefügten GitHub-Geheimnisse verweisen.
- **Lesen der Umgebungsdatei**: Die Umgebungsdatei für den Buildauftrag wird gelesen.
- **Auflösen von Projektabhängigkeiten per PIP**: Die erforderlichen Bibliotheken in unseren Azure-Funktionen werden in die GitHub Actions-Umgebung geladen.
- **Azure-Anmeldung**: In diesem Schritt wird das GitHub-Geheimnis für die Anmeldung bei der Azure CLI mit den Dienstprinzipaldetails verwendet.
- **Bereitstellen von Azure-Ressourcen mit der Azure CLI-Skriptdatei**: Führt das Bereitstellungsskript für die Bereitstellung der Azure-Ressourcen aus.
- **Bereitstellen des Azure-Funktionscodes**: In diesem Schritt wird die Azure-Funktion verpackt und im Verzeichnis „./azure-function“ bereitgestellt. Nachdem die Azure-Funktion erfolgreich bereitgestellt wurde, sollte sie im Azure-Portal unter dem Namen „EventGrid_AMSJob“ angezeigt werden:

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/azurefunction.png" alt-text="Azure-Funktion ist im Azure-Portal sichtbar":::

- **Konfigurieren von Azure-Ressourcen per Azure CLI-Skriptdatei**: Wenn alles korrekt ist, werden im letzten Schritt die bereitgestellten Azure-Dienste konfiguriert, um den Ereignislistener zu aktivieren.

Wählen Sie nach der Aktivierung der Workflows den Workflow „Deploy Azure Media Services FaceRedaction solution“ (Azure Media Services-Lösung „FaceRedaction“ bereitstellen) und dann die Option „Workflow ausführen“ aus. Die Lösung wird nun mit den Variablen bereitgestellt, die in den vorherigen Schritten hinzugefügt wurden. Warten Sie einige Minuten, und vergewissern Sie sich dann, dass die Ausführung erfolgreich war.

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/run-workflow.png" alt-text="Workflow ausführen":::

## <a name="test-your-solution"></a>Testen Ihrer Lösung
Navigieren Sie im Azure-Portal zum Speicher-Explorer Ihrer ADLS Gen2-Instanz. Laden Sie ein Video in den Container „raw“ hoch. Falls Sie ein Testvideo benötigen, können Sie es von [dieser Website](https://www.pexels.com/search/videos/group/) herunterladen. Die folgende Abbildung enthält eine Anleitung zum Hochladen eines Videos in das ADLS Gen2-Speicherkonto:

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/upload-test-data.png" alt-text="Hochladen von Videos":::

Vergewissern Sie sich auf Ihrer Azure Media Services-Instanz, dass ein Auftrag erstellt wurde. Navigieren Sie hierfür zu Ihrem Azure Media Services-Konto, und wählen Sie im Menü die Option „Transforms + Jobs“ (Transformationen und Aufträge) aus. Wählen Sie anschließend die Transformation für die Gesichtsbearbeitung (Face Redactor) aus.

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-transform.png" alt-text="AMS-Transformation":::

Auf dieser Seite sollte der Auftrag angezeigt werden, der von der Azure-Funktion ausgelöst wurde. Der Auftrag kann entweder bereits abgeschlossen sein oder sich noch in der Verarbeitung befinden.

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-job.png" alt-text="AMS Job":::

Wenn Sie den Auftrag auswählen, werden einige zugehörige Details angezeigt. Wenn Sie den Namen des Ausgabemedienobjekts auswählen und dann den Link zum verknüpften Speichercontainer verwenden, können Sie Ihr verarbeitetes Video sehen, nachdem der Auftrag abgeschlossen wurde.

:::image type="content" source="./media/face-redaction-event-based-python-quickstart/ams-output.png" alt-text="AMS-Ausgabe":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie die Schnellstartanleitung durchgearbeitet haben, sollten Sie die in der Ressourcengruppe erstellten Ressourcen löschen. Darüber hinaus können Sie auch das geforkte Repository löschen.

## <a name="next-steps"></a>Nächste Schritte

Falls Sie dieses Beispiel abändern möchten, soll der Code wahrscheinlich lokal ausgeführt werden. Für die lokale Entwicklung reichen die Variablen in der Datei „sample.env“ aus, da der Dienstprinzipal nicht benötigt wird, wenn für ein Benutzerkonto die Anmeldung bei der lokal installierten Azure CLI durchgeführt wird. Eine Anleitung zur lokalen Verwendung Ihrer Azure-Funktion finden Sie in [diesen Dokumenten](../../azure-functions/create-first-function-vs-code-python.md).
