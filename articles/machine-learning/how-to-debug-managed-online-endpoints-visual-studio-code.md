---
title: Lokales Debuggen von Onlineendpunkten in VS Code (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Visual Studio Code verwenden, um Onlineendpunkte lokal zu testen und zu debuggen, bevor sie in Azure bereitgestellt werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: luisquintanilla
ms.author: luquinta
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 33b46c490912d0c651c5629da17795efe074b6d9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566425"
---
# <a name="debug-online-endpoints-locally-in-visual-studio-code-preview"></a>Lokales Debuggen von Onlineendpunkten in Visual Studio Code (Vorschau)

Erfahren Sie, wie Sie den Visual Studio Code (VS Code) Debugger verwenden, um Onlineendpunkte lokal zu testen und zu debuggen, bevor sie in Azure bereitgestellt werden.

Mit den lokalen Endpunkten von Azure Machine Learning können Sie Ihr Bewertungsskript, die Umgebungskonfiguration, die Codekonfiguration und das Machine Learning-Modell lokal testen und debuggen.

## <a name="online-endpoint-local-debugging"></a>Lokales Debuggen des Onlineendpunkts

Wenn Sie Endpunkte lokal debuggen, bevor Sie sie in der Cloud bereitstellen, können Sie Fehler in Ihrem Code und Ihrer Konfiguration früher abfangen. Sie haben verschiedene Optionen zum lokalen Debuggen von Endpunkten mit VS Code.

- [HTTP-Rückschlussserver von Azure Machine Learning (Vorschau)](how-to-inference-server-http.md)
- Lokaler Endpunkt

Dieser Leitfaden konzentriert sich auf lokale Endpunkte.

Die folgende Tabelle enthält eine Übersicht über Szenarien, damit Sie die für Sie am besten geeignete Variante auswählen können.

| Szenario | HTTP-Rückschlussserver | Lokaler Endpunkt |
|--|--|--|
| Aktualisieren der lokalen Python-Umgebung **ohne Neuerstellung** des Docker-Images | Ja | Nein |
| Aktualisieren des Bewertungsskripts | Ja | Ja |
| Aktualisieren von Bereitstellungskonfigurationen (Bereitstellung, Umgebung, Code, Modell) | Nein | Ja |
| VS Code-Debuggerintegration | Ja | Ja |

## <a name="prerequisites"></a>Voraussetzungen

In diesem Leitfaden wird davon ausgegangen, dass Sie die folgenden Elemente lokal auf Ihrem PC installiert haben.

- [Docker](https://docs.docker.com/engine/install/)
- [VS-Code](https://code.visualstudio.com/#alt-downloads)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
- [Azure CLI-`ml`Erweiterung](how-to-configure-cli.md)

Weitere Informationen finden Sie im Leitfaden zur Vorbereitung Ihres Systems auf [die Bereitstellung verwalteter Onlineendpunkte](how-to-deploy-managed-online-endpoints.md#prepare-your-system).

Die Beispiele in diesem Artikel basieren auf Codebeispielen, die im Repository [azureml-examples ](https://github.com/azure/azureml-examples) enthalten sind. Um die Befehle lokal auszuführen, ohne YAML und andere Dateien kopieren/einfügen zu müssen, klonen Sie das Repository, und wechseln Sie dann in das `cli` Verzeichnis im Repository:

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Wenn Sie die Standardeinstellungen für die Azure-Befehlszeilenschnittstelle noch nicht festgelegt haben, speichern Sie Ihre Standardeinstellungen. Um zu vermeiden, dass Sie die Werte für Ihr Abonnement, Ihren Arbeitsbereich und Ihre Ressourcengruppe mehrfach eingeben müssen, verwenden Sie die folgenden Befehle. Ersetzen Sie die folgenden Parameter durch Werte für Ihre spezifische Konfiguration:

* Ersetzen Sie `<subscription>` durch Ihre Azure-Abonnement-ID.
* Ersetzen Sie `<workspace>` durch den Namen Ihres Azure Machine Learning-Arbeitsbereichs.
* Ersetzen Sie `<resource-group>` durch die Azure-Ressourcengruppe, die Ihren Arbeitsbereich enthält.
* Ersetzen Sie `<location>` durch die Azure-Region, die Ihren Arbeitsbereich enthält.

> [!TIP]
> Mit dem Befehl `az configure -l` können Sie die aktuellen Standardwerte anzeigen.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

## <a name="launch-development-container"></a>Starten des Entwicklungscontainers

Die lokalen Endpunkte von Azure Machine Learning verwenden Docker- und VS Code-Entwicklungscontainer (Dev-Container), um eine lokale Debugging-Umgebung zu erstellen und zu konfigurieren. Mit Entwicklungscontainern können Sie die Vorteile der VS Code-Funktionen innerhalb eines Docker-Containers nutzen. Weitere Informationen zu Entwicklungscontainern finden Sie unter [Erstellen eines Entwicklungscontainers](https://code.visualstudio.com/docs/remote/create-dev-container).

Um Online-Endpunkte lokal in VS Code zu debuggen, verwenden Sie das Flag `--vscode-debug` beim Erstellen oder Aktualisieren einer Azure Machine Learning-Onlinebereitstellung. Der folgende Befehl verwendet ein Bereitstellungsbeispiel aus dem Beispiele-Repository:

```azurecli
az ml online-deployment create --file endpoints/online/managed/sample/blue-deployment.yml --local --vscode-debug
```

> [!IMPORTANT]
> Auf dem Windows-Subsystem für Linux (WSL) müssen Sie Ihre PATH-Umgebungsvariable aktualisieren, um den Pfad zur ausführbaren VS-Code-Datei einzuschließen, oder WSL-Interop verwenden. Weitere Informationen finden Sie unter [Windows Interoperabilität mit Linux](/windows/wsl/interop).

Ein Docker-Image wird lokal erstellt. Alle Fehler bei der Umgebungskonfiguration oder Modelldatei werden in dieser Phase des Prozesses angezeigt.

> [!NOTE]
> Wenn Sie zum ersten Mal einen neuen oder aktualisierten Entwicklungscontainer starten, kann dies einige Minuten dauern.

Sobald das Image erfolgreich erstellt wurde, wird Ihr Entwicklungscontainer in einem VS Code-Fenster geöffnet.

Sie verwenden einige VS Code-Erweiterungen, um Ihre Bereitstellungen im Entwicklungscontainer zu debuggen. Azure Machine Learning installiert diese Erweiterungen automatisch in Ihrem Entwicklungscontainer.

- Rückschlussdebuggen
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> [!IMPORTANT]
> Stellen Sie vor dem Starten der Debugsitzung sicher, dass VS Code-Erweiterungen in Ihrem Entwicklungscontainer installiert wurden.  

## <a name="start-debug-session"></a>Debugsitzung starten

Nachdem Ihre Umgebung eingerichtet wurde, verwenden Sie den VS Code-Debugger, um Ihre Bereitstellung lokal zu testen und zu debuggen.

1. Öffnen Sie Ihr Bewertungsskript in Visual Studio Code.

    > [!TIP]
    > Das score.py-Skript, das vom zuvor bereitgestellten Endpunkt verwendet wird, befindet sich unter `azureml-samples/cli/endpoints/online/managed/sample/score.py` in dem Repository, das Sie geklont haben. Die Schritte in diesem Leitfaden funktionieren jedoch mit jedem Bewertungsskript.

1. Legen Sie hierfür an einer beliebigen Stelle im Code einen Breakpoint fest.

    - Um das Startverhalten zu debuggen, platzieren Sie Ihre(n) Breakpoint(s) in der Funktion `init`.
    - Um das Bewertungsverhalten zu debuggen, platzieren Sie Ihre(n) Breakpoint(s) in der Funktion `run`.

1. Wählen Sie die Ansicht „VS Code-Ausführung“ aus.
1. Wählen Sie in der Dropdownliste „Ausführen und Debuggen“ die Option **Azure ML: Lokalen Endpunkt debuggen** aus, um das lokale Debuggen Ihres Endpunkts zu starten.

    Überprüfen Sie im Abschnitt **Breakpoints** der Ansicht „Ausführen“, ob:

    - **Ausgelöste Ausnahmen** deaktiviert **sind**
    - Ob **abgefangene Ausnahmen** aktiviert  **sind**

    :::image type="content" source="media/how-to-debug-managed-online-endpoints-visual-studio-code/configure-debug-profile.png" alt-text="Debug-Profil der lokalen Umgebung von Azure ML Debug konfigurieren":::

1. Wählen Sie das Wiedergabesymbol neben der Dropdownliste „Ausführen und Debuggen“ aus, um ihre Debugsitzung zu starten.

    An diesem Punkt werden alle Breakpoints in Ihrer `init` Funktion erfasst. Verwenden Sie die Debugaktionen, um den Code schrittweise auszuführen. Weitere Informationen zu Debugaktionen finden Sie im [Leitfaden zu Debugaktionen](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Weitere Informationen zum VS Code-Debugger finden Sie unter [Debuggen in VS Code](https://code.visualstudio.com/Docs/editor/debugging)

## <a name="debug-your-endpoint"></a>Ihren Endpunkt debuggen

Nachdem Ihre Anwendung nun im Debugger ausgeführt wird, versuchen Sie, eine Vorhersage zu treffen, um Ihr Bewertungsskript zu debuggen.

Verwenden Sie den `ml`Erweiterungs-`invoke`Befehl, um eine Anforderung an Ihren lokalen Endpunkt zu senden.

```azurecli
az ml online-endpoint invoke --name <ENDPOINT-NAME> --request-file <REQUEST-FILE> --local
```

In diesem Fall ist `<REQUEST-FILE>` eine JSON-Datei, die Eingabedatenbeispiele für das Modell zur Erstellung von Vorhersagen enthält, ähnlich der folgenden JSON-Datei:

```json
{"data": [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]}
```

> [!TIP]
> Der Bewertungs-URI ist die Adresse, an der Ihr Endpunkt auf Anforderungen wartet. Verwenden Sie die `ml`-Erweiterung, um den Bewertungs-URI zu erhalten.
>
>    ```azurecli
>    az ml online-endpoint show --name <ENDPOINT-NAME> --local
>    ```
>
> Die Ausgabe sollte in etwa wie folgt aussehen:
>
> ```json
> {
>  "auth_mode": "aml_token",
>  "location": "local",
>  "name": "my-new-endpoint",
>  "properties": {},
>  "provisioning_state": "Succeeded",
>  "scoring_uri": "http://localhost:5001/score",
>  "tags": {},
>  "traffic": {},
>  "type": "online"
>}
>```
>
>Den Bewertungs-URI finden Sie in der Eigenschaft `scoring_uri`.

An diesem Punkt werden alle Breakpoints in Ihrer `run` Funktion erfasst. Verwenden Sie die Debugaktionen, um den Code schrittweise auszuführen. Weitere Informationen zu Debugaktionen finden Sie im [Leitfaden zu Debugaktionen](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

## <a name="edit-your-endpoint"></a>Bearbeiten Ihres Endpunkts

Beim Debuggen und Beheben von Problemen mit Ihrer Anwendung gibt es Szenarien, in denen Sie Ihr Bewertungsskript und die Konfigurationen aktualisieren müssen.

So wenden Sie Änderungen auf Ihren Code an:

1. Aktualisieren Ihres Codes
1. Starten Sie die Debugsitzung neu, indem Sie den Befehl `Developer: Reload Window` in der Befehlspalette verwenden. Weitere Informationen finden Sie in der [Befehlspaletten-Dokumentation](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette).

> [!NOTE]
> Da das Verzeichnis, das Ihren Code und Ihre Endpunktressourcen enthält, in den Entwicklungscontainer eingebunden ist, werden alle Änderungen, die Sie im Entwicklungscontainer vornehmen, mit Ihrem lokalen Dateisystem synchronisiert.

Für umfangreichere Änderungen, die Aktualisierungen Ihrer Umgebung und Endpunktkonfiguration beinhalten, verwenden Sie den `ml` Erweiterungsbefehl `update`. Dadurch wird eine vollständige Image-Neuerstellung mit Ihren Änderungen ausgelöst.

```azurecli
az ml online-deployment update --file <DEPLOYMENT-YAML-SPECIFICATION-FILE> --local --vscode-debug
```

Nachdem das aktualisierte Image erstellt und Ihr Entwicklungscontainer gestartet wurde, verwenden Sie den VS Code-Debugger, um Ihren aktualisierten Endpunkt zu testen und Probleme zu beheben.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
- [Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)](how-to-troubleshoot-managed-online-endpoints.md)
