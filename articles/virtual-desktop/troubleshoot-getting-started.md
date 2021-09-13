---
title: Problembehandlung beim Feature „Erste Schritte“ von Azure Virtual Desktop
description: Hier erfahren Sie, wie Probleme mit dem Feature „Erste Schritte“ von Azure Virtual Desktop behandelt werden.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347012"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>Problembehandlung beim Feature „Erste Schritte“ von Azure Virtual Desktop

Beim Feature „Erste Schritte“ von Azure Virtual Desktop werden geschachtelte Vorlagen verwendet, um Azure-Ressourcen für die Überprüfung und Automatisierung in Azure Virtual Desktop bereitzustellen. Mit dem Feature „Erste Schritte“ werden je nachdem, ob für das Abonnement, unter dem das Feature ausgeführt wird, Active Directory Domain Services (AD DS) oder Azure Active Directory Domain Services (Azure AD DS) verwendet wird, zwei oder drei Ressourcengruppen erstellt. Alle Ressourcengruppen beginnen mit demselben benutzerdefinierten Präfix.

Wenn Sie die geschachtelten Vorlagen ausführen, werden drei Ressourcengruppen und eine Vorlage erstellt, mit der Azure Resource Manager-Ressourcen bereitgestellt werden. In der folgenden Liste sind die einzelnen Ressourcengruppen und die Vorlagen aufgeführt, die von diesen Ressourcengruppen ausgeführt werden.

Diese Vorlagen werden von der Ressourcengruppe ausgeführt, die mit „-deployment“ endet:

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>Die Vorlage „easy-button-prerequisite-user-setup-linked-template“ kann optional verwendet werden und wird nur angezeigt, wenn ein Validierungsbenutzer erstellt wurde.

Diese Vorlagen werden von der Ressourcengruppe ausgeführt, die mit „-wvd“ endet:

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

Diese Vorlagen werden von der Ressourcengruppe ausgeführt, die mit „-prerequisite“ endet:

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>Diese Ressourcengruppe kann optional verwendet werden und wird nur angezeigt, wenn Azure AD DS bzw. AD DS nicht im Abonnement enthalten ist.

## <a name="no-subscriptions"></a>Keine Abonnements

Bei diesem Problem wird beim Aufrufen des Features „Erste Schritte“ die Fehlermeldung „Keine Abonnements“ angezeigt. Dies geschieht, wenn versucht wird, das Feature ohne aktives Azure-Abonnement aufzurufen.

Sie können dieses Problem beheben, indem Sie prüfen, ob Ihr Abonnement aktiv ist oder der betreffende Benutzer über ein aktives Azure-Abonnement verfügt. Ist dies nicht der Fall, weisen Sie dem Benutzer die RBAC-Rolle „Besitzer“ für sein Abonnement zu.

## <a name="you-dont-have-permissions"></a>Sie besitzen keine Berechtigungen

Dieses Problem tritt auf, wenn Sie das Feature „Erste Schritte“ aufrufen und die Fehlermeldung „Sie besitzen keine Berechtigungen“ angezeigt wird. Diese Meldung wird angezeigt, wenn der Benutzer, der das Feature ausführt, keine Berechtigungen als Besitzer für das aktive Azure-Abonnement besitzt.

Sie können dieses Problem beheben, indem Sie sich bei einem Azure-Konto anmelden, das über Berechtigungen als Besitzer verfügt, und dann dem betreffenden Konto die RBAC-Rolle „Besitzer“ zuweisen.

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>Felder auf der Registerkarte „Virtual Machine“ (Virtueller Computer) sind abgeblendet

Dieses Problem tritt auf, wenn Sie die Registerkarte **Virtual Machine** (Virtueller Computer) aufrufen und feststellen, dass die Felder unter „Do you want users to share this machine?“ (Möchten Sie, dass Benutzer diesen Computer freigeben?) abgeblendet sind. Aufgrund dieses Problems ist es nicht möglich, den Imagetyp zu ändern, das zu verwendende Image auszuwählen oder die Größe des virtuellen Computers zu ändern.

Dieses Problem tritt auf, wenn Sie das Feature mit dem Präfix ausführen, das bereits zum Starten einer Bereitstellung verwendet wurde. Wenn mit dem Feature eine Bereitstellung erstellt wird, wird ein Objekt zur Darstellung der Bereitstellung in Azure erstellt. Bestimmte Werte im Objekt, wie das Image, werden an dieses Objekt angefügt, um zu verhindern, dass ein Image von mehreren Objekten verwendet wird.

