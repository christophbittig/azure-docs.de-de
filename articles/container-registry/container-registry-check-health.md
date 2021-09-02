---
title: Überprüfen des Registrierungszustands
description: Erfahren Sie, wie Sie einen Kurzdiagnosebefehl ausführen können, um gängige Probleme bei Verwendung einer Azure-Containerregistrierung zu ermitteln, einschließlich lokaler Docker-Konfiguration und Konnektivität mit der Registrierung.
ms.topic: article
ms.date: 07/14/2021
ms.openlocfilehash: b0a95179e01de63185092c965b3290017dc96dfb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464301"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Überprüfen der Integrität einer Azure-Containerregistrierung

Wenn Sie eine Azure-Containerregistrierung verwenden, können gelegentlich Probleme auftreten. Möglich ist beispielsweise, dass Sie ein Containerimage aufgrund eines Problems mit Docker nicht per Pull in Ihre lokale Umgebung übertragen können. Oder ein Netzwerkproblem verhindert ggf. das Herstellen einer Verbindung mit der Registrierung. 

Führen Sie als ersten Diagnoseschritt den Befehl [az acr check-health][az-acr-check-health] aus, um Informationen zur Integrität der Umgebung abzurufen und optional Zugriff auf eine Zielregistrierung zu erhalten. Dieser Befehl ist ab Version 2.0.67 der Azure-Befehlszeilenschnittstelle verfügbar. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

Weitere Hinweise zum Beheben von Problemen bei der Registrierung finden Sie hier:
* [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md)
* [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md)
* [Beheben von Problemen mit der Registrierungsleistung](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Ausführen von „az acr check-health“

Die folgenden Beispiele zeigen verschiedene Möglichkeiten zum Ausführen des Befehls `az acr check-health`.

> [!NOTE]
> Wenn Sie den Befehl in Azure Cloud Shell ausführen, wird die lokale Umgebung nicht überprüft. Sie können jedoch den Zugriff auf eine Zielregistrierung überprüfen.

### <a name="check-the-environment-only"></a>Ausschließliche Überprüfung der Umgebung

Um den lokalen Docker-Daemon, die CLI-Version und die Helm-Clientkonfiguration zu überprüfen, führen Sie den Befehl ohne zusätzliche Parameter aus:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Überprüfen der Umgebung und einer Zielregistrierung

Um den Zugriff auf eine Registrierung zu überprüfen und lokale Umgebungsprüfungen durchzuführen, übergeben Sie den Namen einer Zielregistrierung. Beispiel:

```azurecli
az acr check-health --name myregistry
```

### <a name="check-registry-access-in-a-virtual-network"></a>Überprüfen des Registrierungszugriffs in einem virtuellen Netzwerk

Um die DNS-Einstellungen für die Weiterleitung an einen privaten Endpunkt zu überprüfen, übergeben Sie den Namen oder die Ressourcen-ID des virtuellen Netzwerks. Die Ressourcen-ID ist erforderlich, wenn sich das virtuelle Netzwerk in einem anderen Abonnement oder einer anderen Ressourcengruppe als die Registrierung befindet.

```azurecli
az acr check-health --name myregistry --vnet myvnet
```

## <a name="error-reporting"></a>Fehlerberichterstellung

Der Befehl protokolliert Informationen in der Standardausgabe. Bei Erkennen eines Problems werden ein Fehlercode und eine Beschreibung bereitgestellt. Weitere Informationen zu den Fehlercodes und mögliche Lösungen finden Sie in der [Fehlerreferenz](container-registry-health-error-reference.md).

Standardmäßig wird der Befehl beendet, wenn ein Fehler gefunden wird. Sie können den Befehl auch so ausführen, dass er eine Ausgabe für alle Integritätsprüfungen liefert, auch wenn Fehler gefunden werden. Fügen Sie den Parameter `--ignore-errors` wie in den folgenden Beispielen gezeigt hinzu:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry; skip confirmation to pull image
az acr check-health --name myregistry --ignore-errors --yes
```      

Beispielausgabe:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  

## <a name="next-steps"></a>Nächste Schritte

Informationen zu Fehlercodes, die vom Befehl [az acr check-health][az-acr-check-health] zurückgegeben werden, finden Sie in der [Fehlerreferenz zur Integritätsprüfung](container-registry-health-error-reference.md).

Häufig gestellte Fragen zu Azure Container Registry und andere bekannte Probleme finden Sie unter [FAQ](container-registry-faq.yml).





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
