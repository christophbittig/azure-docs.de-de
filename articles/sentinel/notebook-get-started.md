---
title: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel
description: Gehen Sie den „Leitfaden für die ersten Schritte für Microsoft Sentinel ML-Notebooks“ durch, um die Grundlagen von Microsoft Sentinel-Notebooks mit MSTICPy und Abfragen zu erlernen.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 3bcafc82c3fced8a5c3ff53feddb8be5d4338178
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517460"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Tutorial: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Tutorial wird beschrieben, wie Sie den **Leitfaden für die ersten Schritte für Microsoft Sentinel ML-Notebooks** ausführen, der die grundlegenden Konfigurationen für die Ausführung von Jupyter-Notebooks in Microsoft Sentinel und das Ausführen einfacher Datenabfragen einrichtet.

In dem **Leitfaden für die ersten Schritte für Microsoft Sentinel ML-Notebooks** wird MSTICPy verwendet. Das ist eine Python-Bibliothek mit Cybersicherheitstools, die von Microsoft erstellt wurde und Funktionen zur Bedrohungssuche und Untersuchung bereitstellt.

MSTICPy reduziert die Codemenge, die Kunden für Microsoft Sentinel schreiben müssen und bietet Folgendes:

- Datenabfragefunktionen für Microsoft Sentinel-Tabellen, Microsoft Defender für Endpoint, Splunk und andere Datenquellen.
- Threat Intelligence-Suchen mit TI-Anbietern wie z. B. VirusTotal und AlienVault OTX.
- Anreicherungsfunktionen wie Geolocation von IP-Adressen, Indikator der Gefährdungsextraktion (Indicator of Compromise, IoC) und Whols-Suchen.
- Visualisierungstools mit Hilfe von Ereigniszeitachsen, Prozessstrukturen und geografischer Zuordnung.
- Erweiterte Analysen, wie z. B. Zeitreihenzerlegung, Anomalieerkennung und Clustering.

In den Schritten in diesem Tutorial wird beschrieben, wie Sie den **Leitfaden für die ersten Schritte für Microsoft Sentinel ML-Notebooks** in Ihrem Azure ML-Arbeitsbereich über Microsoft Sentinel ausführen. Sie können dieses Lernprogramm auch als Anleitung für die Durchführung ähnlicher Schritte verwenden, um Notebooks in anderen Umgebungen, auch lokal, durchzuführen.

