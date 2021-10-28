---
title: Bereitstellen von Abhängigkeiten und Bibliotheken von Drittanbietern in Azure Functions
description: Erfahren Sie, wie Sie Dateien oder Drittanbieterbibliotheken mitbringen
ms.date: 4/6/2020
ms.topic: article
zone_pivot_groups: bring-third-party-dependency-programming-functions
ms.openlocfilehash: be01f07a9906da58d080060185104aef29f78b77
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265303"
---
# <a name="bring-dependencies-or-third-party-library-to-azure-functions"></a>Bereitstellen von Abhängigkeiten und Bibliotheken von Drittanbietern in Azure Functions

In diesem Artikel erfahren Sie, wie Sie Abhängigkeiten von Drittanbietern in Ihre Funktions-Apps übernehmen. Beispiele für Abhängigkeiten von Drittanbietern sind .json-Dateien, Binärdateien und Machine Learning-Modelle. 

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Einbringen von Abhängigkeiten über das Functions Code-Projekt 
::: zone pivot="programming-language-python"
> [!div class="checklist"]
> * Einbinden von Abhängigkeiten durch Einbinden der Azure-Dateifreigabe
::: zone-end

## <a name="bring-in-dependencies-from-the-project-directory"></a>Einbringen von Abhängigkeiten aus dem Projektverzeichnis
::: zone pivot="programming-language-python"
Eine der einfachsten Möglichkeiten, Abhängigkeiten einzubringen, ist das Zusammenbringen der Dateien/Artefakte mit dem Funktions-App-Code in der Verzeichnisstruktur des Functions-Projekts. Hier sehen Sie ein Beispiel für die Verzeichnisbeispiele in einem Python-Funktionsprojekt:
```
<project_root>/
 | - my_first_function/
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - dependencies/
 | | - dependency1
 | - .funcignore
 | - host.json
 | - local.settings.json
```
Indem die Abhängigkeiten in einem Ordner im Projektverzeichnis der Functions-App abgelegt werden, wird der Abhängigkeiten-Ordner zusammen mit dem Code bereitgestellt. Daher kann Ihr Funktionscode über die Dateisystem-API auf die Abhängigkeiten in der Cloud zugreifen. 

### <a name="accessing-the-dependencies-in-your-code"></a>Zugreifen auf die Abhängigkeiten in Ihrem Code

Im Folgenden finden Sie ein Beispiel für den Zugriff auf und die Ausführung von ```ffmpeg```-Abhängigkeiten, die im ```<project_root>/ffmpeg_lib```-Verzeichnis gespeichert werden. 


```python
import logging

import azure.functions as func
import subprocess

FFMPEG_RELATIVE_PATH = "../ffmpeg_lib/ffmpeg"

def main(req: func.HttpRequest,
         context: func.Context) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    # context.function_directory returns the current directory in which functions is executed 
    ffmpeg_path = "/".join([str(context.function_directory), FFMPEG_RELATIVE_PATH])
    
    try:
        byte_output  = subprocess.check_output([ffmpeg_path, command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```
>[!NOTE]
> Möglicherweise müssen Sie `chmod` verwenden, um `Execute`-Rechte für die ffmpeg-Binärdatei in einer Linux-Umgebung zu bieten
::: zone-end

::: zone pivot="programming-language-java"
Eine der einfachsten Möglichkeiten, Abhängigkeiten einzubringen, ist das Zusammenbringen der Dateien/Artefakte mit dem Funktions-App-Code in der Verzeichnisstruktur des Funktionsprojekts. Hier sehen Sie ein Beispiel für die Verzeichnisbeispiele in einem Java-Funktionsprojekt:
```
<project_root>/
 | - src/
 | | - main/java/com/function
 | | | - Function.java
 | | - test/java/com/function
 | - artifacts/
 | | - dependency1
 | - host.json
 | - local.settings.json
 | - pom.xml
```
Insbesondere für Java müssen Sie die Artefakte beim Kopieren von Ressourcen explizit in den Build-/Zielordner hinzufügen. Hier sehen Sie ein Beispiel für die Verwendung in Maven:

