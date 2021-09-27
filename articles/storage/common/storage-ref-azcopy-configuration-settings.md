---
title: AzCopy v10-Konfigurationseinstellung (Azure Storage) | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zu AzCopy V10-Konfigurationseinstellungen.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c3ad407b0c412e3cf4dbb5cc1635376c718a747
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605808"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy v10-Konfigurationseinstellungen (Azure Storage)

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. Dieser Artikel enthält eine Liste der Umgebungsvariablen, die Sie zum Konfigurieren von AzCopy v10 verwenden können.

> [!NOTE]
> Inhalte, die Sie beim Einstieg in AzCopy unterstützen, finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md).

## <a name="azcopy-v10-environment-variables"></a>AzCopy v10-Umgebungsvariablen

Die folgende Tabelle beschreibt die einzelnen Umgebungsvariablen und enthält Links zu Inhalten, die Sie bei der Verwendung der Variablen unterstützen können.

| Umgebungsvariable | BESCHREIBUNG |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services-Zugriffsschlüssel. Stellt einen Schlüssel für die Autorisierung bei Amazon Web Services bereit. [Kopieren von Daten aus Amazon S3 in Azure Storage mithilfe von AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Mit dem geheimen Zugriffsschlüssel von Amazon Web Services wird ein geheimer Schlüssel für die Autorisierung bei Amazon Web Services bereitgestellt. [Kopieren von Daten aus Amazon S3 in Azure Storage mithilfe von AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Der zu verwendende Azure Active Directory-Endpunkt. Diese Variable dient nur zur automatischen Anmeldung. Verwenden Sie stattdessen das Befehlszeilenflag beim Aufrufen des Anmeldebefehls. |
| AZCOPY_AUTO_LOGIN_TYPE | Legen Sie diese Variable auf `DEVICE`, `MSI` oder `SPN` fest. Diese Variable bietet die Möglichkeit, die Autorisierung ohne den Befehl `azcopy login` durchzuführen. Dieser Mechanismus ist nützlich in Fällen, in denen Ihr Betriebssystem über keinen Geheimnisspeicher wie z. B. einen *Linux-Schlüsselbund* verfügt. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_BUFFER_GB | Geben Sie die maximale Kapazität an Systemarbeitsspeicher an, die AzCopy beim Herunterladen und Hochladen von Dateien verwenden soll. Geben Sie diesen Wert in Gigabytes (GB) an. Informationen hierzu finden Sie unter [Optimieren der Arbeitsspeicherauslastung](storage-use-azcopy-optimize.md#optimize-memory-use). |
| AZCOPY_CACHE_PROXY_LOOKUP | Standardmäßig speichert AzCopy unter Windows Proxyserver-Lookups auf Hostnamensebene zwischen (ohne Berücksichtigung des URL-Pfads). Legen Sie einen anderen Wert als TRUE fest, um den Cache zu deaktivieren. |
| AZCOPY_CONCURRENCY_VALUE | Gibt die zulässige Anzahl gleichzeitiger Anforderungen an. Sie können diese Variable verwenden, um den Durchsatz zu erhöhen. Wenn Ihr Computer über weniger als 5 CPUs verfügt, wird der Wert dieser Variablen auf `32` festgelegt. Andernfalls ist der Standardwert gleich 16, multipliziert mit der Anzahl der CPUs. Der maximale Standardwert dieser Variablen ist `3000`, aber Sie können diesen Wert manuell höher oder niedriger festlegen. Weitere Informationen finden Sie unter [Erhöhen der Parallelität](storage-use-azcopy-optimize.md#increase-concurrency). |
| AZCOPY_CONCURRENT_FILES | Überschreibt die (ungefähre) Anzahl gleichzeitig verarbeiteter Dateien, indem gesteuert wird, für wie viele Dateien gleichzeitig Übertragungen initiiert werden. |
| AZCOPY_CONCURRENT_SCAN | Steuert den (maximalen) Parallelitätsgrad, der während der Überprüfung verwendet wird. Betrifft nur parallelisierte Enumeratoren, zu denen Azure Files/Blobs und lokale Dateisysteme gehören. |
| AZCOPY_CONTENT_TYPE_MAP  | Hiermit wird mindestens eine von Ihrem Betriebssystem definierte Standardzuordnung des MIME-Typs überschrieben. Legen Sie diese Variable auf den Pfad einer JSON-Datei fest, die eine beliebige Zuordnung definiert.  Hier sehen Sie den Inhalt einer JSON-Beispieldatei: <br><br> {<br>&nbsp;&nbsp;"MIMETypeMapping": { <br>&nbsp;&nbsp;&nbsp;&nbsp;".323": "text/h323",<br>&nbsp;&nbsp;&nbsp;&nbsp;".aaf": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp; ".aca": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp;&nbsp;".accdb": "application/msaccess",<br>&nbsp;&nbsp;&nbsp;&nbsp;  }<br>}
|
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Überschreibt die Dienst-API-Version, sodass AzCopy benutzerdefinierte Umgebungen wie Azure Stack umfassen kann. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Gilt nur, wenn Azure-Blobs als Quelle verwendet werden. Die gleichzeitige Überprüfung ist schneller, verwendet jedoch die API für hierarchische Auflistungen. Dies kann zur Erhöhung der E/A/Kosten führen. Geben Sie TRUE an, um zugunsten von Kosteneinsparungen eine geringere Leistung in Kauf zu nehmen. |
| AZCOPY_JOB_PLAN_LOCATION | Überschreibt den Speicherort für Auftragsplandateien (für die Fortschrittsnachverfolgung und Fortsetzung), um das Auffüllen eines Datenträgers zu vermeiden. |
| AZCOPY_LOG_LOCATION | Überschreibt den Speicherort für Protokolldateien, um das Auffüllen eines Datenträgers zu vermeiden. |
| AZCOPY_MSI_CLIENT_ID | Die Client-ID einer benutzerseitig zugewiesenen verwalteten Identität. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `MSI` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_MSI_OBJECT_ID | Die Objekt-ID der benutzerseitig zugewiesenen verwalteten Identität. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `MSI` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_MSI_RESOURCE_STRING | Die Ressourcen-ID der benutzerseitig zugewiesenen verwalteten Identität. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_PACE_PAGE_BLOBS | Gibt an, ob der Durchsatz für Seitenblobs automatisch entsprechend den Dienstgrenzwerten angepasst werden soll. Der Standardwert ist "True". Legen Sie diese Einstellung auf FALSE fest, um sie zu deaktivieren. |
| AZCOPY_PARALLEL_STAT_FILES | Veranlasst AzCopy zur Suche nach Dateieigenschaften für parallele „Threads“ bei der Überprüfung des lokalen Dateisystems.  Die Threads werden aus dem durch AZCOPY_CONCURRENT_SCAN definierten Pool abgerufen.  Durch Festlegen dieser Einstellung auf TRUE kann die Überprüfungsleistung unter Linux verbessert werden.  Unter Windows nicht erforderlich und nicht empfohlen. |
| AZCOPY_SHOW_PERF_STATES | Wenn diese Einstellung auf einen beliebigen Wert festgelegt wird, enthält die Ausgabe auf dem Bildschirm die Anzahl von Blöcken nach Status. |
| AZCOPY_SPA_APPLICATION_ID | Die Anwendungs-ID der App-Registrierung Ihres Dienstprinzipals. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `SPN` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CERT_PASSWORD | Das Kennwort eines Zertifikats. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `SPN` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CERT_PATH | Der relative oder vollqualifizierte Pfad zu einer Zertifikatdatei. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `SPN` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_SPA_CLIENT_SECRET | Der geheime Schlüssel des Clients. Verwenden Sie sie, wenn `AZCOPY_AUTO_LOGIN_TYPE` auf `SPN` festgelegt ist. Informationen hierzu finden Sie unter [Autorisieren ohne Geheimnisspeicher](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store). |
| AZCOPY_TENANT_ID | Die Azure Active Directory-Mandanten-ID, die für die interaktive OAuth-Geräteanmeldung verwendet werden soll. Diese Variable dient nur zur automatischen Anmeldung. Verwenden Sie stattdessen das Befehlszeilenflag beim Aufrufen des Anmeldebefehls. |
| AZCOPY_TUNE_TO_CPU | Durch Festlegen auf FALSE wird verhindert, dass AzCopy die CPU-Auslastung beim automatischen Optimieren der Parallelitätsstufe berücksichtigt (z. B. im Benchmarkbefehl). |
| AZCOPY_USER_AGENT_PREFIX | Fügen Sie dem standardmäßigen AzCopy-Benutzer-Agent für Telemetriezwecke ein Präfix hinzu. Ein Leerzeichen wird automatisch eingefügt. |
| GOOGLE_APPLICATION_CREDENTIALS | Der absolute Pfad zur Dienstkontoschlüssel-Datei. Stellt einen Schlüssel für die Autorisierung bei Google Cloud Storage bereit. [Kopieren von Daten aus Google Cloud Storage in Azure Storage mithilfe von AzCopy (Vorschau)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Konfiguriert Proxyeinstellungen für AzCopy. Legen Sie diese Variable auf die Proxy-IP-Adresse und die Proxyportnummer fest. Beispiel: `xx.xxx.xx.xxx:xx`. Unter Windows ist diese Einstellung nicht erforderlich, da AzCopy Proxyeinstellungen automatisch erkennt. Falls Sie diese Einstellung unter Windows verwenden, wird dadurch die automatische Erkennung außer Kraft gesetzt. Informationen hierzu finden Sie unter [Konfigurieren von Proxyeinstellungen](#configure-proxy-settings). |

## <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Um die Proxyeinstellungen für AzCopy zu konfigurieren, legen Sie die Umgebungsvariable `HTTPS_PROXY` fest. Unter Windows ist diese Einstellung nicht erforderlich, da AzCopy Proxyeinstellungen automatisch erkennt. Falls Sie diese Einstellung unter Windows verwenden, wird dadurch die automatische Erkennung außer Kraft gesetzt.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | Verwenden Sie in einer Eingabeaufforderung: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> Verwenden Sie in PowerShell: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

AzCopy unterstützt zurzeit keine Proxys, für die eine Authentifizierung mit NTLM oder Kerberos erforderlich ist.

### <a name="bypassing-a-proxy"></a>Umgehen eines Proxys

Wenn AzCopy unter Windows ausgeführt wird und Sie angeben möchten, dass gar *kein* Proxy verwendet werden soll (anstatt die Einstellungen automatisch zu erkennen), verwenden Sie diese Befehle. Mit diesen Einstellungen sucht AzCopy keinen Proxy und versucht auch nicht, einen zu verwenden.

| Betriebssystem | Environment | Befehle  |
|--------|-----------|----------|
| **Windows** | Befehlszeile (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Bei anderen Betriebssystemen belassen Sie die HTTPS_PROXY-Variable einfach nicht festgelegt, wenn Sie keinen Proxy verwenden möchten.

## <a name="see-also"></a>Weitere Informationen

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Optimieren der Leistung von AzCopy v10 mit Azure Storage](storage-use-azcopy-optimize.md)
- [Konfigurieren, Optimieren und Problembehandlung in AzCopy](storage-use-azcopy-configure.md)
