---
title: Konfigurieren der automatischen Anmeldebeschleunigung mit der Startbereichsermittlung
titleSuffix: Azure AD
description: Erfahren Sie, wie Sie eine Richtlinie für die Startbereichsermittlung für die Azure Active Directory-Authentifizierung für Verbundbenutzer konfigurieren, einschließlich automatischer Beschleunigung und Domänenhinweisen.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: c0d182f2e0eb4fc3efe21be1c0a4e6111ad8de39
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555487"
---
# <a name="configure-sign-in-behavior"></a>Konfigurieren des Anmeldeverhaltens

Dieser Artikel enthält eine Einführung in die Konfiguration des Azure Active Directory-Authentifizierungsverhaltens (Azure AD) für Verbundbenutzer*innen unter Verwendung einer Richtlinie für die Startbereichsermittlung (Home Realm Discovery, HRD).  Er behandelt die Verwendung der automatischen Beschleunigung, durch die der Bildschirm für die Eingabe des Benutzernamens übersprungen wird und Benutzer automatisch an Endpunkte mit Verbundanmeldung weitergeleitet werden.  Microsoft empfiehlt das Konfigurieren der automatischen Beschleunigung nicht mehr, da diese die Verwendung sichererer Authentifizierungsmethoden wie FIDO (Fast Identity Online) verhindern und bei der Zusammenarbeit hinderlich sein kann.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren einer Richtlinie für die Startbereichsermittlung für eine Anwendung in Azure AD benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
- Die aktuelle Vorschauversion von Azure AD-PowerShell-Cmdlet

## <a name="set-up-an-hrd-policy-on-an-application"></a>Einrichten einer Richtlinie für die Startbereichsermittlung für eine Anwendung

Wir verwenden Azure AD PowerShell-Cmdlets, um einige Szenarien zu erläutern, darunter:

- Einrichten einer Richtlinie zur Startbereichsermittlung zum Durchführen der automatischen Beschleunigung für eine Anwendung in einem Mandanten mit einer einzigen Verbunddomäne.

- Einrichten einer Richtlinie zur Startbereichsermittlung zum Durchführen der automatischen Beschleunigung für eine Anwendung in einer von mehreren Domänen, die für Ihren Mandanten überprüft wurden.

- Einrichten einer Richtlinie für die Startbereichsermittlung, um einer Legacy-Anwendung die direkte Authentifizierung von Verbundbenutzer*innen bei Azure AD über Benutzername und Kennwort zu ermöglichen

- Auflisten der Anwendungen, für die eine Richtlinie konfiguriert ist.

In den folgenden Beispielen erstellen, aktualisieren, verknüpfen und löschen Sie Richtlinien für Anwendungsdienstprinzipale in Azure AD.

1. Führen Sie zunächst den Befehl „Connect“ aus, um sich bei Ihrem Azure AD-Administratorkonto anzumelden:

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Führen Sie den folgenden Befehl aus, um alle Richtlinien in Ihrer Organisation anzuzeigen:

    ```powershell
    Get-AzureADPolicy
    ```

Wenn nichts zurückgegeben wird, bedeutet dies, dass in Ihrem Mandanten keine Richtlinien erstellt wurden.

In diesem Beispiel erstellen Sie eine Richtlinie, die eine der folgenden Aktionen durchführt, sobald Sie einer Anwendung zugewiesen wird:

- Wenn Ihr Mandant eine einzige Domäne aufweist, werden Benutzer beim Anmelden bei einer Anwendung automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet.
- Automatisches beschleunigtes Weiterleiten von Benutzer*innen zu einem Anmeldebildschirm für die Active Directory-Verbunddienste (AD FS), wenn Ihr Mandant über mehrere Verbunddomänen verfügt
- Ermöglichen einer nicht interaktiven, direkten Anmeldung bei Azure AD über Benutzername und Kennwort für Verbundbenutzer*innen für die Anwendungen, denen die Richtlinie zugewiesen ist

## <a name="create-an-hrd-policy"></a>Erstellen einer Richtlinie zur Startbereichsermittlung