```xml
...
<execution>
    <id>copy-resources</id>
    <phase>package</phase>
    <goals>
        <goal>copy-resources</goal>
    </goals>
    <configuration>
        <overwrite>true</overwrite>
        <outputDirectory>${stagingDirectory}</outputDirectory>
        <resources>
            <resource>
                <directory>${project.basedir}</directory>
                <includes>
                    <include>host.json</include>
                    <include>local.settings.json</include>
                    <include>artifacts/**</include>
                </includes>
            </resource>
        </resources>
    </configuration>
</execution>
...
```
Indem die Abhängigkeiten in einem Ordner im Projektverzeichnis der Functions-App abgelegt werden, wird der Abhängigkeiten-Ordner zusammen mit dem Code bereitgestellt. Daher kann Ihr Funktionscode über die Dateisystem-API auf die Abhängigkeiten in der Cloud zugreifen. 

### <a name="accessing-the-dependencies-in-your-code"></a>Zugreifen auf die Abhängigkeiten in Ihrem Code

Im Folgenden finden Sie ein Beispiel für den Zugriff auf und die Ausführung von ```ffmpeg```-Abhängigkeiten, die im ```<project_root>/ffmpeg_lib```-Verzeichnis gespeichert werden. 


```java
public class Function {
    final static String BASE_PATH = "BASE_PATH";
    final static String FFMPEG_PATH = "/artifacts/ffmpeg/ffmpeg.exe";
    final static String HELP_FLAG = "-h";
    final static String COMMAND_QUERY = "command";

    @FunctionName("HttpExample")
    public HttpResponseMessage run(
            @HttpTrigger(
                name = "req",
                methods = {HttpMethod.GET, HttpMethod.POST},
                authLevel = AuthorizationLevel.ANONYMOUS)
                HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) throws IOException{
        context.getLogger().info("Java HTTP trigger processed a request.");
            
        // Parse query parameter
        String flags = request.getQueryParameters().get(COMMAND_QUERY);
        
        if (flags == null || flags.isBlank()) {
            flags = HELP_FLAG;
        }

        Runtime rt = Runtime.getRuntime();
        String[] commands = { System.getenv(BASE_PATH) + FFMPEG_PATH, flags};
        Process proc = rt.exec(commands);
        
        BufferedReader stdInput = new BufferedReader(new 
        InputStreamReader(proc.getInputStream()));
   
        String out = stdInput.lines().collect(Collectors.joining("\n"));
        if(out.isEmpty()) {
            BufferedReader stdError = new BufferedReader(new 
                InputStreamReader(proc.getErrorStream()));
            out = stdError.lines().collect(Collectors.joining("\n"));
        }
        return request.createResponseBuilder(HttpStatus.OK).body(out).build();

    }
```
>[!NOTE]
> Damit dieser Codeausschnitt in Azure funktioniert, müssen Sie die benutzerdefinierte Anwendungseinstellung „BASE_PATH“ mit dem Wert „/home/site/wwwroot“ angeben
::: zone-end


::: zone pivot="programming-language-python"
## <a name="bring-dependencies-by-mounting-a-file-share"></a>Einbinden von Abhängigkeiten durch Einbinden einer Dateifreigabe

Wenn Sie Ihre Funktions-App unter Linux ausführen, gibt es eine andere Möglichkeit, Abhängigkeiten von Drittanbietern einzubringen. Mit Functions können Sie eine Dateifreigabe einbinden, die in Azure Files gehostet wird. Wählen Sie diesen Ansatz, wenn Sie Abhängigkeiten oder Artefakte von Ihrem Anwendungscode entkoppeln möchten.

Zunächst müssen Sie ein Azure-Speicherkonto erstellen. Im Konto müssen Sie auch eine Dateifreigabe in Azure Files erstellen. Befolgen Sie diese [Anleitung](../storage/files/storage-how-to-use-files-portal.md), um diese Ressourcen zu erstellen

Nachdem Sie das Speicherkonto und die Dateifreigabe erstellt haben, verwenden Sie den Befehl [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add), um die Dateifreigabe an Ihre Funktions-App anzufügen, wie im folgenden Beispiel gezeigt.

