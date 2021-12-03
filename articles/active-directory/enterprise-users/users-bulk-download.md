---
title: Herunterladen einer Liste von Benutzern im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Laden Sie Benutzerdatensätze in einem Massenvorgang im Azure Admin Center in Azure Active Directory herunter.
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 10/26/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfeef7e89d94b2f00d1ca1795bb9b4874858411
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402084"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Herunterladen einer Liste von Benutzern im Azure Active Directory-Portal

Azure Active Directory (Azure AD) unterstützt Massenvorgänge zum Herunterladen von Benutzerlisten.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Sowohl Administratoren als auch Benutzer ohne Administratorrechte können Benutzerlisten herunterladen.

## <a name="to-download-a-list-of-users"></a>So laden Sie eine Liste von Benutzern herunter

1. [Melden Sie sich bei Ihrer Azure AD-Organisation an](https://aad.portal.azure.com).
2. Navigieren Sie zu **Azure Active Directory** > **Benutzer**.
3. Wählen Sie in Azure AD **Benutzer** > **Benutzer herunterladen** aus. Standardmäßig werden alle Benutzerprofile exportiert.
4. Wählen Sie auf der Seite **Benutzer herunterladen** die Option **Start** aus, um eine CSV-Datei mit der Auflistung von Benutzerprofileigenschaften zu erhalten. Wenn Fehler auftreten, können Sie die Ergebnisdatei auf der Seite **Ergebnisse von Massenvorgängen** herunterladen und anzeigen. Die Datei enthält den Grund für die einzelnen Fehler.

   ![Wählen Sie den Speicherort aus, wo Sie die Liste der Benutzer herunterladen möchten.](./media/users-bulk-download/bulk-download.png)
   
>[!NOTE]
>Die Downloaddatei enthält die gefilterte Liste der Benutzer, basierend auf dem Bereich der angewendeten Filter.

   Enthalten sind die folgenden Benutzerattribute:

   - `userPrincipalName`
   - `displayName`
   - `surname`
   - `mail`
   - `givenName`
   - `objectId`
   - `userType`
   - `jobTitle`
   - `department`
   - `accountEnabled`
   - `usageLocation`
   - `streetAddress`
   - `state`
   - `country`
   - `physicalDeliveryOfficeName`
   - `city`
   - `postalCode`
   - `telephoneNumber`
   - `mobile`
   - `authenticationAlternativePhoneNumber`
   - `authenticationEmail`
   - `alternateEmailAddress`
   - `ageGroup`
   - `consentProvidedForMinor`
   - `legalAgeGroupClassification`

## <a name="check-status"></a>Status überprüfen

Sie können den Status Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Grenzwerte für den Massendownloaddienst

Jede Massenaktivität zum Exportieren einer Liste von Benutzern kann bis zu einer Stunde dauern. Diese Geschwindigkeit ermöglicht das Exportieren und Herunterladen einer Liste mit bis zu 500.000 Benutzern.

## <a name="next-steps"></a>Nächste Schritte

- [Benutzer per Massenvorgang hinzufügen](users-bulk-add.md)
- [Massenlöschung von Benutzern](users-bulk-delete.md)
- [Massenwiederherstellung von Benutzern](users-bulk-restore.md)