Wenn Sie dieses Problem beheben möchten, können Sie alle Ressourcengruppen mit dem vorhandenen Präfix löschen oder ein neues Präfix verwenden.

## <a name="username-must-not-include-reserved-words"></a>Benutzernamen dürfen keine reservierten Wörter enthalten

Dieses Problem tritt auf, wenn der neue Benutzername, den Sie in das Feld eingeben, vom Feature „Erste Schritte“ nicht akzeptiert wird.

Diese Fehlermeldung wird angezeigt, weil bei Azure bestimmte Wörter in Benutzernamen für öffentliche Endpunkte nicht zulässig sind. Eine umfassende Liste mit blockierten Wörtern finden Sie unter [Fehler vom Typ „Reservierter Ressourcenname“ beheben](../azure-resource-manager/templates/error-reserved-resource-name.md).

Wenn Sie dieses Problem beheben möchten, geben Sie ein neues Wort ein oder fügen dem blockierten Wort Buchstaben hinzu, sodass es eindeutig ist. Wenn beispielsweise das Wort „admin“ blockiert ist, verwenden Sie stattdessen „AVDadmin“.

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>Der Wert muss eine Länge von 12 bis 72 Zeichen haben.

Diese Fehlermeldung wird angezeigt, wenn ein Kennwort eingegeben wird, das gemäß der vorgegebenen Anzahl von Zeichen zu lang oder zu kurz ist. Anforderungen an die Länge und Komplexität von Azure-Kennwörtern gelten sogar für Felder, die Sie später in Windows verwenden, wo weniger strenge Anforderungen gelten.

