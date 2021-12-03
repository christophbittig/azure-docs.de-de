---
title: Ausblenden einer Unternehmensanwendung
titleSuffix: Azure AD
description: Ausblenden einer Unternehmensanwendung auf der Benutzeroberfläche in Zugriffsbereichen von Azure Active Directory oder Microsoft 365-Anwendungsstartprogrammen.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80469924b9a4bbc53ac076751026071be2955ad
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550739"
---
# <a name="hide-an-enterprise-application"></a>Ausblenden einer Unternehmensanwendung

Erfahren Sie, wie Sie Unternehmensanwendung in Azure Active Directory ausblenden. Wenn eine Anwendung ausgeblendet ist, verfügen Benutzer trotzdem über Berechtigungen für die Anwendung.

## <a name="prerequisites"></a>Voraussetzungen

- Die Rechte eines Anwendungsadministrators sind erforderlich, um eine Anwendung im Portal „Meine Apps“ und im Microsoft 365-Anwendungsstartprogramm auszublenden.

- Die Rechte eines globalen Administrators sind erforderlich, um alle Microsoft 365-Anwendungen auszublenden.

## <a name="hide-an-application-from-the-end-user"></a>Ausblenden einer Anwendung für den Endbenutzer

Führen Sie die folgenden Schritte aus, um eine Anwendung im Portal „Meine Apps“ und im Microsoft 365-Anwendungsstartprogramm auszublenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
1. Wählen Sie **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen**.
1. Wählen Sie unter **Anwendungstyp** die Option **Unternehmensanwendungen** aus, falls sie noch nicht ausgewählt ist.
1. Suchen Sie nach der Anwendung, die Sie ausblenden möchten, und wählen Sie diese aus.
1. Wählen Sie bei der Frage **Für Benutzer sichtbar?** die Option **Nein** aus.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> Diese Anweisungen gelten nur für Unternehmensanwendungen.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Ausblenden einer Anwendung mit Azure AD PowerShell

Wenn Sie eine Anwendung im Portal „Meine Apps“ ausblenden möchten, können Sie dem Dienstprinzipal für die Anwendung das HideApp-Tag manuell hinzufügen. Führen Sie die folgenden [AzureAD PowerShell](/powershell/module/azuread/#service_principals)-Befehle aus, um die Eigenschaft **Für Benutzer sichtbar?** auf **Nein** festzulegen.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>Ausblenden von Microsoft 365-Anwendungen im Portal „Meine Apps“

Mit den folgenden Schritten können Sie alle Microsoft 365-Anwendungen im Portal „Meine Apps“ ausblenden. Die Anwendungen sind im Office 365-Portal weiterhin sichtbar.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator für Ihr Verzeichnis an.
1. Wählen Sie **Azure Active Directory** aus.
1. Wählen Sie **Benutzer** aus.
1. Wählen Sie **Benutzereinstellungen** aus.
1. Wählen Sie unter **Unternehmensanwendungen** die Option **Start und Anzeige von Anwendungen durch Endbenutzer verwalten** aus.
1. Wählen Sie für **Benutzer können Office 365-Apps nur im Office 365-Portal anzeigen** die Option **Ja** aus.
1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App](./assign-user-or-group-access-portal.md)
