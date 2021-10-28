---
title: Herunterladen einer Liste von Benutzern im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Laden Sie Benutzerdatensätze in einem Massenvorgang im Azure Admin Center in Azure Active Directory herunter.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f094bfa6f7be93631d04c3e14c91b2ad47fefa9
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985388"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Herunterladen einer Liste von Benutzern im Azure Active Directory-Portal

Azure Active Directory (Azure AD) unterstützt Massenvorgänge zum Importieren (Erstellen) von Benutzern.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Um die Liste der Benutzer aus dem Azure AD Admin Center herunterzuladen, müssen Sie als Benutzer angemeldet sein, der einer oder mehreren Administratorrollen auf Organisationsebene in Azure AD zugewiesen ist (es wird mindestens „Benutzeradministrator“ benötigt). Gasteinladender und Anwendungsentwickler werden nicht als Administratorrollen betrachtet.

## <a name="to-download-a-list-of-users"></a>So laden Sie eine Liste von Benutzern herunter

1. [Melden Sie sich bei Ihrer Azure AD-Organisation](https://aad.portal.azure.com) mit einem Benutzeradministratorkonto an.
2. Navigieren Sie zu Azure Active Directory > Benutzer. Wählen Sie dann die Benutzer aus, die Sie in den Download einbeziehen möchten, indem Sie in der linken Spalte neben den einzelnen Benutzern das Kontrollkästchen aktivieren. Hinweis: Derzeit gibt es keine Möglichkeit, alle Benutzer für den Export auszuwählen. Jeder muss einzeln ausgewählt werden.
3. Wählen Sie in Azure AD **Benutzer** > **Benutzer herunterladen** aus.
4. Wählen Sie auf der Seite **Benutzer herunterladen** die Option **Start** aus, um eine CSV-Datei mit der Auflistung von Benutzerprofileigenschaften zu erhalten. Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite „Ergebnisse von Massenvorgängen“ herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler.

   ![Wählen Sie den Speicherort aus, wo Sie die Liste der Benutzer herunterladen möchten.](./media/users-bulk-download/bulk-download.png)
   
>[!NOTE]
>Die Downloaddatei enthält die gefilterte Liste der Benutzer, basierend auf dem Bereich der angewendeten Filter.

   Enthalten sind die folgenden Benutzerattribute:

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationAlternativePhoneNumber
   - AuthenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Status überprüfen

Sie können den Status Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Grenzwerte für den Massendownloaddienst

Jede Massenaktivität zum Erstellen einer Liste von Benutzern kann bis zu einer Stunde dauern. Dies ermöglicht das Erstellen und Herunterladen einer Liste von bis zu 500.000 Benutzern.

## <a name="next-steps"></a>Nächste Schritte

- [Benutzer per Massenvorgang hinzufügen](users-bulk-add.md)
- [Massenlöschung von Benutzern](users-bulk-delete.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