Wenn Sie dieses Problem beheben möchten, können Sie ein Konto verwenden, das den [Kennwortrichtlinien von Microsoft](https://www.microsoft.com/research/publication/password-guidance) entspricht oder für das der [Azure AD-Kennwortschutz](../active-directory/authentication/concept-password-ban-bad.md) verwendet wird.

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>Fehlermeldungen für „easy-button-prerequisite-user-setup-linked-template“

Wenn dem virtuellen AD DS-Computer, den Sie verwenden, bereits eine Erweiterung mit dem Namen „Microsoft.Powershell.DSC“ zugewiesen ist, wird eine Fehlermeldung angezeigt, die wie folgt aussieht:

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

Wenn Sie dieses Problem beheben möchten, deinstallieren Sie die Erweiterung „Microsoft.Powershell.DSC“, und führen Sie dann das Feature „Erste Schritte“ erneut aus.

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>Fehlermeldungen für „easy-button-prerequisite-job-linked-template“

Wenn eine Fehlermeldung wie diese angezeigt wird, bedeutet das, dass der Ressourcenvorgang für die Vorlage „easy-button-prerequisite-job-linked-template“ nicht erfolgreich ausgeführt wurde:

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

Stellen Sie sicher, dass dies das Problem ist, mit dem Sie es zu tun haben:

1. Wählen Sie zunächst **easy-button-prerequisite-job-linked-template** und anschließend im angezeigten Fenster mit der Fehlermeldung **OK** aus.

2. Wechseln Sie zur **Ressourcengruppe „\<prefix\>-deployment“** , und wählen Sie **resourceSetupRunbook** aus.

3. Wählen Sie den Status aus, der **Failed** (Fehler) lauten sollte.

4. Wählen Sie die Registerkarte **Exception** (Ausnahme) aus. Hier sollte eine Fehlermeldung wie die folgende angezeigt werden:
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

Es gibt derzeit keine Möglichkeit, dieses Problem endgültig zu beheben. Sie können das Problem umgehen, indem Sie das Feature „Erste Schritte“ von Azure Virtual Desktop erneut ausführen, diesmal jedoch keinen Validierungsbenutzer erstellen. Anschließend erstellen Sie neue Benutzer ausschließlich manuell.

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>Überprüfen, ob der Benutzerprinzipalname des Domänenadministrators für ein neues Profil vorhanden ist

So überprüfen Sie, ob das Problem mit der Vorlage durch die Adresse des Benutzerprinzipalnamens verursacht wird:

1. Wählen Sie **easy-button-prerequisite-job-linked-template**, und bestätigen Sie für den Schritt mit dem Fehler die Fehlermeldung.

2. Navigieren Sie zur **Ressourcengruppe „\<prefix\>-deployment“** , und klicken Sie auf **resourceSetupRunbook**.

3. Wählen Sie den Status aus, der **Failed** (Fehler) lauten sollte.

4. Wählen Sie die Registerkarte **Output** (Ausgabe) aus.

Wenn der Benutzerprinzipalname im neuen Abonnement vorhanden ist, gibt es zwei mögliche Ursachen für das Problem:

- Das Profil des Domänenadministrators wurde vom Feature „Erste Schritte“ nicht erstellt, da der Benutzer bereits vorhanden ist. Wenn Sie dieses Problem beheben möchten, führen Sie das Feature „Erste Schritte“ erneut aus, geben diesmal jedoch einen Benutzernamen ein, der im Identitätsanbieter noch nicht vorhanden ist.
- Das Profil des Validierungsbenutzers wurde vom Feature „Erste Schritte“ nicht erstellt. Wenn Sie dieses Problem beheben möchten, führen Sie das Feature „Erste Schritte“ erneut aus, erstellen diesmal jedoch keine Validierungsbenutzer. Anschließend erstellen Sie neue Benutzer ausschließlich manuell.

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>Fehlermeldungen für „easy-button-inputvalidation-job-linked-template“

Wenn ein Problem mit der Vorlage „easy-button-inputvalidation-job-linked-template“ auftritt, wird eine Fehlermeldung angezeigt, die wie folgt aussieht:

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

So stellen Sie sicher, dass dies das aufgetretene Problem ist:

1. Öffnen Sie die Ressourcengruppe „\<prefix\>-deployment“, und suchen Sie nach **inputValidationRunbook**.

2. Unter den kürzlich ausgeführten Aufträgen wird ein Auftrag mit dem Status „Failed“ (Fehler) angezeigt. Klicken Sie auf **Failed** „Fehler“.

3. Klicken Sie im Fenster **job details** (Auftragsdetails) auf **Exception** (Ausnahme).

Dieser Fehler tritt auf, wenn der eingegebene Benutzerprinzipalname des Azure-Administrators fehlerhaft ist. Wenn Sie dieses Problem beheben möchten, geben Sie den richtigen Benutzernamen und das entsprechende Kennwort ein, und versuchen es erneut.

## <a name="multiple-vmextensions-per-handler-not-supported"></a>Mehrere VMExtensions-Werte pro Handler werden nicht unterstützt

Wenn Sie das Feature „Erste Schritte“ für ein Abonnement ausführen, das über Azure AD DS oder AD DS verfügt, wird vom Feature die Erweiterung „Microsoft.Powershell.DSC“ verwendet, um Validierungsbenutzer zu erstellen und FSLogix zu konfigurieren. Allerdings kann auf virtuellen Windows-Computern in Azure immer jeweils nur ein Erweiterungstyp ausgeführt werden.

Wenn Sie versuchen, mehrere Versionen von „Microsoft.Powershell.DSC“ auszuführen, wird eine Fehlermeldung angezeigt, die wie folgt aussieht:

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

Wenn Sie dieses Problem beheben möchten, entfernen Sie vor dem Ausführen des Features „Erste Schritte“ alle derzeit ausgeführten Instanzen von „Microsoft.Powershell.DSC“ auf dem virtuellen Domänencontrollercomputer.

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>Fehler in „easy-button-prerequisitecompletion-job-linked-template“

Die Benutzergruppe für die Validierungsbenutzer befindet sich im Container „USERS“ (BENUTZER). Damit die Benutzergruppe ordnungsgemäß funktioniert, muss sie mit Azure AD synchronisiert werden. Geschieht dies nicht, wird eine Fehlermeldung angezeigt, die wie folgt aussieht:

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

Stellen Sie sicher, dass das Problem dadurch verursacht wird, dass die Gruppe der Validierungsbenutzer nicht synchronisiert wurde, indem Sie die Ressourcengruppe „\<prefix\>-prerequisites“ öffnen und nach einer Datei mit dem Namen **prerequisiteSetupCompletionRunbook** suchen. Wählen Sie das Runbook und dann **All Logs** (Alle Protokolle) aus.

So lösen Sie das Problem:

1. Aktivieren Sie für den Container „USERS“ (BENUTZER) die Synchronisierung mit Azure AD.

2. Erstellen Sie die Gruppe „AVDValidationUsers“ in einer Organisationseinheit, die mit Azure synchronisiert wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Feature „Erste Schritte“ finden Sie unter [Feature „Erste Schritte“ zum Bereitstellen von Azure Virtual Desktop](getting-started-feature.md).
