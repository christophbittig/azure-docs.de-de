---
title: Netzwerkverbindungen in Azure Storage-Explorer | Microsoft-Dokumentation
description: Dokumentation zum Herstellen einer Verbindung mit Ihrem Netzwerk in Azure Storage-Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: a4afdc8897e15fcb563812bda9da378c66ca4bd9
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432426"
---
# <a name="network-connections-in-storage-explorer"></a>Netzwerkverbindungen in Storage-Explorer

Wenn Sie keine Verbindung mit einem lokalen Emulator herstellen, verwendet Storage-Explorer Ihr Netzwerk, um Anforderungen an Ihre Speicherressourcen und andere Azure- und Microsoft-Dienste zu richten.

## <a name="hostnames-accessed-by-storage-explorer"></a>Hostnamen, auf die der Storage-Explorer zugreift

Storage-Explorer richtet Anforderungen an verschiedene Endpunkte, während sie verwendet werden. Die folgende Liste enthält allgemeine Hostnamen, an die Storage-Explorer Anforderungen richtet:
- ARM-Endpunkte:
    - `management.azure.com` (Azure weltweit)
    - `management.chinacloudapi.cn` (Azure China)
    - `management.microsoftazure.de` (Azure Deutschland)
    - `management.usgovcloudapi.net` (Azure US Government)
- Anmeldeendpunkte:
    - `login.microsoftonline.com` (Azure weltweit)
    - `login.chinacloudapi.cn` (Azure China)
    - `login.microsoftonline.de` (Azure Deutschland)
    - `login.microsoftonline.us` (Azure US Government)
- Graph-Endpunkte:
    - `graph.windows.net` (Azure weltweit)
    - `graph.chinacloudapi.cn` (Azure China)
    - `graph.cloudapi.de` (Azure Deutschland)
    - `graph.windows.net` (Azure US Government)
- Azure Storage-Endpunkte:
    - `(blob|file|queue|table|dfs).core.windows.net` (Azure weltweit)
    - `(blob|file|queue|table|dfs).core.chinacloudapi.net` (Azure China)
    - `(blob|file|queue|table|dfs).core.cloudapi.net` (Azure Deutschland)
    - `(blob|file|queue|table|dfs).core.cloudapi.net` (Azure US Government)
- Storage-Explorer-Aktualisierung: `storageexplorerpublish.blob.core.windows.net` 
- Microsoft-Linkweiterleitung:
    - `aka.ms`
    - `go.microsoft.com`
- Alle benutzerdefinierten Domänen, privaten Links oder für Azure Stack-Instanzen spezifische Endpunkte, hinter denen sich Ihre Ressourcen befinden
- Hostnamen des Remoteemulators

## <a name="proxy-sources"></a>Proxyquellen

Storage-Explorer bietet mehrere Optionen dazu, wie/wo die Informationen, die zum Herstellen einer Verbindung mit Ihrem Proxy benötigt werden, abgerufen werden können. Um zu ändern, welche Option verwendet wird, wechseln Sie zu **Einstellungen** (Zahnradsymbol auf der linken vertikalen Symbolleiste) > **Anwendung** > **Proxy**. Sobald Sie sich im Proxyabschnitt der Einstellungen befinden, können Sie auswählen, wie/wo der Storage-Explorer Ihre Proxyeinstellungen abrufen soll:
- Proxy nicht verwenden
- Verwenden von Umgebungsvariablen
- Verwenden von App-Proxyeinstellungen
- Verwenden des Systemproxys (Vorschau)

### <a name="do-not-use-proxy"></a>Proxy nicht verwenden

Wenn diese Option ausgewählt ist, unternimmt der Storage-Explorer keinen Versuch, eine Verbindung mit einem Proxy herzustellen. Den Proxy nicht zu verwenden, ist die Standardoption.

### <a name="use-environment-variables"></a>Verwenden von Umgebungsvariablen

Wenn diese Option ausgewählt ist, sucht der Storage-Explorer nach Proxyinformationen aus bestimmten Umgebungsvariablen. Es handelt sich um folgende Variablen:
- `HTTP_PROXY`
- `HTTPS_PROXY`

Wenn beide Variablen definiert sind, ruft der Storage-Explorer Proxyinformationen von `HTTPS_PROXY` ab.

Der Wert dieser Umgebungsvariablen muss eine URL dieses Formats sein:

`(http|https)://(username:password@)<hostname>:<port>`

Nur das Protokoll (`http|https`) und der Hostname sind erforderlich. Wenn Sie über einen Benutzernamen verfügen, müssen Sie kein Kennwort angeben.

### <a name="use-app-proxy-settings"></a>Verwenden von App-Proxyeinstellungen

