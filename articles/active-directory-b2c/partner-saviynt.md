---
title: Tutorial zum Konfigurieren von Saviynt mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: In diesem Tutorial konfigurieren Sie Azure Active Directory B2C mit Saviynt für die anwendungsübergreifende Integration, um die IT-Modernisierung zu optimieren und bessere Sicherheit, Governance und Compliance zu fördern. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2685570bb14cf177f0a658196aa1750f9d8e02a1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558346"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Saviynt mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure Active Directory B2C mit [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). Die Sicherheits-Manager-Plattform von Saviynt bietet die Sichtbarkeit, Sicherheit und Governance, die für moderne Unternehmen erforderlich ist, in einer einheitlichen Plattform. Saviynt umfasst Anwendungsrisiken und Governance, Infrastrukturverwaltung, Verwaltung privilegierter Konten und Risikoanalysen für Kunden.

In diesem Beispieltutorial richten Sie Saviynt ein, um präzise Zugriffssteuerung auf Grundlage der delegierten Verwaltung für Azure AD B2C-Benutzer anzubieten. Saviynt führt die folgenden Überprüfungen durch, um zu bestimmen, ob ein Benutzer dazu autorisiert ist, Azure AD B2C-Benutzer zu verwalten.

- Sicherheit auf Featureebene, um zu bestimmen, ob ein Benutzer einen spezifischen Vorgang ausführen darf (Zum Beispiel: Benutzer erstellen, Benutzer ändern, Benutzerkennwort zurücksetzen und so weiter)

- Sicherheit auf Feldebene, um zu bestimmen, ob ein Benutzer über Lese-/Schreibberechtigungen für ein spezifisches Attribut eines anderen Benutzers während Benutzerverwaltungsvorgängen verfügt (Beispielsweise kann der Helpdesk-Agent nur die Telefonnummer ändern, alle anderen Attribute sind schreibgeschützt.)

- Sicherheit auf Datenebene, um zu bestimmen, ob ein Benutzer einen bestimmten Vorgang für einen bestimmten Benutzer durchführen kann (Beispielsweise kann der Helpdesk-Administrator der Region „Vereinigtes Königreich“ nur Benutzer aus dieser Region verwalten.)

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md). Der Mandant ist mit Ihrem Azure-Abonnement verknüpft.

