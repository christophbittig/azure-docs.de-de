---
title: 'Bekannte Probleme: Azure Digital Twins'
description: Hilfreiche Informationen zur Erkennung und Behebung von bekannten Problemen mit Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 09/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 02a1a6d5088a722c0b919811afc0ac89c7c52550
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593570"
---
# <a name="known-issues-in-azure-digital-twins"></a>Bekannte Probleme in Azure Digital Twins

Dieser Artikel enthält Informationen zu bekannten Problemen mit Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>„400 Clientfehler: Ungültige Anforderung“ in Cloud Shell

**Problembeschreibung:** Für Befehle in Cloud Shell, die auf *https://shell.azure.com* ausgeführt werden, kann zeitweilig der folgende Fehler auftreten: „400 Clientfehler: Ungültige Anforderung für URL: http://localhost:50342/oauth2/token“ (gefolgt von der vollständigen Stapelüberwachung).

| Betrifft mich dies? | Ursache | Lösung |
| --- | --- | --- |
| In &nbsp;Azure&nbsp;Digital&nbsp;Twins wirkt sich dies auf die folgenden Befehlsgruppen aus:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Dies ist das Ergebnis eines bekannten Problems in Cloud Shell: [Beim Abrufen des Tokens aus Cloud Shell tritt zeitweilig der Fehler 400 Client Error: Bad Request](https://github.com/Azure/azure-cli/issues/11749) auf.<br><br>Es stellt ein Problem mit Authentifizierungstoken der Azure Digital Twins-Instanz und der Standardauthentifizierung von Cloud Shell dar, die auf [verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md) basiert. <br><br>Dies betrifft nicht die Azure Digital Twins-Befehle aus den Befehlsgruppen `az dt` oder `az dt endpoint`, da sie eine andere Art von Authentifizierungstoken (basierend auf Azure Resource Manager) verwenden, das kein Problem mit der Authentifizierung über verwaltete Identitäten von Cloud Shell hat. | Eine Möglichkeit, dieses Problem zu beheben, besteht im erneuten Ausführen des Befehls `az login` in Cloud Shell und der nachfolgenden Anmeldeschritte. Durch diese Maßnahme wird Ihre Sitzung aus der Authentifizierung mit der verwalteten Identität umgeschaltet, wodurch das ursächliche Problem vermieden wird. Anschließend können Sie den Befehl erneut ausführen.<br><br>Alternativ dazu können Sie den Cloud Shell-Bereich im Azure-Portal öffnen und Ihre Arbeit in Cloud Shell dort fortsetzen.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Screenshot des Cloud Shell-Symbols in der Symbolleiste des Azure-Portals." lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Eine weitere Lösung besteht darin, [die Azure CLI](/cli/azure/install-azure-cli) auf Ihrem Computer zu installieren, sodass Sie Azure CLI-Befehle lokal ausführen können. Bei der lokalen CLI tritt dieses Problem nicht auf. |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problem bei der interaktiven Browserauthentifizierung unter Azure.Identity 1.2.0

**Problembeschreibung:** Beim Schreiben von Authentifizierungscode in Azure Digital Twins-Anwendungen mit Version **1.2.0** der Bibliothek [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) treten möglicherweise Probleme mit der [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)-Methode auf. Dieses Problem umfasst die Fehlermeldung Azure.Identity.AuthenticationFailedException, wenn versucht wird, sich in einem Browserfenster zu authentifizieren. Möglicherweise wird das Browserfenster nicht vollständig gestartet, oder der Benutzer wird scheinbar erfolgreich authentifiziert, während in der Clientanwendung weiterhin der Fehler auftritt.

| Betrifft mich dies? | Ursache | Lösung |
| --- | --- | --- |
| Die&nbsp;betroffene&nbsp;Methode&nbsp;wird&nbsp;in&nbsp;folgenden&nbsp;Artikeln&nbsp;verwendet:<br><br>[Codieren einer Client-App](tutorial-code.md)<br><br>[Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md)<br><br>[Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md) | Bei einigen Benutzern ist dieses Problem mit Version **1.2.0** der `Azure.Identity`-Bibliothek aufgetreten. | Um das Problem zu beheben, aktualisieren Sie Ihre Anwendungen für die Verwendung einer [neueren Version](https://www.nuget.org/packages/Azure.Identity) von `Azure.Identity`. Nachdem Sie die Version der Bibliothek aktualisiert haben, sollte der Browser wie erwartet geladen werden und die Authentifizierung durchführen. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problem mit der Standardauthentifizierung von Azure-Anmeldeinformationen in Azure.Identity 1.3.0

**Problembeschreibung:** Beim Schreiben von Authentifizierungscode mit der Version **1.3.0** der Bibliothek [Azure.Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) treten bei bestimmten Benutzern Probleme mit der [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)-Methode auf, die in vielen Beispielen in diesen Azure Digital Twins-Dokumenten verwendet werden. Dabei wird die Fehlermeldung „Azure.Identity.AuthenticationFailedException: SharedTokenCacheCredential-Authentifizierungsfehler“ ausgegeben, wenn der Code die Authentifizierung versucht.

| Betrifft mich dies? | Ursache | Lösung |
| --- | --- | --- |
| `DefaultAzureCredential` wird in den meisten Dokumentationsbeispielen für diesen Dienst verwendet, die eine Authentifizierung einschließen. Wenn Sie den Authentifizierungscode mit `DefaultAzureCredential` mit Version 1.3.0 der `Azure.Identity`-Bibliothek schreiben und diese Fehlermeldung erhalten, sind Sie vom Problem betroffen. | Dies ist wahrscheinlich die Folge eines Konfigurationsproblems mit `Azure.Identity`. | Eine Strategie, um das Problem zu beheben, besteht darin, `SharedTokenCacheCredential` bei Ihren Anmeldeinformationen auszuschließen, wie in diesem [DefaultAzureCredential-Problem](https://github.com/Azure/azure-sdk/issues/1970) beschrieben, das derzeit für `Azure.Identity` geöffnet ist.<br>Eine andere Möglichkeit besteht darin, die Anwendung so zu ändern, dass sie eine frühere Version von `Azure.Identity` verwendet, z. B. [Version 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Die Verwendung einer früheren Version hat keine funktionalen Auswirkungen auf Azure Digital Twins, es ist also eine akzeptierte Lösung. |

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die Sicherheit und Berechtigungen in Azure Digital Twins:
* [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md)