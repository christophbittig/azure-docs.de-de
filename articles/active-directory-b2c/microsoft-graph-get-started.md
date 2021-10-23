---
title: Registrieren einer Microsoft Graph-Anwendung
titleSuffix: Azure AD B2C
description: Bereiten Sie die Verwaltung von Azure AD B2C-Ressourcen mit Microsoft Graph vor, indem Sie eine Anwendung registrieren, der die erforderlichen Graph-API-Berechtigungen erteilt wurden.
services: B2C
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 48f60c6769812e28a0981329885c60995d1d0e1b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036411"
---
# <a name="register-a-microsoft-graph-application"></a>Registrieren einer Microsoft Graph-Anwendung

Mit [Microsoft Graph][ms-graph] können Sie viele der Ressourcen innerhalb Ihres Azure AD B2C-Mandanten verwalten, einschließlich der Benutzerkonten von Kunden und benutzerdefinierter Richtlinien. Durch das Schreiben von Skripts oder Anwendungen, die die [Microsoft Graph-API][ms-graph-api] aufrufen, können Sie Aufgaben zur Mandantenverwaltung wie die folgenden automatisieren:

* Migrieren eines vorhandenen Benutzerspeichers zu einem Azure AD B2C-Mandanten
* Bereitstellen benutzerdefinierter Richtlinien mit einer Azure-Pipeline in Azure DevOps und Verwalten von benutzerdefinierten Richtlinienschlüsseln
* Hosten der Benutzerregistrierung auf Ihrer eigenen Seite und Erstellen von Benutzerkonten im Hintergrund im Azure AD B2C-Verzeichnis
* Automatisieren der Anwendungsregistrierung
* Abrufen von Überwachungsprotokollen

Die folgenden Abschnitte helfen Ihnen bei der Vorbereitung auf die Verwendung der Microsoft Graph-API zum Automatisieren der Verwaltung von Ressourcen in Ihrem Azure AD B2C-Verzeichnis.

## <a name="microsoft-graph-api-interaction-modes"></a>Interaktionsmodi der Microsoft Graph-API

Es gibt zwei Kommunikationsmodi, die Sie bei der Arbeit mit der Microsoft Graph-API verwenden können, um Ressourcen in Ihrem Azure AD B2C-Mandanten zu verwalten:

* **Interaktiv:** Dieser Modus eignet sich für einmalig ausgeführte Aufgaben. Sie verwenden dabei ein Administratorkonto im B2C-Mandanten, um die Verwaltungsaufgaben auszuführen. Dieser Modus erfordert die Anmeldung eines Administrators mit seinen Anmeldeinformationen, bevor die Microsoft Graph-API aufgerufen wird.

* **Automatisiert:** Dieser Modus eignet sich für geplante oder fortlaufend ausgeführte Aufgaben. Bei dieser Methode wird ein Dienstkonto verwendet, das Sie mit den erforderlichen Berechtigungen zum Ausführen der Verwaltungsaufgaben konfigurieren. Sie erstellen das Dienstkonto in Azure AD B2C, indem Sie eine Anwendung registrieren, die von Ihren Anwendungen und Skripts zum Authentifizieren über die *Anwendungs-ID (Client-ID)* und die gewährten **OAuth 2.0-Clientanmeldeinformationen** verwendet wird. In diesem Fall ruft die Anwendung im eigenen Namen die Microsoft Graph-API auf, nicht über den Administratorbenutzer wie bei der zuvor beschriebenen interaktiven Methode.

Sie aktivieren das **automatisierte** Interaktionsszenario, indem Sie eine Anwendungsregistrierung erstellen, die in den folgenden Abschnitten gezeigt wird.

Der Flow für die Gewährung von OAuth 2.0-Clientanmeldeinformationen wird derzeit nicht direkt vom Azure AD B2C-Authentifizierungsdienst unterstützt. Sie können den Clientanmeldeinformations-Flow jedoch mithilfe von Azure AD und Microsoft Identity Platform/Tokenendpunkt für eine Anwendung in Ihrem Azure AD B2C-Mandanten einrichten. Ein Azure AD B2C-Mandant teilt sich einige Funktionen mit Azure AD-Unternehmensmandanten.

## <a name="register-management-application"></a>Registrieren von Managementanwendungen

