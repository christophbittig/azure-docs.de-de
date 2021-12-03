---
title: Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung
titleSuffix: Azure AD
description: Erfahren Sie, wie Sie eine mandantenweite Einwilligung für eine Anwendung erteilen, sodass die Endbenutzer beim Anmelden bei der Anwendung keine Einwilligung erteilen müssen.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8845643a7ef3816cb4bb3b2e25d511467d2bfcbb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546593"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung

  In diesem Artikel erfahren Sie, wie Sie einer Anwendung in Azure Active Directory (Azure AD) eine mandantenweite Administratoreinwilligung erteilen.

Wenn Sie einer Anwendung mandantenweite Administratoreinwilligung erteilen, können sich alle Benutzer bei der App anmelden. Um einzuschränken, welche Benutzer sich bei einer Anwendung anmelden können, konfigurieren Sie die Anwendung so, dass eine Benutzerzuweisung erforderlich ist, und weisen Sie der Anwendung dann Benutzer oder Gruppen zu. Weitere Informationen finden Sie unter [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md).

Durch die mandantenweite Administratoreinwilligung für eine App erhalten die App und der Herausgeber der App Zugriff auf die Daten Ihrer Organisation. Überprüfen Sie vor dem Erteilen einer Einwilligung sorgfältig die Berechtigungen, die von der Anwendung angefordert werden. Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

Durch das Erteilen der mandantenweiten Administratoreinwilligung werden möglicherweise alle Berechtigungen widerrufen, die zuvor mandantenweit erteilt wurden. Berechtigungen, die zuvor von Benutzern im eigenen Auftrag erteilt wurden, sind nicht betroffen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Erteilen einer mandantenweiten Administratoreinwilligung müssen Sie sich als Benutzer anmelden, der zum Zustimmen im Namen der Organisation autorisiert ist.

Folgendes ist zum Erteilen einer mandantenweiten Administratoreinwilligung erforderlich:

- Ein Azure-Konto mit einer der folgenden Rollen: Globaler Administrator, Administrator für privilegierte Rollen, Cloudanwendungsadministrator oder Anwendungsadministrator. Ein Benutzer kann auch für die mandantenweite Einwilligung autorisiert sein, wenn ihm eine [benutzerdefinierte Verzeichnisrolle](../roles/custom-create.md) zugewiesen ist, die die [Berechtigung zum Erteilen von Berechtigungen für Anwendungen](../roles/custom-consent-permissions.md) enthält.

## <a name="grant-tenant-wide-admin-consent-in-enterprise-apps"></a>Erteilen einer mandantenweiten Administratoreinwilligung in Enterprise-Apps

Sie können die mandantenweite Administratoreinwilligung über *Unternehmensanwendungen* erteilen, wenn die Anwendung bereits in Ihrem Mandanten bereitgestellt wurde. Eine Anwendung wurde z. B. in Ihrem Mandanten bereitgestellt, wenn mindestens ein Benutzer bereits eine Einwilligung für die Anwendung erteilt hat. Weitere Informationen finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../develop/active-directory-how-applications-are-added.md).

So erteilen Sie die mandantenweite Administratoreinwilligung für eine in **Unternehmensanwendungen** aufgeführte Anwendung:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit einer der Rollen an, die im Abschnitt „Voraussetzungen“ aufgeführt sind.
1. Wählen Sie **Azure Active Directory** und dann **Unternehmensanwendungen** aus.
1. Wählen Sie die Anwendung aus, der Sie eine mandantenweite Administratoreinwilligung erteilen möchten, und wählen Sie dann **Berechtigungen** aus.
   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="Screenshot: Erteilen der mandantenweiten Administratoreinwilligung.":::

1. Überprüfen Sie sorgfältig die Berechtigungen, die die Anwendung anfordert. **Erteilen Sie die Administratoreinwilligung**, wenn Sie mit den angeforderten Berechtigungen der Anwendung einverstanden sind.

## <a name="grant-admin-consent-in-app-registrations"></a>Erteilen der Administratoreinwilligung in App-Registrierungen

Bei Anwendungen, die Ihre Organisation entwickelt hat, oder die direkt in Ihrem Azure AD-Mandant registriert sind, können Sie auch eine mandantenweite Administratoreinwilligung in **App-Registrierungen** im Azure-Portal erteilen.

So erteilen Sie die mandantenweite Administratoreinwilligung in **App-Registrierungen**:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) mit einer der Rollen an, die im Abschnitt „Voraussetzungen“ aufgeführt sind.
1. Wählen Sie **Azure Active Directory** und dann **App-Registrierungen** aus.
1. Wählen Sie die Anwendung aus, der Sie eine mandantenweite Administratoreinwilligung erteilen möchten.
1. Wählen Sie **API-Berechtigungen anfordern** aus.
1. Überprüfen Sie sorgfältig die Berechtigungen, die die Anwendung anfordert. Wählen Sie **Administratoreinwilligung erteilen** aus, wenn Sie einverstanden sind.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Erstellen der URL zum Erteilen der mandantenweiten Administratoreinwilligung

Wenn Sie mithilfe einer der oben beschriebenen Methoden die mandantenweite Administratoreinwilligung erteilen, öffnet sich im Azure-Portal ein Fenster mit der Aufforderung zur mandantenweiten Administratoreinwilligung. Wenn Sie die Client-ID (auch Anwendungs-ID) der Anwendung kennen, können Sie die gleiche URL erstellen, um die mandantenweite Administratoreinwilligung zu erteilen.

Die URL für die mandantenweite Administratoreinwilligung verfügt über folgendes Format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

Dabei gilt:

- `{client-id}` ist die Client-ID der Anwendung, auch App-ID.
- `{tenant-id}` ist die Mandanten-ID Ihrer Organisation oder ein beliebiger verifizierter Domänenname.

Überprüfen Sie vor dem Erteilen einer Einwilligung wie immer sorgfältig die Berechtigungen, die von einer Anwendung angefordert werden.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Art der Benutzereinwilligung für eine Anwendung in Azure Active Directory](configure-user-consent.md)

[Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)
