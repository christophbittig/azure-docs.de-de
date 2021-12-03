---
title: Einschränken von Azure AD-Apps auf eine Gruppe von Benutzern | Azure
titleSuffix: Microsoft identity platform
description: Es wird beschrieben, wie Sie den Zugriff auf Ihre Apps, die für Azure AD registriert sind, auf eine Gruppe von ausgewählten Benutzern beschränken.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/18/2021
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5e5c06174201fd1ef426a3b9fde3d4a54971eb19
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058163"
---
# <a name="restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Einschränken Ihrer Azure AD-App auf eine Gruppe von Benutzern in einem Azure AD-Mandanten

Anwendungen, die für einen Azure AD-Mandanten (Azure Active Directory) registriert sind, sind standardmäßig für alle Benutzer des Mandanten verfügbar, deren Authentifizierung erfolgreich war.

Ebenso können in einer [mehrinstanzenfähigen](howto-convert-app-to-be-multi-tenant.md) Anwendung alle Benutzer*innen im Azure AD-Mandanten, in dem die Anwendung bereitgestellt wird, auf die Anwendung zugreifen, nachdem sie sich erfolgreich bei ihrem jeweiligen Mandanten authentifiziert haben.

Für Mandantenadministrator*innen und Entwickler*innen ist es häufig erforderlich, dass eine Anwendung auf eine bestimmte Gruppe von Benutzer*innen beschränkt werden muss. Es gibt zwei Möglichkeiten, eine Anwendung auf eine bestimmte Gruppe von Benutzer*innen oder Sicherheitsgruppen zu beschränken:

- Entwickler*innen können gängige Autorisierungsmuster wie die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](howto-implement-rbac-for-apps.md) verwenden.
- Mandantenadministrator*innen und Entwickler*innen können das integrierte Feature von Azure AD verwenden.

## <a name="supported-app-configurations"></a>Unterstützte App-Konfigurationen

Die Option zum Beschränken einer App auf eine bestimmte Gruppe von Benutzern oder Sicherheitsgruppen in einem Mandanten funktioniert für die folgenden Arten von Anwendungen:

- Anwendungen, die für die einmalige Verbundanmeldung mit SAML-basierter Authentifizierung konfiguriert sind.
- Anwendungsproxyanwendungen, die Azure AD-Vorauthentifizierung verwenden.
- Anwendungen, die direkt auf der Azure AD-Anwendungsplattform mit Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat).

## <a name="update-the-app-to-require-user-assignment"></a>Aktualisieren der Anwendung, um eine Benutzerzuweisung zu erfordern

Um eine Anwendung so zu aktualisieren, dass eine Benutzerzuweisung erforderlich ist, müssen Sie Besitzer*in der Anwendung unter „Unternehmens-Apps“ sein, oder Ihnen muss eine der Verzeichnisrollen **Globaler Administrator**, **Anwendungsadministrator** oder **Cloudanwendungsadministrator** zugewiesen sein.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden den **Verzeichnisse + Abonnements**-Filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: im oberen Menü, um zu dem Mandanten zu wechseln, für den Sie die Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Optionen **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie die Anwendung aus, die Sie so konfigurieren möchten, dass eine Zuweisung erforderlich ist. Verwenden Sie oben im Fenster die Filter, um nach einer bestimmten Anwendung zu suchen.
1. Wählen Sie auf der Seite **Übersicht** der Anwendung unter **Verwalten** die Option **Eigenschaften** aus.
1. Suchen Sie nach der Einstellung **Benutzerzuweisung erforderlich?** , und legen Sie sie auf **Ja** fest. Wenn diese Option auf **Ja** festgelegt ist, müssen Benutzer und Dienste, die auf die Anwendung oder Dienste zugreifen möchten, zuerst für diese Anwendung zugewiesen werden, ansonsten können sie sich nicht anmelden oder kein Zugriffstoken erhalten.
1. Wählen Sie **Speichern** aus.

Wenn eine Anwendung eine Zuweisung erfordert, ist die Benutzereinwilligung für diese Anwendung nicht zulässig. Dies gilt auch dann, wenn die Benutzereinwilligung für diese App andernfalls zugelassen worden wäre. Vergewissern Sie sich, dass Sie für Apps, die eine Zuweisung erfordern, eine [mandantenweite Administratoreinwilligung erteilen](../manage-apps/grant-admin-consent.md).

## <a name="assign-the-app-to-users-and-groups"></a>Zuweisung der App an Benutzer und Gruppen

Nachdem Sie Ihre App für die Aktivierung der Benutzerzuweisung konfiguriert haben, können Sie damit beginnen, die App Benutzern und Gruppen zuzuweisen.

1. Wählen Sie unter **Verwalten** die Optionen **Benutzer und Gruppen** > **Benutzer/Gruppe hinzufügen** aus.
1. Wählen Sie die Auswahl **Benutzer** aus.

   Eine Liste mit Benutzern und Sicherheitsgruppen wird mit einem Textfeld angezeigt, mit dem bestimmte Benutzer oder Gruppen gesucht werden können. Auf diesem Bildschirm können Sie mehrere Benutzer und Gruppen auf einmal auswählen.

1. Wenn Sie die Benutzer*innen und Gruppen ausgewählt haben, wählen Sie **Auswählen** aus.
1. (Optional) Wenn Sie App-Rollen in Ihrer Anwendung definiert haben, können mit der Option **Rolle auswählen** die App-Rolle ausgewählten Benutzern und Gruppen zuweisen.
1. Wählen Sie **Zuweisen** aus, um die Zuweisungen der App an Benutzer und Gruppen abzuschließen.
1. Vergewissern Sie sich, dass die hinzugefügten Benutzer und Gruppen in der aktualisierten Liste **Benutzer und Gruppen** angezeigt werden.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu Rollen und Sicherheitsgruppen finden Sie unter:

- [Vorgehensweise: Hinzufügen von App-Rollen in Ihrer Anwendung](./howto-add-app-roles-in-azure-ad-apps.md)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=LRoc-na27l0) (Verwenden von Sicherheitsgruppen und Anwendungsrollen in Ihren Apps (Video))
- [Azure Active Directory-App-Manifest](./reference-app-manifest.md)