Wenn Ihr Mandant eine einzige Domäne aufweist, werden Benutzer beim Anmelden bei einer Anwendung durch die folgende Richtlinie automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Wenn Ihr Mandant über mehrere Verbunddomänen verfügt, werden Benutzer*innen durch die folgende Richtlinie automatisch beschleunigt zu einem AD FS-Anmeldebildschirm weitergeleitet. Wenn mehrere Verbunddomänen vorliegen, die Benutzer für Anwendungen authentifizieren, müssen Sie die Domäne für die automatische Beschleunigung angeben.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Führen Sie den folgenden Befehl aus, um eine Richtlinie zum Aktivieren der direkten Authentifizierung von Verbundbenutzer*innen mit Azure AD über Benutzername und Kennwort für bestimmte Anwendungen zu erstellen:

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Um Ihre neue Richtlinie und deren **ObjectId** abzurufen, führen Sie den folgenden Befehl aus:

```powershell
Get-AzureADPolicy
```

Um die Richtlinie zur Startbereichsermittlung nach ihrer Erstellung anzuwenden, können Sie sie mehreren Anwendungsdienstprinzipalen zuweisen.

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>Ermitteln des Dienstprinzipals, dem die Richtlinie zugewiesen werden soll

Sie benötigen die **ObjectID** der Dienstprinzipale, denen Sie die Richtlinie zuweisen möchten. Es gibt mehrere Möglichkeiten, die **ObjectID** von Dienstprinzipalen zu ermitteln.

Sie können das [Azure-Portal](https://portal.azure.com) verwenden oder eine Abfrage für [Microsoft Graph](/graph/api/resources/serviceprincipal) ausführen. Sie können auch zum [Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) navigieren und sich bei Ihrem Azure AD-Konto anmelden, um alle Dienstprinzipale Ihrer Organisation anzuzeigen.

Da Sie PowerShell verwenden, können Sie das folgende Cmdlet verwenden, um die Dienstprinzipale sowie deren IDs aufzulisten.

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>Zuweisen der Richtlinie zu Ihrem Dienstprinzipal

Nachdem Sie über die **ObjectID** des Dienstprinzipals der Anwendung verfügen, für die Sie automatische Beschleunigung konfigurieren möchten, führen Sie den folgenden Befehl aus. Dieser Befehl ordnet die Richtlinie zur Startbereichsermittlung, die Sie in Schritt 1 erstellt haben, dem Dienstprinzipal zu, den Sie in Schritt 2 ermittelt haben.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Sie können diesen Befehl für jeden Dienstprinzipal wiederholen, dem Sie die Richtlinie hinzufügen möchten.

Falls einer Anwendung bereits eine HomeRealmDiscovery-Richtlinie zugewiesen ist, können Sie keine zweite hinzufügen.  In diesem Fall ändern Sie die Definition der Richtlinie zur Startbereichsermittlung, die der Anwendung zugewiesen ist, und fügen Sie zusätzliche Parameter hinzu.

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Überprüfen, welchen Anwendungsdienstprinzipalen Ihre Richtlinie zur Startbereichsermittlung zugewiesen ist

Um zu überprüfen, welche Anwendungen über eine konfigurierte Richtlinie zur Startbereichsermittlung verfügen, verwenden Sie das Cmdlet **Get-AzureADPolicyAppliedObject**. Übergeben Sie ihm die **ObjectID** der Richtlinie, die Sie überprüfen möchten.

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

Notieren Sie sich die **ObjectID** der Richtlinie, für die Sie Zuordnungen auflisten möchten.

2. Auflisten der Dienstprinzipale, denen die Richtlinie zugewiesen ist

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>Entfernen einer Richtlinie zur Startbereichsermittlung aus einer Anwendung

1. Abrufen der ObjectID

Verwenden Sie das vorherige Beispiel, um die **ObjectID** der Richtlinie und die ObjectID des Anwendungsdienstprinzipals abzurufen, von dem Sie sie entfernen möchten.

2. Entfernen der Richtlinienzuordnung vom Anwendungsdienstprinzipal

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. Überprüfen der Entfernung durch Auflisten der Dienstprinzipale, denen die Richtlinie zugewiesen ist

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Funktionsweise der Startbereichsermittlung in Azure AD finden Sie unter [Startbereichsermittlung für eine Anwendung in Azure AD](home-realm-discovery-policy.md).