---
title: Aktivieren der Self-Service-Anwendungszuweisung
titleSuffix: Azure AD
description: Aktivieren des Self-Service-Anwendungszugriffs, um Benutzern die Suche ihrer eigenen Anwendungen im „Meine Apps“-Portal zu ermöglichen
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: phsignor
ms.openlocfilehash: 7bc1f981287c71d967084694c144983167a4386d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057320"
---
# <a name="enable-self-service-application-assignment-in-azure-active-directory"></a>Aktivieren der Self-Service-Anwendungszuweisung in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie den Self-Service-Anwendungszugriff mithilfe des Azure Active Directory Admin Center aktivieren.

Bevor Ihre Benutzer Anwendungen über das Portal „Meine Apps“ selbst ermitteln können, müssen Sie den **Self-Service-Anwendungszugriff** auf alle Anwendungen aktivieren, die Benutzer selbst ermitteln und für die sie den Zugriff anfordern können sollen. Diese Funktion ist für Anwendungen verfügbar, die aus dem [Azure AD-Katalog](./add-application-portal.md), [Azure AD-Anwendungsproxy](../app-proxy/application-proxy.md) oder über [Benutzer- oder Administratoreinwilligung](../develop/application-consent-experience.md) hinzugefügt wurden.

Mit dieser Funktion können Sie die folgenden Schritte ausführen:

- Sie können es Benutzern ermöglichen, Anwendungen über das [Portal „Meine Apps“](https://myapps.microsoft.com/) ohne Zutun der IT-Abteilung selbst zu ermitteln.

- Diese Benutzer einer vorkonfigurierten Gruppe hinzufügen, sodass Sie sehen können, welcher Benutzer den Zugriff angefordert hat, sowie den Zugriff aufheben und die den Benutzern zugewiesenen Rollen verwalten können

- Optional festlegen, dass eine genehmigende Person des Unternehmens Anforderungen für den Anwendungszugriff genehmigt, sodass dies nicht durch die IT-Abteilung erfolgen muss

- Optional bis zu 10 Personen konfigurieren, die den Zugriff auf eine Anwendung genehmigen können

- Sie können optional angeben, dass eine genehmigende Person aus einer Geschäftsabteilung direkt über das Portal „Meine Apps“ die Kennwörter festlegen kann, die Benutzer zum Anmelden bei der Anwendung verwenden können.

- Optional Benutzer mit Self-Service-Zuweisung automatisch direkt einer Anwendungsrolle zuweisen

## <a name="prerequisites"></a>Voraussetzungen

Zum Aktivieren von Self-Service-Anwendungszugriff brauchen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Eine Azure Active Directory Premium-Lizenz (P1 oder P2) ist erforderlich, damit Benutzer den Beitritt einer Self-Service-App anfordern und Besitzer Anforderungen genehmigen oder verweigern können. Ohne eine Azure Active Directory Premium-Lizenz können Benutzer keine Self-Service-Apps hinzufügen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivieren des Self-Service-Anwendungszugriffs, um Benutzern die Suche ihrer eigenen Anwendungen zu ermöglichen

Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Für SSO-Anwendungen mit Kennwort können Sie auch der Geschäftseinheit ermöglichen, die Anmeldeinformationen zu verwalten, die diesen Benutzern in ihrem Portal „Meine Apps“ zugewiesen wurden.

Führen Sie die folgenden Schritte aus, um den Self-Service-Anwendungszugriff auf eine Anwendung zu aktivieren:

1. Melden Sie sich als globaler Administrator am [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Azure Active Directory** aus. Wählen Sie im linken Navigationsmenü **Unternehmensanwendungen** aus.

1. Wählen Sie die Anwendung in der Liste aus. Wenn die Anwendung nicht angezeigt wird, beginnen Sie mit der Eingabe ihres Namens in das Suchfeld. Oder verwenden Sie die Filtersteuerelemente, um den Anwendungstyp, Status oder die Sichtbarkeit auszuwählen, und wählen Sie dann **Übernehmen** aus.

1. Wählen Sie im Navigationsmenü auf der linken Seite **Self-Service** aus.

1. Um den Self-Service-Anwendungszugriff auf die Anwendung zu aktivieren, legen Sie **Benutzern das Anfordern des Zugriffs auf diese Anwendung erlauben?** auf **Ja** fest.

1. Wählen Sie neben **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?** **Gruppe auswählen** aus. Wählen Sie eine Gruppe, und wählen Sie **Auswählen**. Wenn die Anforderung eines Benutzers genehmigt wird, wird er dieser Gruppe hinzugefügt. Wenn Sie die Mitgliedschaft dieser Gruppe prüfen, können Sie erkennen, wem der Zugriff auf die Anwendung über einen Self-Service-Zugriff gewährt wurde.
  
    > [!NOTE]
    > Diese Einstellung unterstützt keine Gruppen, die lokal synchronisiert werden.

1. **Optional:** Um eine Genehmigung des Unternehmens anzufordern, ehe Benutzer Zugriff erhalten, legen Sie **Genehmigung anfordern, bevor Zugriff auf diese Anwendung gewährt wird?** auf **Ja** fest.

1. **Optional: nur für Anwendungen mit einmaligem Anmelden per Kennwort:** Wenn Sie möchten, dass die genehmigenden Personen des Unternehmens die für genehmigte Benutzer an die Anwendung gesendeten Kennwörter angeben können, legen Sie **Genehmigenden Personen das Festlegen von Benutzerkennwörtern für diese Anwendung gestatten?** auf **Ja** fest.

1. **Optional:** Um die genehmigenden Personen des Unternehmens anzugeben, die den Zugriff auf die Anwendung genehmigen können, wählen Sie neben **Wer darf den Zugriff auf diese Anwendung genehmigen?** **Genehmigende Person auswählen** aus. Hier können Sie bis zu 10 genehmigende Personen des Unternehmens auswählen. Wählen Sie dann **Auswählen** aus.

    >[!NOTE]
    >Gruppen werden nicht unterstützt. Sie können bis zu 10 genehmigende Personen des Unternehmens auswählen. Wenn Sie mehrere genehmigende Personen angeben, kann jede einzelne genehmigende Person eine Zugriffsanforderung genehmigen.

1. **Optional:** Wenn Sie **für Anwendungen, die Rollen verfügbar machen**, den für den Self-Service genehmigten Benutzern eine Rolle zuweisen möchten, wählen Sie neben **Welcher Rolle sollen Benutzer in dieser Anwendung zugewiesen werden?** **Rolle auswählen** aus, und wählen Sie dann die Rolle aus, die diesen Benutzern zugewiesen werden soll. Wählen Sie dann **Auswählen** aus.

1. Wählen Sie abschließend oben im Bereich die Schaltfläche **Speichern** aus.

Nachdem Sie die Self-Service-Anwendungskonfiguration abgeschlossen haben, können Benutzer in ihrem Portal „Meine Apps“ **Self-Service-Apps hinzufügen** auswählen und die Apps suchen, die für den Self-Service-Zugriff aktiviert sind. Außerdem wird genehmigenden Personen des Unternehmens in ihrem „Meine Apps“-Portal eine Benachrichtigung angezeigt. Sie können festlegen, dass sie in einer E-Mail darüber benachrichtigt werden, dass ein Benutzer den Zugriff auf eine Anwendung angefordert hat, der zu genehmigen ist.

## <a name="next-steps"></a>Nächste Schritte

[Einrichten von Azure Active Directory zur Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md)
