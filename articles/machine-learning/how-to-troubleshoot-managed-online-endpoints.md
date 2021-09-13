---
title: Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte (Vorschau)
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie die Problembehandlung für einige häufige Fehler bei der Bereitstellung und Bewertung mit verwalteten Onlineendpunkten durchführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 05/13/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 8a68538657762417c5b6e23e455efa43fed0450a
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419409"
---
# <a name="troubleshooting-managed-online-endpoints-deployment-and-scoring-preview"></a>Problembehandlung für die Bereitstellung und Bewertung verwalteter Onlineendpunkte (Vorschau)

In diesem Artikel wird beschrieben, wie Sie häufige Probleme bei der Bereitstellung und Bewertung von verwalteten Azure Machine Learning-Onlineendpunkten (Vorschau) beheben.

Dieses Dokument ist so strukturiert, wie Sie die Problembehandlung durchführen sollten:

1. Verwenden Sie die [lokale Bereitstellung](#deploy-locally), um Ihre Modelle vor der Bereitstellung in der Cloud zu testen und zu debuggen.
1. Verwenden Sie [Containerprotokolle](#get-container-logs) zum Debuggen von Problemen.
1. Machen Sie sich mit [häufigen Bereitstellungsfehlern](#common-deployment-errors), die ggf. auftreten können, und deren Behebung vertraut.

Im Abschnitt [HTTP-Statuscodes](#http-status-codes) wird beschrieben, welche Aufruf- und Vorhersagefehler und HTTP-Statuscodes beim Bewerten von Endpunkten mit REST-Anforderungen zusammengehören.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.
* Die [Azure CLI](/cli/azure/install-azure-cli)
* Schritte unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md).

## <a name="deploy-locally"></a>Lokale Bereitstellung

Bei der lokalen Bereitstellung wird ein Modell in einer lokalen Docker-Umgebung bereitgestellt. Die lokale Bereitstellung ist für Test- und Debugvorgänge vor der Bereitstellung in der Cloud nützlich.

Für die lokale Bereitstellung werden das Erstellen, Aktualisieren und Löschen eines lokalen Endpunkts unterstützt. Darüber hinaus können Sie hierbei Protokolle aufrufen bzw. vom Endpunkt abrufen. Fügen Sie dem entsprechenden CLI-Befehl `--local` hinzu, um die lokale Bereitstellung zu verwenden:

```azurecli
az ml endpoint create -n <endpoint-name> -f <spec_file.yaml> --local
```
Im Rahmen der lokalen Bereitstellung werden die folgenden Schritte ausgeführt:

- Docker erstellt entweder ein neues Containerimage oder pullt ein vorhandenes Image aus dem lokalen Docker-Cache. Ein vorhandenes Image wird verwendet, falls für ein Image eine Übereinstimmung mit dem Umgebungsteil der Spezifikationsdatei besteht.
- Docker startet einen neuen Container mit bereitgestellten lokalen Artefakten, z. B. Modell- und Codedateien.

Weitere Informationen finden Sie unter „Lokales Bereitstellen“ in [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints).

## <a name="get-container-logs"></a>Abrufen von Containerprotokollen

Sie können keinen direkten Zugriff auf den virtuellen Computer erhalten, auf dem das Modell bereitgestellt wird. Es ist aber möglich, Protokolle aus einigen Containern abzurufen, die auf dem virtuellen Computer ausgeführt werden. Die Menge der Informationen hängt vom Bereitstellungsstatus der Bereitstellung ab. Wenn der angegebene Container betriebsbereit ist und ausgeführt wird, wird die Konsolenausgabe angezeigt. Andernfalls erhalten Sie eine Meldung mit dem Hinweis, dass Sie den Vorgang später noch einmal wiederholen sollen.

Verwenden Sie den folgenden CLI-Befehl, um die Protokollausgabe für den Container anzuzeigen:

```azurecli
az ml endpoint get-logs -n <endpoint-name> -d <deployment-name> -l 100
```

oder

```azurecli
    az ml endpoint get-logs --name <endpoint-name> --deployment <deployment-name> --lines 100
```

Fügen Sie den obigen Befehlen `--resource-group` und `--workspace-name` hinzu, falls Sie diese Parameter nicht bereits mit `az configure` festgelegt haben.

Führen Sie Folgendes aus, um Informationen dazu anzuzeigen, wie Sie diese Parameter festlegen und welche Werte bereits festgelegt wurden:

```azurecli
az ml endpoint get-logs -h
```

Standardmäßig werden die Protokolle per Pullvorgang vom Rückschlussserver abgerufen. Die Protokolle enthalten das Konsolenprotokoll vom Rückschlussserver, in dem die print/log-Anweisungen aus Ihrem „score.py“-Code enthalten sind.

> [!NOTE]
> Stellen Sie sicher, dass Sie bei Verwendung der Python-Protokollierung die richtige Protokolliergrad-Reihenfolge für die Nachrichten nutzen, die in Protokollen veröffentlicht werden sollen. Beispiel: INFO.


Sie können Protokolle auch aus dem Container mit dem Speicherinitialisierer abrufen, indem Sie `–-container storage-initializer` übergeben. Diese Protokolle enthalten Informationen dazu, ob das Herunterladen der Code- und Modelldaten in den Container erfolgreich war.

Fügen Sie den Befehlen `--help` bzw. `--debug` hinzu, um weitere Informationen zu erhalten. Fügen Sie den Header `x-ms-client-request-id` ein, um die Problembehandlung zu erleichtern.

## <a name="common-deployment-errors"></a>Häufige Bereitstellungsfehler

Unten ist eine Liste mit häufigen Bereitstellungsfehlern angegeben, die im Rahmen der Betriebsstatusmeldung für die Bereitstellung angezeigt werden.

### <a name="err_1100-not-enough-quota"></a>ERR_1100: Not enough quota (Unzureichendes Kontingent)

Vor der Bereitstellung eines Modells müssen Sie über ein ausreichendes Computekontingent verfügen. Anhand dieses Kontingents wird definiert, wie viele virtuelle Kerne pro Abonnement, Arbeitsbereich, SKU und Region verfügbar sind. Bei jeder Bereitstellung wird das verfügbare Kontingent reduziert und je nach Typ der SKU nach dem Löschen wieder erhöht.

Eine mögliche Lösung ist die Durchführung einer Überprüfung darauf, ob ungenutzte Bereitstellungen vorhanden sind, die gelöscht werden können. Alternativ können Sie auch eine [Anforderung für eine Kontingenterhöhung](./how-to-manage-quotas.md) übermitteln.

### <a name="err_1101-out-of-capacity"></a>ERR_1101: Out of capacity (Keine weitere Kapazität verfügbar)

Die angegebene VM-Größe konnte aufgrund einer nicht ausreichenden Azure Machine Learning-Kapazität nicht bereitgestellt werden. Versuchen Sie es später erneut, oder stellen Sie in einer anderen Region bereit.

### <a name="err_1200-unable-to-download-user-container-image"></a>ERR_1200: Unable to download user container image (Benutzercontainerimage kann nicht heruntergeladen werden)

Während des Erstellungsvorgangs nach der Bereitstellung der Computeressourcen wird von Azure versucht, das Benutzercontainerimage per Pullvorgang aus der privaten ACR-Instanz (Azure Container Registry) des Arbeitsbereichs abzurufen. Hierbei können zwei Probleme auftreten.

- Das Benutzercontainerimage kann nicht gefunden werden.

  Vergewissern Sie sich, dass das Containerimage auf der ACR-Instanz des Arbeitsbereichs verfügbar ist.
Wenn das Image beispielsweise `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest` lautet, sollten Sie das Repository wie folgt überprüfen: `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table`.

- Beim Zugriff auf ACR liegt ein Berechtigungsproblem vor.

  Zum Pullen des Images verwendet Azure für den Zugriff auf ACR [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md). 

  - Wenn Sie den zugeordneten Endpunkt mit „SystemAssigned“ erstellt haben, wird die Berechtigung für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure automatisch gewährt, und es sind keine weiteren Berechtigungen erforderlich.
  - Wenn Sie den zugeordneten Endpunkt mit „UserAssigned“ erstellt haben, muss die verwaltete Identität des Benutzers über die Berechtigung „AcrPull“ für die ACR-Instanz des Arbeitsbereichs verfügen.

Führen Sie Folgendes aus, um weitere Details zu diesem Fehler abzurufen:

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --tail 100
```

### <a name="err_1300-unable-to-download-user-modelcode-artifacts"></a>ERR_1300: Unable to download user model/code artifacts (Benutzermodell-/Codeartefakte können nicht heruntergeladen werden)

Nach der Bereitstellung der Computeressource versucht Azure beim Erstellen der Bereitstellung, die Benutzermodell- und Codeartefakte aus dem Speicherkonto des Arbeitsbereichs in den Benutzercontainer einzubinden.

- Benutzermodell-/Codeartefakte können nicht gefunden werden.

  - Vergewissern Sie sich, dass die Modell- und Codeartefakte für denselben Arbeitsbereich wie die Bereitstellung registriert sind. Verwenden Sie den Befehl `show`, um die Details zu einem Modell- oder Codeartefakt in einem Arbeitsbereich anzuzeigen. Beispiel: 
  
    ```azurecli
    az ml model show --name <model-name>
    az ml code show --name <code-name> --version <version>
    ```

  - Sie können auch überprüfen, ob die Blobs im Speicherkonto des Arbeitsbereichs vorhanden sind.

    Wenn das Blob beispielsweise `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl` lautet, können Sie mit dem folgenden Befehl überprüfen, ob es vorhanden ist: `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

- Beim Zugriff auf ACR liegt ein Berechtigungsproblem vor.

  Beim Pullen von Blobs werden von Azure [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) verwendet, um auf das Speicherkonto zuzugreifen.

  - Wenn Sie den zugeordneten Endpunkt mit „SystemAssigned“ erstellt haben, wird die Berechtigung für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure automatisch gewährt, und es sind keine weiteren Berechtigungen erforderlich.

  - Wenn Sie den zugeordneten Endpunkt mit „UserAssigned“ erstellt haben, muss die verwaltete Identität des Benutzers über die Berechtigung „Leser von Speicherblobdaten“ für das Speicherkonto des Arbeitsbereichs verfügen.

Führen Sie Folgendes aus, um weitere Details zu diesem Fehler abzurufen:

```azurecli
az ml endpoint get-logs -n <endpoint-name> --deployment <deployment-name> --lines 100
```

### <a name="err_1350-unable-to-download-user-model-not-enough-space-on-the-disk"></a>ERR_1350: Unable to download user model, not enough space on the disk (Benutzermodell kann nicht heruntergeladen werden, nicht genügend Speicherplatz auf dem Datenträger verfügbar)

Dieses Problem tritt auf, wenn die Größe des Modells größer als der verfügbare Speicherplatz ist. Probieren Sie eine SKU mit mehr Speicherplatz aus.

### <a name="err_2100-unable-to-start-user-container"></a>ERR_2100: Unable to start user container (Benutzercontainer kann nicht gestartet werden)

Um die bei der Bereitstellung angegebene Datei `score.py` auszuführen, wird von Azure ein Container mit allen Ressourcen erstellt, die für `score.py` benötigt werden, und das Bewertungsskript für den Container ausgeführt.

Dieser Fehler ist ein Hinweis darauf, dass dieser Container nicht gestartet werden konnte. Dies bedeutet, dass der Bewertungsvorgang nicht durchgeführt werden kann. Unter Umständen werden vom Container mehr Ressourcen angefordert, als von `instance_type` unterstützt werden können. In diesem Fall sollten Sie erwägen, das `instance_type`-Element der Onlinebereitstellung zu aktualisieren.

Führen Sie Folgendes aus, um die genaue Ursache für einen Fehler zu ermitteln: 

```azurecli
az ml endpoint get-logs
```

### <a name="err_2200-user-container-has-crashedterminated"></a>ERR_2200: User container has crashed/terminated (Benutzercontainer abgestürzt/beendet)

Um die bei der Bereitstellung angegebene Datei `score.py` auszuführen, wird von Azure ein Container mit allen Ressourcen erstellt, die für `score.py` benötigt werden, und das Bewertungsskript für den Container ausgeführt.  Der Fehler besteht bei diesem Szenario darin, dass dieser Container bei der Ausführung abstürzt. Dies bedeutet, dass der Bewertungsvorgang nicht durchgeführt werden konnte. Dieser Fehler tritt in folgenden Fällen auf:

- `score.py` enthält einen Fehler. Verwenden Sie `get--logs`, um häufige Probleme zu diagnostizieren:
    - Ein Paket, das importiert wurde, sich aber nicht in der Conda-Umgebung befindet.
    - Ein Syntaxfehler.
    - Ein Fehler in der `init()`-Methode.
- Bereitschafts- oder Livetests sind nicht richtig eingerichtet.
- Die Einrichtung der Umgebung für den Container enthält einen Fehler, z. B. eine fehlende Abhängigkeit.

### <a name="err_5000-internal-error"></a>ERR_5000: Internal error (Interner Fehler)

Wir tun zwar unser Bestes, um einen stabilen und zuverlässigen Dienst bereitzustellen, aber es kann vorkommen, dass nicht immer alles nach Plan verläuft. Das Auftreten dieses Fehlers bedeutet, dass bei uns ein Problem besteht, das behoben werden muss. Übermitteln Sie ein [Kundensupportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) mit allen zugehörigen Informationen, damit wir das Problem beheben können.  

## <a name="http-status-codes"></a>HTTP-Statuscodes

Wenn Sie auf verwaltete Onlineendpunkte mit REST-Anforderungen zugreifen, sind die zurückgegebenen Statuscodes an den Standardvorgaben für [HTTP-Statuscodes](https://aka.ms/http-status-codes) ausgerichtet. Unten finden Sie ausführliche Informationen dazu, welche HTTP-Statuscodes für welche Aufruf- und Vorhersagefehler für verwaltete Endpunkte gelten.

| Statuscode| Ursachentext |  Grund für den Code |
| --- | --- | --- |
| 200 | OK | Die Ausführung Ihres Modells war erfolgreich, und der Grenzwert für die Latenz wurde eingehalten. |
| 401 | Nicht autorisiert | Sie verfügen nicht über die Berechtigung für die Durchführung der angeforderten Aktion, z. B. einer Bewertung, oder Ihr Token ist abgelaufen. |
| 404 | Nicht gefunden | Ihre URL ist fehlerhaft. |
| 408 | Anforderungszeitlimit | Die Modellausführung hat mehr Zeit benötigt, als in der Konfiguration Ihrer Modellbereitstellung in `request_timeout_ms` unter `request_settings` als Zeitlimit angegeben ist.|
| 413 | Payload too large (Nutzlast zu groß) | Ihre Anforderungsnutzlast ist größer als 1,5 MB. |
| 424 | Modellfehler: original-code=`<original code>` | Wenn von Ihrem Modellcontainer eine andere Antwort als „200“ zurückgegeben wird, gibt Azure „424“ zurück. |
| 424 | Response payload too large (Antwortnutzlast zu groß) | Wenn Ihr Container eine Nutzlast zurückgibt, die größer als 1,5 MB ist, gibt Azure „424“ zurück. |
| 429 | Rate-limiting (Ratenbegrenzung) | Sie haben versucht, mehr als 100 Anforderungen pro Sekunde an Ihren Endpunkt zu senden. |
| 429 | Too many pending requests (Zu viele ausstehende Anforderungen) | Ihr Modell erhält mehr Anforderungen, als es verarbeiten kann. Es sind jeweils zwei Anforderungen vom Typ *`max_concurrent_requests_per_instance` bzw.* `instance_count` zulässig. Weitere Anforderungen werden abgelehnt. Sie können diese Einstellungen in der Konfiguration Ihrer Modellbereitstellung unter `request_settings` und `scale_settings` überprüfen. Wenn Sie die automatische Skalierung verwenden, erhält Ihr Modell die Anforderungen in so kurzer Zeit, dass das System mit dem Hochskalieren nicht Schritt halten kann. Bei der automatischen Skalierung können Sie versuchen, Anforderungen mit [exponentiellem Backoff](https://aka.ms/exponential-backoff) erneut zu senden. Das System hat dann Zeit, die entsprechenden Anpassungen vorzunehmen. |
| 500 | Interner Serverfehler | Fehler bei der von Azure ML bereitgestellten Infrastruktur. |

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md)

