---
title: Problembehandlung bei der Bereitstellung von Onlineendpunkten (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Problembehandlung für einige häufige Fehler bei der Bereitstellung und Bewertung mit Onlineendpunkten durchführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: petrodeg
ms.author: petrodeg
ms.reviewer: laobri
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 02f65a5c07536afb1fb20c3f85c444f2376c9b34
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137584"
---
# <a name="troubleshooting-online-endpoints-deployment-and-scoring-preview"></a>Problembehandlung bei der Bereitstellung und Bewertung von Onlineendpunkten (Vorschau)

Erfahren Sie, wie Sie häufige Probleme bei der Bereitstellung und Bewertung von Azure Machine Learning-Onlineendpunkten (Vorschau) beheben.

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

> [!TIP]
> Verwenden Sie Visual Studio Code, um Ihre Endpunkte lokal zu testen und zu debuggen. Weitere Informationen finden Sie unter [Lokales Debuggen von Onlineendpunkten in Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

Für die lokale Bereitstellung werden das Erstellen, Aktualisieren und Löschen eines lokalen Endpunkts unterstützt. Darüber hinaus können Sie hierbei Protokolle aufrufen bzw. vom Endpunkt abrufen. Fügen Sie dem entsprechenden CLI-Befehl `--local` hinzu, um die lokale Bereitstellung zu verwenden:

```azurecli
az ml online-deployment create --endpoint-name <endpoint-name> -n <deployment-name> -f <spec_file.yaml> --local
```

Im Rahmen der lokalen Bereitstellung werden die folgenden Schritte ausgeführt:

- Docker erstellt entweder ein neues Containerimage oder pullt ein vorhandenes Image aus dem lokalen Docker-Cache. Ein vorhandenes Image wird verwendet, falls für ein Image eine Übereinstimmung mit dem Umgebungsteil der Spezifikationsdatei besteht.
- Docker startet einen neuen Container mit bereitgestellten lokalen Artefakten, z. B. Modell- und Codedateien.

Weitere Informationen finden Sie unter „Lokales Bereitstellen“ in [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints).

## <a name="get-container-logs"></a>Abrufen von Containerprotokollen

Sie können keinen direkten Zugriff auf den virtuellen Computer erhalten, auf dem das Modell bereitgestellt wird. Es ist aber möglich, Protokolle aus einigen Containern abzurufen, die auf dem virtuellen Computer ausgeführt werden. Die Menge der Informationen hängt vom Bereitstellungsstatus der Bereitstellung ab. Wenn der angegebene Container betriebsbereit ist und ausgeführt wird, wird die Konsolenausgabe angezeigt. Andernfalls erhalten Sie eine Meldung mit dem Hinweis, dass Sie den Vorgang später noch einmal wiederholen sollen.

Verwenden Sie den folgenden CLI-Befehl, um die Protokollausgabe für den Container anzuzeigen:

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

oder

```azurecli
    az ml online-deployment get-logs --endpoint-name <endpoint-name> --name <deployment-name> --lines 100
```

Fügen Sie den obigen Befehlen `--resource-group` und `--workspace-name` hinzu, falls Sie diese Parameter nicht bereits mit `az configure` festgelegt haben.

Führen Sie Folgendes aus, um Informationen dazu anzuzeigen, wie Sie diese Parameter festlegen und welche Werte bereits festgelegt wurden:

```azurecli
az ml online-deployment get-logs -h
```

Standardmäßig werden die Protokolle per Pullvorgang vom Rückschlussserver abgerufen. Die Protokolle enthalten das Konsolenprotokoll vom Rückschlussserver, in dem die print/log-Anweisungen aus Ihrem „score.py“-Code enthalten sind.

> [!NOTE]
> Stellen Sie sicher, dass Sie bei Verwendung der Python-Protokollierung die richtige Protokolliergrad-Reihenfolge für die Nachrichten nutzen, die in Protokollen veröffentlicht werden sollen. Beispiel: INFO.


Sie können Protokolle auch aus dem Container mit dem Speicherinitialisierer abrufen, indem Sie `–-container storage-initializer` übergeben. Diese Protokolle enthalten Informationen dazu, ob das Herunterladen der Code- und Modelldaten in den Container erfolgreich war.

Fügen Sie den Befehlen `--help` bzw. `--debug` hinzu, um weitere Informationen zu erhalten. Fügen Sie den Header `x-ms-client-request-id` ein, um die Problembehandlung zu erleichtern.

## <a name="common-deployment-errors"></a>Häufige Bereitstellungsfehler

Unten ist eine Liste mit häufigen Bereitstellungsfehlern angegeben, die im Rahmen der Betriebsstatusmeldung für die Bereitstellung angezeigt werden.

* [Kontingenterschöpft](#error-outofquota)
* [Kapazitätsüberschreitung](#error-outofcapacity)
* [BadArgument](#error-badargument)
* [RessourceNichtBereit](#error-resourcenotready)
* [ResourceNotFound](#error-resourcenotfound)
* [Vorgang abgebrochen](#error-operationcancelled)
* [InternalServerError](#error-internalservererror)

### <a name="error-outofquota"></a>ERROR: Kontingenterschöpft

Im Folgenden finden Sie eine Liste gängiger Ressourcen, bei denen die Kontingente bei der Nutzung von Azure-Diensten überschritten werden können:

* [CPU](#cpu-quota)
* [Rollenzuweisungen](#role-assignment-quota)
* [Endpunkte](#endpoint-quota)
* [Kubernetes](#kubernetes-quota)
* [Andere](#other-quota)

#### <a name="cpu-quota"></a>CPU-Kontingent

Vor der Bereitstellung eines Modells müssen Sie über ein ausreichendes Computekontingent verfügen. Anhand dieses Kontingents wird definiert, wie viele virtuelle Kerne pro Abonnement, Arbeitsbereich, SKU und Region verfügbar sind. Bei jeder Bereitstellung wird das verfügbare Kontingent reduziert und je nach Typ der SKU nach dem Löschen wieder erhöht.

Eine mögliche Lösung ist die Durchführung einer Überprüfung darauf, ob ungenutzte Bereitstellungen vorhanden sind, die gelöscht werden können. Alternativ können Sie auch eine [Anforderung für eine Kontingenterhöhung](how-to-manage-quotas.md#request-quota-increases) übermitteln.

#### <a name="role-assignment-quota"></a>Quote für die Rollenzuweisung

Versuchen Sie, einige nicht verwendete Rollenzuweisungen in diesem Abonnement zu löschen. Sie können alle Rollenzuweisungen im Azure-Portal im Menü Zugriffskontrolle überprüfen.

#### <a name="endpoint-quota"></a>Endpunktkontingent

Versuchen Sie, einige nicht verwendete Endpunkte in diesem Abonnement zu löschen.

#### <a name="kubernetes-quota"></a>Kubernetes-Kontingent

Die angeforderte CPU oder der angeforderte Arbeitsspeicher konnte nicht bereitgestellt werden. Bitte passen Sie Ihre Anforderung oder den Cluster an.

#### <a name="other-quota"></a>Anderes Kontingent

Um die bei der Bereitstellung angegebene Datei `score.py` auszuführen, wird von Azure ein Container mit allen Ressourcen erstellt, die für `score.py` benötigt werden, und das Bewertungsskript für den Container ausgeführt.

Wenn Ihr Container nicht gestartet werden konnte, bedeutet dies, dass keine Wertung erfolgen konnte. Es könnte sein, dass der Container mehr Ressourcen anfordert, als `instance_type` unterstützen kann. In diesem Fall sollten Sie erwägen, das `instance_type`-Element der Onlinebereitstellung zu aktualisieren.

Führen Sie Folgendes aus, um die genaue Ursache für einen Fehler zu ermitteln: 

```azurecli
az ml online-deployment get-logs -e <endpoint-name> -n <deployment-name> -l 100
```

### <a name="error-outofcapacity"></a>ERROR: Kapazitätsüberschreitung

Die angegebene VM-Größe konnte aufgrund einer nicht ausreichenden Azure Machine Learning-Kapazität nicht bereitgestellt werden. Versuchen Sie es später erneut, oder stellen Sie in einer anderen Region bereit.

### <a name="error-badargument"></a>ERROR: Falsches Argument

Nachfolgend finden Sie eine Liste der Gründe, die zu diesem Fehler führen können:

* [Ressourcenanforderung war größer als die Grenzen](#resource-requests-greater-than-limits)
* [Ressourcen können nicht heruntergeladen werden](#unable-to-download-resources)

#### <a name="resource-requests-greater-than-limits"></a>Ressourcenanforderungen übersteigen die Grenzen

Anforderungen für Ressourcen müssen kleiner oder gleich den Grenzwerten sein. Wenn Sie keine Grenzwerte festlegen, werden Standardwerte festgelegt, wenn Sie Ihre Rechenleistung mit einem Azure Machine Learning-Arbeitsbereich verbinden. Sie können die Grenzwerte im Azure-Portal oder mit dem Befehl `az ml compute show` überprüfen.

#### <a name="unable-to-download-resources"></a>Ressourcen können nicht heruntergeladen werden

Nach der Bereitstellung der Compute-Ressource versucht Azure während der Deployment-Erstellung, das Benutzer-Container-Image aus der privaten Azure Container Registry (ACR) des Arbeitsbereichs zu ziehen und das Benutzermodell und die Code-Artefakte aus dem Speicherkonto des Arbeitsbereichs in den Benutzer-Container einzuhängen.

Prüfen Sie zunächst, ob es ein Problem mit den Zugriffsrechten auf ACR gibt.

Beim Pullen von Blobs werden von Azure [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) verwendet, um auf das Speicherkonto zuzugreifen.

  - Wenn Sie den zugeordneten Endpunkt mit „SystemAssigned“ erstellt haben, wird die Berechtigung für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure automatisch gewährt, und es sind keine weiteren Berechtigungen erforderlich.

  - Wenn Sie den zugeordneten Endpunkt mit „UserAssigned“ erstellt haben, muss die verwaltete Identität des Benutzers über die Berechtigung „Leser von Speicherblobdaten“ für das Speicherkonto des Arbeitsbereichs verfügen.

Während dieses Prozesses kann es zu verschiedenen Problemen kommen, je nachdem, in welcher Phase der Vorgang fehlgeschlagen ist:

* [Benutzer-Container-Image kann nicht heruntergeladen werden](#unable-to-download-user-container-image)
* [Nicht möglich, Benutzermodell oder Code-Artefakte herunterzuladen](#unable-to-download-user-model-or-code-artifacts)

Um weitere Details zu diesen Fehlern zu erhalten, führen Sie aus:

```azurecli
az ml online-deployment get-logs -n <endpoint-name> --deployment <deployment-name> --l 100
``` 

#### <a name="unable-to-download-user-container-image"></a>Benutzer-Container-Image kann nicht heruntergeladen werden

Es ist möglich, dass der Benutzer-Container nicht gefunden werden konnte.

Vergewissern Sie sich, dass das Containerimage auf der ACR-Instanz des Arbeitsbereichs verfügbar ist.

Wenn das Image beispielsweise `testacr.azurecr.io/azureml/azureml_92a029f831ce58d2ed011c3c42d35acb:latest` lautet, sollten Sie das Repository wie folgt überprüfen: `az acr repository show-tags -n testacr --repository azureml/azureml_92a029f831ce58d2ed011c3c42d35acb --orderby time_desc --output table`.

#### <a name="unable-to-download-user-model-or-code-artifacts"></a>Herunterladen von Benutzermodellen oder Code-Artefakten nicht möglich

Es ist möglich, dass das Benutzermodell oder Code-Artefakte nicht gefunden werden können.

Vergewissern Sie sich, dass die Modell- und Codeartefakte für denselben Arbeitsbereich wie die Bereitstellung registriert sind. Verwenden Sie den Befehl `show`, um die Details zu einem Modell- oder Codeartefakt in einem Arbeitsbereich anzuzeigen. 

- Beispiel: 
  
  ```azurecli
  az ml model show --name <model-name>
  az ml code show --name <code-name> --version <version>
  ```
 
  Sie können auch überprüfen, ob die Blobs im Speicherkonto des Arbeitsbereichs vorhanden sind.

- Wenn der Blob zum Beispiel `https://foobar.blob.core.windows.net/210212154504-1517266419/WebUpload/210212154504-1517266419/GaussianNB.pkl` ist, können Sie mit diesem Befehl prüfen, ob er existiert:

  `az storage blob exists --account-name foobar --container-name 210212154504-1517266419 --name WebUpload/210212154504-1517266419/GaussianNB.pkl --subscription <sub-name>`

### <a name="error-resourcenotready"></a>ERROR: RessourceNichtBereit

Um die bei der Bereitstellung angegebene Datei `score.py` auszuführen, wird von Azure ein Container mit allen Ressourcen erstellt, die für `score.py` benötigt werden, und das Bewertungsskript für den Container ausgeführt. Der Fehler in diesem Szenario besteht darin, dass dieser Container bei der Ausführung abstürzt, was bedeutet, dass keine Wertung stattfinden kann. Dieser Fehler tritt in folgenden Fällen auf:

- `score.py` enthält einen Fehler. Verwenden Sie `get-logs`, um häufige Probleme zu diagnostizieren:
    - Ein Paket, das importiert wurde, sich aber nicht in der conda-Umgebung befindet.
    - Ein Syntaxfehler.
    - Ein Fehler in der `init()`-Methode.
- Wenn mit `get-logs` keine Protokolle generiert werden, bedeutet dies normalerweise, dass der Container nicht gestartet wurde. Versuchen Sie, die [Bereitstellung stattdessen lokal durchzuführen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/how-to-troubleshoot-online-endpoints.md#deploy-locally), um dieses Problem zu beheben.
- Bereitschafts- oder Livetests sind nicht richtig eingerichtet.
- Die Einrichtung der Umgebung für den Container enthält einen Fehler, z. B. eine fehlende Abhängigkeit.

### <a name="error-resourcenotfound"></a>ERROR: Ressource nicht gefunden

Dieser Fehler tritt auf, wenn der Azure Resource Manager eine benötigte Ressource nicht finden kann. Diese Fehlermeldung wird zum Beispiel angezeigt, wenn ein Speicherkonto angegeben wurde, aber nicht unter dem angegebenen Pfad gefunden werden kann. Achten Sie darauf, dass Sie die Quellen, die Sie möglicherweise zur Verfügung gestellt haben, auf den genauen Pfad oder die Schreibweise ihrer Namen überprüfen.

Weitere Informationen finden Sie unter [Beheben von Fehlern des Typs „Ressource nicht gefunden“](../azure-resource-manager/troubleshooting/error-not-found.md). 

### <a name="error-operationcancelled"></a>ERROR: Vorgang abgebrochen

Azure-Operationen haben eine bestimmte Prioritätsstufe und werden von der höchsten zur niedrigsten ausgeführt. Dieser Fehler tritt auf, wenn Ihr Vorgang durch einen anderen Vorgang mit höherer Priorität überschrieben wird. Durch einen erneuten Versuch kann der Vorgang möglicherweise ohne Abbruch durchgeführt werden.

### <a name="error-internalservererror"></a>ERROR: InternerServerFehler

Obwohl wir unser Bestes tun, um einen stabilen und zuverlässigen Dienst anzubieten, läuft manchmal nicht alles nach Plan. Wenn Sie diese Fehlermeldung erhalten, bedeutet das, dass auf unserer Seite etwas nicht stimmt und wir das Problem beheben müssen. Übermitteln Sie ein [Kundensupportticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) mit allen zugehörigen Informationen, damit wir das Problem beheben können. 

## <a name="autoscaling-issues"></a>Probleme mit der automatischen Skalierung

Wenn Sie Probleme mit der automatischen Skalierung haben, finden Sie weitere Informationen unter [Problembehandlung für automatische Skalierung in Azure](../azure-monitor/autoscale/autoscale-troubleshoot.md).

## <a name="http-status-codes"></a>HTTP-Statuscodes

Wenn Sie mit REST-Anforderungen auf Onlineendpunkte zugreifen, entsprechen die zurückgegebenen Statuscodes den Standardvorgaben für [HTTP-Statuscodes](https://aka.ms/http-status-codes). Unten finden Sie ausführliche Informationen dazu, welche HTTP-Statuscodes für welche Aufruf- und Vorhersagefehler für Endpunkte gelten.

| Statuscode| Ursachentext |  Grund für den Code |
| --- | --- | --- |
| 200 | OK | Die Ausführung Ihres Modells war erfolgreich, und der Grenzwert für die Latenz wurde eingehalten. |
| 401 | Nicht autorisiert | Sie verfügen nicht über die Berechtigung für die Durchführung der angeforderten Aktion, z. B. einer Bewertung, oder Ihr Token ist abgelaufen. |
| 404 | Nicht gefunden | Ihre URL ist fehlerhaft. |
| 408 | Anforderungszeitlimit | Die Modellausführung hat mehr Zeit benötigt, als in der Konfiguration Ihrer Modellbereitstellung in `request_timeout_ms` unter `request_settings` als Zeitlimit angegeben ist.|
| 413 | Payload too large (Nutzlast zu groß) | Ihre Anforderungsnutzlast ist größer als 1,5 MB. |
| 424 | Modellfehler | Wenn von Ihrem Modellcontainer eine andere Antwort als „200“ zurückgegeben wird, gibt Azure „424“ zurück. Weitere Informationen finden Sie unter den Antwortheadern `ms-azureml-model-error-statuscode` und `ms-azureml-model-error-reason`. |
| 424 | Response payload too large (Antwortnutzlast zu groß) | Wenn Ihr Container eine Nutzlast zurückgibt, die größer als 1,5 MB ist, gibt Azure „424“ zurück. |
| 429 | Rate-limiting (Ratenbegrenzung) | Sie haben versucht, mehr als 100 Anforderungen pro Sekunde an Ihren Endpunkt zu senden. |
| 429 | Too many pending requests (Zu viele ausstehende Anforderungen) | Ihr Modell erhält mehr Anforderungen, als es verarbeiten kann. Es sind jeweils 2 × `max_concurrent_requests_per_instance` * `instance_count` Anforderungen zulässig. Weitere Anforderungen werden abgelehnt. Sie können diese Einstellungen in der Konfiguration Ihrer Modellbereitstellung unter `request_settings` und `scale_settings` überprüfen. Wenn Sie die automatische Skalierung verwenden, erhält Ihr Modell die Anforderungen in so kurzer Zeit, dass das System mit dem Hochskalieren nicht Schritt halten kann. Bei der automatischen Skalierung können Sie versuchen, Anforderungen mit [exponentiellem Backoff](https://aka.ms/exponential-backoff) erneut zu senden. Das System hat dann Zeit, die entsprechenden Anpassungen vorzunehmen. |
| 500 | Interner Serverfehler | Fehler bei der von Azure ML bereitgestellten Infrastruktur. |

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)
- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md)