Bevor Ihre Skripts und Anwendungen mit der [Microsoft Graph-API][ms-graph-api] interagieren können, um Azure AD B2C-Ressourcen zu verwalten, müssen Sie eine Anwendungsregistrierung in Ihrem Azure AD B2C-Mandanten erstellen, die die erforderlichen API-Berechtigungen erteilt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Zum Beispiel *managementapp1*.
1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Deaktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie die **Anwendungs-ID (Client)** , die auf der Übersichtsseite der Anwendung angezeigt wird. Sie verwenden diesen Wert in einem späteren Schritt.

## <a name="grant-api-access"></a>Gewähren des API-Zugriffs

Damit Ihre Anwendung auf Daten in Microsoft Graph zugreifen kann, erteilen Sie der registrierten Anwendung die entsprechenden [Anwendungsberechtigungen](/graph/permissions-reference). Die effektiven Berechtigungen Ihrer Anwendung entsprechen der vollen Stufe der Berechtigungen, die anhand der Berechtigung impliziert werden. Um zum Beispiel jeden Benutzer in Ihrem Azure AD B2C-Anker zu *erstellen*, zu *lesen*, zu *aktualisieren* und zu *löschen*, fügen Sie die Berechtigung **User.ReadWrite.All** hinzu. 

> [!NOTE]
> Die Berechtigung **User.ReadWrite.All** enthält nicht die Möglichkeit, Kennwörter von Benutzerkonten zu aktualisieren. Wenn Ihre Anwendung Benutzerkonten-Kennwörter aktualisieren muss, [erteilen Sie die Benutzer-Administrator-Rolle](#optional-grant-user-administrator-role). Beim Erteilen der [Benutzer-Administrator](../active-directory/roles/permissions-reference.md#user-administrator)-Rolle ist **User.ReadWrite.All** nicht erforderlich. Die Benutzer-Administrator-Rolle enthält alles, was zum Verwalten von Benutzern erforderlich ist.

Sie können Ihrer Anwendung mehrere Anwendungsberechtigungen erteilen. Wenn Ihre Anwendung z. b. auch Gruppen in Ihrem Azure AD B2C Anker verwalten muss, fügen Sie die Berechtigung **Group.ReadWrite.All** ebenfalls hinzu. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[Fakultativ] Benutzer-Administrator-Rolle erteilen

Wenn Ihre Anwendung oder Ihr Skript Benutzer löschen oder deren Kennwörter aktualisieren muss, weisen Sie der Anwendung die Rolle *Benutzer-Administrator* zu. Die [Benutzer-Administrator](../active-directory/roles/permissions-reference.md#user-administrator) Rolle verfügt über einen festgelegten Satz Berechtigungen, den Sie Ihrer Anwendung gewähren. 

Führen Sie die folgenden Schritte aus, um die *Benutzer-Administrator*-Rolle hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** den Eintrag **Rollen und Administratoren** aus.
1. Wählen Sie die Rolle **Benutzeradministrator** aus. 
1. Wählen Sie **Zuweisungen hinzufügen** aus.
1. Geben Sie im **Auswählen**-Textfeld den Namen oder die ID der Anwendung ein, die Sie zuvor registriert haben, z. B., *managementapp1*. Wählen Sie Ihre Anwendung aus, wenn sie in den Suchergebnissen angezeigt wird.
1. Wählen Sie **Hinzufügen**. Es kann einige Minuten dauern, bis die Berechtigungen vollständig verteilt sind.

## <a name="create-client-secret"></a>Erstellen eines geheimen Clientschlüssels

Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Client-Schlüssel, wenn sie ein Token anfordert. Führen Sie folgende Schritte aus, um den geheimen Clientschlüssel hinzuzufügen:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Managementanwendung registriert und ihr die erforderlichen Berechtigungen gewährt haben, können Ihre Anwendungen und Dienste (z. B. Azure Pipelines) ihre Anmeldeinformationen und Berechtigungen zum Interagieren mit der Microsoft Graph-API verwenden. 

* [Abrufen eines Zugriffstokens von Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Verwenden des Zugriffstokens zum Aufrufen von Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Von Microsoft Graph unterstützte B2C-Vorgänge](microsoft-graph-operations.md)
* [Verwalten von Azure AD B2C-Benutzerkonten mit Microsoft Graph](microsoft-graph-operations.md)
* [Abrufen von Überwachungsprotokollen mit der Azure AD-Berichterstellungs-API](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview