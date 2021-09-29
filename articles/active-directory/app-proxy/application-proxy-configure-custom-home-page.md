---
title: Benutzerdefinierte Startseite für veröffentlichte Apps – Azure Active Directory-Anwendungsproxys
description: Hier finden Sie grundlegende Informationen zu Azure Active Directory-Anwendungsproxyconnectors.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: harshja
ms.openlocfilehash: fbeadc3a8c947f673ca134e0daf8a0a1c1d68b80
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787507"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-active-directory-application-proxy"></a>Festlegen einer benutzerdefinierten Startseite für veröffentlichte Apps mithilfe eines Azure Active Directory-Anwendungsproxys

In diesem Artikel wird erläutert, wie Sie eine App so konfigurieren, dass sie Benutzer zu einer benutzerdefinierten Homepage weiterleiten. Wenn Sie eine App mit dem Anwendungsproxy veröffentlichen, legen Sie eine interne URL fest. Dies ist jedoch in einigen Fällen nicht die Seite, die den Benutzern zuerst angezeigt werden soll. Legen Sie eine benutzerdefinierte Startseite fest, sodass die Benutzer beim Zugreifen auf die App zur richtigen Seite gelangen. Benutzern wird die benutzerdefinierte Startseite angezeigt, die Sie festlegen, egal ob sie über „Meine Apps“ in Azure Active Directory oder das Microsoft 365-App-Startfeld auf die App zugreifen.

Wenn ein Benutzer die App startet, wird er standardmäßig zur Stammdomänen-URL der veröffentlichten App weitergeleitet. Die Zielseite wird in der Regel als URL der Startseite festgelegt. Definieren Sie mithilfe des Azure AD-PowerShell-Moduls eine benutzerdefinierte Homepage-URL, wenn App-Benutzer zu einer bestimmten Seite innerhalb der App gelangen sollen.

Hier ist ein Szenario, das erklärt, warum Ihr Unternehmen eine benutzerdefinierte Homepage festlegen sollte:

- In Ihrem Unternehmensnetzwerk navigiert ein Benutzer zu `https://ExpenseApp/login/login.aspx`, um sich anzumelden und auf Ihre App zuzugreifen.
- Da Sie auf oberster Ebene der Ordnerstruktur über andere Ressourcen verfügen, auf die der Anwendungsproxy zugreifen muss (z. B. Images), veröffentlichen Sie die App mit `https://ExpenseApp` als interner URL.
- Die externe Standard-URL ist `https://ExpenseApp-contoso.msappproxy.net`. Über diese gelangen die Benutzer jedoch nicht auf die Anmeldeseite.
- Sie möchten `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` als URL der Homepage festlegen, damit externen Benutzern zuerst die Anmeldeseite angezeigt wird.

> [!NOTE]
> Wenn Sie Benutzern Zugriff auf veröffentlichte Apps erteilen, werden die Apps in [Meine Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) und im [Office 365-App-Startfeld](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/) angezeigt.

## <a name="before-you-start"></a>Vorbereitung

Bevor Sie die URL der Homepage festlegen, berücksichtigen Sie die folgenden Anforderungen:

- Bei dem angegebenen Pfad muss es sich um einen Unterdomänenpfad der Stammdomänen-URL handeln.

  Wenn die URL der Stammdomäne beispielsweise `https://apps.contoso.com/app1/` lautet, muss die konfigurierte Homepage-URL mit `https://apps.contoso.com/app1/` beginnen.

- Nach einer Änderung der veröffentlichten App wird der Wert der Startseiten-URL unter Umständen zurückgesetzt. Wenn Sie die App in Zukunft aktualisieren, sollten Sie die URL der Startseite erneut überprüfen und bei Bedarf aktualisieren.

Sie können die URL der Homepage über das Azure-Portal oder mithilfe von PowerShell festlegen.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändern der Startseite im Azure-Portal

Um die URL der Homepage Ihrer App über das Azure AD-Portal zu ändern, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Administrator an.
1. Wählen Sie **Azure Active Directory** und dann **App-Registrierungen** aus. Die Liste der registrierten Apps wird angezeigt.
1. Wählen Sie Ihre App in der Liste aus. Eine Seite mit den Details der registrierten App wird angezeigt.
1. Wählen Sie unter **Verwalten** die Option **Branding** aus.
1. Aktualisieren Sie **URL der Startseite** mit dem neuen Pfad.

   ![Brandingseite für eine registrierte App mit dem Feld „URL der Startseite“](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Wählen Sie **Speichern** aus.

## <a name="change-the-home-page-with-powershell"></a>Ändern der Startseite mit PowerShell

Um die Homepage einer App über PowerShell zu konfigurieren, gehen Sie folgendermaßen vor:

1. Installieren Sie das Azure AD PowerShell-Modul.
1. Suchen Sie den Wert ObjectId der App.
1. Aktualisieren Sie die URL der Homepage für die App mit PowerShell-Befehlen.

### <a name="install-the-azure-ad-powershell-module"></a>Installieren des Azure AD PowerShell-Moduls

Bevor Sie mithilfe von PowerShell eine benutzerdefinierte Homepage-URL definieren, installieren Sie das Azure AD PowerShell-Modul.  Das entsprechende Paket können Sie aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16) herunterladen. Dieser verwendet den Graph-API-Endpunkt.

