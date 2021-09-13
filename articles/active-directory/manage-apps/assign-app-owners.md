---
title: Zuweisen von Unternehmensanwendungsbesitzern – Azure AD | Microsoft-Dokumentation
description: Besitzer zu Anwendungen in Azure Active Directory zuweisen
services: active-directory
documentationcenter: ''
author: davidmu1
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: davidmu
ms.openlocfilehash: 18649f5c597be73e9f14abf00a2df584c56df5d4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446846"
---
# <a name="assign-enterprise-application-owners"></a>Zuweisen von Unternehmensanwendungsbesitzern

Die Zuweisung von Besitzern ist eine einfache Möglichkeit, die Berechtigung zu erteilen, alle Aspekte der Azure AD-Konfiguration für eine bestimmte Anwendungsregistrierung oder Unternehmensanwendung zu verwalten. Als Besitzer kann ein Benutzer die organisationsspezifische Konfiguration der Unternehmensanwendung verwalten, z. B. die Konfiguration für einmaliges Anmelden, die Bereitstellung und Benutzerzuweisungen. Ein Besitzer kann außerdem andere Besitzer hinzufügen oder entfernen. Im Gegensatz zu globalen Administratoren können Besitzer nur die Unternehmensanwendungen verwalten, deren Besitzer sie sind.

Nur Benutzer können Besitzer von Unternehmensanwendungen sein. Gruppen können nicht als Besitzer zugewiesen werden. Besitzer können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Identität der Anwendung zu wechseln. Die Anwendung umfasst möglicherweise mehr Berechtigungen als der Besitzer, sodass dies Rechteerweiterungen im Hinblick auf den Zugriff des Besitzers als Benutzer oder Dienstprinzipal zur Folge hat. 

Beim Wechseln der Identität der Anwendung kann ein Anwendungsbesitzer abhängig von den Berechtigungen der Anwendung potenziell Benutzer oder andere Objekte erstellen oder aktualisieren. Die Besitzer der Anwendungen verfügen über dieselben Berechtigungen wie Anwendungsadministratoren, die sich jedoch auf bestimmte Anwendungen beziehen. Weitere Informationen finden Sie unter [Integrierte Azure AD-Rollen](../roles/permissions-reference.md#application-administrator). 

## <a name="assign-an-owner"></a>Zuweisen eines Besitzers

So weisen Sie einer Unternehmensanwendung einen Besitzer zu:

1. Melden Sie sich bei Ihrer [Azure AD-Organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für die Rolle **Anwendungsadministrator** oder die Rolle **Cloudanwendungsadministrator** für die Organisation berechtigt ist.
2. Wählen Sie zunächst die Option **Unternehmensanwendungen** und dann die Anwendung aus, die Sie zu einem Besitzer hinzufügen möchten.
3. Wählen Sie **Besitzer** und dann **Hinzufügen** aus, um eine Liste der Benutzerkonten zu erhalten, aus denen Sie einen Besitzer auswählen können.
4. Suchen Sie nach dem Benutzerkonto, das Sie als Besitzer der Anwendung verwenden möchten, und wählen Sie es aus.
5. Klicken Sie auf **Auswählen**, um das Benutzerkonto hinzuzufügen, das Sie als Besitzer der Anwendung verwenden möchten.

> [!NOTE]
> Wenn die Benutzereinstellung **Zugriff auf Azure AD Verwaltungsportal einschränken** auf `Yes` festgelegt ist, können Benutzer ohne Administratorrechte das Azure-Portal nicht verwenden, um die Anwendungen zu verwalten, die sie besitzen. Weitere Informationen über die Aktionen, die bei Unternehmensanwendungen ausgeführt werden können, die sich im Besitz der Benutzer befinden, finden Sie unter [Unternehmensanwendungen im Besitz des Benutzers](../fundamentals/users-default-permissions.md#owned-enterprise-applications). 

## <a name="next-steps"></a>Nächste Schritte

- [Delegieren von App-Registrierungsberechtigungen in Azure Active Directory](../roles/delegate-app-roles.md)
