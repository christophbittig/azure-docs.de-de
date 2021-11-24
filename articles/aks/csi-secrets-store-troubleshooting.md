---
title: Problembehandlung für Azure Key Vault-Anbieter für Secrets Store CSI-Treiber in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie häufige Probleme bei der Verwendung des Azure Key Vault-Anbieters für geheime Store CSI-Treiber mit Azure Kubernetes Service (AKS) beheben.
author: nickomang
ms.service: container-service
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: nickoman
ms.openlocfilehash: 94532cd6d0aa8f65bbd17b26886da4ee05a5860e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335934"
---
# <a name="troubleshooting-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Problembehandlung für Azure Key Vault-Anbieter für geheime Store CSI-Treiber

Dieser Artikel bietet eine Übersicht über Komponenten, die bei der Problembehandlung helfen können, sowie eine Liste allgemeiner Probleme und deren Lösungen.

## <a name="logging"></a>Protokollierung

Azure Key Vault Anbieterprotokolle sind in den Anbieterpods verfügbar. Um Probleme mit dem Anbieter zu beheben, können Sie Protokolle des Anbieterpods anzeigen, der auf demselben Knoten wie Ihr Anwendungspod ausgeführt wird:

```bash
# find the secrets-store-provider-azure pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-provider-azure -n kube-system -o wide
kubectl logs -l app=secrets-store-provider-azure -n kube-system --since=1h | grep ^E
```

Auf geheime Store CSI-Treiberprotokolle kann ebenfalls zugegriffen werden:

```bash
# find the secrets-store-csi-driver pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-csi-driver -n kube-system -o wide
kubectl logs -l app=secrets-store-csi-driver -n kube-system --since=1h | grep ^E
```

## <a name="common-issues"></a>Häufige Probleme

### <a name="failed-to-get-key-vault-token-nmi-response-failed-with-status-code-404"></a>Fehler beim Abrufen des Schlüsseltresortokens: Fehler bei nmi-Antwort mit Statuscode: 404

Wenn Sie die folgende Fehlermeldung in den Protokollen/Ereignissen erhalten haben:

```bash
Warning  FailedMount  74s    kubelet            MountVolume.SetUp failed for volume "secrets-store-inline" : kubernetes.io/csi: mounter.SetupAt failed: rpc error: code = Unknown desc = failed to mount secrets store objects for pod default/test, err: rpc error: code = Unknown desc = failed to mount objects, error: failed to get keyvault client: failed to get key vault token: nmi response failed with status code: 404, err: <nil>
```

Dies bedeutet, dass die NMI-Komponente in aad-pod-identity einen Fehler für die Tokenanforderung meldet. Weitere Informationen zum Fehler finden Sie in den NMI-Pod-Protokollen, und lesen Sie den [Leitfaden zur {1}Problembehandlung][aad-troubleshooting] für AAD Pod-Identität, um das Problem zu beheben.

### <a name="keyvaultbaseclientgetsecret-failure-sending-request-statuscode0--original-error-context-canceled"></a>keyvault.BaseClient#GetSecret: Fehler beim Senden der Anforderung: StatusCode=0 – Ursprünglicher Fehler: Kontext abgebrochen

Wenn Sie die folgende Fehlermeldung in den Protokollen/Ereignissen erhalten haben:

```bash
E1029 17:37:42.461313       1 server.go:54] failed to process mount request, error: keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 -- Original Error: context deadline exceeded
```

Dies bedeutet, dass der Anbieterpod aus folgenden Gründen nicht auf die AKV-Instanz zugreifen kann:

- Es gibt eine Firewallregel, die ausgehenden Datenverkehr vom Anbieter blockiert.
- Im Cluster konfigurierte Netzwerkrichtlinien, die ausgehenden Datenverkehr blockieren.
- Die Anbieterpods werden auf hostNetwork ausgeführt. Wenn also eine Richtlinie diesen Datenverkehr blockiert oder Netzwerk-Jitter auf dem Knoten vorhanden sind, kann dies zu obigem Fehler führen. Überprüfen Sie, ob Richtlinien konfiguriert sind, um Datenverkehr zu blockieren und Anbieterpods zuzulassen. Stellen Sie außerdem sicher, dass über den Knoten Konnektivität mit Azure AD und Key Vault besteht.

Sie können Key Vault-Konnektivität von Pods, die im Hostnetzwerk ausgeführt werden, wie folgt testen:

- Erstellen eines Pods

  ```bash
  cat <<EOF | kubectl apply -f -
  apiVersion: v1
  kind: Pod
  metadata:
    name: curl
  spec:
    hostNetwork: true
    containers:
    - args:
      - tail
      - -f
      - /dev/null
      image: curlimages/curl:7.75.0
      name: curl
    dnsPolicy: ClusterFirst
    restartPolicy: Always
  EOF
  ```

- Ausführen in den oben erstellten Pod

  ```bash
  kubectl exec -it curl -- sh
  ```

- Authentifizierung mit AKV

  ```bash
  curl -X POST 'https://login.microsoftonline.com/<AAD_TENANT_ID>/oauth2/v2.0/token' -d 'grant_type=client_credentials&client_id=<AZURE_CLIENT_ID>&client_secret=<AZURE_CLIENT_SECRET>&scope=https://vault.azure.net/.default'
  ```

- Versuchen Sie, ein Geheimnis zu erhalten, das bereits in AKV erstellt wurde

  ```bash
  curl -X GET 'https://<KEY_VAULT_NAME>.vault.azure.net/secrets/<SECRET_NAME>?api-version=7.2' -H "Authorization: Bearer <ACCESS_TOKEN_ACQUIRED_ABOVE>"
  ```

<!-- LINKS EXTERNAL -->
[aad-troubleshooting]: https://azure.github.io/aad-pod-identity/docs/troubleshooting/
