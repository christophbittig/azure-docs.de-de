---
title: Onboarding in Microsoft Defender für Cloud mit PowerShell
description: Dieses Dokument führt Sie durch den Prozess der Aktivierung von Microsoft Defender für Cloud mit PowerShell-Cmdlets.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/20/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c8fb5a75cd9254023555e7765cf4678c7431bec5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100356"
---
# <a name="automate-onboarding-of-microsoft-defender-for-cloud-using-powershell"></a>Automatisieren des Onboardings von Microsoft Defender für Cloud mithilfe von PowerShell

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Sie können Ihre Azure-Workloads mithilfe des PowerShell-Moduls „Microsoft Defender für Cloud“ programmgesteuert schützen.
Mithilfe von PowerShell können Sie Tasks automatisieren und menschliche Fehler bei manuellen Aufgaben vermeiden. Dies ist vor allem bei umfangreichen Bereitstellungen mit Dutzenden Abonnements und Hunderttausenden Ressourcen praktisch, die alle von Anfang an geschützt werden müssen.

Durch das Onboarding von Microsoft Defender für Cloud mithilfe von PowerShell können Sie das Onboarding und die Verwaltung Ihrer Azure-Ressourcen programmgesteuert automatisieren sowie die erforderlichen Sicherheitskontrollen hinzufügen.

Dieser Artikel enthält ein PowerShell-Beispielskript, das zur abonnementübergreifenden Einführung von Defender für Cloud angepasst und verwendet werden kann. 

In diesem Beispiel aktivieren Sie Defender für Cloud für ein Abonnement mit der ID „d07c0080-170c-4c24-861d-9c817742786c“ und wenden die empfohlenen Einstellungen an, die ein hohes Maß an Schutz bieten. Dazu aktivieren Sie die erweiterten Sicherheitsfeatures von Microsoft Defender für Cloud, die erweiterte Funktionen für Bedrohungsschutz und Erkennung bieten:

1. Aktivieren Sie die [erweiterte Sicherheit in Microsoft Defender](enable-enhanced-security.md).
 
2. Legen Sie den Log Analytics-Arbeitsbereich fest, an den der Log Analytics-Agent die Daten sendet, die auf den mit dem Abonnement verknüpften virtuellen Computern erfasst werden. In diesem Beispiel wird ein vorhandener benutzerdefinierter Arbeitsbereich (myWorkspace) verwendet.

3. Aktivieren Sie die automatische Agent-Bereitstellung von Defender für Cloud, wodurch der [Log Analytics-Agent bereitgestellt](enable-data-collection.md#auto-provision-mma) wird.

5. Legen Sie den [CISO der Organisation als Sicherheitskontakt für Defender für Cloud-Warnungen und relevante Ereignisse](configure-email-notifications.md) fest.

6. Weisen Sie die [Standardsicherheitsrichtlinien](tutorial-security-policy.md) für Defender für Cloud zu.

## <a name="prerequisites"></a>Voraussetzungen

Diese Schritte sollten ausgeführt werden, bevor Sie die Defender für Cloud-Cmdlets ausführen:

1. Führen Sie PowerShell als Administrator aus.

1. Führen Sie die folgenden Befehle in PowerShell aus:
      
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy AllSigned
    ```

    ```powershell
    Install-Module -Name Az.Security -Force
    ```

## <a name="onboard-defender-for-cloud-using-powershell"></a>Onboarding von Defender für Cloud mithilfe von PowerShell

1. Registrieren Sie Ihre Abonnements beim Defender für Cloud-Ressourcenanbieter:

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
    ```

1. Optional: Legen Sie die Abdeckungsebene (erweiterte Sicherheitsfeatures von Microsoft Defender für Cloud aktiviert/deaktiviert) der Abonnements fest. Wenn diese Features nicht definiert werden, sind sie deaktiviert:

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Set-AzSecurityPricing -Name "default" -PricingTier "Standard"
    ```

1. Konfigurieren Sie einen Log Analytics-Arbeitsbereich, an den die Agents Daten melden. Sie benötigen einen bereits erstellten Log Analytics-Arbeitsbereich, an den die virtuellen Computer im Abonnement Daten melden können. Sie können mehrere Abonnements festlegen, die an den gleichen Arbeitsbereich Daten melden. Wird keine Angabe gemacht, wird der Standardarbeitsbereich verwendet.

    ```powershell
    Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"
    ```

1. Installation mit automatischer Bereitstellung des Log Analytics-Agents auf Ihren virtuellen Azure-Computern:
    
    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```
    
    ```powershell
    Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision
    ```

    > [!NOTE]
    > Es wird empfohlen, dass Sie die automatische Bereitstellung aktivieren, um sicherzustellen, dass Ihre virtuellen Azure-Computer von Microsoft Defender für Cloud automatisch geschützt werden.
    >

1. Optional: Es wird dringend empfohlen, dass Sie die [Sicherheitskontaktinformationen](configure-email-notifications.md) für die zu integrierenden Abonnements angeben. Die Sicherheitskontakte sind die Empfänger der von Defender für Cloud generierten Warnungen und Benachrichtigungen:

    ```powershell
    Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert
    ```

1. Weisen Sie die Standardrichtlinieninitiative für Defender für Cloud zu:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
    ```

    ```powershell
    $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Defender for Cloud Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'
    ```

Sie haben das Onboarding von Microsoft Defender für Cloud mit PowerShell erfolgreich durchgeführt.

Diese PowerShell-Cmdlets können nun mit Automatisierungsskripts verwendet werden, um Abonnements und Ressourcen programmgesteuert zu durchlaufen. Das spart Zeit und trägt dazu bei, menschliche Fehler zu vermeiden. Sie können dieses [Beispielskript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) als Referenz verwenden.




## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zum Automatisieren der Integration in Defender für Cloud mithilfe von PowerShell finden Sie im folgenden Artikel:

* [Az.Security](/powershell/module/az.security)

Weitere Informationen zu Defender für Cloud finden Sie in den folgenden Artikeln:

* [Festlegen von Sicherheitsrichtlinien in Microsoft Defender für Cloud](tutorial-security-policy.md) – Hier erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