```console
az webapp config storage-account add \
  --name < Function-App-Name > \
  --resource-group < Resource-Group > \
  --subscription < Subscription-Id > \
  --custom-id < Unique-Custom-Id > \
  --storage-type AzureFiles \
  --account-name < Storage-Account-Name > \
  --share-name < File-Share-Name >  \
  --access-key < Storage-Account-AccessKey > \
  --mount-path </path/to/mount>
```



|       Flag    |  Wert     |
| ------------- | ---------------------------------- | 
| custom-id      |  Eine beliebige eindeutige Zeichenfolge |
| storage-type      |  Derzeit wird nur AzureFiles unterstützt |
| share-name      |  Bereits vorhandene Freigabe |
| mount-path     |   Pfad, unter dem innerhalb des Containers auf die Freigabe zugegriffen werden kann. Der Wert muss das Format `/dir-name` aufweisen und kann nicht mit `/home` beginnen |

Weitere Befehle zum Ändern/Löschen der Dateifreigabekonfiguration finden Sie [hier](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-update)


### <a name="uploading-the-dependencies-to-azure-files"></a>Hochladen der Abhängigkeiten in Azure Files

Eine Möglichkeit zum Hochladen Ihrer Abhängigkeit in Azure Files ist über das Azure-Portal. Anweisungen zum Hochladen von Abhängigkeiten über das Portal finden Sie in diesem [Leitfaden](../storage/files/storage-how-to-use-files-portal.md#upload-a-file). Weitere Optionen zum Hochladen Ihrer Abhängigkeiten in Azure Files sind [Azure CLI](../storage/files/storage-how-to-use-files-portal.md#upload-a-file) und [PowerShell](../storage/files/storage-how-to-use-files-portal.md#upload-a-file).


### <a name="accessing-the-dependencies-in-your-code"></a>Zugreifen auf die Abhängigkeiten in Ihrem Code

Nachdem Ihre Abhängigkeiten in die Dateifreigabe hochgeladen wurden, können Sie über Ihren Code auf die Abhängigkeiten zugreifen. Die bereitgestellte Freigabe ist unter dem angegebenen *Bereitstellungspfad* verfügbar, z. B. ```/path/to/mount```. Sie können mithilfe von Dateisystem-APIs auf das Zielverzeichnis zugreifen.

Das folgende Beispiel zeigt den HTTP-Triggercode, der auf die `ffmpeg`-Bibliothek zugreift, die in einer bereitgestellten Dateifreigabe gespeichert ist.

```python
import logging

import azure.functions as func
import subprocess 

FILE_SHARE_MOUNT_PATH = os.environ['FILE_SHARE_MOUNT_PATH']
FFMPEG = "ffmpeg"

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    command = req.params.get('command')
    # If no command specified, set the command to help
    if not command:
        command = "-h"

    try:
        byte_output  = subprocess.check_output(["/".join(FILE_SHARE_MOUNT_PATH, FFMPEG), command])
        return func.HttpResponse(byte_output.decode('UTF-8').rstrip(),status_code=200)
    except Exception as e:
        return func.HttpResponse("Unexpected exception happened when executing ffmpeg. Error message:" + str(e),status_code=200)
```

Wenn Sie diesen Code für eine Funktions-App in Azure bereitstellen, müssen Sie eine [App-Einstellung](functions-how-to-use-azure-function-app-settings.md#settings) mit dem Schlüsselnamen `FILE_SHARE_MOUNT_PATH` und dem Wert des bereitgestellten Dateifreigabepfads erstellen, in diesem Beispiel `/azure-files-share`. Zum lokalen Debuggen müssen Sie `FILE_SHARE_MOUNT_PATH` mit dem Dateipfad auffüllen, in dem Ihre Abhängigkeiten auf Ihrem lokalen Computer gespeichert sind. Hier ist ein Beispiel, das mit `local.settings.json` auf `FILE_SHARE_MOUNT_PATH` festgelegt werden soll:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "FILE_SHARE_MOUNT_PATH" : "PATH_TO_LOCAL_FFMPEG_DIR"
  }
}

```
::: zone-end



## <a name="next-steps"></a>Nächste Schritte

+ [Python-Entwicklerhandbuch für Azure Functions](functions-reference-python.md)
+ [Java-Entwicklerhandbuch für Azure Functions](functions-reference-java.md)
+ [Entwicklerreferenz zu Azure Functions](functions-reference.md)