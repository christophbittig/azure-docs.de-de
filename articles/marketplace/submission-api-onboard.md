---
title: Übersicht über die Partner Center-Übermittlungs-API
description: Eine Übersicht über die Partner Center-Übermittlungs-API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: e75b85ea7557e4eb6688e8a549e92820d580359b
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614743"
---
# <a name="partner-center-submission-api-onboarding"></a>Einführung in die Partner Center-Übermittlungs-API

Verwenden Sie die Partner Center-Übermittlungs-API, um kommerzielle Marketplace-Angebote programmgesteuert abzufragen, zu erstellen und zu veröffentlichen. Diese API ist nützlich, wenn Ihr Konto viele Angebote verwaltet und Sie den Übermittlungsvorgang für diese Angebote automatisieren und optimieren möchten.

## <a name="api-prerequisites"></a>API-Voraussetzungen

Die Partner Center-Übermittlungs-API erfordert einige programmgesteuerte Ressourcen:

- Eine Azure Active Directory-Anwendung
- Ein Azure Active Directory-Zugriffstoken (Azure AD)

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Schritt 1: Vollständige Voraussetzungen für die Verwendung der Partner Center-Übermittlungs-API

Bevor Sie beginnen, Code für das Aufrufen der Partner Center-Übermittlungs-API zu schreiben, sollten Sie sicherstellen, dass die folgenden Voraussetzungen erfüllt sind.

- Sie (oder Ihre Organisation) müssen über ein Azure AD-Verzeichnis verfügen, und Ihnen müssen die Berechtigungen [globaler Administrator](../active-directory/roles/permissions-reference.md) für das Verzeichnis gewährt worden sein. Wenn Sie Microsoft 365 oder andere Unternehmensdienste von Microsoft verwenden, verfügen Sie bereits über ein Azure AD-Verzeichnis. Andernfalls können Sie ohne zusätzliche Kosten [eine neue Azure AD-Instanz in Partner Center erstellen](manage-tenants.md#create-a-new-tenant).
- Sie müssen Ihrem Partner Center-Konto eine [Azure AD-Anwendung zuordnen](manage-aad-apps.md) und Ihre Mandanten-ID, die Client-ID und den Schlüssel abrufen. Sie müssen das Azure AD-Zugriffstoken abrufen, das Sie in Aufrufen an die Microsoft Store-Übermittlungs-API verwenden werden.

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>Verknüpfen einer Azure AD-Anwendung mit Ihrem Partner Center-Konto

Um die Partner Center-Übermittlungs-API zu verwenden, müssen Sie Ihrem Partner Center-Konto eine Azure AD-Anwendung zuordnen, die Mandanten-ID und die Client-ID für die Anwendung abrufen und einen Schlüssel generieren. Die Azure AD-Anwendung stellt die App oder den Dienst dar, von der bzw. dem aus Sie die Partner Center-Übermittlungs-API aufrufen möchten. Sie benötigen die Mandanten-ID, die Client-ID und den Schlüssel, um ein Azure AD-Zugriffstoken abzurufen, das an die API übergeben werden soll.

> [!NOTE]
> Sie müssen diese Aufgabe nur einmal ausführen. Nachdem Sie über die Mandanten-ID, die Client-ID und den Schlüssel verfügen, können Sie diese jederzeit wiederverwenden, wenn Sie ein neues Azure AD-Zugriffstoken erstellen müssen.

1. Verknüpfen Sie in Partner Center [das Partner Center-Konto Ihrer Organisation](manage-tenants.md) mit dem Azure AD-Verzeichnis Ihrer Organisation.

1. Fügen Sie auf der Seite **Benutzer** im Abschnitt **Kontoeinstellungen** von Partner Center die [Azure AD-Anwendung](manage-aad-apps.md) hinzu, die die App oder den Dienst darstellt, mit dem Sie auf die Übermittlungen Ihres Partner Center-Kontos zugreifen werden. Stellen Sie sicher, dass Sie dieser Anwendung die Rolle **Verwalter** zuweisen. Wenn die Anwendung noch nicht in Ihrem Azure AD-Verzeichnis vorhanden ist, [erstellen Sie in Partner Center eine neue Azure AD-Anwendung ](manage-aad-apps.md#add-new-azure-ad-applications).

1. Kehren Sie zur Seite **Benutzer** zurück, klicken Sie auf den Namen Ihrer Azure AD-Anwendung, um die Anwendungseinstellungen zu öffnen, und schreiben Sie die Werte **Mandanten-ID** und **Client-ID** auf.

1. Klicken Sie dann auf **Neuen Schlüssel hinzufügen**. Notieren Sie auf dem folgenden Bildschirm den Wert von **Schlüssel**. Nachdem Sie diese Seite verlassen haben, können Sie nicht mehr auf diese Informationen zugreifen. Weitere Informationen finden Sie unter [Verwalten von Schlüsseln für eine Azure AD-Anwendung](manage-aad-apps.md#manage-keys-for-an-azure-ad-application).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Schritt 2: Abrufen eines Azure AD-Zugriffstokens

Bevor Sie eine der Methoden in der Partner Center-Übermittlungs-API aufrufen, müssen Sie zunächst ein Azure AD-Zugriffstoken abrufen, das Sie an den **Authorization**-Header jeder Methode in der API übergeben. Ein Zugriffstoken läuft 60 Minuten nach der Ausstellung ab. Danach können Sie es aktualisieren, damit Sie es in zukünftigen Aufrufen der API verwenden können.

Um das Zugriffstoken zu erhalten, befolgen Sie die Anweisungen in [Verwenden von Clientanmeldeinformationen für Dienst-zu-Dienst-Aufrufe](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md), um eine `HTTP POST`-Anweisung an den Endpunkt `https://login.microsoftonline.com/<tenant_id>/oauth2/token` zu senden. Hier ist eine Beispielanforderung:

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

Geben Sie die Mandanten-ID, die Client-ID und den Schlüssel für die Anwendung, die Sie im vorherigen Abschnitt aus Partner Center abgerufen haben, für den Wert „tenant_id“ im POST-URI sowie die Parameter „client_id“ und „client_secret“ an. Für den Parameter resource müssen Sie `https://api.partner.microsoft.com` angeben.

### <a name="step-3-use-the-partner-center-submission-api"></a>Schritt 3: Verwenden der Partner Center-Übermittlungs-API

Da Sie nun über ein Azure AD-Zugriffstoken verfügen, können Sie in der Partner Center-Übermittlungs-API Methoden aufrufen. Zum Erstellen oder Aktualisieren von Übermittlungen werden in der Partner Center-Übermittlungs-API in der Regel mehrere Methoden in einer bestimmten Reihenfolge aufgerufen. Informationen zu den einzelnen Szenarios und der Syntax der einzelnen Methoden finden Sie in der [Erfassungs-API](https://apidocs.microsoft.com/services/partneringestion/) in Swagger.

## <a name="next-steps"></a>Nächste Schritte

- Nutzen Sie die Partner Center-Übermittlungs-API wie oben beschrieben.
