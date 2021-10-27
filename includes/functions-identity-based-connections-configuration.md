---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/11/2021
ms.author: mahender
ms.openlocfilehash: 8cef6aa5daa8bbbdda6971724343e65ea7e47aef
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992733"
---
Identitätsbasierte Verbindungen verwenden eine [verwaltete Identität](../articles/app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json), wenn sie im Azure Functions-Dienst gehostet werden. Standardmäßig wird eine vom System zugewiesene Identität verwendet, auch wenn mit den Eigenschaften `credential` und `clientID` eine vom Benutzer zugewiesene Identität angegeben werden kann. Bei Ausführung in anderen Kontexten (z. B. bei der lokalen Entwicklung) wird stattdessen Ihre Entwickleridentität verwendet, Dieses Verhalten kann angepasst werden. Weitere Informationen finden Sie unter [Lokale Entwicklung mit identitätsbasierten Verbindungen](../articles/azure-functions/functions-reference.md#local-development-with-identity-based-connections).

#### <a name="grant-permission-to-the-identity"></a>Erteilen der Berechtigung für die Identität

Unabhängig davon, welche Identität verwendet wird, muss diese über Berechtigungen zum Ausführen der vorgesehenen Aktionen verfügen. Sie müssen [eine Rolle in Azure RBAC](../articles/role-based-access-control/role-assignments-steps.md) zuweisen, indem Sie entweder integrierte oder benutzerdefinierte Rollen verwenden, die diese Berechtigungen bereitstellen.

> [!IMPORTANT]
> Vom Zieldienst werden möglicherweise einige nicht für alle Kontexte erforderliche Berechtigungen verfügbar gemacht. Befolgen Sie nach Möglichkeit das **Prinzip der geringsten Berechtigung**, und gewähren Sie der Identität nur die erforderlichen Berechtigungen. Wenn die App beispielsweise nur Daten aus einer Datenquelle lesen muss, verwenden Sie eine Rolle, die nur über Leseberechtigungen verfügt. Es wäre nicht angemessen, eine Rolle zu zuweisen, die auch das Schreiben in diesen Dienst zulässt, da dies eine übermäßige Berechtigung für einen Lesevorgang wäre. Ebenso sollten Sie sicherstellen, dass die Rollenzuweisung auf die Ressourcen begrenzt ist, die gelesen werden müssen.