Weitere Informationen finden Sie unter [Verwenden von Notebooks für Untersuchungen](hunting.md#use-notebooks-to-power-investigations) und Verwenden von [Jupyter-Notebooks zum Suchen nach Sicherheitsbedrohungen.](notebooks.md)

> [!NOTE]
> Mehrere Microsoft Sentinel Notebooks verwenden MSTICPy nicht, wie z. B. die **Anmeldeinformationsscanner**-Notebooks oder die PowerShell- und C#-Beispiele. Notebooks, die MSTICpy nicht verwenden, benötigen die in diesem Artikel beschriebene MSTICPy-Konfiguration nicht.
>

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen, um Notebooks in Microsoft Sentinel zu verwenden. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Microsoft Sentinel-Notebooks](notebooks.md#manage-access-to-microsoft-sentinel-notebooks).

- Sie benötigen Python ab Version 3.6 um die Schritte in diesem Tutorial durchzuführen. In Azure ML können Sie entweder einen Python 3.8-Kernel (empfohlen) oder einen Python 3.6-Kernel verwenden.

- Dieses Notebook verwendet den Geolocation-Suchdienst [MaxMind GeoLite2](https://www.maxmind.com) für IP-Adressen. Um den MaxMind GeoLite2-Dienst verwenden zu können, benötigen Sie einen Kontoschlüssel. Sie können sich auf der [Maxmind-Anmeldeseite](https://www.maxmind.com/en/geolite2/signup) registrieren, um ein kostenloses Konto zu erstellen und einen Schlüssel zu erhalten.

- Dieses Notebook verwendet [VirusTotal](https://www.virustotal.com) (VT) als Threat Intelligence-Quelle. Um die Suche nach VirusTotal Threat Intelligence verwenden zu können, benötigen Sie ein VirusTotal-Konto und einen API-Schlüssel.

    Sie können sich auf der Seite [Erste Schritte mit VirusTotal](https://developers.virustotal.com/v3.0/reference#getting-started) registrieren, um ein kostenloses VT-Konto zu erstellen. Wenn Sie bereits ein Benutzer von VirusTotal sind, können Sie Ihren vorhandenen Schlüssel verwenden.

    > [!WARNING]
    > Wenn Sie einen VT-Unternehmensschlüssel verwenden, speichern Sie ihn in Azure Key Vault anstelle der **msticpyconfig.yaml**-Datei. Weitere Informationen finden Sie unter [Angeben von Geheimnissen als Key Vault Geheimnisse](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets) in der MSTICPY-Dokumentation.
    >
    > Wenn Sie derzeit keinen Azure Key Vault einrichten möchten, registrieren Sie sich für ein kostenloses Konto und verwenden Sie es, bis Sie den Key Vault Speicher einrichten können.

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>Ausführen und Initialisieren von „Leitfaden für die ersten Schritte“

In diesem Verfahren wird beschrieben, wie Sie Ihr Notebook starten und MSTICpy initialisieren.

1. Wählen Sie in Microsoft Sentinel auf der linken Seite **Notebooks** aus.

1. Wählen Sie auf der Registerkarte **Vorlagen** die Option **Leitfaden für die ersten Schritte für Microsoft Sentinel ML-Notebooks** und dann  > **Notebook sichern** aus, um es in Ihrem Azure ML-Arbeitsbereich zu speichern.

    Wählen Sie **Notebook starten** aus, um das Notebook auszuführen. Das Notebook enthält eine Reihe von Zellen:

    - *Markdown*-Zellen enthalten Text und Grafiken mit Anweisungen zur Verwendung des Notebooks
    - *Code* zellen enthalten ausführbaren Code, der die Notebookfunktionen durchführt

    **Lesen und Ausführen von Codezellen**

    Lesen und Ausführen von Codezellen der Reihenfolge nach. Das Überspringen von Zellen oder deren Ausführung in falscher Reihenfolge kann später zu Fehlern im Notebook führen.

    Führen Sie jede Zelle aus, indem Sie auf die Schaltfläche "Abspielen" links neben jeder Zelle auswählen. Abhängig von der ausgeführten Funktion kann der Code in der Zelle sehr schnell ausgeführt werden oder es kann einige Sekunden dauern, bis er abgeschlossen ist.

    Bei der Ausführung ändert sich die Wiedergabeschaltfläche in einen Wartekreisel für den Ladevorgang. Der Status des Ladevorgangs `Executing` wird zusammen mit der verstrichenen Zeit am unteren Rand der Zelle angezeigt.

    > [!TIP]
    > Wenn Ihr Notebook nicht wie beschrieben funktioniert, starten Sie den Kernel neu, und führen Sie das Notebook noch einmal von Anfang an aus. Wenn die Ausführung einer Zelle im **Leitfaden für die ersten Schritte** für Notebooks beispielsweise länger als eine Minute dauert, versuchen Sie, den Kernel neu zu starten und das Notebook erneut auszuführen.
    >
    > Der **Leitfaden für die ersten Schritte** für Notebooks enthält Anweisungen zur grundlegenden Verwendung von Jupyter-Notebooks, einschließlich des Neustarts des Jupyter-Kernels.
    >

    Nachdem Sie das Lesen und Ausführen der Zellen im Abschnitt **Was ist ein Jupyter Notebook** abgeschlossen haben, können Sie die Konfigurationsaufgaben starten, beginnend mit dem Abschnitt **Einrichten der Notebookumgebung**.

1. Führen Sie die erste Codezelle im Abschnitt **Einrichten der Notebookumgebung** Ihres Notebooks aus, der den folgenden Code enthält:

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    Der Initialisierungsstatus wird in der Ausgabe angezeigt. Konfigurationswarnungen zu fehlenden Einstellungen in der `Missing msticpyconfig.yaml` Datei sind zu erwarten, da Sie noch nichts konfiguriert haben.

> [!NOTE]
> Die meisten Microsoft Sentinel-Notebooks beginnen mit einer MSTICpy-Initialisierungszelle, die:
>
> - Die Mindestversionen für Python und MSTICPy definiert, die das Notebook benötigt.
> - Sicher stellt, dass die neueste Version von MSTICPy installiert ist.
> - Die Funktion importiert und ausführt `init_notebook`.
>

## <a name="create-your-configuration-file"></a>Schreiben Sie Ihre Konfigurationsdatei

Nach der grundlegenden Initialisierung können Sie Ihre Konfigurationsdatei mit den grundlegenden Einstellungen für die Arbeit mit MSTICPy erstellen.

Viele Microsoft Sentinel-Notebooks stellen eine Verbindung mit externen Diensten, wie z. B. [VirusTotal](https://www.virustotal.com) (VT) her, um Daten zu sammeln und zu erweitern. Um eine Verbindung mit diesen Diensten herzustellen, müssen Sie die Konfigurationsdetails festlegen und speichern, z. B. Authentifizierungstoken. Es ist nicht mehr notwendig, dass Sie bei jeder Verwendung eines Notebooks die Authentifizierungstoken und Arbeitsbereichsdetails eingeben, wenn Sie diese Daten in Ihrer Konfigurationsdatei haben.

MSTICPy verwendet die Datei **msticpyconfig.yaml** zum Speichern einer Vielzahl von Konfigurationsdetails.  Standardmäßig wird die Datei **msticpyconfig.yaml** von der Notebook-Initialisierungsfunktion generiert. Wenn Sie [dieses Notebook aus dem Microsoft Sentinel-Portal geklont haben](#run-and-initialize-the-getting-started-guide-notebook), wird die Konfigurationsdatei mit Microsoft Sentinel-Arbeitsbereichsdaten aufgefüllt. Diese Daten werden aus einer **config.json**-Datei gelesen, die beim Starten Ihres Notebooks im Azure ML-Arbeitsbereich erstellt wurde. Weitere Informationen finden Sie in der [Dokumentation zur MSTICPy Paket Konfiguration](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html).

In den folgenden Abschnitten wird beschrieben, wie Sie der Datei **msticpyconfig.yaml** zusätzliche Konfigurationsdetails hinzufügen.

> [!NOTE]
> Wenn Sie den *Leitfaden für die ersten Schritte* für Notebook erneut ausführen und bereits über eine minimal konfigurierte Datei **msticpyconfig.yaml** verfügen, überschreibt oder ändert die `init_notebook` Funktion Ihre vorhandene Datei nicht.
>

> [!TIP]
> Wählen Sie zu einem beliebigen Zeitpunkt das Dropdownmenü **-Help** im MSTICPy-Konfigurationstool aus, um weitere Anweisungen und Links zur ausführlichen Dokumentation zu erhalten.
>

### <a name="display-the-msticpy-settings-editor"></a>Anzeigen des Editors für MSTICPy-Einstellungen

1. Führen Sie in einer Codezelle den folgenden Code aus, um das `MpConfigEdit` Tool zu importieren und einen Einstellungseditor für die Datei **msticpyconfig.yaml** anzuzeigen:

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    Beispiel:

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="Screenshot des Editors für MSTICPy-Einstellungen":::.

    Die automatisch erstellte Datei **msticpyconfig.yaml**, die im Einstellungseditor angezeigt wird, enthält zwei Einträge im Abschnitt Microsoft Sentinel. Beide Einträge werden mit Details zum Microsoft Sentinel-Arbeitsbereich aufgefüllt, aus dem das Notebook geklont wurde. Ein Eintrag trägt den Namen Ihres Arbeitsbereichs, der andere den Namen **Standard**.

    Mit MSTICPy können Sie Konfigurationen für mehrere Microsoft Sentinel-Arbeitsbereiche speichern und zwischen diesen wechseln. Mit dem **Standard** eintrag können Sie sich standardmäßig bei Ihrem „Home“-Arbeitsbereich authentifizieren, ohne ihn explizit benennen zu müssen. Wenn Sie zusätzliche Arbeitsbereiche hinzufügen, können Sie einen davon als **Standardeintrag** konfigurieren.

    > [!NOTE]
    > In der Azure ML-Umgebung kann es 10 bis 20 Sekunden lang dauern, bis der Einstellungseditor angezeigt wird.

1. Überprüfen Sie Ihre aktuellen Einstellungen, und wählen Sie **Einstellungen Speichern** aus.

### <a name="add-threat-intelligence-provider-settings"></a>Hinzufügen von Einstellungen des Threat Intelligence-Anbieters

In diesem Verfahren wird beschrieben, wie Sie Ihren [VirusTotal-API-Schlüssel](#prerequisites) in der Datei **msticpyconfig.yaml** speichern. Sie können den API-Schlüssel in Azure Key Vault hochladen, aber Sie müssen zuerst die Key Vault Einstellungen konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Key Vault-Einstellungen](#configure-key-vault-settings).

**So fügen Sie Details von VirusTotal im Editor für die MSTICPy-Einstellungen hinzu**:

1. Geben Sie den folgenden Code in eine Codezelle ein und führen Sie ihn aus:

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. Wählen Sie auf der Registerkarte **TI-Anbieter** die Option **Provider hinzufügen** > **VirusTotal** > **Hinzufügen** aus.

1. Wählen Sie unter **Authentifizierungsschlüssel** neben der **Option Speichern** die Option **Text** aus.

1. Fügen Sie ihren API-Schlüssel in das Feld **Wert** ein.

1. Wählen Sie **Aktualisieren** und dann unten im Einstellungseditor **Einstellungen Speichern** aus.

> [!TIP]
> Weitere Informationen zu anderen unterstützten Threat Intelligence-Anbietern finden Sie unter [Threat Intelligence-Anbieter](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html) in der MSTICPy-Dokumentation und [Threat Intelligence-Integration in Microsoft Sentinel](threat-intelligence-integration.md).
>
### <a name="add-geoip-provider-settings"></a>Hinzufügen von GeoIP-Anbietereinstellungen

In diesem Verfahren wird beschrieben, wie Sie einen [MaxMind GeoLite2-Kontoschlüssel](#prerequisites) in der Datei **msticpyconfig.yaml** speichern, wodurch Ihr Notebook Geolocation-Suchdienste für IP-Adressen verwenden kann.

**So fügen Sie GeoIP-Anbietereinstellungen im Einstellungseditor für MSTICPy hinzu**:

1. Geben Sie den folgenden Code in eine Codezelle ein und führen Sie ihn aus:

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. Wählen Sie auf der Registerkarte **GeoIP-Anbieter** die Option **Provider hinzufügen** > **GeoIPLite** > **Hinzufügen** aus.

1. Geben Sie im Feld **Wert** Ihren MaxMind-Kontoschlüssel ein.

1. Aktualisieren Sie bei Bedarf den Standardordner **~/.msticpy** zum Speichern der heruntergeladenen GeoIP-Datenbank.

    - Unter Windows wird dieser Ordner **%USERPROFILE%/.msticpy** zugeordnet.
    - Unter Linux oder macOS wird dieser Pfad dem Ordner **.msticpy** in Ihrem Basisordner zugeordnet.

> [!TIP]
> Weitere Informationen zu anderen unterstützten Geolocation-Suchdiensten finden Sie in der [Dokumentation zu GEOIP-Anbietern für MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).
>

### <a name="configure-azure-cloud-settings"></a>Konfigurieren von Azure Cloud Shell Einstellungen

Wenn Ihre Organisation die öffentliche Azure-Cloud nicht verwendet, müssen Sie das in Ihren Einstellungen angeben, um erfolgreich Daten aus Microsoft Sentinel und Azure zu authentifizieren und zu verwenden. Weitere Informationen finden Sie unter [Angeben der Azure-Cloud und der Azure-Standardauthentifizierungsmethoden](#specify-the-azure-cloud-and-azure-authentication-methods).

### <a name="validate-settings"></a>Einstellungen überprüfen

Wählen Sie im Einstellungseditor **Einstellungen überprüfen** aus.

Warnmeldungen zu fehlenden Konfigurationen sind zu erwartet, Sie sollten jedoch keine für Threat Intelligence-Anbieter oder GeoIP-Anbietereinstellungen erhalten.

Je nach Ihrer Umgebung müssen Sie möglicherweise auch die [Key Vault Einstellungen konfigurieren](#configure-key-vault-settings) oder die [Azure-Cloud](#specify-the-azure-cloud-and-azure-authentication-methods) angeben.

Wenn Sie aufgrund der Überprüfung Änderungen vornehmen müssen, nehmen Sie diese Änderungen vor, und wählen Sie dann **Einstellungen Speichern** aus.

Wenn Sie fertig sind, wählen Sie die Schaltfläche **Schließen** aus, um die Validierungsausgabe auszublenden.

Weitere Informationen finden Sie unter: [Erweiterte Konfigurationen für Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebooks-msticpy-advanced.md)

## <a name="load-saved-msticpy-settings"></a>Laden Sie die gespeicherten MSTICPy-Einstellungen

Mit der Prozedur [Ihre Konfigurationsdatei erstellen](#create-your-configuration-file) haben Sie Ihre Einstellungen in der lokalen Datei **msticpyconfig.yaml** gespeichert.

MSTICPy lädt diese Einstellungen jedoch erst dann automatisch neu, wenn Sie den Kernel neu starten oder ein anderes Notebook ausführen. Um MSTICPy zum erneuten Laden aus der neuen Konfigurationsdatei zu zwingen, wechseln Sie mit dem folgenden Code zu der nächsten Codezelle, und führen Sie sie aus:

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>Testen Sie Ihr Notebook

Nachdem Sie Ihre Umgebung initialisiert und die grundlegenden Einstellungen für Ihren Arbeitsbereich konfiguriert haben, verwenden Sie die `QueryProvider` MSTICPy-Klasse, um das Notebook zu testen. `QueryProvider` fragt eine Datenquelle ab, in diesem Fall Ihr Microsoft Sentinel-Arbeitsbereich, und stellt die abgefragten Daten zum Anzeigen und Analysieren in Ihrem Notebook zur Verfügung.

Verwenden Sie die folgenden Verfahren, um eine Instanz der `QueryProvider`-Klasse zu erstellen. Authentifizieren Sie sich über Ihr Notebook bei Microsoft Sentinel und zeigen Sie Abfragen mit einer Vielzahl verschiedener Parameteroptionen an und führen Sie sie aus.

> [!TIP]
> Sie können mehrere Instanzen von `QueryProvider` zur Verwendung mit mehreren Microsoft Sentinel-Arbeitsbereichen oder anderen Datenanbietern wie z. B. Microsoft Defender für Endpoint laden.
>

### <a name="load-the-queryprovider"></a>Laden Sie den QueryProvider

Um den  `QueryProvider` für `AzureSentinel` zu laden, wechseln Sie zu der Zelle mit dem folgenden Code, und führen Sie sie aus:

```python
# Initialize a QueryProvider for Microsoft Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> Wenn beim Laden des Microsoft Sentinel-Treibers eine `Runtime dependency of PyGObject is missing` Warnung angezeigt wird, finden Sie weitere Informationen unter [Fehler: *Runtime-Abhängigkeit von PyGObject fehlt*](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error).
Diese Warnung wirkt sich nicht auf die Notebook-Funktionalität aus.
>

### <a name="authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook"></a>Authentifizieren Sie sich bei Ihrem Microsoft Sentinel-Arbeitsbereich über Ihr Notebook

Authentifizieren Sie sich mithilfe der [Geräteautorisierung](../active-directory/develop/v2-oauth2-device-code.md) mit Ihren Azure-Anmeldeinformationen bei Ihrem Microsoft Sentinel-Arbeitsbereich.

Die Geräteautorisierung fügt der Authentifizierung einen weiteren Faktor hinzu, indem ein einmaliger Gerätecode generiert wird, den Sie im Rahmen des Authentifizierungsprozesses bereitstellen.

**So authentifizieren Sie sich mithilfe der Geräteautorisierung**:

1. Führen Sie die folgende Codezelle aus, um einen Gerätecode zu generieren und anzuzeigen:

   ```python
   # Get the Microsoft Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Microsoft Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    Beispiel:

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="Ein Screenshot, der einen Geräteautorisierungscode zeigt":::.

1. Wählen Sie den angegebenen Code aus, und kopieren Sie ihn in Ihre Zwischenablage. Wechseln Sie dann zu [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) und fügen Sie den Code dort ein, wo Sie dazu aufgefordert werden.

1. Wenn Sie die Bestätigungsmeldung erhalten, dass Sie sich angemeldet haben, schließen Sie die Browser-Registerkarte und kehren Sie zu Ihrem Notebook in Microsoft Sentinel zurück.

   In Ihrem Notebook wird eine Ausgabe ähnlich der Folgenden angezeigt:

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="Ein Screenshot, der zeigt, dass der Geräteautorisierungsprozess abgeschlossen ist":::.

**Zwischenspeichern Ihres Anmeldetokens mit der Azure CLI**

Um zu vermeiden, dass Sie sich erneut authentifizieren müssen, wenn Sie den Kernel neu starten oder andere Notebooks ausführen, können Sie Ihr Anmeldetoken mit Azure CLI zwischenspeichern.

Die Azure CLI-Komponente auf der Compute-Instanz speichert ein *Aktualisierungstoken* zwischen, das wiederverwendet werden kann, bis das Token abläuft. MSTICPy verwendet automatisch die Azure CLI Anmeldeinformationen, sofern diese verfügbar sind.

Geben Sie zum Authentifizieren mit der Azure CLI Folgendes in eine leere Zelle ein und führen Sie sie aus:

```python
!az login
```

> [!NOTE]
> Sie müssen sich erneut authentifizieren, wenn Sie Ihre Compute-Instanz neu starten oder zu einer anderen Instanz wechseln. Weitere Informationen finden Sie im Abschnitt [Zwischenspeichern von Anmeldeinformationen mit der Azure CLI](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI) im Wiki „Microsoft Sentinel Notebooks GitHub Repository“.
>

### <a name="view-the-microsoft-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>Anzeigen des Datenschemas des Microsoft Sentinel-Arbeitsbereichs und integrierter MSTICPy-Abfragen

Nachdem Sie eine Verbindung mit einem Microsoft Sentinel QueryProvider hergestellt haben, können Sie die für Abfragen verfügbaren Datentypen verstehen, indem Sie das Microsoft Sentinel-Datenschema des Arbeitsbereichs abfragen.

Der Microsoft Sentinel QueryProvider verfügt über eine `schema_tables` Eigenschaft, die Ihnen eine Liste von Schematabellen bietet, und eine `schema` Eigenschaft, die auch die Spaltennamen und Datentypen für jede Tabelle enthält.

**So zeigen Sie die ersten 10 Tabellen im Microsoft Sentinel-Schema an**:

Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus. Sie können `[:10]` weglassen, um alle Tabellen in Ihrem Arbeitsbereich aufzulisten.

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

Die folgende Ausgabe wird angezeigt:

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy enthält auch viele integrierte Abfragen, die Sie ausführen können. Listen Sie verfügbare Abfragen mit `.list_queries()` auf und rufen Sie die spezifischen Details zu einer Abfrage ab, indem Sie sie mit einem Fragezeichen (`?`) aufrufen, das als Parameter enthalten ist. Alternativ können Sie die Liste der Abfragen und die zugehörige Hilfe im Abfragebrowser anzeigen.

**So zeigen Sie ein Beispiel für die verfügbaren Abfragen an**:

Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus. Sie können `[::5]` weglassen, um alle Abfragen aufzulisten.

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

Die folgende Ausgabe wird angezeigt:

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

**Sie erhalten Hilfe zu einer Abfrage, indem Sie `?` als**  Parameter übergeben:

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

Die folgende Ausgabe wird angezeigt:

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

**So zeigen Sie die Tabellen und die Abfragen in einer scroll-fähigen, filterbaren Liste an**:

Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

```python
qry_prov.browse_queries()
```

Für die ausgewählte Abfrage werden alle erforderlichen und optionalen Parameter, zusammen mit dem vollständigen Text der Abfrage, angezeigt. Beispiel:

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="Ein Screenshot, der Tabellen und Abfragen, die in einer scroll-fähigen, filterbaren Liste angezeigt werden zeigt":::.

Weitere Informationen finden Sie unter [Ausführen einer vordefinierten Abfrage](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query) in der MSTICPy-Dokumentation.

> [!NOTE]
> Obwohl Sie keine Abfragen im Browser ausführen können, können Sie das Beispiel kopieren und am Ende jeder Abfrage einfügen, um es an anderer Stelle im Notebook auszuführen.
>

### <a name="run-queries-with-time-parameters"></a>Ausführen von Abfragen mit Zeitparametern

Die meisten Abfragen erfordern Zeitparameter. Die Eingabe von Datums-/Uhrzeitzeichenfolgen ist mühsam, und das Ändern dieser Zeichenfolgen an mehreren Stellen kann fehleranfällig sein.

Jeder Abfrageanbieter verfügt über standardmäßige Start- und Endzeitparameter für Abfragen. Diese Zeitparameter werden standardmäßig verwendet, wenn Zeitparameter aufgerufen werden. Sie können den Standardzeitbereich ändern, indem Sie das `query_time` Steuerelement öffnen. Die Änderungen bleiben wirksam, bis Sie sie erneut ändern.

Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

```python
# Open the query time control for your query provider
qry_prov.query_time
```

Legen Sie die `start` und die Uhrzeiten `end` nach Bedarf fest. Beispiel:

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="Ein Screenshot für das Festlegen von Standardzeitparametern für Abfragen":::.

### <a name="run-a-query-using-the-built-in-time-range"></a>Führen Sie eine Abfrage mithilfe des integrierten Zeitbereichs aus

Die Abfrageergebnisse werden als [Pandas-Datenrahmen](https://pandas.pydata.org)zurückgegeben, bei dem es sich um eine tabellarische Datenstruktur handelt, z. B. ein Arbeitsblatt oder eine Datenbanktabelle. Sie können die [Pandas-Funktionen](https://pandas.pydata.org/docs/user_guide/10min.html) verwenden, um eine zusätzliche Filterung und Analyse der Abfrageergebnisse durchzuführen.

Die folgende Codezelle führt eine Abfrage mit den Standardzeiteinstellungen des Abfrageanbieters aus. Sie können diesen Bereich ändern und die Codezelle erneut ausführen, um den neuen Zeitbereich abzufragen.

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

Die Ausgabe zeigt die ersten fünf Ergebniszeilen an. Beispiel:

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="Ein Screenshot, der eine Abfrage, die mit dem integrierten Zeitbereich ausgeführt wird, zeigt":::.

Wenn keine Daten vorhanden sind, werden nur die Spaltenüberschriften angezeigt.

### <a name="run-a-query-using-a-custom-time-range"></a>Führen Sie eine Abfrage mithilfe eines benutzerdefinierten Zeitbereichs durch

Sie können auch ein neues Abfragezeitobjekt erstellen und als Parameter an eine Abfrage übergeben. Dadurch können Sie eine einmalige Abfrage für einen anderen Zeitbereich ausführen, ohne die Standardwerte des Abfrageanbieters zu beeinflussen.

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

Nachdem Sie den gewünschten Zeitbereich festgelegt haben, können Sie den Zeitbereich an die Abfragefunktion übergeben. Dafür müssen Sie den folgenden Code in einer vom vorherigen Code getrennten Zelle ausführen:

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

Sie können die datetime-Werte auch als Python-datetimes- oder date-time-Zeichenfolgen mithilfe der `start`und`end` Parameter übergeben:

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>Anpassen Ihrer Abfragen

Sie können die integrierten Abfragen anpassen, indem Sie eine zusätzliche Abfragelogik hinzufügen oder vollständige Abfragen mithilfe der `exec_query`-Funktion ausführen.

Die meisten integrierten Abfragen unterstützen beispielsweise den `add_query_items`-Parameter, mit dem Sie Filter oder andere Vorgänge an die Abfragen anfügen können.

1. Führen Sie die folgende Codezelle aus, um einen Datenrahmen hinzuzufügen, der die Anzahl der Warnungen anhand des Warnungsnamens zusammenfasst:

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. Übergeben Sie eine vollständige KQL-Abfragezeichenfolge an den Abfrageanbieter. Die Abfrage wird für den verbundenen Arbeitsbereich ausgeführt und die Daten werden als Panda-DataFrame zurückgegeben. Führen Sie Folgendes aus:

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

Weitere Informationen finden Sie unter

- Der [MSTICPy-Abfrageverweis](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [Ausführen von vordefinierten MSTICPy-Abfragen](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>Testen Sie VirusTotal und GeoLite2

**So überprüfen Sie in VirusTotal-Daten auf eine IP-Adresse**:

Um mithilfe von Threat Intelligence zu sehen, ob eine IP-Adresse in VirusTotal-Daten angezeigt wird, führen Sie die Zelle mit dem folgenden Code aus:

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

Die Ausgabe zeigt Details zu den Ergebnissen an. Beispiel:

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="Ein Screenshot einer IP-Adresse, die in VirusTotal-Daten angezeigt wird":::.

Stellen Sie sicher, nach unten zu scrollen, um die vollständigen Ergebnisse anzuzeigen. Weitere Informationen finden Sie unter [Threat Intel-Suchen in MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html).

**So können Sie die Geolocation-IP-Suche** testen:

Führen Sie die Zelle mit dem folgenden Code aus, um die Geolocation-Details für eine IP-Adresse mithilfe des MaxMind-Diensts zu erhalten:

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

Die Ausgabe zeigt die Geolocation-Informationen für die IP-Adresse an. Beispiel:

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> Wenn Sie diesen Code zum ersten Mal ausführen, sollte der GeoLite-Treiber seine Datenbank herunterladen.
>

Weitere Informationen finden Sie unter [MSTICPy GeoIP Anbieter](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).

## <a name="configure-key-vault-settings"></a>Die Key Vault-Einstellungen konfigurieren

Dieser Abschnitt ist nur relevant, wenn Sie Geheimnisse in einem Azure Key Vault speichern.

Wenn Sie Geheimnisse in Azure Key Vault speichern, müssen Sie zuerst den Key Vault im [globalen Azure KeyVault-Verwaltungsportal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults) erstellen.

Die erforderlichen Einstellungen sind alle Werte, die Sie aus den Tresoreigenschaften erhalten, obwohl einige möglicherweise unterschiedliche Namen haben. Beispiel:

- Der **VaultName** wird oben links auf dem Bildschirm mit Azure Key Vault **Eigenschaften** angezeigt
- Die **TenantId** wird als **Verzeichnis-ID** angezeigt
- Die **AzureRegion** wird als **Standort** angezeigt
- Die **Autorität** ist die Cloud für Ihren Azure-Dienst.

Nur die Werte **VaultName**, **TenantId** und **Authority** sind erforderlich, um Geheimnisse aus dem Tresor abzurufen. Die anderen Werte sind erforderlich, wenn Sie einen Tresor aus MSTICPy erstellen möchten. Weitere Informationen finden Sie unter [Angeben von Geheimnissen als Key Vault Geheimnisse](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

Die Option **KeyRing verwenden** ist standardmäßig ausgewählt und ermöglicht es Ihnen, Key Vault-Anmeldeinformationen in einem lokalen KeyRing zwischenspeichern. Weitere Informationen finden Sie in dieser [KeyRing-Dokumentation](https://keyring.readthedocs.io/en/latest/index.html).

> [!CAUTION]
> Verwenden Sie die Option **KeyRing verwenden** nicht, wenn Sie der Host Compute-Instanz, auf dem das Notebook ausgeführt wird, nicht vollständig vertrauen.
>
> In unserem Fall ist die *Compute-Instanz* der Jupyter Hub-Server, auf dem der Notebookkernel ausgeführt wird und nicht notwendigerweise der Computer, auf dem Ihr Browser ausgeführt wird. Bei der Verwendung von Azure ML ist *Compute*, die Azure ML Compute-Instanz, die Sie ausgewählt haben. Der Schlüsselbund führt die Zwischenspeicherung auf dem Host durch, auf dem der Notebookkernel ausgeführt wird.
>

**So fügen Key Vault-Einstellungen im MSTICPy-Einstellungseditor hinzu**:

1. Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. Geben Sie die Tresordaten für Ihren Key Vault ein. Beispiel:

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="Screenshot, der den Abschnitt „Key Vault Setup“ zeigt":::

1. Wählen Sie **Speichern** und dann **Einstellungen speichern** aus.

### <a name="test-key-vault"></a>Schlüsseltresor testen

Um Ihren Schlüsseltresor zu testen, müssen Sie überprüfen, ob Sie eine Verbindung herstellen und Ihre Geheimnisse anzeigen können. Wenn Sie kein Geheimnis hinzugefügt haben, werden Sie auch keine Details sehen können. Fügen Sie bei Bedarf dem Tresor ein Testgeheimnis aus dem Azure Key Vault-Portal hinzu und überprüfen Sie, ob es in Microsoft Sentinel angezeigt wird.

Beispiel:

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> Lassen Sie die angezeigte Ausgabe nicht in Ihrem gespeicherten Notebook. Wenn die Ausgabe echte Geheimnisse enthält, verwenden Sie den Befehl **Ausgabe löschen** des Notebooks, bevor Sie das Notebook speichern.
>
> Löschen Sie außerdem die zwischengespeicherten Kopien des Notebooks. Suchen Sie beispielsweise in dem Unterordner Ihres Notebookverzeichnisses nach **.ipynb_checkpoints** und löschen Sie alle gefundenen Kopien dieses Notebooks. Das Speichern des Notizbuchs mit einer gelöschten Ausgabe sollte die Prüfpunktkopie überschreiben.
>

Nachdem Sie Key Vault konfiguriert haben, können Sie die Schaltfläche **auf KV hochladen** in den Abschnitten Datenanbieter und TI-Anbieter verwenden, um die ausgewählte Einstellung in den Tresor zu verschieben. MSTICPy generiert einen Standardnamen für das Geheimnis basierend auf dem Pfad der Einstellung, wie z. B. `TIProviders-VirusTotal-Args-AuthKey`.

Wenn der Wert erfolgreich hochgeladen wurde, wird der Inhalt des Felds **Wert** im Einstellungseditor gelöscht und die zugrunde liegende Einstellung wird durch einen Platzhalterwert ersetzt. MSTICPy verwendet das, um anzugeben, dass beim Abrufen des Schlüssels der Key Vault-Pfad automatisch generiert werden soll.

Wenn Sie die erforderlichen Geheimnisse bereits in einem Key Vault gespeichert haben, können Sie den Geheimnisnamen in das Feld **Wert** eingeben. Wenn das Geheimnis nicht in Ihrem Standardtresor gespeichert ist (die im Abschnitt [Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault) angegebenen Werte), können Sie den Pfad **VaultName/SecretName** angeben.

Das Abrufen von Einstellungen aus einem Tresor in einem anderen Mandanten wird derzeit nicht unterstützt. Weitere Informationen finden Sie unter [Angeben von Geheimnissen als Key Vault Geheimnisse](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>Geben Sie die Azure-Cloud- und Azure-Authentifizierungsmethoden an

Wenn Sie eine Sovereign Cloud oder Government-Azure-Cloud anstelle der öffentlichen oder globalen Azure-Cloud verwenden, müssen Sie in Ihren Einstellungen die entsprechende Cloud auswählen. Für die meisten Organisationen ist die globale Cloud die Standardeinstellung.

Sie können diese Azure-Einstellungen auch verwenden, um Standardeinstellungen für den Azure-Authentifizierungstyp zu definieren.

**Geben Sie die Azure-Cloud- und Azure-Authentifizierungsmethoden an**:

1. Wechseln Sie mit dem folgenden Code zur nächsten Zelle und führen Sie sie aus:

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. Wählen Sie die von Ihrer Organisation verwendete Cloud aus oder belassen Sie die standardmäßig ausgewählte **globale** Option.

1. Wählen Sie eine oder mehrere der folgenden Methoden aus:

    - **env**, um Ihre Azure-Anmeldeinformationen in den Umgebungsvariablen zu speichern.
    - **msi**, um Managed Service Identity zu verwenden. Dabei handelt es sich um eine Identität, die dem Host oder virtuellen Computer zugewiesen ist, auf dem der Jupyter-Hub ausgeführt wird. MSI wird in Azure ML Compute-Instanzen derzeit nicht unterstützt.
    - **cli**, um die Anmeldeinformationen aus einer authentifizierten Azure CLI zu verwenden.
    - **interactive,** um den interaktiven Geräteautorisierungsablauf mithilfe eines [einmaligen Gerätecodes](#authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook) zu verwenden.

    > [!TIP]
    > In den meisten Fällen wird empfohlen, mehrere Methoden auszuwählen, z. B. sowohl **cli** als auch **interactive**. Die Azure-Authentifizierung versucht jede der konfigurierten Methoden in der oben aufgeführten Reihenfolge anzuwenden, bis eine erfolgreich ist.
    >

1. Wählen Sie **Speichern** und dann **Einstellungen speichern** aus.

Beispiel:

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="Ein Screenshot, der die Einstellungen, die für die Azure Government Cloud definiert sind, zeigt":::.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die Grundlagen der Verwendung von MSTICPy mit Jupyter-Notebooks in Microsoft Sentinel beschrieben. Weitere Informationen finden Sie unter [Erweiterte Konfigurationen für Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebooks-msticpy-advanced.md).

Sie können auch andere Notebooks ausprobieren, die im [GitHub-Repository von Microsoft Sentinel-Notebooks](https://github.com/Azure/Azure-Sentinel-Notebooks) gespeichert sind, wie z. B.:

- [Eine Tour durch die Cybersec-Funktionen](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [Beispiele für Machine Learning Beispiele](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- Die Notebooks der [Entity Explorer-Reihe](https://github.com/Azure/Azure-Sentinel-Notebooks/), die einen tiefen Drilldown in Details zu einem Hostkonto, IP-Adresse und anderen Entitäten ermöglichen.

> [!TIP]
> Wenn Sie das in diesem Tutorial beschriebene Notebook in einer anderen Jupyter-Umgebung verwenden, können Sie einen beliebigen Kernel, der Python 3.6 oder höher unterstützt, verwenden.
>
> Um MSTICPy-Notebooks außerhalb von Microsoft Sentinel und Azure Machine Learning (ML) zu verwenden, müssen Sie auch Ihre Python-Umgebung konfigurieren. Installieren Sie Python 3.6 oder höher mit der Anaconda-Distribution, die viele der erforderlichen Pakete enthält.
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>Weitere Informationen zu MSTICPy und Notebooks

In der folgenden Tabelle sind weitere Verweise mit Informationen über MSTICPy, Microsoft Sentinel und Jupyter-Notebooks aufgeführt.

|Subject  |Weitere Verweise  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy-Paketkonfiguration](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy-Einstellungs-Editor](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Konfigurieren Ihrer Notebook-Umgebung](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [MPSettingsEditor Notebook](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Hinweis**: Das `Azure-Sentinel-Notebooks`-GitHub-Repository enthält auch eine *Msticpyconfig.yaml*-Vorlagendatei mit auskommentierten Abschnitten, die Ihnen helfen könnten, die Einstellungen zu verstehen.      |
|**Microsoft Sentinel und Jupyter Notebooks**     |      - [Jupyter Notebooks: Eine Einführung](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy-Dokumentation](https://msticpy.readthedocs.io/)<br>    - [Dokumentation zu Microsoft Sentinel-Notebooks](notebooks.md)<br>    - [Das Infosec-Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Exemplarische Vorgehensweise zum Linux-Host-Explorer-Notebook](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Gründe für die Verwendung von Jupyter für die Sicherheitsuntersuchungen](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Sicherheitsuntersuchung mit Microsoft Sentinel und Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas-Dokumentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh-Dokumentation](https://docs.bokeh.org/en/latest/)       |
|     |         |
