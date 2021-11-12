---
title: Sichere WebHook-Zustellung mit Azure AD in Azure Event Grid
description: Beschreibt, wie Ereignisse mittels Azure Event Grid an HTTPS-Endpunkte zugestellt werden, die von Azure Active Directory geschützt werden.
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 2e5c816d0902fe34ecdff9b967422814e345599a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511095"
---
# <a name="deliver-events-to-azure-active-directory-protected-endpoints"></a>Bereitstellen von Ereignissen auf mit Azure Active Directory geschützten Endpunkten
Dieser Artikel beschreibt, wie Sie Azure Active Directory (Azure AD) verwenden, um die Verbindung zwischen Ihrem **Ereignisabonnement** und Ihrem **Webhook-Endpunkt** sichern. Eine Übersicht über Azure AD-Anwendungen und -Dienstprinzipale finden Sie unter [Microsoft Identity Platform (v2.0): Übersicht](../active-directory/develop/v2-overview.md).

In diesem Artikel wird das Azure-Portal zur Demonstration verwendet. Die Funktion kann jedoch auch über mit der CLI, der PowerShell oder den SDKs aktiviert werden.

> [!IMPORTANT]
> Im Rahmen der Erstellung oder Aktualisierung des Ereignisabonnements am 30. März 2021 wurde eine zusätzliche Zugriffsüberprüfung eingeführt, um ein Sicherheitsrisiko zu entschärfen. Der Dienstprinzipal des Abonnentenclients muss entweder ein Besitzer sein oder über eine Rollenzuweisung auf dem Dienstprinzipal der Zielanwendung verfügen. Konfigurieren Sie Ihre AAD-Anwendung neu, indem Sie die folgenden neuen Anweisungen befolgen.

## <a name="deliver-events-to-a-webhook-in-the-same-azure-ad-tenant"></a>Übermitteln von Ereignissen an einen Webhook im gleichen Azure AD-Mandanten

![Sichere WebHook-Zustellung mit Azure AD in Azure Event Grid](./media/secure-webhook-delivery/single-tenant-diagram.png)

Führen Sie basierend auf der Abbildung oben die nächsten Schritte aus, um den Mandanten zu konfigurieren.

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Konfigurieren des Ereignisabonnements mit dem Azure AD-Benutzer

1. Erstellen Sie eine Azure AD-Anwendung für den Webhook, der für die Zusammenarbeit mit dem Microsoft-Verzeichnis (einzelner Mandant) konfiguriert ist.