- Ein Saviynt-[Abonnement](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Saviynt-Integration umfasst die folgenden Komponenten:

- [Azure AD B2C:](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) Der Dienst für die Benutzeridentifizierung für Unternehmen, der benutzerdefinierte Kontrolle darüber ermöglicht, wie ihre Kunden sich registrieren, anmelden und ihre Profile verwalten.

- [Saviynt:](https://saviynt.com/integrations/azure-ad/for-b2c/) Die Identitätsgovernanceplattform, die präzise delegierte Verwaltung für die Verwaltung von Benutzerlebenszyklen und den Zugriff von Azure AD B2C-Benutzern bereitstellt.  

- [Microsoft Graph-API:](/graph/use-the-api) Diese API stellt die Schnittstellen für Saviynt bereit, um die Azure AD B2C-Benutzer und deren Zugriff in Azure AD B2C zu verwalten.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung der Saviynt-Architektur](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Ein delegierter Administrator beginnt mit der Verwaltung eines Azure AD B2C-Benutzervorgangs über Saviynt.
| 2. | Die Saviynt-Autorisierungs-Engine überprüft, ob der delegierte Administrator den spezifischen Vorgang ausführen kann.
| 3. | Die Saviynt-Autorisierungs-Engine sendet eine Erfolgs-/Fehlschlagantwort für die Autorisierung.
| 4. | Saviynt erlaubt dem delegierten Administrator das Ausführen des erforderlichen Vorgangs.
| 5. | Saviynt ruft die Microsoft Graph-API und die Benutzerattribute auf, um den Benutzer in Azure AD B2C zu verwalten.
| 6. | Die Microsoft Graph-API erstellt, ändert oder löscht den Benutzer in Azure AD B2C.
| 7. | Azure AD B2C sendet eine Erfolgs-/Fehlschlagmeldung.
| 8. | Die Microsoft Graph-API übermittelt die Antwort dann an Saviynt.

## <a name="onboard-with-saviynt"></a>Onboarding mit Saviynt

1. Kontaktieren Sie [Saviynt](https://saviynt.com/contact-us/), um ein Saviynt-Konto zu erstellen.

2. Erstellen Sie die Richtlinien für die delegierte Verwaltung, und weisen Sie Benutzer als delegierte Administratoren mit verschiedenen Rollen zu.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Konfigurieren von Azure AD B2C mit Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Erstellen einer Azure AD-Anwendung für Saviynt

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#home) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option aus.
1. Wählen Sie **App-Registrierungen** > **Neue Registrierung** aus.
1. Geben Sie unter Name einen Namen für die Anwendung ein. Verwenden Sie beispielsweise „Saviynt“ als Namen, und klicken Sie dann auf **Erstellen**.
1. Navigieren Sie zu **API-Berechtigungen**, und klicken Sie dann auf **+ Berechtigung hinzufügen**.
1. Die Seite API-Berechtigungen anfordern wird angezeigt. Wählen Sie die Registerkarte **Microsoft-APIs** aus, und wählen Sie dann **Microsoft Graph** als häufig verwendete Microsoft-API aus.
1. Fahren Sie mit der nächsten Seite fort, und klicken Sie auf **Anwendungsberechtigungen**.
1. Klicken Sie auf **Verzeichnis**, und aktivieren Sie die Kontrollkästchen **Directory.Read.All** und **Directory.ReadWrite.All**.
1. Klicken Sie auf **Berechtigungen hinzufügen**. Überprüfen Sie die hinzugefügten Berechtigungen.
1. Klicken Sie auf **Grant admin consent for Default Directory** > **Save** (Administratoreinwilligung für Standardverzeichnis gewähren > Speichern).
1. Navigieren Sie zu **Zertifikate und Geheimnisse**, und klicken Sie dann auf **+ Geheimen Clientschlüssel hinzufügen**. Geben Sie eine Beschreibung für den geheimen Clientschlüssel ein, wählen Sie die Ablaufoption aus, und klicken Sie dann auf **Hinzufügen**.
1. Der geheime Schlüssel wird generiert und im Abschnitt „Geheimer Clientschlüssel“ angezeigt. Sie müssen ihn später verwenden.

1. Navigieren Sie zur **Übersicht**, und rufen Sie die **Client-ID** und die **Mandanten-ID** ab.
1. Sie benötigen die Mandanten-ID, die Client-ID und den geheimen Clientschlüssel zum Abschließen des Setups in Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Saviynt das Löschen von Benutzern ermöglichen

In den folgenden Schritten wird erläutert, wie Sie es Saviynt ermöglichen, Benutzerlöschungen in Azure AD B2C durchzuführen.

>[!NOTE]
>[Beachten Sie die Risiken, bevor Sie Administratorrollen den Zugriff auf ein Dienstprinzipal gewähren.](../active-directory/develop/app-objects-and-service-principals.md)

1. Installieren Sie die neueste Version des PowerShell-Moduls „MSOnline“ auf einer Windows-Arbeitsstation oder einem -Server.

2. Stellen Sie eine Verbindung mit dem PowerShell-Modul „AzureAD“ her, und führen Sie die folgenden Befehle aus:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testen der Lösung

Navigieren Sie zu Ihrem Saviynt-Anwendungsmandanten, und testen Sie die Verwaltung von Benutzerlebenszyklen und den Anwendungsfall für die Zugriffsgovernance.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [Erstellen einer Web-API-Anwendung](./add-web-api-application.md)