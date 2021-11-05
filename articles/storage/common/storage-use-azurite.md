---
title: Verwenden des Azurite-Emulators für die lokale Azure Storage-Entwicklung
description: Der Azurite-Open-Source-Emulator bietet eine kostenlose lokale Umgebung zum Testen Ihrer Azure Storage-Anwendungen.
author: normesta
ms.author: normesta
ms.date: 08/02/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 567b54aae5ac2ac1defeedb390ee9d5c04f5f6bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013515"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>Verwenden des Azurite-Emulators für die lokale Azure Storage-Entwicklung

Der Azurite-Open-Source-Emulator bietet eine kostenlose lokale Umgebung zum Testen Ihrer Blob-, Warteschlangenspeicher- und Tabellenspeicheranwendungen in Azure. Wenn Sie mit der Funktion Ihrer Anwendung auf lokaler Ebene zufrieden sind, können Sie zur Verwendung eines Azure Storage-Kontos in der Cloud übergehen. Der Emulator bietet plattformübergreifende Unterstützung unter Windows, Linux und macOS.

Azurite ist die Speicheremulatorplattform der Zukunft. Azurite ersetzt den [Azure-Speicheremulator](storage-use-emulator.md). Azurite wird weiterhin aktualisiert, um die neuesten Versionen der Azure Storage-APIs zu unterstützen.

Es gibt verschiedene Möglichkeiten, Azurite auf Ihrem lokalen System zu installieren und auszuführen. Wählen Sie eine dieser Registerkarten aus.