2. Öffnen Sie die [Azure-Shell](https://portal.azure.com/#cloudshell/) im Mandanten, und wählen Sie die PowerShell-Umgebung aus.

3. Ändern Sie den Wert von **$webhookAadTenantId**, um eine Verbindung mit dem Mandanten herzustellen.

    - Variables:
        - **$webhookAadTenantId**: Azure-Mandanten-ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. Öffnen Sie das [folgende Skript](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md), aktualisieren Sie die Werte von **$webhookAppObjectId** und **$eventSubscriptionWriterUserPrincipalName** mit Ihren Bezeichnern, und fahren Sie dann mit dem Ausführen des Skripts fort.

    - Variables:
        - **$webhookAppObjectId**: Azure AD-Anwendungs-ID, die für den Webhook erstellt wurde
        - **$eventSubscriptionWriterUserPrincipalName**: Azure Benutzerprinzipalname des Benutzers, der das Ereignisabonnement erstellen wird

    > [!NOTE]
    > Sie müssen den Wert von **$eventGridAppId** nicht ändern, für dieses Skript legen wir **AzureEventGridSecureWebhookSubscriber** als Wert für **$eventGridRoleName** fest. Denken Sie daran, dass Sie Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../active-directory/roles/permissions-reference.md#all-roles) sein müssen, um dieses Skript ausführen zu können.

5. Führen Sie beim Erstellen eines Ereignisabonnements im Portal die folgenden Schritte aus:

    1. Wählen Sie den Endpunkttyp als **Webhook** aus.
    2. Geben Sie den **URI** des Endpunkts an.
    
        ![Auswählen des Endpunkttyps „Webhook“](./media/secure-webhook-delivery/select-webhook.png)
    3. Wählen Sie oben auf der Seite **Ereignisabonnements erstellen** die Registerkarte **Zusätzliche Features** aus.
    4. Führen Sie auf der Registerkarte **Zusätzliche Features** die folgenden Schritte aus:
        1. Wählen Sie **AAD-Authentifizierung verwenden** aus, und konfigurieren Sie die Mandanten-ID und die Anwendungs-ID:
        2. Kopieren Sie die Azure AD-Mandanten-ID aus der Ausgabe des Skripts, und geben Sie sie in das Feld **AAD-Mandanten-ID** ein.
        3. Kopieren Sie die Azure AD-Anwendungs-ID aus der Ausgabe des Skripts, und geben Sie sie in das Feld **AAD-Anwendungs-ID** ein. Sie können den AAD-Anwendungs-ID-URI anstelle der Anwendungs-ID verwenden. Weitere Informationen zum URI der Anwendungs-ID finden Sie in [diesem Artikel](../app-service/configure-authentication-provider-aad.md).
    
            ![Sichere Webhookaktion](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Konfigurieren des Ereignisabonnements mit der Azure AD-Anwendung

1. Erstellen Sie eine Azure AD-Anwendung für den Event Grid-Abonnementwriter, der für die Zusammenarbeit mit dem Microsoft-Verzeichnis (einzelner Mandant) konfiguriert ist.

2. Erstellen Sie ein Geheimnis für die Azure AD-Anwendung, die Sie zuvor erstellt haben, und speichern Sie den Wert (Sie benötigen diesen Wert später).

3. Wechseln Sie im Event Grid-Thema zur Zugriffssteuerung (IAM), und fügen Sie die Rollenzuweisung des Event Grid-Abonnementwriters als Event Grid-Mitwirkender hinzu. Dieser Schritt ermöglicht es, Zugriff auf die Event Grid-Ressource zu erhalten, wenn wir uns mit der Azure AD-Anwendung mithilfe der Azure CLI bei Azure angemeldet haben.

4. Erstellen Sie eine Azure AD-Anwendung für den Webhook, der für die Zusammenarbeit mit dem Microsoft-Verzeichnis (einzelner Mandant) konfiguriert ist.

5. Öffnen Sie die [Azure-Shell](https://portal.azure.com/#cloudshell/) im Mandanten, und wählen Sie die PowerShell-Umgebung aus.

6. Ändern Sie den Wert von **$webhookAadTenantId**, um eine Verbindung mit dem Mandanten herzustellen.

    - Variables:
        - **$webhookAadTenantId**: Azure-Mandanten-ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. Öffnen Sie das [folgende Skript](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md), aktualisieren Sie die Werte von **$webhookAppObjectId** und **$eventSubscriptionWriterAppId** mit Ihren Bezeichnern, und fahren Sie dann mit dem Ausführen des Skripts fort.

    - Variables:
        - **$webhookAppObjectId**: Azure AD-Anwendungs-ID, die für den Webhook erstellt wurde
        - **$eventSubscriptionWriterAppId**: Azure AD-Anwendungs-ID für Event Grid-Abonnementwriter

    > [!NOTE]
    > Sie müssen den Wert von **```$eventGridAppId```** nicht ändern, für dieses Skript legen wir **AzureEventGridSecureWebhookSubscriber** als Wert für **```$eventGridRoleName```** fest. Denken Sie daran, dass Sie Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../active-directory/roles/permissions-reference.md#all-roles) sein müssen, um dieses Skript ausführen zu können.

8. Melden Sie sich als die Azure AD-Anwendung Event Grid-Abonnementwriter an, indem Sie den Befehl ausführen.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. Erstellen Sie Ihr Abonnement, indem Sie den Befehl ausführen.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > In diesem Szenario verwenden wir ein Event Grid-Systemthema. Weitere Informationen finden Sie [hier](/cli/azure/eventgrid), wenn Sie ein Abonnement für benutzerdefinierte Themen oder Event Grid-Domänen mithilfe der Azure CLI erstellen möchten.

10. Wenn alles ordnungsgemäß konfiguriert wurde, können Sie das Webhookabonnement in Ihrem Event Grid-Thema erfolgreich erstellen.

    > [!NOTE]
    > Zu diesem Zeitpunkt übergibt Event Grid das Azure AD-Bearertoken in jeder Nachricht an den Webhookclient. Sie müssen das Autorisierungstoken in Ihrem Webhook validieren.

## <a name="deliver-events-to-a-webhook-in-a-different-azure-ad-tenant"></a>Übermitteln von Ereignissen an einen Webhook in einem anderen Azure AD-Mandanten 

Zum Schützen der Verbindung zwischen Ihrem Ereignisabonnement und Ihrem Webhookendpunkt, die sich in verschiedenen Azure AD Mandanten befinden, müssen Sie eine Azure AD Anwendung verwenden, wie in diesem Abschnitt gezeigt. Derzeit ist es nicht möglich, diese Verbindung mithilfe eines Azure AD Benutzers im Azure-Portal zu sichern. 

![Ereignisse mehrerer Mandanten mit Azure AD und Webhooks](./media/secure-webhook-delivery/multitenant-diagram.png)

Führen Sie basierend auf der Abbildung oben die nächsten Schritte aus, um beide Mandanten zu konfigurieren.

### <a name="tenant-a"></a>Mandant A

Gehen Sie in **Mandant A** wie folgt vor: 

1. Erstellen Sie eine Azure AD-Anwendung für den Event Grid-Abonnementwriter, der für die Zusammenarbeit mit einem beliebigen Azure AD-Verzeichnis (mehrere Mandanten) konfiguriert ist.

2. Erstellen Sie ein Geheimnis für die Azure AD-Anwendung, die Sie zuvor in **Mandant A** erstellt haben, und speichern Sie den Wert (Sie werden diesen Wert später benötigen).

3. Navigieren Sie zur Seite **Zugriffssteuerung (IAM)** für das Event Grid-Thema. Fügen Sie Azure AD Anwendung des Event Grid-Abonnementwriters zur Rolle **Event Grid Mitwirkender** hinzu. Dieser Schritt ermöglicht der Anwendung den Zugriff auf die Event Grid Ressource, wenn Sie sich mit der Azure AD Anwendung mithilfe des Azure CLI bei Azure anmelden.

### <a name="tenant-b"></a>Mandant B

Gehen Sie in **Mandant B** wie folgt vor:

1. Erstellen Sie eine Azure AD-Anwendung für den Webhook, der für die Zusammenarbeit mit dem Microsoft-Verzeichnis (einzelner Mandant) konfiguriert ist.
5. Öffnen Sie die [Azure-Shell](https://portal.azure.com/#cloudshell/) im Mandanten, und wählen Sie die PowerShell-Umgebung aus.
6. Ändern Sie den Wert von **$webhookAadTenantId**, um eine Verbindung mit **Mandant B** herzustellen.
    - Variables:
        - **$webhookAadTenantId**: Azure-Mandanten-ID für **Mandant B**

        ```Shell
        PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
        PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
        ```
7. Öffnen Sie das [folgende Skript](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md), aktualisieren Sie die Werte von **$webhookAppObjectId** und **$eventSubscriptionWriterAppId** mit Ihren Bezeichnern, und fahren Sie dann mit dem Ausführen des Skripts fort.

    - Variables:
        - **$webhookAppObjectId**: Azure AD-Anwendungs-ID, die für den Webhook erstellt wurde
        - **$eventSubscriptionWriterAppId**: Azure AD-Anwendungs-ID für Event Grid-Abonnementwriter

            > [!NOTE]
            > Sie müssen den Wert von **```$eventGridAppId```** nicht ändern, für dieses Skript legen wir **AzureEventGridSecureWebhookSubscriber** als Wert für **```$eventGridRoleName```** fest. Denken Sie daran, dass Sie Mitglied der [Rolle „Azure AD-Anwendungsadministrator“](../active-directory/roles/permissions-reference.md#all-roles) sein müssen, um dieses Skript ausführen zu können.

### <a name="tenant-a"></a>Mandant A

Wieder zurück in **Mandant A**, gehen Sie wie folgt vor: 

1. Öffnen Sie die [Azure-Shell](https://portal.azure.com/#cloudshell/), und melden Sie sich als Azure AD-Anwendung Event Grid-Abonnementwriter an, indem Sie den Befehl ausführen.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```
2. Erstellen Sie Ihr Abonnement, indem Sie den Befehl ausführen.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > In diesem Szenario verwenden wir ein Event Grid-Systemthema. Weitere Informationen finden Sie [hier](/cli/azure/eventgrid), wenn Sie ein Abonnement für benutzerdefinierte Themen oder Event Grid-Domänen mithilfe der Azure CLI erstellen möchten.
3. Wenn alles ordnungsgemäß konfiguriert wurde, können Sie das Webhookabonnement in Ihrem Event Grid-Thema erfolgreich erstellen.

    > [!NOTE]
    > An diesem Punkt übergibt Event Grid nun den Azure AD Bearertoken in jeder Nachricht an den Webhookclient. Sie müssen den Autorisierungstoken in Ihrem Webhook überprüfen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Überwachung von Ereignisübermittlungen finden Sie unter [Überwachen der Event Grid-Nachrichtenübermittlung](monitor-event-delivery.md).
* Weitere Informationen zum Authentifizierungsschlüssel finden Sie unter [Event Grid – Sicherheit und Authentifizierung](security-authentication.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).