Wenn diese Option ausgewählt ist, verwendet der Storage-Explorer die Proxyeinstellungen in der App. Diese Einstellungen umfassen Folgendes:
- Protocol
- Hostname
- Port
- Anmeldeinformationen verwenden/nicht verwenden
- Anmeldeinformationen

Alle Einstellungen, die keine Anmeldeinformationen sind, können auf folgende Arten verwaltet werden:
- **Einstellungen** (Zahnradsymbol auf der linken vertikalen Symbolleiste) > **Anwendung** > **Proxy** > **Anmeldeinformationen verwenden**.
- Das Dialogfeld „Proxyeinstellungen“ (**Bearbeiten** > **Proxy konfigurieren**).

Zum Festlegen von Anmeldeinformationen müssen Sie zum Dialogfeld „Proxyeinstellungen“ wechseln (**Bearbeiten** > **Proxy konfigurieren**).

### <a name="use-system-proxy-preview"></a>Verwenden des Systemproxys (Vorschau)

Wenn diese Option ausgewählt ist, verwendet der Storage-Explorer die Proxyeinstellungen in Ihrem Betriebssystem. Genauer gesagt führt dies dazu, dass Netzwerkaufrufe mithilfe des Chromium-Netzwerkstapels vorgenommen werden. Der Chromium-Netzwerkstapel ist wesentlich robuster als der NodeJS-Netzwerkstapel, der normalerweise vom Storage-Explorer verwendet wird. Dieser Ausschnitt aus der [Chromium-Dokumentation](https://www.chromium.org/developers/design-documents/network-settings) verschafft Ihnen einen Überblick:

> Der Chromium-Netzwerkstapel verwendet die Systemnetzwerkeinstellungen, damit Benutzer und Administratoren die Netzwerkeinstellungen aller Anwendungen problemlos steuern können. Zu den Netzwerkeinstellungen zählen:
> - Proxyeinstellungen
> - SSL/TLS-Einstellungen
> - Einstellungen zur Zertifikatsperrüberprüfung
> - Speicher für Zertifikat- und private Schlüssel

Wenn Ihr Proxyserver Anmeldeinformationen erfordert und diese Anmeldeinformationen nicht in Ihren Betriebssystemeinstellungen konfiguriert sind, müssen Sie die Verwendung Ihrer Anmeldeinformationen im Storage-Explorer aktivieren und sie festlegen. Zum Umschalten der Verwendung der Anmeldeinformationen können Sie folgende Möglichkeiten nutzen:
- **Einstellungen** (Zahnradsymbol auf der linken vertikalen Symbolleiste) > **Anwendung** > **Proxy** > **Anmeldeinformationen verwenden**.
- Das Dialogfeld „Proxyeinstellungen“ (**Bearbeiten** > **Proxy konfigurieren**).

Zum Festlegen von Anmeldeinformationen müssen Sie zum Dialogfeld „Proxyeinstellungen“ wechseln (**Bearbeiten** > **Proxy konfigurieren**).

Diese Option befindet sich in der Vorschau, da derzeit nicht alle Features den Systemproxy unterstützen. Eine vollständige Liste der Features finden Sie unter [Features, die den Systemproxy unterstützen](#features-that-support-system-proxy). Wenn der Systemproxy aktiviert ist, versuchen Features, die den Systemproxy nicht unterstützen, nicht, eine Verbindung mit einem Proxy herzustellen.

Wenn bei der Verwendung des Systemproxys mit einem unterstützten Feature ein Problem auftritt, [öffnen Sie ein Issue auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

## <a name="proxy-server-authentication"></a>Proxyserverauthentifizierung

Wenn Sie den Storage-Explorer mit Quellproxyeinstellungen aus **Umgebungsvariablen** oder **App-Proxyeinstellungen** konfiguriert haben, werden nur Proxyserver unterstützt, die die Standardauthentifizierung verwenden.

Wenn Sie den Storage-Explorer zur Verwendung des **Systemproxys** konfiguriert haben, werden Proxyserver unterstützt, die eine der folgenden Authentifizierungsmethoden verwenden:
- Einfach
- Digest
- NTLM
- Aushandeln

## <a name="which-proxy-source-should-i-choose"></a>Welche Proxyquelle sollte ich auswählen?

Wenn Sie nur [Features verwenden, die den Systemproxy unterstützen](#features-that-support-system-proxy), sollten Sie zunächst versuchen, den [**Systemproxy**](#use-system-proxy-preview) zu verwenden. Wenn bei der Verwendung des Systemproxys mit einem unterstützten Feature ein Problem auftritt, [öffnen Sie ein Issue auf GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues/new).

Wenn Sie Features verwenden, die keinen Systemproxy unterstützen, sind [**App-Einstellungen**](#use-app-proxy-settings) wahrscheinlich die nächstbeste Option. Die GUI-basierte Benutzeroberfläche zum Konfigurieren der Proxykonfiguration reduziert die Wahrscheinlichkeit, dass Ihre Proxyinformationen richtig eingegeben werden. Wenn Sie jedoch bereits Proxyumgebungsvariablen konfiguriert haben, ist es möglicherweise besser, [**Umgebungsvariablen**](#use-environment-variables) zu verwenden.

## <a name="azcopy-proxy-usage"></a>Verwendung des AzCopy-Proxys

Der Storage-Explorer verwendet AzCopy für die meisten Datenübertragungsvorgänge. AzCopy ist mit einem anderen Satz von Technologien als Storage-Explorer geschrieben und verfügt daher über einen etwas anderen Satz von Proxyfunktionen.

Wenn der Storage-Explorer dazu konfiguriert ist, **keinen Proxy** oder **einen Systemproxy** zu verwenden, wird AzCopy angewiesen, seine eigenen Funktionen zur automatischen Proxyerkennung zu verwenden, um zu bestimmen, ob und wie Anforderungen an einen Proxy gestellt werden sollen. Wenn Sie jedoch den Storage-Explorer mit Quellproxyeinstellungen aus **Umgebungsvariablen** oder **App-Proxyeinstellungen** konfiguriert haben, weist der Storage-Explorer AzCopy an, dieselben Proxyeinstellungen zu verwenden.

Wenn Sie möchten, dass AzCopy unter keinen Umständen den Proxy verwendet, können Sie die Proxyverwendung deaktivieren, indem Sie **Einstellungen** (Zahnradsymbol auf der linken vertikalen Symbolleiste) > **Übertragungen** > **AzCopy** > **AzCopy-Proxyverwendung deaktivieren** umschalten.

Derzeit unterstützt AzCopy nur Proxyserver, die die **Standardauthentifizierung** verwenden.

## <a name="ssl-certificates"></a>SSL-Zertifikate

Standardmäßig verwendet der Storage-Explorer den NodeJS-Netzwerkstapel. Zum Lieferumfang von NodeJS gehört eine vordefinierte Liste vertrauenswürdiger SSL-Zertifikate. Einige Netzwerktechnologien wie Proxyserver oder Antivirensoftware injizieren eigene SSL-Zertifikate in den Netzwerkdatenverkehr. Diese Zertifikate sind häufig nicht in der NodeJS-Zertifikatliste vorhanden. NodeJS vertraut keinen Antworten, die ein solches Zertifikat enthalten. Wenn NodeJS einer Antwort nicht vertraut, erhält der Storage-Explorer eine Fehlermeldung.

Sie haben mehrere Optionen zum Beheben solcher Fehler:
- Verwenden Sie den [**Systemproxy**](#use-system-proxy-preview) als Proxyquelle.
- Importieren Sie eine Kopie des SSL-Zertifikats, das den Fehler verursacht hat (oder der Zertifikate).
- Deaktivieren Sie das SSL-Zertifikat. (**nicht empfohlen**)

## <a name="features-that-support-system-proxy"></a>Features, die den Systemproxy unterstützen

Die folgende Liste enthält die vom **Systemproxy** unterstützten Funktionen:

- Suchen nach und Herunterladen von Updates
- Auflisten von Abonnements
- Speicherkontofunktionen
    - Auflistung
- Blobfeatures
    - Container
        - Erstellen
        - Auflistung
        - Verwalten von gespeicherten Zugriffsrichtlinien
        - Ändern der öffentlichen Zugriffsebene
        - Leasen
        - Eigenschaften
        - Löschen
    - Blobs
        - Auflistung
        - Statistik
        - Wiederherstellen
    - ADLS Gen2-Blobs
        - Auflistung
        - Statistik
        - Verwalten von ACLs (nur Anzeigen und Ändern vorhandener Entitäten)
        - Verteilen von ACLs
        - Move
        - Umbenennen
        - Ordner erstellen
- Warteschlangenfunktionen
    - Warteschlangen
        - Erstellen
        - Auflistung
        - Verwalten von Zugriffsrichtlinien
        - Eigenschaften
        - Löschen
        - Clear
    - Warteschlangennachrichten
        - Auflistung
        - Move
        - Hinzufügen
        - Entfernen aus der Warteschlange
- Dateifreigabefeatures
    - Dateien und Ordner
        - Neuer Ordner
        - Eigenschaften
- Datenträgerfeatures
    - Auflisten von Ressourcengruppen
    - Auflisten von Datenträgern
    - Hochladen von Datenträgern
    - Herunterladen von Datenträgern
    - Kopieren von Datenträgern
    - Erstellen von Momentaufnahmen
    - Löschen von Datenträgern

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln von Proxyproblemen](./storage-explorer-troubleshooting.md#proxy-issues)
* [Behandeln von Zertifikatproblemen](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