## <a name="install-and-run-azurite"></a>Installieren und Ausführen von Azurite

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!TIP]
> Azurite ist in [Visual Studio 2022-Preview](https://visualstudio.microsoft.com/vs/preview/) automatisch verfügbar. Wenn Sie also die Vorschauversion installieren, müssen Sie Azurite nicht manuell installieren. 

Bei den Anweisungen in diesem Abschnitt wird davon ausgegangen, dass Sie Visual Studio 2022-Preview noch nicht installiert haben.

Erstellen Sie in Visual Studio ein Azure-Projekt, z. B. ein **Azure Functions**-Projekt.

![Neues Azure-Funktionsprojekt](media/storage-use-azurite/visual-studio-azure-function-project.png)

Ausgehend davon, dass Sie ein **Azure Functions**-Projekt erstellen, müssen Sie **HTTP-Trigger** auswählen. Wählen Sie dann in der Dropdownliste **Autorisierungsstufe** die Option **Anonym** aus.

![Funktionsprojekteinstellungen](media/storage-use-azurite/visual-studio-azure-function-project-settings.png)

Installieren Sie [Node.js-Version 8.0 oder höher](https://nodejs.org). Node Package Manager (npm) ist das Paketverwaltungstool, das in jeder Installation von Node.js enthalten ist. Führen Sie nach der Installation von Node.js den folgenden `npm`-Befehl aus, um Azurite zu installieren.

```console
npm install -g azurite
```

Starten Sie Azurite in der Befehlszeile mit dem folgenden Befehl:

```console
azurite
```

Daraufhin werden in der Konsole ähnliche Ausgabeinformationen wie folgende angezeigt.

![Befehlszeilenausgabe](media/storage-use-azurite/azurite-command-line-output.png)

Wechseln Sie zur [Release-Buildkonfiguration](/visualstudio/debugger/how-to-set-debug-and-release-configurations#change-the-build-configuration), und führen Sie dann das Projekt aus.

> [!NOTE]
> Wenn Sie das Projekt mit der Debug-Buildkonfiguration starten, erhalten Sie möglicherweise einen Fehler. Das liegt daran, Visual Studio versucht, den Legacyspeicheremulator zu starten, der in Visual Studio integriert ist. Jeder Versuch, den Legacyemulator zu starten, wird blockiert, da Azurite die Lauschports verwendet, die vom Legacyspeicheremulator benötigt werden.

Die folgende Abbildung zeigt die Befehlszeilenausgabe, die angezeigt wird, wenn Sie ein Azure-Funktionsprojekt ausführen.

![Befehlszeilenausgabe nach Ausführen des Projekts](media/storage-use-azurite/azurite-command-line-output-2.png)

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Wählen Sie in Visual Studio Code den Bereich **EXTENSIONS** aus, und suchen Sie dann unter **EXTENSIONS:MARKETPLACE** nach *Azurite*.

![Visual Studio Code-Marketplace für Erweiterungen](media/storage-use-azurite/azurite-vs-code-extension.png)

Sie können auch in Ihrem Browser zum [Visual Studio Code-Marketplace für Erweiterungen](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) navigieren. Wählen Sie die Schaltfläche **Installieren** aus, um Visual Studio Code zu öffnen, und wechseln Sie dann direkt zur Azurite-Erweiterungsseite.

Die Erweiterung unterstützt die folgenden Visual Studio Code-Befehle. Drücken Sie in Visual Studio Code F1, um die Befehlspalette zu öffnen.

   - **Azurite: Bereinigen** – dauerhafte Daten aller Azurite-Dienste zurücksetzen
   - **Azurite: Blob-Dienst bereinigen** – Blob-Dienst bereinigen
   - **Azurite: Warteschlangendienst bereinigen** – Warteschlangendienst bereinigen
   - **Azurite: Tabellendienst bereinigen** – Tabellendienst bereinigen
   - **Azurite: Schließen** – alle Azurite-Dienste schließen
   - **Azurite: Blob-Dienst schließen** – Blob-Dienst schließen
   - **Azurite: Warteschlangendienst schließen** – Warteschlangendienst schließen
   - **Azurite: Tabellendienst schließen** – Tabellendienst schließen
   - **Azurite: Starten** – alle Azurite-Dienste starten
   - **Azurite: Blob-Dienst starten** – Blob-Dienst starten
   - **Azurite: Warteschlangendienst starten** – Warteschlangendienst starten
   - **Azurite: Tabellendienst starten** – Tabellendienst starten

Um Azurite in Visual Studio Code zu konfigurieren, wählen Sie den Erweiterungsbereich aus. Wählen Sie das Symbol **Verwalten** (Zahnrad) für **Azurite** aus. Wählen Sie **Erweiterungseinstellungen** aus.

![Konfigurieren der Erweiterungseinstellungen für Azurite](media/storage-use-azurite/azurite-configure-extension-settings.png)

Die folgenden Einstellungen werden unterstützt:

   - **Azurite: Blobhost** – der Lauschendpunkt des Blob-Diensts. Die Standardeinstellung ist 127.0.0.1.
   - **Azurite: Blobport** – der Lauschport des Blob-Diensts. Der Standardport ist 10000.
   - **Azurite: Cert** – Pfad zu einem lokal vertrauenswürdigen PEM- oder PFX-Zertifikatdateipfad zum Aktivieren des HTTPS-Modus.
   - **Azurite: Debuggen** – Ausgabe des Debugprotokolls an den Azurite-Kanal. Der Standardwert ist **false**.
   - **Azurite: Key** – Pfad zu einer lokal vertrauenswürdigen PEM-Schlüsseldatei, die erforderlich ist, wenn **Azurite: Cert** auf eine PEM-Datei verweist.
   - **Azurite: Speicherort** – der Speicherpfad des Arbeitsbereichs. Der Standard ist der Arbeitsordner für Visual Studio Code.
   - **Azurite: Loose** – aktiviert den Loose-Modus, der nicht unterstützte Header und Parameter ignoriert.
   - **Azurite: Oauth** – optionale OAuth-Ebene.
   - **Azurite: Pwd** – Kennwort für die PFX-Datei. Erforderlich, wenn **Azurite: Cert** auf eine PFX-Datei verweist.
   - **Azurite: Warteschlangenhost** – der Lauschendpunkt des Warteschlangendiensts. Die Standardeinstellung ist 127.0.0.1.
   - **Azurite: Warteschlangenport** – der Lauschport des Warteschlangendiensts. Der Standardport ist 10001.
   - **Azurite: Lautlos** – Zugriffsprotokoll im Lautlosmodus deaktivieren. Der Standardwert ist **false**.
   - **Azurite: API-Versionsüberprüfung überspringen** – Versionsüberprüfung der angeforderten API überspringen. Der Standardwert ist **false**.
   - **Azurite: Tabellenhost** – der Lauschendpunkt des Tabellendiensts, die Standardeinstellung ist 127.0.0.1.
   - **Azurite: Tabellenport** – der Lauschport des Tabellendiensts, standardmäßig 10002.

### <a name="npm"></a>[npm](#tab/npm)

Diese Installationsmethode setzt voraus, dass Sie [mindestens Version 8.0 von Node.js](https://nodejs.org) installiert haben. Node Package Manager (npm) ist das Paketverwaltungstool, das in jeder Installation von Node.js enthalten ist. Führen Sie nach der Installation von Node.js den folgenden `npm`-Befehl aus, um Azurite zu installieren.

```console
npm install -g azurite
```

Nachdem Sie Azurite installiert haben, finden Sie weitere Informationen unter [Ausführen von Azurite über die Befehlszeile](#run-azurite-from-a-command-line).

### <a name="docker-hub"></a>[Docker Hub](#tab/docker-hub)

Verwenden Sie [DockerHub](https://hub.docker.com/), um das [neueste Azurite-Image](https://hub.docker.com/_/microsoft-azure-storage-azurite) mit dem folgenden Befehl zu laden:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Ausführen des Azurite-Docker-Images:**

Der folgende Befehl führt das Azurite-Docker-Image aus. Der Parameter `-p 10000:10000` leitet Anforderungen von Port 10000 des Hostcomputers an die Docker-Instanz weiter.

```console
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 \
    mcr.microsoft.com/azure-storage/azurite
```

**Angeben des Speicherorts für den Arbeitsbereich:**

Im folgenden Beispiel ist im Parameter `-v c:/azurite:/data` der persistente Datenspeicherort *c:/azurite* für Azurite angegeben. Vor dem Ausführen des Docker-Befehls muss das Verzeichnis *c:/azurite* erstellt werden.

```console
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Ausführen nur des Blob-Diensts**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Weitere Informationen zum Konfigurieren von Azurite beim Start finden Sie unter [Befehlszeilenoptionen](#command-line-options).

### <a name="github"></a>[GitHub](#tab/github)

Diese Installationsmethode setzt voraus, dass Sie [Git](https://git-scm.com/) installiert haben. Klonen Sie das [GitHub-Repository](https://github.com/azure/azurite) für das Azurite-Projekt mit dem folgenden Konsolenbefehl.

```console
git clone https://github.com/Azure/Azurite.git
```

Nachdem Sie den Quellcode geklont haben, führen Sie folgende Befehle im Stammverzeichnis des geklonten Repositorys aus, um Azurite zu erstellen und zu installieren.

```console
npm install
npm run build
npm install -g
```

Nachdem Sie Azurite installiert und erstellt haben, finden Sie weitere Informationen unter [Ausführen von Azurite über die Befehlszeile](#run-azurite-from-a-command-line).

---

## <a name="run-azurite-from-a-command-line"></a>Ausführen von Azurite über die Befehlszeile

> [!NOTE]
> Azurite kann nicht über die Befehlszeile ausgeführt werden, wenn Sie nur die Visual Studio Code-Erweiterung installiert haben. Verwenden Sie stattdessen die Visual Studio Code-Befehlspalette.

Um sofort mit der Befehlszeile zu beginnen, erstellen Sie das Verzeichnis *C:\azurite*, und starten Sie dann Azurite mit dem folgenden Befehl:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Dieser Befehl weist Azurite an, alle Daten im Verzeichnis *c:\azurite* zu speichern. Wenn die Option `--location` ausgelassen wird, wird das aktuelle Arbeitsverzeichnis verwendet.

## <a name="command-line-options"></a>Befehlszeilenoptionen

Dieser Abschnitt beschreibt die Befehlszeilenoptionen, die zum Starten von Azurite verfügbar sind.

### <a name="help"></a>Hilfe

**Optional** – Abrufen von Befehlszeilenhilfe mit dem Switch `-h` oder `--help`.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Bloblauschhost

**Optional** – Standardmäßig lauscht Azurite an 127.0.0.1 als lokaler Server. Verwenden Sie die Option `--blobHost`, um die Adresse entsprechend Ihren Anforderungen festzulegen.

Nur Anforderungen auf dem lokalen Computer annehmen:

```console
azurite --blobHost 127.0.0.1
```

Remoteanforderungen annehmen:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Wenn Sie Remoteanforderungen zulassen, wird Ihr System eventuell anfälliger für externe Bedrohungen.

### <a name="blob-listening-port-configuration"></a>Konfiguration des Bloblauschports

**Optional** – Standardmäßig lauscht Azurite an Port 10000 auf den Blob-Dienst. Verwenden Sie die Option `--blobPort`, um den erforderlichen Lauschport anzugeben.

> [!NOTE]
> Nachdem Sie einen benutzerdefinierten Port verwendet haben, müssen Sie die Verbindungszeichenfolge oder die entsprechende Konfiguration in Ihren Azure Storage-Tools oder -SDKs aktualisieren.

Lauschport für den Blob-Dienst anpassen:

```console
azurite --blobPort 8888
```

Automatische Auswahl eines verfügbaren Ports durch das System:

```console
azurite --blobPort 0
```

Der verwendete Port wird beim Start von Azurite angezeigt.

### <a name="queue-listening-host"></a>Warteschlangen-Lauschhost

**Optional** – Standardmäßig lauscht Azurite an 127.0.0.1 als lokaler Server. Verwenden Sie die Option `--queueHost`, um die Adresse entsprechend Ihren Anforderungen festzulegen.

Nur Anforderungen auf dem lokalen Computer annehmen:

```console
azurite --queueHost 127.0.0.1
```

Remoteanforderungen annehmen:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Wenn Sie Remoteanforderungen zulassen, wird Ihr System eventuell anfälliger für externe Bedrohungen.

### <a name="queue-listening-port-configuration"></a>Konfiguration des Warteschlangen-Lauschports

**Optional** – Standardmäßig lauscht Azurite an Port 10001 auf den Warteschlangendienst. Verwenden Sie die Option `--queuePort`, um den erforderlichen Lauschport anzugeben.

> [!NOTE]
> Nachdem Sie einen benutzerdefinierten Port verwendet haben, müssen Sie die Verbindungszeichenfolge oder die entsprechende Konfiguration in Ihren Azure Storage-Tools oder -SDKs aktualisieren.

Lauschport für den Warteschlangendienst anpassen:

```console
azurite --queuePort 8888
```

Automatische Auswahl eines verfügbaren Ports durch das System:

```console
azurite --queuePort 0
```

Der verwendete Port wird beim Start von Azurite angezeigt.

### <a name="table-listening-host"></a>Tabellenlauschhost

**Optional** – Standardmäßig lauscht Azurite an 127.0.0.1 als lokaler Server. Verwenden Sie die Option `--tableHost`, um die Adresse entsprechend Ihren Anforderungen festzulegen.

Nur Anforderungen auf dem lokalen Computer annehmen:

```console
azurite --tableHost 127.0.0.1
```

Remoteanforderungen annehmen:

```console
azurite --tableHost 0.0.0.0
```

> [!CAUTION]
> Wenn Sie Remoteanforderungen zulassen, wird Ihr System eventuell anfälliger für externe Bedrohungen.

### <a name="table-listening-port-configuration"></a>Konfiguration des Tabellenlauschports

**Optional** – Standardmäßig lauscht Azurite an Port 10002 für den Tabellendienst. Verwenden Sie die Option `--tablePort`, um den erforderlichen Lauschport anzugeben.

> [!NOTE]
> Nachdem Sie einen benutzerdefinierten Port verwendet haben, müssen Sie die Verbindungszeichenfolge oder die entsprechende Konfiguration in Ihren Azure Storage-Tools oder -SDKs aktualisieren.

Lauschport für den Tabellendienst anpassen:

```console
azurite --tablePort 11111
```

Automatische Auswahl eines verfügbaren Ports durch das System:

```console
azurite --tablePort 0
```

Der verwendete Port wird beim Start von Azurite angezeigt.

### <a name="workspace-path"></a>Arbeitsbereichspfad

**Optional** – Azurite speichert während der Ausführung Daten auf dem lokalen Datenträger. Verwenden Sie die Option `-l` oder `--location`, um einen Pfad als Speicherort für den Arbeitsbereich anzugeben. Standardmäßig wird das Arbeitsverzeichnis des aktuellen Prozesses verwendet. Beachten Sie den Kleinbuchstaben „l“.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Zugriffsprotokoll

**Optional** – Standardmäßig wird das Zugriffsprotokoll im Konsolenfenster angezeigt. Deaktivieren Sie die Anzeige des Zugriffsprotokolls mit der Option `-s` oder `--silent`.

```console
azurite -s
azurite --silent
```


### <a name="debug-log"></a>Debugprotokoll

**Optional** – Das Debugprotokoll enthält ausführliche Informationen zu jeder Anforderung und jeder Ausnahmestapelüberwachung. Aktivieren Sie das Debugprotokoll, indem Sie für die Option `-d` oder `--debug` einen gültigen lokalen Dateipfad angeben.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Loose-Modus

**Optional** – Standardmäßig wendet Azurite den Strict-Modus an, um nicht unterstützte Anforderungsheader und Parameter zu blockieren. Der Strict-Modus kann mithilfe der Option `-L` oder `--loose` deaktiviert werden. Beachten Sie den Großbuchstaben „L“.

```console
azurite -L
azurite --loose
```


### <a name="version"></a>Version

**Optional**: Anzeigen der installierten Azurite-Versionsnummer mit der Option `-v` oder `--version`.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Zertifikatkonfiguration (HTTPS)

**Optional** – Standardmäßig verwendet Azurite das HTTP-Protokoll. Aktivieren Sie den HTTPS-Modus, indem Sie einen Pfad zu einer PEM- (Privacy Enhanced Mail) oder [PFX-Zertifikatdatei (Personal Information Exchange)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) für den Switch `--cert` angeben.

Wenn `--cert` für eine PEM-Datei bereitgestellt wird, müssen Sie einen entsprechenden Switch `--key` angeben.

```console
azurite --cert path/server.pem --key path/key.pem
```

Wenn `--cert` für eine PFX-Datei bereitgestellt wird, müssen Sie einen entsprechenden Switch `--pwd` angeben.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Ausführliche Informationen zum Erstellen von PEM- und PFX-Dateien finden Sie unter [HTTPS-Setup](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>OAuth-Konfiguration

**Optional** – Aktivieren von OAuth-Authentifizierung für Azurite mithilfe der Option `--oauth`.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> Für OAuth ist ein HTTPS-Endpunkt erforderlich. Stellen Sie sicher, dass HTTPS aktiviert ist, indem Sie den Switch `--cert` zusammen mit dem Switch `--oauth` angeben.

Azurite unterstützt die Standardauthentifizierung durch Angabe des `basic`-Parameters für den Switch `--oauth`. Azurite führt Standardauthentifizierung aus, z. B. Überprüfen des eingehenden Bearertokens, Überprüfen des Ausstellers, der Zielgruppe und des Ablaufs. Azurite überprüft nicht die Tokensignatur oder -berechtigungen.

### <a name="skip-api-version-check"></a>Überspringen der API-Versionsüberprüfung

**Optional** – Beim Starten überprüft Azurite, ob die angeforderte API-Version gültig ist. Mit dem folgenden Befehl wird die Überprüfung der API-Version übersprungen:

```console
azurite --skipApiVersionCheck
```

## <a name="authorization-for-tools-and-sdks"></a>Autorisierung für Tools und SDKs

Stellen Sie über Azure Storage SDKs oder Tools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) mithilfe einer beliebigen Authentifizierungsstrategie eine Verbindung mit Azurite her. Eine Authentifizierung ist erforderlich. Azurite unterstützt die Autorisierung mit OAuth, gemeinsamem Schlüssel und Shared Access Signatures (SAS). Außerdem unterstützt Azurite anonymen Zugriff auf öffentliche Container.

Wenn Sie die Azure SDKs verwenden, starten Sie Azurite mit den `--oauth basic and --cert --key/--pwd`-Optionen.

### <a name="well-known-storage-account-and-key"></a>Bekannte Speicherkonten und Schlüssel

Azurite akzeptiert dasselbe bekannte Konto und den Schlüssel, die auch vom früheren Azure Storage-Emulator verwendet werden.

- Kontoname: `devstoreaccount1`
- Kontoschlüssel: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Benutzerdefinierte Speicherkonten und Schlüssel

Azurite unterstützt benutzerdefinierte Speicherkontonamen und Schlüssel. Hierzu muss die Umgebungsvariable `AZURITE_ACCOUNTS` im folgenden Format festgelegt werden: `account1:key1[:key2];account2:key1[:key2];...`.

Verwenden Sie beispielsweise ein benutzerdefiniertes Speicherkonto mit einem einzelnen Schlüssel:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

Oder verwenden Sie mehrere Speicherkonten mit jeweils zwei Schlüsseln:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Benutzerdefinierte Kontonamen und Schlüssel aus der Umgebungsvariablen werden von Azurite standardmäßig im Minutentakt aktualisiert. Mit diesem Feature können Sie den Kontoschlüssel dynamisch rotieren oder neue Speicherkonten hinzufügen, ohne Azurite neu zu starten.

> [!NOTE]
> Wenn Sie benutzerdefinierte Speicherkonten festlegen, wird das Standardspeicherkonto `devstoreaccount1` deaktiviert.

### <a name="connection-strings"></a>Verbindungszeichenfolgen

Am einfachsten können Sie über Ihre Anwendung eine Verbindung mit Azurite herstellen, indem Sie in der Konfigurationsdatei der Anwendung eine Verbindungszeichenfolge konfigurieren, die auf die Verknüpfung *UseDevelopmentStorage=true* verweist. Eine Verbindungszeichenfolge in der Datei *app.config* kann beispielsweise wie folgt aussehen:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP-Verbindungszeichenfolgen

Sie können die folgenden Verbindungszeichenfolgen an das [Azure SDK](https://aka.ms/azsdk) oder Tools übergeben, etwa an die Azure CLI 2.0 oder Storage-Explorer.

Die vollständige Verbindungszeichenfolge lautet:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Zum Herstellen einer Verbindung nur mit dem Blob-Dienst lautet die Verbindungszeichenfolge wie folgt:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Zum Herstellen einer Verbindung nur mit dem Warteschlangendienst lautet die Verbindungszeichenfolge wie folgt:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS-Verbindungszeichenfolgen

Die vollständige HTTPS-Verbindungszeichenfolge lautet:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Um nur den Blob-Dienst zu verwenden, lautet die HTTPS-Verbindungszeichenfolge wie folgt:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Um nur den Warteschlangendienst zu verwenden, lautet die HTTPS-Verbindungszeichenfolge wie folgt:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Wenn Sie `dotnet dev-certs` verwendet haben, um Ihr selbstsigniertes Zertifikat zu generieren, verwenden Sie die folgende Verbindungszeichenfolge.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Aktualisieren Sie die Verbindungszeichenfolge, wenn Sie [benutzerdefinierte Speicherkonten und Schlüssel](#custom-storage-accounts-and-keys) verwenden.

Weitere Informationen hierzu finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Azure SDKs

Verwenden Sie die OAuth-und HTTPS-Optionen, um Azurite mit den [Azure SDKs](https://aka.ms/azsdk) zu verwenden:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Anschließend können Sie einen BlobContainerClient, BlobServiceClient oder BlobClient instanziieren.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

Sie können auch einen QueueClient oder QueueServiceClient instanziieren.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage-Explorer

Sie können Storage-Explorer zum Anzeigen der in Azurite gespeicherten Daten verwenden.

#### <a name="connect-to-azurite-using-http"></a>Herstellen einer Verbindung mit Azurite mithilfe von HTTP

Stellen Sie in Storage-Explorer eine Verbindung mit Azurite her, indem Sie die folgenden Schritte ausführen:

 1. Wählen Sie das Symbol **Konten verwalten** aus.
 1. Wählen Sie **Konto hinzufügen** aus.
 1. Wählen Sie **An einen lokalen Emulator anfügen** aus.
 1. Wählen Sie **Weiter** aus.
 1. Bearbeiten Sie das Feld **Anzeigename**, indem Sie einen Namen Ihrer Wahl eingeben.
 1. Wählen Sie **Weiter** erneut aus.
 1. Wählen Sie **Verbinden** aus.

#### <a name="connect-to-azurite-using-https"></a>Herstellen einer Verbindung mit Azurite mithilfe von HTTPS

Standardmäßig öffnet Storage-Explorer keinen HTTPS-Endpunkt, der ein selbstsigniertes Zertifikat verwendet. Wenn Sie Azurite mit HTTPS ausführen, verwenden Sie wahrscheinlich ein selbstsigniertes Zertifikat. Importieren Sie in Storage-Explorer SSL-Zertifikate über das Dialogfeld **Bearbeiten** -> **SSL-Zertifikate** -> **Zertifikate importieren**.

##### <a name="import-certificate-to-storage-explorer"></a>Importieren des Zertifikats in Storage-Explorer

1. Suchen Sie auf dem lokalen Computer nach dem Zertifikat.
1. Navigieren Sie in Storage-Explorer zu **Bearbeiten** -> **SSL-Zertifikate** -> **Zertifikate importieren**, und importieren Sie das Zertifikat.

Wenn Sie kein Zertifikat importieren, erhalten Sie eine Fehlermeldung:

`unable to verify the first certificate` oder `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Hinzufügen von Azurite über eine HTTPS-Verbindungszeichenfolge

Führen Sie die folgenden Schritte aus, um Storage-Explorer Azurite HTTPS hinzuzufügen:

1. Wählen Sie **Explorer umschalten** aus.
1. Wählen Sie **Lokal & angefügt** aus.
1. Klicken Sie mit der rechten Maustaste auf **Speicherkonten**, und klicken Sie dann auf **Verbindung mit Azure Storage herstellen**.
1. Wählen Sie **Verbindungszeichenfolge verwenden** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie einen Wert in das Feld **Anzeigename** ein.
1. Geben Sie die [HTTPS-Verbindungszeichenfolge](#https-connection-strings) aus dem vorherigen Abschnitt dieses Dokuments ein.
1. Wählen Sie **Weiter** aus.
1. Wählen Sie **Verbinden** aus.

## <a name="workspace-structure"></a>Arbeitsbereichstruktur

Die folgenden Dateien und Ordner können beim Initialisieren von Azurite am Arbeitsbereich-Speicherort erstellt werden.

- `__blobstorage__`: Verzeichnis, das persistent gespeicherte Daten des Blob-Diensts von Azurite enthält.
- `__queuestorage__`: Verzeichnis, das persistent gespeicherte Daten des Warteschlangendiensts von Azurite enthält.
- `__azurite_db_blob__.json`: Metadatendatei des Blob-Diensts von Azurite.
- `__azurite_db_blob_extent__.json`: Erweiterungsmetadatendatei des Blob-Diensts von Azurite.
- `__azurite_db_queue__.json`: Metadatendatei des Warteschlangendiensts von Azurite.
- `__azurite_db_queue_extent__.json`: Erweiterungsmetadatendatei des Warteschlangendiensts von Azurite.

Wenn Sie Azurite bereinigen möchten, löschen Sie die oben aufgeführten Dateien und Ordner, und starten Sie den Emulator neu.

## <a name="differences-between-azurite-and-azure-storage"></a>Unterschiede zwischen Azurite und Azure Storage

Es gibt funktionale Unterschiede zwischen einer lokalen Azurite-Instanz und einem Azure Storage-Konto in der Cloud.

### <a name="endpoint-and-connection-url"></a>Endpunkt und Verbindungs-URL

Die Dienstendpunkte für Azurite unterscheiden sich von denen eines Azure Storage-Kontos. Der lokale Computer führt keine Auflösung von Domänennamen durch, sodass Azurite-Endpunkte lokale Adressen sein müssen.

Wenn Sie eine Ressource in einem Azure Storage-Konto ansprechen, ist der Kontoname Teil des URI-Hostnamens. Die angesprochene Ressource ist Teil des URI-Pfades:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Der folgende URI ist eine gültige Adresse für ein Blob in einem Azure Storage-Konto:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Da der lokale Computer keine Domänennamen auflöst, ist der Kontoname Teil des URI-Pfads und nicht des Hostnamens. Verwenden Sie für Ressourcen in Azurite das folgende URI-Format:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Die folgende Adresse kann beispielsweise für den Zugriff auf ein Blob in Azurite verwendet werden:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Skalierung und Leistung

Azurite unterstützt keine große Anzahl verbundener Clients. Es gibt keine Leistungsgarantie. Azurite ist für Entwicklungs- und Testzwecke ausgelegt.

### <a name="error-handling"></a>Fehlerbehandlung

Azurite ist mit der Fehlerbehandlungslogik von Azure Storage abgestimmt, es gibt jedoch Unterschiede. Beispielsweise können Fehlermeldungen bei übereinstimmenden Fehlerstatuscodes unterschiedlich ausfallen.

### <a name="ra-grs"></a>RA-GRS

Azurite unterstützt die georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Greifen Sie für Speicherressourcen auf den sekundären Speicherort zu, indem Sie `-secondary` an den Kontonamen anfügen. Beispielsweise kann die folgende Adresse mithilfe des schreibgeschützten sekundären Speicherorts in Azurite für den Zugriff auf ein Blob verwendet werden:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>Tabellenunterstützung

Die Unterstützung für Tabellen in Azurite befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie im [Azurite V3 Table](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table)-Projekt.

Für die Unterstützung langlebiger Funktionen sind Tabellen erforderlich.

> [!IMPORTANT]
>
> Die Azurite-Unterstützung für Table Storage befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="azurite-is-open-source"></a>Azurite ist Open Source.

Wir freuen uns über Beiträge und Vorschläge für Azurite. Auf der Seite des [GitHub-Projekts](https://github.com/Azure/Azurite/projects) und der [GitHub-Problemseite](https://github.com/Azure/Azurite/issues) für Azurite finden Sie Meilensteine und Arbeitselemente, die wir für zukünftige Features und Fehlerbehebungen berücksichtigen. Außerdem werden in GitHub detaillierte Arbeitselemente nachverfolgt.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Verwenden des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md) wird der veraltete Azure Storage-Emulator beschrieben, der durch Azurite ersetzt wird.
- Unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](storage-configure-connection-string.md) finden Sie eine Erläuterung des Aufbaus einer gültigen Azure Storage-Verbindungszeichenfolge.
