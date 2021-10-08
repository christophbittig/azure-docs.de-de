---
title: Zugriffsüberprüfung für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung
description: Erfahren Sie, wie Sie in Azure Active Directory-Zugriffsüberprüfungen eine Zugriffsüberprüfung für Zugriffspakete in der Berechtigungsverwaltung durchführen.
services: active-directory
documentationCenter: ''
author: amsliu
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2021
ms.author: amsliu
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b86ddd01b155b54eaa954df2a67df907901b288
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639677"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Zugriffsüberprüfung für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

Die Azure AD-Berechtigungsverwaltung erleichtert Unternehmen die Verwaltung des Zugriffs auf Gruppen, Anwendungen und SharePoint-Websites. In diesem Artikel wird beschrieben, wie Sie als designierter Prüfer eine Zugriffsüberprüfung für andere Benutzer durchführen, die einem Zugriffspaket zugewiesen sind.

## <a name="prerequisites"></a>Voraussetzungen

Für die Überprüfung der aktiven Zugriffspaketzuweisungen der Benutzer muss der Ersteller einer Überprüfung die folgenden Voraussetzungen erfüllen:
- Azure AD Premium P2
- Globaler Administrator, Identity Governance-Administrator oder Benutzeradministrator

Weitere Informationen finden Sie unter [Lizenzanforderungen](entitlement-management-overview.md#license-requirements).

>[!NOTE]
>Der Prüfer kann eine beliebige Person sein, die vom Ersteller einer Überprüfung ausgewählt wird (Besitzer der Gruppe, Manager des Benutzers, der Benutzer selbst oder ein anderer Benutzer bzw. eine Gruppe).


## <a name="open-the-access-review"></a>Öffnen der Zugriffsüberprüfung

Führen Sie die folgenden Schritte aus, um die Zugriffsüberprüfung zu finden und zu öffnen:

1. Sie erhalten möglicherweise eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Suchen Sie nach der E-Mail, um die Zugriffsüberprüfung zu öffnen. Nachfolgend sehen Sie eine Beispiel-E-Mail, die zur Überprüfung des Zugriffs auffordert:
    
    ![E-Mail zur Zugriffsüberprüfung an Prüfer](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Klicken Sie auf den Link **Benutzerzugriff überprüfen**, um die Zugriffsüberprüfung zu öffnen. 

1. Wenn Ihnen die E-Mail nicht vorliegt, können Sie Ihre ausstehenden Zugriffsüberprüfungen anzeigen, indem Sie direkt zu https://myaccess.microsoft.com navigieren.  (Für US Government verwenden Sie stattdessen `https://myaccess.microsoft.us`.)

1. Klicken Sie auf der linken Navigationsleiste auf **Zugriffsüberprüfungen**, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen, die Ihnen zugewiesen sind.
    
    ![Auswählen von Zugriffsüberprüfungen in „Mein Zugriff“](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klicken Sie auf die Überprüfung, die Sie starten möchten.
    
    ![Auswählen der Zugriffsüberprüfung](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Durchführen der Zugriffsüberprüfung

Nachdem Sie die Zugriffsüberprüfung geöffnet haben, sehen Sie die Namen der Benutzer, für die Sie eine Überprüfung durchführen müssen. Es gibt zwei Möglichkeiten, Zugriff zu gewähren oder zu verweigern:
- Sie können einem oder mehreren Benutzern den Zugriff manuell gewähren oder verweigern.
- Sie können die Systemempfehlungen akzeptieren.

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Manuelles Genehmigen oder Verweigern des Zugriffs für einen oder mehrere Benutzer
1. Überprüfen Sie die Liste der Benutzer, und legen Sie fest, welche Benutzer weiterhin Zugriff benötigen.

    ![Liste der zu überprüfenden Benutzer](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Um den Zugriff zu genehmigen oder zu verweigern, aktivieren Sie das Optionsfeld links neben dem jeweiligen Benutzernamen.

1. Wählen Sie in der Leiste oberhalb der Benutzernamen **Genehmigen** oder **Verweigern** aus.

    ![Auswählen des Benutzers](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Wenn Sie nicht sicher sind, können Sie auf die Schaltfläche **Weiß nicht** klicken.

    Wenn Sie diese Auswahl treffen, behält der Benutzer den Zugriff, und diese Auswahl wird in die Überwachungsprotokolle geschrieben. Das Protokoll zeigt anderen Prüfern an, dass Sie die Prüfung dennoch abgeschlossen haben.

1. Sie müssen möglicherweise einen Grund für Ihre Entscheidung angeben. Geben Sie einen Grund ein, und klicken Sie auf **Übermitteln**.

    ![Genehmigen oder Verweigern des Zugriffs](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Sie können Ihre Entscheidung jederzeit vor dem Abschluss der Überprüfung ändern. Hierzu wählen Sie den Benutzer aus der Liste aus und ändern Ihre Entscheidung. Sie können beispielsweise den Zugriff für einen Benutzer genehmigen, für den der Zugriff zuvor verweigert wurde.

Wenn es mehr als einen Reviewer gibt, wird die letzte abgegebene Antwort übernommen. Stellen Sie sich z.B. vor, dass ein Administrator zwei Reviewer auswählt, Alice und Bob. Alice öffnet die Überprüfung als Erste und gewährt den Zugriff. Vor dem Abschluss der Überprüfung öffnet Bob die Überprüfung und verweigert den Zugriff. In diesem Fall wird die letzte Entscheidung – die Zugriffsverweigerung – protokolliert.

>[!NOTE]
>Wenn der Zugriff für einen Benutzer in der Überprüfung verweigert wird, wird dieser nicht sofort aus dem Zugriffspaket entfernt. Der Benutzer wird aus dem Zugriffspaket entfernt, sobald die Überprüfungsergebnisse angewendet werden, nachdem die Überprüfung geschlossen wurde. Die Überprüfung wird automatisch am Ende der Überprüfungsdauer oder früher geschlossen, wenn ein Administrator die Überprüfung manuell beendet. 

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Genehmigen oder Verweigern des Zugriffs mithilfe der systemseitig generierten Empfehlungen

Um die Zugriffsüberprüfung für mehrere Benutzer zu beschleunigen, können Sie die systemseitig generierten Empfehlungen verwenden, die mit einem einzigen Klick akzeptiert werden können. Diese Empfehlungen werden auf Grundlage der Anmeldeaktivität des Benutzers generiert.

1.  Klicken Sie in der Leiste oben auf der Seite auf **Empfehlungen akzeptieren**.
    
    ![Auswahl von „Empfehlungen akzeptieren“](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Es wird eine Zusammenfassung der empfohlenen Aktionen angezeigt.

1.  Klicken Sie auf **Übermitteln**, um die Empfehlungen zu akzeptieren.

## <a name="next-steps"></a>Nächste Schritte

- [Selbstüberprüfung von Zugriffspaketen](entitlement-management-access-reviews-self-review.md)