Führen Sie diese Schritte aus, um das Paket zu installieren:

1. Öffnen Sie ein standardmäßiges PowerShell-Fenster, und führen Sie dann den folgenden Befehl aus:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Wenn Sie den Befehl als Benutzer ohne Administratorrechte ausführen, verwenden Sie die Option `-scope currentuser`.

1. Wählen Sie während der Installation **J** aus, um zwei Pakete von „Nuget.org“ zu installieren. Beide Pakete sind erforderlich.

### <a name="find-the-objectid-of-the-app"></a>Ermitteln der Objekt-ID der App

Sie erhalten die Objekt-ID der App, indem Sie nach dem Anzeigenamen oder der Homepage der App suchen.

1. Importieren Sie in demselben PowerShell-Fenster das Azure AD-Modul.

   ```powershell
   Import-Module AzureAD
   ```

1. Melden Sie sich beim Azure AD-Modul als Mandantenadministrator an.

   ```powershell
   Connect-AzureAD
   ```

1. Suchen Sie die App. In diesem Beispiel wird die Objekt-ID mithilfe von PowerShell gesucht, indem nach der App mit dem Anzeigenamen `SharePoint` gesucht wird.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Das Ergebnis sieht etwa wie das hier gezeigte aus. Kopieren Sie die GUID für die Objekt-ID zur Verwendung im nächsten Abschnitt.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativ können Sie auch einfach die Liste aller Apps abrufen, die Liste nach der App mit einem bestimmten Anzeigenamen oder einer bestimmten Homepage durchsuchen und die Objekt-ID der App anschließend kopieren.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Schritt 2: Aktualisieren der URL der Startseite

Erstellen Sie die URL der Startseite, und aktualisieren Sie die App mit diesem Wert. Fahren Sie im selben PowerShell-Fenster fort, oder wenn Sie stattdessen ein neues PowerShell-Fenster verwenden, melden Sie sich mit `Connect-AzureAD` erneut beim Azure AD-Modul an. Führen Sie dann die folgenden Schritte durch:

1. Erstellen Sie eine Variable für den Objekt-ID-Wert, den Sie im vorherigen Abschnitt kopiert haben. (Ersetzen Sie den Objekt-ID-Wert in diesem SharePoint-Beispiel durch die Objekt-ID Ihrer App.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Vergewissern Sie sich, dass die richtige App vorliegt, indem Sie folgenden Befehl ausführen. Die Ausgabe sollte mit der im vorherigen Abschnitt angezeigten Ausgabe ([Ermitteln der Objekt-ID der App](#find-the-objectid-of-the-app)) übereinstimmen.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Erstellen Sie ein leeres Anwendungsobjekt, um die gewünschten Änderungen zu speichern.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Legen Sie die URL der Startseite auf den gewünschten Wert fest. Der Wert muss ein Unterdomänenpfad der veröffentlichten App sein. Wenn Sie die URL der Startseite also beispielsweise von `https://sharepoint-iddemo.msappproxy.net/` in `https://sharepoint-iddemo.msappproxy.net/hybrid/` ändern, werden App-Benutzer direkt zur benutzerdefinierten Startseite weitergeleitet.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Nehmen Sie das Update der Homepage vor.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Um zu bestätigen, dass die Änderung erfolgreich war, führen Sie den folgenden Befehl aus Schritt 2 erneut aus.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   In unserem Beispiel sollte die Ausgabe der folgenden gleichen:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Starten Sie die App neu, um zu bestätigen, dass die Homepage wie erwartet als erster Bildschirm angezeigt wird.

> [!NOTE]
> Sämtliche Änderungen, die Sie an der App vornehmen, können die URL der Startseite zurücksetzen. Wenn die URL der Startseite zurückgesetzt wird, wiederholen Sie die Schritte in diesem Abschnitt, um sie wiederherzustellen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy](./application-proxy-integrate-with-sharepoint-server.md)
- [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md)