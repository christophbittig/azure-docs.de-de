---
title: Abrufen eines Benutzertokens in Azure Cloud Shell
description: Abrufen eines Tokens für den authentifizierten Benutzer in Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362738"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>Abrufen eines Tokens in Azure Cloud Shell

Azure Cloud Shell stellt einen Endpunkt bereit, der den beim Azure-Portal angemeldeten Benutzer automatisch authentifiziert. Verwenden Sie diesen Endpunkt, um Zugriffstoken für die Interaktion mit Azure-Diensten abzurufen.

## <a name="authenticating-in-the-cloud-shell"></a>Authentifizieren in Cloud Shell
Azure Cloud Shell verfügt über einen eigenen Endpunkt, der mit Ihrem Browser interagiert, um Sie automatisch anzumelden. Wenn dieser Endpunkt eine Anforderung empfängt, sendet er die Anforderung zurück an Ihren Browser, der sie an den übergeordneten Portalframe weiterleitet. Im Portalfenster wird eine Anforderung an Azure Active Directory gestellt, und das resultierende Token wird zurückgegeben.

Wenn Sie sich mit anderen Anmeldeinformationen authentifizieren möchten, können Sie dazu `az login` oder `Connect-AzAccount` verwenden.

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>Abrufen und Verwenden eines Zugriffstokens in Cloud Shell

### <a name="acquire-token"></a>Abrufen eines Token

Führen Sie die folgenden Befehle aus, um Ihr Benutzerzugriffstoken als Umgebungsvariable `access_token` festzulegen.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>Verwenden von Token

Führen Sie den folgenden Befehl aus, um eine Liste aller VMs in Ihrem Konto mithilfe des Tokens abzurufen, das Sie im vorherigen Schritt abgerufen haben.

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>Vorgehen bei Tokenablauf

Token werden am lokalen Authentifizierungsendpunkt zwischengespeichert. Sie können sie beliebig oft aufrufen, und ein Authentifizierungsaufruf an Azure Active Directory erfolgt nur, wenn kein Token im Cache gespeichert oder wenn das Token abgelaufen ist.

## <a name="limitations"></a>Einschränkungen
- Es gibt eine Positivliste von Ressourcen, für die Cloud Shell-Token bereitgestellt werden können. Wenn Sie einen Befehl ausführen und eine Meldung der Art `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}` erhalten, ist diese Einschränkung in Kraft getreten. Sie können auf [GitHub](https://github.com/Azure/CloudShell/issues) ein Issue erstellen, um anzufordern, dass dieser Dienst der Positivliste hinzugefügt wird.
- Wenn Sie sich explizit über den Befehl `az login` anmelden, werden alle Regeln für bedingten Zugriff, die Ihr Unternehmen möglicherweise eingerichtet hat, basierend auf dem Cloud Shell-Container und nicht basierend auf dem Computer ausgewertet, auf dem Ihr Browser ausgeführt wird. Der Cloud Shell-Container zählt für diese Richtlinien nicht als verwaltetes Gerät, daher werden die Rechte durch die Richtlinie möglicherweise eingeschränkt.
- Verwaltete Azure-Identitäten sind in Azure Cloud Shell nicht verfügbar. Weitere Informationen zu verwalteten Azure-Identitäten finden Sie [hier](../active-directory/managed-identities-azure-resources/overview.md).
