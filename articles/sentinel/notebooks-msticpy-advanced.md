---
title: Erweiterte Konfigurationen für Jupyter Notebooks und MSTICPy in Microsoft Sentinel | Microsoft-Dokumentation
description: Erfahren Sie mehr über erweiterte Konfigurationen, die für Jupyter Notebooks mit MSTICPy verfügbar sind, wenn Sie in Microsoft Sentinel arbeiten.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 513f7c3c7e80f526f76e899a509de2b743fc9e7c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132516967"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Erweiterte Konfigurationen für Jupyter Notebooks und MSTICPy in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden erweiterte Konfigurationen für die Arbeit mit Jupyter Notebooks und MSTICPy in Microsoft Sentinel beschrieben.

Weitere Informationen finden Sie unter [Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks](notebooks.md) und [Tutorial: Erste Schritte mit Jupyter Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md).

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist eine Fortsetzung von [Tutorial: Erste Schritte mit Jupyter Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md). Es wird empfohlen, das Tutorial durchzuarbeiten, bevor Sie mit den weiter unten beschriebenen erweiterten Verfahren fortfahren.

## <a name="specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis"></a>Angeben von Authentifizierungsparametern für Azure- und Microsoft Sentinel-APIs

In diesem Verfahren wird beschrieben, wie Authentifizierungsparameter für Microsoft Sentinel und andere Azure-API-Ressourcen in der Datei **msticpyconfig.yaml** konfiguriert werden.

**So fügen Sie Einstellungen für die Azure-Authentifizierung und die Microsoft Sentinel-API im MSTICPy-Einstellungs-Editor hinzu**

1. Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. Wählen Sie auf der Registerkarte **Datenanbieter** die Option **AzureCLI** > **Hinzufügen** aus.

1. Wählen Sie die zu verwendende Authentifizierungsmethode aus:

    - Sie können zwar einen anderen Satz von Methoden als die [Azure-Standardeinstellungen](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods) verwenden, aber diese Verwendung ist keine typische Konfiguration.
    - Wenn Sie nicht die **env**-Authentifizierung (Umgebungsvariable) verwenden möchten, lassen Sie die Felder **clientId**, **tenantiId** und **clientSecret** leer.
    - Obwohl nicht empfohlen, unterstützt MSTICPy auch die Verwendung von Client-App-IDs und -Geheimnissen für Ihre Authentifizierung. In solchen Fällen definieren Sie die Felder **clientId**, **tenantId** und **clientSecret** direkt auf der Registerkarte **Datenanbieter**.

1. Wählen Sie **Datei speichern** aus, um Ihre Änderungen zu speichern.

## <a name="define-autoloading-query-providers"></a>Definieren des automatischen Ladens von Abfrageanbietern

Definieren Sie alle Abfrageanbieter, die MSTICPy automatisch laden soll, wenn Sie die `nbinit.init_notebook`-Funktion ausführen.

Wenn Sie häufig neue Notebooks erstellen, können Sie mit dem automatischen Laden von Abfrageanbietern Zeit sparen, indem Sie sicherstellen, dass die erforderlichen Anbieter vor anderen Komponenten wie Pivotfunktionen und Notebooks geladen werden.

**So fügen Sie automatisch ladende Abfrageanbieter hinzu**

1. Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. Gehen Sie auf der Registerkarte **Autoload QueryProv** (QueryProv automatisch laden) wie folgt vor:

   - Bei Microsoft Sentinel-Anbietern geben Sie sowohl den Anbieternamen als auch den Namen des Arbeitsbereichs an, mit dem Sie eine Verbindung herstellen möchten.
   - Geben Sie für andere Abfrageanbieter nur den Anbieternamen an.

   Jeder Anbieter verfügt auch über die folgenden optionalen Werte:

   - **Automatische Verbindung:** Diese Option ist standardmäßig als **True** definiert, und MSTICPy versucht, sich sofort nach dem Laden beim Anbieter zu authentifizieren. MSTICPy geht davon aus, dass Sie Anmeldeinformationen für den Anbieter in Ihren Einstellungen konfiguriert haben.

   - **Alias:** Wenn MSTICPy einen Anbieter lädt, wird der Anbieter einem Python-Variablennamen zugewiesen. Standardmäßig lautet der Variablenname **qryworkspace_name** für Microsoft Sentinel-Anbieter und **qryprovider_name** für andere Anbieter.

        Wenn Sie z. B. einen Abfrageanbieter für den *ContosoSOC*-Arbeitsbereich laden, wird dieser Abfrageanbieter in Ihrer Notebook-Umgebung mit dem Namen `qry_ContosoSOC` erstellt. Fügen Sie einen Alias hinzu, wenn Sie einen kürzeren oder leichter einzugebenden und zu merkenden Namen verwenden möchten. Der Name der Anbietervariable lautet `qry_<alias>`, wobei `<alias>` der Name durch den von Ihnen angegebenen Aliasnamen ersetzt wird.

        Anbieter, die Sie über diesen Mechanismus laden, werden auch dem MSTICPy-Attribut `current_providers` hinzugefügt, das z. B. im folgenden Code verwendet wird:

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. Wählen Sie **Einstellungen speichern** aus, um die Änderungen zu speichern.

## <a name="define-autoloaded-msticpy-components"></a>Definieren automatisch geladener MSTICPy-Komponenten

In diesem Verfahren wird beschrieben, wie Sie andere Komponenten definieren, die automatisch von MSTICPy geladen werden, wenn Sie die `nbinit.init_notebook`-Funktion ausführen.

Zu den unterstützten Komponenten gehören in der folgenden Reihenfolge:

1. **TILookup:** Die [TI-Anbieterbibliothek](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. **GeoIP:** Der [GeoIP-Anbieter](notebook-get-started.md#add-geoip-provider-settings), den Sie verwenden möchten
1. **AzureData:** Das Modul, das Sie zum Abfragen von Details zu [Azure-Ressourcen](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis) verwenden
1. **AzureSentinelAPI:** Das Modul, das Sie zum Abfragen der [Microsoft Sentinel-API](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis) verwenden
1. **Notebooklets:** Notebooklets aus dem [msticnb-Paket](https://msticnb.readthedocs.io/en/latest/)
1. **Pivot:** Pivotfunktionen

> [!NOTE]
> Die Komponenten werden in dieser Reihenfolge geladen, da die Pivotkomponente Abfragen und andere Anbieter benötigt, um die Pivotfunktionen zu finden, die sie an Entitäten anfügt. Weitere Informationen finden Sie in der [MSTICPy-Dokumentation](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html).
>

**So definieren Sie automatisch geladene MSTICPy-Komponenten**

1. Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. Definieren Sie auf der Registerkarte **Autoload Components** (Komponenten automatisch laden) alle erforderlichen Parameterwerte. Beispiel:

   - **GeoIpLookup**.  Geben Sie den Namen des GeoIP-Anbieters ein, den Sie verwenden möchten, entweder *GeoLiteLookup* oder *IPStack*.  Weitere Informationen finden Sie unter [Hinzufügen von GeoIP-Anbietereinstellungen](notebook-get-started.md#add-geoip-provider-settings).

   - **AzureData- und AzureSentinelAPI-Komponenten**.  Definieren Sie die folgenden Werte:

      - **auth_methods:** Setzen Sie die Standardeinstellungen für AzureCLI außer Kraft, und stellen Sie die Verbindung mit den ausgewählten Methoden her.
      - **Auto-connect:** Legen Sie den Wert auf „false“ fest, um ohne das Herstellen einer Verbindung zu laden.

      Weitere Informationen finden Sie unter [Angeben von Authentifizierungsparametern für Azure- und Microsoft Sentinel-APIs](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis).

   - **Notebooklets**. Die **Notebooklets**-Komponente verfügt über einen einzelnen Parameterblock: **AzureSentinel**.

      Geben Sie Ihren Microsoft Sentinel-Arbeitsbereich mit der folgenden Syntax an: `workspace:\<workspace name>`. Der Arbeitsbereichsname muss einer der Arbeitsbereiche sein, die auf der Registerkarte **Microsoft Sentinel** definiert sind.

      Wenn Sie weitere Parameter hinzufügen möchten, die an die Funktion `notebooklets init` gesendet werden sollen, geben Sie sie als Schlüssel-Wert-Paare an, getrennt durch Zeilenvorschübe. Beispiel:

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      Weitere Informationen finden Sie in der [MSTICNB-Dokumentation (MSTIC Notebooklets)](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init).

    Einige Komponenten wie **TILookup** und **Pivot** erfordern keine Parameter.

1. Wählen Sie **Einstellungen speichern** aus, um die Änderungen zu speichern.

## <a name="switch-between-python-36-and-38-kernels"></a>Wechseln zwischen Python 3.6- und 3.8-Kerneln

Wenn Sie zwischen den Python-Kerneln 3.65 und 3.8 wechseln, kann es sein, dass MSTICPy und andere Pakete nicht wie erwartet installiert werden.

Dies kann der Fall sein, wenn der `!pip install pkg`-Befehl in der ersten Umgebung ordnungsgemäß installiert wird, in der zweiten jedoch nicht. Dies führt zu einer Situation, in der die zweite Umgebung das Paket nicht importieren oder verwenden kann.

Es wird empfohlen, keine `!pip install...`-Pakete in Azure ML-Notebooks zu installieren. Verwenden Sie stattdessen eine der folgenden Optionen:

- **Verwenden Sie den %pip-Zeilen-Magic-Befehl innerhalb eines Notebooks**. Führen Sie Folgendes aus:

  ```python

  %pip install --upgrade msticpy
  ```

- **Installation über ein Terminal**:

  1. Öffnen Sie ein Terminal in Azure ML-Notebooks, und führen Sie die folgenden Befehle aus:

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. Schließen Sie das Terminal, und starten Sie den Kernel neu.

## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>Festlegen einer Umgebungsvariablen für die Datei „msticpyconfig.yaml“

Wenn Sie in Azure ML arbeiten und Ihre **msticpyconfig.yaml**-Datei im Stammverzeichnis Ihres Benutzerordners speichern, wird MSTICPy diese Einstellungen automatisch finden. Wenn Sie die Notebooks jedoch in einer anderen Umgebung ausführen, befolgen Sie die Anweisungen in diesem Abschnitt, um eine Umgebungsvariable festzulegen, die auf den Speicherort Ihrer Konfigurationsdatei verweist.

Wenn Sie den Pfad zur Datei **msticpyconfig.yaml** in einer Umgebungsvariablen definieren, können Sie die Datei an einem bekannten Speicherort speichern und sicherstellen, dass Sie immer die gleichen Einstellungen laden.

Verwenden Sie mehrere Konfigurationsdateien mit mehreren Umgebungsvariablen, wenn Sie unterschiedliche Einstellungen für verschiedene Notebooks verwenden möchten.

1. Entscheiden Sie sich für einen Speicherort für Ihre **msticpyconfig.yaml**-Datei, wie in **~/.msticpyconfig.yaml** oder **%userprofile%/msticpyconfig.yaml**.

    **Azure ML-Benutzer**: Wenn Sie Ihre Konfigurationsdatei in Ihrem Azure ML-Benutzerordner speichern, wird die Datei von der MSTICPy-Funktion `init_notebook` (in der Initialisierungszelle ausgeführt) automatisch gefunden und verwendet, und Sie müssen keine **MSTICPYCONFIG**-Umgebungsvariable festlegen.

    Wenn Sie jedoch auch Geheimnisse in der Datei gespeichert haben, wird empfohlen, die Konfigurationsdatei auf dem lokalen Computelaufwerk zu speichern. Auf den internen Computespeicher kann nur die Person zugreifen, die die Compute-Instanz erstellt hat, während der freigegebene Speicher für alle Benutzer zugänglich ist, die Zugriff auf Ihren Azure ML-Arbeitsbereich haben.

    Weitere Informationen hierzu finden Sie unter [Was ist eine Azure Machine Learning-Compute-Instanz?](../machine-learning/concept-compute-instance.md)

1. Kopieren Sie bei Bedarf die Datei **msticpyconfig.yaml** an den ausgewählten Speicherort.

1. Legen Sie die Umgebungsvariable **MSTICPYCONFIG** so fest, dass sie auf diesen Speicherort verweist.

Verwenden Sie eines der folgenden Verfahren, um die Umgebungsvariable **MSTICPYCONFIG** zu definieren.

# <a name="windows"></a>[Windows](#tab/windows)

So legen Sie z. B. die Umgebungsvariable **MSTICPYCONFIG** für Windows-Systeme fest:

1. Verschieben Sie die Datei **msticpyconfig.yaml** nach Bedarf in die Compute-Instanz.
1. Öffnen Sie das Dialogfeld **Systemeigenschaften** mit der Registerkarte **Erweitert**.
1. Wählen Sie **Umgebungsvariablen...** aus, um das Dialogfeld **Umgebungsvariablen** zu öffnen.
1. Wählen Sie im Bereich **Systemvariablen** die Option **Neu...** aus, und definieren Sie die Werte wie folgt:

    - **Variablenname**: Definieren Sie ihn als `MSTICPYCONFIG`.
    - **Variablenwert**: Geben Sie den Pfad zu Ihrer Datei **msticpyconfig.yaml** ein.

# <a name="linux"></a>[Linux](#tab/linux)

In diesem Verfahren wird beschrieben, wie Sie die **BASHRC**-Datei aktualisieren, um die Umgebungsvariable **MSTICPYCONFIG** für Linux-Systeme festzulegen.

1. Verschieben Sie die Datei **msticpyconfig.yaml** nach Bedarf in die Compute-Instanz.

1. Öffnen Sie ein Azure ML-Terminal, z. B. auf der Microsoft Sentinel-Seite **Notebooks**.

1. Überprüfen Sie, ob Sie auf die Datei **msticpyconfig.yaml** zugreifen können.

   In Ihrem Azure ML-Terminal sollte Ihr aktuelles Verzeichnis Ihr Azure ML-Basisverzeichnis des Dateispeichers sein, das im Compute-Linux-System eingebunden ist. Die Eingabeaufforderung sieht ähnlich aus wie im folgenden Beispiel:

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   Führen Sie alle Dateien im Basisverzeichnis auf, einschließlich der Datei **msticpyconfig.yaml**, indem Sie `ls` eingeben.

1. Um die Datei **msticpyconfig.yaml** in den Computedateispeicher zu verschieben, geben Sie Folgendes ein:

   ```python
   mv msticpyconfig.yaml ~
   ```

1. Verwenden Sie einen der folgenden Prozesse, um die **BASHRC**-Datei für Ihre Umgebungsvariable zu bearbeiten:

    |Befehl  |Schritte  |
    |---------|---------|
    |**vim**     |     1. Ausführen von: `vim ~/.bashrc` <br>2. Wechseln Sie zum Ende der Datei, indem Sie **UMSCHALT+G** > **ENDE** drücken. 3. Erstellen Sie eine neue Zeile, indem Sie **a** eingeben und dann die **EINGABETASTE** drücken. <br>4. Fügen Sie Ihre Umgebungsvariable hinzu, und drücken Sie dann die **ESC-TASTE**, um wieder in den Befehlsmodus zu wechseln. <br>5. Speichern Sie die Datei, indem Sie **:wq** eingeben.    |
    |**nano**     |           1. Ausführen von: `nano ~/.bashrc`<br>        1. Wechseln Sie zum Ende der Datei, indem Sie **ALT+/** oder **OPTION+/** drücken.<br>        1. Fügen Sie Ihre Umgebungsvariable hinzu, und speichern Sie dann die Datei. Drücken Sie **STRG+X** und dann **Y**.      |
    |     |         |

    Fügen Sie eine der folgenden Umgebungsvariablen hinzu:

    - Wenn Sie die Datei **msticpyconfig.yaml** verschoben haben, führen Sie `export MSTICPYCONFIG=~/msticpyconfig.yaml` aus.
    - Wenn Sie die Datei **msticpyconfig.yaml** nicht verschoben haben, führen Sie `export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml` aus.

# <a name="azure-ml-options"></a>[Azure ML-Optionen](#tab/azure-ml)

Wenn Sie die Datei **msticpyconfig.yaml** an einer anderen Stelle als Ihrem Azure ML-Benutzerordner speichern müssen, verwenden Sie eine der folgenden Optionen:

- **Eine *nbuser_settings.py*-Datei im Stammverzeichnis Ihres Benutzerordners**.  Dieser Prozess ist zwar einfacher und weniger intrusiv als das Bearbeiten der Datei **kernel.json**, wird jedoch nur unterstützt, wenn Sie die Funktion `init_notebook` am Anfang Ihres Notebook-Codes ausführen. Dies ist zwar das Standardverhalten, aber wenn Sie den Notebook-Code ausführen, ohne zuerst `init_notebook` auszuführen, kann MSTICPy die Konfigurationsdatei möglicherweise nicht finden.

    1. Erstellen Sie im Azure ML-Terminal die Datei **nbuser_settings.py** im Stammverzeichnis Ihres Benutzerordners, d. h. dem Ordner mit Ihrem Benutzernamen.
    1. Fügen Sie in der Datei **nbuser_settings.py** die folgenden Zeilen hinzu:

        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    Diese Datei wird automatisch von der `init_notebook`-Funktion importiert und legt die Umgebungsvariable `MSTICPYCONFIG` für das aktuelle Notebook fest.

- **Die Datei *kernel.json* für Ihren Python-Kernel.** Verwenden Sie dieses Verfahren, wenn Sie planen, das Notebook manuell auszuführen, und möglicherweise ohne die `init_notebook`-Funktion am Anfang aufzurufen.

  Es gibt Kernel für Python 3.6 und Python 3.8. Wenn Sie beide Kernel verwenden, bearbeiten Sie beide Dateien.

  - **Python 3.8-Speicherort**: */usr/local/share/jupyter/kernels/python38-azureml/kernel.json*
  - **Python 3.6-Speicherort**: */usr/local/share/jupyter/kernels/python3-azureml/kernel.json*

  So legen Sie die Umgebungsvariable in der Datei **kernel.json** fest

  1. Erstellen Sie eine Kopie der Datei **kernel.json**, und öffnen Sie das Original in einem Editor. Möglicherweise müssen Sie `sudo` verwenden, um die Datei **kernel.json** zu überschreiben, und der Inhalt der Datei sieht in etwa wie im folgenden Beispiel aus:

      ```python
      {
         "argv": [
         "/anaconda/envs/azureml_py38/bin/python",
         "-m",
         "ipykernel_launcher",
         "-f",
         "{connection_file}"
         ],
         "display_name": "Python 3.8 - AzureML",
         "language": "python"
      }
      ```

  1. Fügen Sie nach dem `"language"`-Element die folgende Zeile hinzu: `"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }`

      Stellen Sie sicher, dass Sie das Komma am Ende der Zeile `"language": "python"` hinzufügen. Beispiel:

      ```python
      {
          "argv": [
          "/anaconda/envs/azureml_py38/bin/python",
          "-m",
          "ipykernel_launcher",
          "-f",
          "{connection_file}"
          ],
          "display_name": "Python 3.8 - AzureML",
          "language": "python",
          "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
      }
      ```

---

> [!NOTE]
> Für die Linux- und Windows-Optionen müssen Sie Ihren Jupyter-Server neu starten, damit er die von Ihnen definierte Umgebungsvariablen aufnehmen kann.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

|Subject  |Weitere Verweise  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy-Paketkonfiguration](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy-Einstellungs-Editor](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Konfigurieren Ihrer Notebook-Umgebung](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [MPSettingsEditor Notebook](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Hinweis**: Das Azure Sentinel-Notebooks-GitHub-Repository enthält auch eine *Msticpyconfig.yaml*-Vorlagendatei mit auskommentierten Abschnitten, die Ihnen helfen könnten, die Einstellungen zu verstehen.      |
|**Microsoft Sentinel und Jupyter Notebooks**     |      - [Jupyter Notebooks: Eine Einführung](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy-Dokumentation](https://msticpy.readthedocs.io/)<br>    - [Dokumentation zu Microsoft Sentinel-Notebooks](notebooks.md)<br>    - [Das Infosec-Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Exemplarische Vorgehensweise zum Linux-Host-Explorer-Notebook](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Gründe für die Verwendung von Jupyter für die Sicherheitsuntersuchungen](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Sicherheitsuntersuchung mit Microsoft Sentinel und Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas-Dokumentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh-Dokumentation](https://docs.bokeh.org/en/latest/)       |
|     |         |
