---
title: 'PowerShell-Beispiel: Azure Active Directory-Anwendungsproxy-Apps mit benutzerdefinierten Domänen'
description: Hier finden Sie ein PowerShell-Beispiel, das alle Anwendungsproxyanwendungen von Azure Active Directory (Azure AD) auflistet, die benutzerdefinierte Domänen verwenden, und die entsprechenden Zertifikatinformationen angibt.
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
ms.openlocfilehash: 67992ae9732a0a8658ae3d3275677f14d4e28563
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988036"
---
# <a name="get-all-application-proxy-apps-using-custom-domains-and-certificate-information"></a>Abrufen aller Anwendungsproxy-Apps, die benutzerdefinierte Domänen verwenden, und der Zertifikatinformationen

Mit diesem PowerShell-Beispielskript werden alle Anwendungsproxyanwendungen von Azure Active Directory (Azure AD) aufgelistet, die benutzerdefinierte Domänen verwenden, und die den benutzerdefinierten Domänen zugeordneten Zertifikatinformationen angegeben.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Für dieses Beispiel ist das [Azure AD PowerShell V2-Modul für Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) oder das [Azure AD PowerShell V2-Modul in der Vorschauversion für Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) erforderlich.

## <a name="sample-script"></a>Beispielskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domains-and-certs.ps1 "Get all Application Proxy apps using custom domains and certificate information")]

## <a name="script-explanation"></a>Erläuterung des Skripts

| Get-Help | Notizen |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Ruft einen Dienstprinzipal ab. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Ruft eine Azure AD-Anwendung ab. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Ruft eine für den Anwendungsproxy in Azure AD konfigurierte Anwendung ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure AD PowerShell-Modul finden Sie in der [Übersicht über das Azure AD PowerShell-Modul](/powershell/azure/active-directory/overview).

Weitere PowerShell-Beispiele für den Anwendungsproxy finden Sie unter [Azure AD PowerShell-Beispiele für Azure AD-Anwendungsproxy](../application-proxy-powershell-samples.md).
