---
title: 'PowerShell-Beispiel: Auflisten von zusätzlichen Informationen zu Azure Active Directory-Anwendungsproxy-Apps'
description: Hier finden Sie ein PowerShell-Beispiel, mit dem alle Azure Active Directory-Anwendungsproxyanwendungen (Azure AD) zusammen mit der Anwendungs-ID (AppId), dem Namen (DisplayName), der externen URL (ExternalUrl), der internen URL (InternalUrl) und dem Authentifizierungstyp (ExternalAuthenticationType) aufgelistet werden.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 16fd5ac821f09363249f904b14ac836b063e1b25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988124"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Abrufen aller Anwendungsproxy-Apps und Auflisten zusätzlicher Informationen

Dieses PowerShell-Beispielskript listet Informationen zu allen Azure Active Directory-Anwendungsproxyanwendungen (Azure AD) auf, z. B. Anwendungs-ID (AppId), Name (DisplayName), externe URL (ExternalUrl), interne URL (InternalUrl), Authentifizierungstyp (ExternalAuthenticationType), SSO-Modus und weitere Einstellungen.

Wenn Sie den Wert der Variablen „$ssoMode“ ändern, ist eine gefilterte Ausgabe für den SSO-Modus möglich. Weitere Details sind im Skript dokumentiert.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Für dieses Beispiel ist das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) erforderlich.

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Erläuterung des Skripts

| Get-Help | Notizen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Ruft einen Dienstprinzipal ab. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Ruft eine Azure AD-Anwendung ab. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Ruft eine für den Anwendungsproxy in Azure AD konfigurierte Anwendung ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure AD PowerShell-Modul finden Sie in der [Übersicht über das Azure AD PowerShell-Modul](/powershell/azure/active-directory/overview).

Weitere PowerShell-Beispiele für den Anwendungsproxy finden Sie unter [Azure AD PowerShell-Beispiele für Azure AD-Anwendungsproxy](../application-proxy-powershell-samples.md).
