---
title: Verwaltete Identitäten im Azure Web PubSub-Dienst
description: Hier erfahren Sie, wie verwaltete Identitäten im Azure Web PubSub-Dienst funktionieren und wie verwaltete Identitäten in serverlosen Szenarios verwendet werden können.
author: chenyl
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/22/2021
ms.author: chenyl
ms.openlocfilehash: c328e0e3bbff8c6e25e6f7b2df1c38cc710ace6b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270439"
---
# <a name="managed-identities-for-azure-web-pubsub-service"></a>Verwaltete Identitäten für den Azure Web PubSub-Dienst

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität für den Azure Web PubSub-Dienst erstellen und verwenden.

> [!Important] 
> Der Azure Web PubSub-Dienst kann nur eine verwaltete Identität unterstützen. Das bedeutet, dass Sie entweder eine systemseitig oder benutzerseitig zugewiesene Identität hinzufügen können. 

## <a name="add-a-system-assigned-identity"></a>Hinzufügen einer systemseitig zugewiesenen Identität

Erstellen Sie zum Einrichten einer verwalteten Identität im Azure-Portal zunächst eine Azure Web PubSub-Instanz, und aktivieren Sie dann das Feature.

1. Erstellen Sie wie gewohnt eine Azure Web PubSub-Instanz im Portal. Navigieren Sie im Portal zu dieser Instanz.

2. Wählen Sie **Identität** aus.

4. Ändern Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** in **Ein**. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/howto-use-managed-identity/system-identity-portal.png" alt-text="Hinzufügen einer systemseitig zugewiesenen Identität im Portal":::

## <a name="add-a-user-assigned-identity"></a>Hinzufügen einer benutzerseitig zugewiesenen Identität

Für das Erstellen einer Azure Web PubSub-Instanz mit einer benutzerseitig zugewiesenen Identität müssen Sie die Identität erstellen und dann den Ressourcenbezeichner der Identität zu Ihrem Dienst hinzufügen.

1. Folgen Sie zum Erstellen einer benutzerseitig verwalteten Identitätsressource [diesen Anweisungen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Erstellen Sie wie gewohnt eine Azure Web PubSub-Instanz im Portal. Navigieren Sie im Portal zu dieser Instanz.

3. Wählen Sie **Identität** aus.

4. Wählen Sie auf der Registerkarte **Benutzerseitig zugewiesen** die Option **Hinzufügen** aus.

5. Suchen Sie nach der zuvor erstellten Identität, und wählen Sie sie aus. Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/howto-use-managed-identity/user-identity-portal.png" alt-text="Hinzufügen einer benutzerseitig zugewiesenen Identität im Portal":::

## <a name="use-a-managed-identity-in-client-events-scenarios"></a>Verwenden einer verwalteten Identität in Clientereignisszenarios

Azure Web PubSub ist ein vollständig verwalteter Dienst, sodass Sie keine verwaltete Identität verwenden können, um Token manuell abzurufen. Wenn der Azure Web PubSub-Dienst Ereignisse an den Ereignishandler sendet, wird stattdessen die verwaltete Identität verwendet, um ein Zugriffstoken abzurufen. Der Dienst legt dann das Zugriffstoken im `Authorization`-Header in der HTTP-Anforderung fest.

### <a name="enable-managed-identity-authentication-in-event-handler-settings"></a>Aktivieren der Authentifizierung der verwalteten Identität in den Ereignishandlereinstellungen

1. Fügen Sie eine systemseitig oder benutzerseitig zugewiesene Identität hinzu.

2. Navigieren Sie zur Regel, und aktivieren Sie die **Authentifizierung**.

    :::image type="content" source="media/howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. Wählen Sie die Anwendung aus. Die Anwendungs-ID wird zum `aud`-Anspruch in dem erhaltenen Zugriffstoken, der als Teil der Validierung in Ihrem Ereignishandler verwendet werden kann. Sie können eine der folgenden Optionen auswählen:
    - AAD-Standardanwendung verwenden
    - Aus vorhandenen AAD-Anwendungen auswählen. Die Anwendungs-ID Ihrer Wahl wird verwendet.
    - AAD-Anwendung angeben. Der Wert sollte der [Ressourcen-ID eines Azure-Diensts](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) entsprechen.

    > [!NOTE]
    > Wenn Sie in Ihrem Dienst ein Zugriffstoken selbst überprüfen, können Sie eines der Ressourcenformate auswählen. Wenn Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) für eine Datenebene verwenden, müssen Sie die Ressource verwenden, die der Dienstanbieter anfordert.

### <a name="validate-access-tokens"></a>Überprüfen von Zugriffstoken

Das Token im `Authorization`-Header ist ein [Microsoft Identity Platform-Zugriffstoken](../active-directory/develop/access-tokens.md#validating-tokens).

Um Zugriffstoken zu überprüfen, sollte Ihre App auch die Zielgruppe und die signierenden Token validieren. Diese müssen anhand der Werte im OpenID Discovery-Dokument überprüft werden. Ein Beispiel finden Sie in der [mandantenunabhängigen Version des Dokuments](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Die Azure Active Directory-Middleware (Azure AD) verfügt über integrierte Funktionen zur Validierung von Zugriffstoken. Sie können unsere [Beispiele](../active-directory/develop/sample-v2-code.md) durchsuchen, um eines in der Sprache Ihrer Wahl zu finden.

Wir bieten Bibliotheken und Codebeispiele, die zeigen, wie Sie die Tokenüberprüfung ausführen können. Für die JSON Web Token-Validierung (JWT) stehen auch mehrere Open-Source-Partnerbibliotheken zur Verfügung. Es gibt dort mindestens eine Option für nahezu jede Plattform und Sprache. Weitere Informationen zu Azure AD-Authentifizierungsbibliotheken sowie Codebeispiele finden Sie unter [Microsoft Identity Platform-Authentifizierungsbibliotheken](../active-directory/develop/reference-v2-libraries.md).

Insbesondere wenn der Ereignishandler in der Azure-Funktion oder in Web-Apps gehostet wird, ist es einfach, [einen Azure AD-Anmeldenamen zu konfigurieren](../app-service/configure-authentication-provider-aad.md).

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Verwenden einer verwalteten Identität als Key Vault-Verweis

Web PubSub kann auf Key Vault zugreifen, um ein Geheimnis mithilfe der verwalteten Identität abzurufen.

1. Fügen Sie eine vom System oder vom Benutzer zugewiesene Identität für Azure Web PubSub hinzu.

2. Erteilen Sie der verwalteten Identität in den Zugriffsrichtlinien im Key Vault Leseberechtigung. Weitere Informationen finden Sie unter [Zuweisen einer Key Vault-Zugriffsrichtlinie über das Azure-Portal](../key-vault/general/assign-access-policy-portal.md).

Derzeit kann dieses Feature in folgenden Szenarien verwendet werden:

- Verwenden Sie die Syntax `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}`, um Geheimnisse aus Key Vault in der URL-Vorlageneinstellung des Ereignishandlers abzurufen.

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer serverlosen Echtzeit-Chat-App mit Azure Functions und dem Azure Web PubSub-Dienst](quickstart-serverless.md)
