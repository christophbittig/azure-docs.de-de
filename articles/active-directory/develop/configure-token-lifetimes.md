---
title: Festlegen der Lebensdauer von Token
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie die Gültigkeitsdauer für Token festlegen, die von Microsoft Identity Platform ausgestellt werden. Erfahren Sie, wie Sie die Standardrichtlinie einer Organisation verwalten, eine Richtlinie für die Webanmeldung erstellen, eine Richtlinie für eine native App erstellen, die eine Web-API aufruft, und eine erweiterte Richtlinie verwalten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: ac1fa8db8d153365f990c4d3eff120a254ed8096
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060629"
---
# <a name="configure-token-lifetime-policies-preview"></a>Konfigurieren von Richtlinien zur Tokenlebensdauer (Vorschau)
Sie können die Gültigkeitsdauer eines Zugriffs-, SAML- oder ID-Tokens angeben, das von Microsoft Identity Platform ausgestellt wird. Die Tokengültigkeitsdauer können Sie für alle Apps Ihrer Organisation, für eine mehrinstanzenfähige Anwendung (Multiorganisationsanwendung) oder für einen bestimmten Dienstprinzipal in Ihrer Organisation festlegen. Weitere Informationen hierzu finden Sie unter [Konfigurierbare Tokengültigkeitsdauer in Microsoft Identity Platform (Vorschau)](active-directory-configurable-token-lifetimes.md).

In diesem Abschnitt werden einige allgemeine Richtlinienszenarien beschrieben, die Ihnen ermöglichen, neue Regeln für die Tokengültigkeitsdauer vorzugeben. In diesem Beispiel erfahren Sie, wie Sie eine Richtlinie erstellen, mit der sich Benutzer häufiger für Ihre Web-App authentifizieren müssen.

## <a name="get-started"></a>Erste Schritte

Laden Sie zuerst die aktuelle [Public Preview des Azure AD PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunter.

Führen Sie als Nächstes den Befehl `Connect` aus, um sich bei Ihrem Azure AD-Administratorkonto anzumelden. Führen Sie diesen Befehl bei jedem Start einer neuen Sitzung aus.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Erstellen einer Richtlinie für die Webanmeldung

In diesem Beispiel erstellen Sie eine Richtlinie, bei der es erforderlich ist, dass sich Benutzer häufiger für Ihre Web-App authentifizieren. Diese Richtlinie legt die Gültigkeitsdauer des Zugriffs-/ID-Tokens für den Dienstprinzipal Ihrer Web-App fest.

1. Erstellen Sie eine Tokengültigkeitsdauer-Richtlinie.

    Diese Richtlinie legt die Gültigkeitsdauer des Zugriffs-/ID-Tokens für die Webanmeldung auf zwei Stunden fest.

    Um die Richtlinie zu erstellen, führen Sie das Cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um Ihre neue Richtlinie anzuzeigen und deren **ObjectId** abzurufen:

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Weisen Sie die Richtlinie Ihrem Dienstprinzipal zu. Rufen Sie außerdem die **ObjectId** Ihres Dienstprinzipals ab.

    Verwenden Sie das Cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal), um alle Dienstprinzipale Ihrer Organisation oder einen einzelnen Dienstprinzipal anzuzeigen.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Wenn Sie über den Dienstprinzipal verfügen, führen Sie das Cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) aus:

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Anzeigen vorhandener Richtlinien in einem Mandanten

Führen Sie das Cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) aus, um alle Richtlinien anzuzeigen, die in Ihrer Organisation erstellt wurden.  Alle Ergebnisse mit definierten Eigenschaftswerten, die sich von den oben aufgeführten Standardwerten unterscheiden, sind von der Einstellung betroffen.

```powershell
Get-AzureADPolicy -All $true
```

Führen Sie das im Anschluss angegebene Cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) aus, und ersetzen Sie dabei **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** durch eine Ihrer Richtlinien-IDs, um zu sehen, welche Apps und Dienstprinzipale mit einer bestimmten Richtlinie verknüpft sind, die Sie identifiziert haben. Anschließend können Sie entscheiden, ob Sie die Anmeldehäufigkeit des bedingten Zugriffs konfigurieren oder die Azure AD-Standardwerte verwenden möchten.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Wenn Ihr Mandant über Richtlinien mit benutzerdefinierten Werten für die Konfigurationen von Aktualisierungs- und Sitzungstoken verfügt, empfiehlt Microsoft, diese Richtlinien auf Werte festzulegen, die den oben beschriebenen Standardwerten entsprechen. Wenn keine Änderungen vorgenommen werden, nutzt Azure AD automatisch die Standardwerte.

### <a name="troubleshooting"></a>Problembehandlung
Einige Benutzer haben nach dem Ausführen des Cmdlets `Get-AzureADPolicy` den Fehler `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` gemeldet. Führen Sie als Problemumgehung Folgendes aus, um das AzureAD-Modul zu deinstallieren/neu zu installieren, und installieren Sie dann das AzureADPreview-Modul:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber
Note: You cannot install both the preview and the GA version on the same computer at the same time. 

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie etwas über [Funktionen für das Verwalten von Authentifizierungssitzungen](../conditional-access/howto-conditional-access-session-lifetime.md) des bedingten Zugriffs in Azure AD.
