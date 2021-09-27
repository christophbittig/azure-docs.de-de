---
title: Migrieren von Richtlinien für bedingten Zugriff mit mehrstufiger Authentifizierung (Multi-Factor Authentication, MFA) – Azure Active Directory
description: Dieser Artikel zeigt, wie Sie eine klassische Richtlinie, die eine mehrstufige Authentifizierung erfordert, in das Azure-Portal migrieren.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c11ba308a05238fc8403340cb5437191f61560
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128568358"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrieren einer klassischen Richtlinie im Azure-Portal

Dieser Artikel zeigt, wie Sie eine klassische Richtlinie migrieren, die eine **mehrstufige Authentifizierung** für eine Cloud-App erfordert. Es stellt zwar keine Voraussetzung dar, wir empfehlen Ihnen aber, [Migrieren klassischer Richtlinien in das Azure-Portal](policy-migration.md) zu lesen, bevor Sie mit der Migration Ihrer klassischen Richtlinien beginnen.

![Details zu klassischen Richtlinien, die MFA für die Salesforce-App erfordern](./media/policy-migration/33.png)

Der Migrationsvorgang besteht aus folgenden Schritten:

1. [Öffnen Sie die klassische Richtlinie](#open-a-classic-policy) zum Abrufen der Konfigurationseinstellungen.
1. Erstellen Sie eine neue Azure AD-Richtlinie für bedingten Zugriff, um Ihre klassische Richtlinie zu ersetzen. 
1. Deaktivieren Sie die klassische Richtlinie.

## <a name="open-a-classic-policy"></a>Öffnen einer klassischen Richtlinie

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Klassische Richtlinien** aus.

   ![Ansicht „Klassische Richtlinien“](./media/policy-migration-mfa/12.png)

1. Wählen Sie in der Liste der klassischen Richtlinien die Richtlinie aus, die Sie migrieren möchten. Dokumentieren Sie die Konfigurationseinstellungen, damit Sie sie mit einer neuen Richtlinie für bedingten Zugriff erneut erstellen können.

## <a name="create-a-new-conditional-access-policy"></a>Erstellen einer neuen Richtlinie für bedingten Zugriff

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie zum Erstellen einer neuen Richtlinie für bedingten Zugriff die Option **Neue Richtlinie** aus.
1. Geben Sie auf der Seite **Neu** im Textfeld **Name** einen Namen für Ihre Richtlinie ein.
1. Klicken Sie im Abschnitt **Zuweisungen** auf **Benutzer und Gruppen**.
   1. Wenn Sie alle Benutzer in der klassischen Richtlinie ausgewählt haben, klicken Sie auf **Alle Benutzer**. 
   1. Wenn Sie Gruppen in Ihrer klassischen Richtlinie ausgewählt haben, klicken Sie auf **Benutzer und Gruppen auswählen**, und wählen Sie dann die gewünschten Benutzer und Gruppen aus.
   1. Wenn Sie ausgeschlossene Gruppen haben, klicken Sie auf die Registerkarte **Ausschließen**, und wählen Sie dann die gewünschten Benutzer und Gruppen aus. 
   1. Wählen Sie **Fertig** aus.
1. Klicken Sie im Abschnitt **Zuweisung** auf **Cloud-Apps oder Aktionen**.
1. Führen Sie auf der Seite **Cloud-Apps oder Aktionen** die folgenden Schritte aus:
   1. Klicken Sie auf **Apps auswählen**.
   1. Klicken Sie auf **Auswählen**.
   1. Wählen Sie auf der Seite **Auswählen** Ihre Cloud-App aus, und klicken Sie dann auf **Auswählen**.
   1. Klicken Sie auf der Seite **Cloud-Apps** auf **Fertig**.
1. Falls **Mehrstufige Authentifizierung anfordern** ausgewählt ist:
   1. Klicken Sie im Abschnitt **Zugriffssteuerungen** auf **Gewähren**.
   1. Klicken Sie auf der Seite **Gewähren** auf **Zugriff gewähren**, und klicken Sie dann auf **Mehrstufige Authentifizierung anfordern**.
   1. Klicken Sie auf **Auswählen**.
1. Klicken Sie auf **Ein**, um Ihre Richtlinie zu aktivieren, und wählen Sie dann **Speichern** aus.

   ![Einstellung einer Richtlinie für bedingten Zugriff](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Deaktivieren der klassischen Richtlinie

Klicken Sie zum Deaktivieren Ihrer klassischen Richtlinie in der Ansicht **Details** auf **Deaktivieren**.

![Deaktivieren von klassischen Richtlinien](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen über die Migration von klassischen Richtlinien finden Sie unter [Migrieren klassischer Richtlinien in das Azure-Portal](policy-migration.md).
- [Verwenden des Modus „Nur Bericht“ für bedingten Zugriff zum Ermitteln der Auswirkungen neuer Richtlinienentscheidungen](concept-conditional-access-report-only.md)