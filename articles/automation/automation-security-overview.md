---
title: Übersicht über die Azure Automation-Kontoauthentifizierung
description: Dieser Artikel enthält eine Übersicht über die Azure Automation-Kontoauthentifizierung.
keywords: Automation-Sicherheit, sicher Automation; Automation-Authentifizierung
services: automation
ms.subservice: process-automation
ms.date: 11/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d6a509e4d99b95e2113aceb00ff1dab9a98fd91
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866656"
---
# <a name="azure-automation-account-authentication-overview"></a>Übersicht über die Azure Automation-Kontoauthentifizierung

Mit Azure Automation können Sie Aufgaben für Ressourcen in Azure, lokal und mit anderen Cloudanbietern, z.B. Amazon Web Services (AWS), automatisieren. Mit Runbooks können Sie Ihre Aufgaben automatisieren. Wenn Sie Geschäfts- oder Betriebsprozesse außerhalb von Azure verwalten müssen, verwenden Sie einen Hybrid Runbook Worker. In beiden Umgebungen werden Berechtigungen benötigt, um mit minimalen Rechten sicher auf die Ressourcen zugreifen zu können.

In diesem Artikel werden von Azure Automation unterstützte Authentifizierungsszenarien beschrieben. Außerdem werden die ersten Schritte für die Umgebungen erläutert, die Sie verwalten müssen.

## <a name="automation-account"></a>Automation-Konto

Wenn Sie das erste Mal mit Azure Automation arbeiten, müssen Sie zunächst mindestens ein Automation-Konto erstellen. Mithilfe von Automation-Konten können Sie Ihre Automation-Ressourcen, Runbooks, Objekte und Konfigurationen von den Ressourcen anderer Konten isolieren. Sie können Automation-Konten dazu verwenden, Ressourcen in separate logische Umgebungen oder delegierte Verantwortlichkeiten zu trennen. Beispielsweise können Sie ein Konto für die Entwicklung, ein Konto für die Produktion und ein Konto für Ihre lokale Umgebung nutzen. Sie können auch ein Automation-Konto zum Verwalten von Betriebssystemupdates auf allen Computern mit [Updateverwaltung](update-management/overview.md) festlegen.

Ein Azure Automation-Konto unterscheidet sich von einem Microsoft-Konto, das unter Ihrem Azure-Abonnement erstellt wird. Eine Einführung in die Erstellung eines Automation-Kontos finden Sie unter [Erstellen eines Azure Automation-Kontos](./quickstarts/create-account-portal.md).

## <a name="automation-resources"></a>Automation-Ressourcen

Die Automation-Ressourcen für jedes Automation-Konto sind zwar mit einer einzelnen Azure-Region verknüpft, mit dem Konto können jedoch alle Ressourcen in Ihrem Azure-Abonnement verwaltet werden. Automation-Konten werden in erster Linie dann in unterschiedlichen Regionen erstellt, wenn Sie über Richtlinien verfügen, die eine Isolierung von Daten und Ressourcen innerhalb einer spezifischen Region erfordern.

Alle Aufgaben, die Sie für Ressourcen mit Azure Resource Manager und den PowerShell-Cmdlets in Azure Automation erstellen, müssen gegenüber Azure mit Azure Active Directory (Azure AD) basierend auf den Anmeldeinformationen für die Organisationsidentität authentifiziert werden.

## <a name="managed-identities"></a>Verwaltete Identitäten

Durch eine verwaltete Entität aus Azure Active Directory (Azure AD) kann Ihr Runbook mühelos auf andere durch Azure AD geschützte Ressourcen zugreifen. Da die Identität von der Azure-Plattform verwaltet wird, müssen Sie keine Geheimnisse bereitstellen oder rotieren. Weitere Informationen zu verwalteten Identitäten in Azure AD finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

Verwaltete Identitäten werden zur Authentifizierung in Ihren Runbooks empfohlen und als Standardauthentifizierungsmethode für Ihr Automation-Konto eingesetzt.

> [!NOTE]
> Wenn Sie ein Automation-Konto erstellen, ist die Option zum Erstellen eines ausführenden Kontos nicht mehr verfügbar. Wir unterstützen ein ausführendes Konto jedoch weiterhin bei vorhandenen und neuen Automation-Konten. Sie können ein [ausführendes Konto](create-run-as-account.md) in Ihrem Automation-Konto über das Azure-Portal oder mithilfe von PowerShell erstellen.

Nachstehend sind einige Vorteile der Verwendung von verwalteten Identitäten beschrieben:

- Die Verwendung einer verwalteten Identität anstelle des ausführenden Automation-Kontos vereinfacht die Verwaltung. Sie müssen das vom ausführenden Konto verwendete Zertifikat nicht erneuern.

- Die Nutzung von verwalteten Identitäten verursacht keine zusätzlichen Kosten.

- Sie müssen das Verbindungsobjekt „Ausführen als“ nicht in Ihrem Runbook-Code angeben. Sie können mithilfe der verwalteten Identität Ihres Automation-Kontos über ein Runbook auf Ressourcen zugreifen, ohne Zertifikate, Verbindungen, ausführende Konten usw. zu erstellen.

Ein Automation-Konto kann sich mit zwei Arten von verwalteten Identitäten authentifizieren:

- Eine systemseitig zugewiesene Identität ist an Ihre Anwendung gebunden und wird gelöscht, wenn Ihre App gelöscht wird. Eine App kann nur über eine systemseitig zugewiesene Identität verfügen.

- Eine benutzerseitig zugewiesene Identität ist eine eigenständige Azure-Ressource, die Ihrer App zugewiesen werden kann. Eine App kann über mehrere benutzerseitig zugewiesene Identitäten verfügen.

> [!NOTE]
> Benutzerseitig zugewiesene Identitäten werden nur für Cloudaufträge unterstützt. Weitere Informationen zu den verschiedenen verwalteten Identitäten finden Sie unter [Verwalten von Identitätstypen](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Weitere Informationen zur Verwendung verwalteter Identitäten finden Sie unter [Aktivieren der verwalteten Identität für Azure Automation](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Ausführende Konten

Ausführende Konten in Azure Automation bieten Authentifizierung für die Verwaltung von Azure Resource Manager-Ressourcen oder von Ressourcen, die im klassischen Bereitstellungsmodell bereitgestellt wurden. In Azure Automation gibt es zwei Arten von ausführenden Konten:

Zum Erstellen oder Erneuern eines ausführenden Kontos sind Berechtigungen auf drei Ebenen erforderlich:

- Abonnement,
- Azure Active Directory (Azure AD) und
- Automation-Konto

> [!NOTE]
> Azure Automation erstellt das ausführende Konto nicht automatisch. Es wurde durch verwaltete Identitäten ersetzt. Wir unterstützen ein ausführendes Konto jedoch weiterhin bei vorhandenen und neuen Automation-Konten. Sie können ein [ausführendes Konto](create-run-as-account.md) in Ihrem Automation-Konto über das Azure-Portal oder mithilfe von PowerShell erstellen.

### <a name="subscription-permissions"></a>Abonnementberechtigungen

Sie benötigen die Berechtigung `Microsoft.Authorization/*/Write`. Diese Berechtigung erhalten Sie durch Mitgliedschaft bei einer der folgenden integrierten Azure-Rollen:

- [Besitzer](../role-based-access-control/built-in-roles.md#owner)
- [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator)

Zum Konfigurieren oder Erneuern klassischer ausführender Konten benötigen Sie die Rolle „Co-Administrator“ auf Abonnementebene. Weitere Informationen zu klassischen Abonnementberechtigungen finden Sie unter [Administratoren für klassische Azure-Abonnements](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

### <a name="azure-ad-permissions"></a>Azure AD-Berechtigungen

Um den Dienstprinzipal erstellen oder erneuern zu können, müssen Sie Mitglied einer der folgenden integrierten Azure AD-Rollen sein:

- [Anwendungsadministrator](../active-directory/roles/permissions-reference.md#application-administrator)
- [Anwendungsentwickler](../active-directory/roles/permissions-reference.md#application-developer)

Die Mitgliedschaft kann **ALLEN** Benutzern im Mandanten auf Verzeichnisebene zugewiesen werden, wobei es sich um das Standardverhalten handelt. Sie können die Mitgliedschaft bei jeder Rolle auf Verzeichnisebene gewähren. Weitere Informationen finden Sie unter [Wer hat die Berechtigung zum Hinzufügen von Anwendungen zu meiner Azure AD-Instanz?](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)

### <a name="automation-account-permissions"></a>Berechtigungen für das Automation-Konto

Um das Automation-Konto erstellen oder aktualisieren zu können, müssen Sie Mitglied einer der folgenden Automation-Kontorollen sein:

- [Besitzer](./automation-role-based-access-control.md#owner)
- [Mitwirkender](./automation-role-based-access-control.md#contributor)
- [Benutzerdefinierter Azure Automation-Mitwirkender](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)

Weitere Informationen zum Resource Manager-Bereitstellungsmodell und zum klassischen Bereitstellungsmodell finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Azure Cloud Solution Provider-Abonnements (Azure CSP) unterstützen nur das Azure Resource Manager-Modell. Dienste, die nicht auf Azure Resource Manager basieren, sind in diesem Programm nicht verfügbar. Wenn Sie ein CSP-Abonnement verwenden, wird kein klassisches ausführendes Azure-Konto erstellt, sondern das ausführende Azure-Konto. Weitere Informationen zu CSP-Abonnements finden Sie unter [Verfügbare Dienste in CSP-Abonnements](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Wenn Sie ein Automation-Konto erstellen, wird standardmäßig gleichzeitig ein ausführendes Konto mit einem selbstsignierten Zertifikat erstellt. Wenn Sie sich entscheiden, es nicht zusammen mit dem Automation-Konto zu erstellen, können Sie es zu einem späteren Zeitpunkt separat erstellen. Ein klassisches ausführendes Azure-Konto ist optional und wird getrennt erstellt, wenn Sie klassische Ressourcen verwalten müssen.

> [!NOTE]
> Das ausführende Konto wird nicht automatisch von Azure Automation erstellt. Es wurde durch die Nutzung verwalteter Identitäten ersetzt.

Wenn Sie anstelle des standardmäßigen selbstsignierten Zertifikats ein Zertifikat verwenden möchten, das von Ihrer Unternehmens- oder einer Drittanbieterzertifizierungsstelle ausgestellt wurde, können Sie das [PowerShell-Skript zum Erstellen eines ausführenden Kontos](create-run-as-account.md#powershell-script-to-create-a-run-as-account) für Ihr ausführendes Konto und für klassische ausführende Konten verwenden.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Ausführendes Konto

Ein ausführendes Azure-Konto wird für die folgenden Aufgaben verwendet:

* Erstellung einer Azure AD-Anwendung mit einem selbstsignierten Zertifikat, Erstellung eines Dienstprinzipalkonto für die Anwendung in Azure AD und Zuweisung der Rolle [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) für das Konto in Ihrem aktuellen Abonnement. Sie können die Zertifikateinstellung in [Leser](../role-based-access-control/built-in-roles.md#reader) oder eine beliebige andere Rolle ändern. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md).

* Erstellt ein Automation-Zertifikatasset mit dem Namen `AzureRunAsCertificate` im angegebenen Automation-Konto. Das Zertifikatasset enthält den privaten Schlüssel des Zertifikats, der von der Azure AD-Anwendung verwendet wird.

* Erstellt ein Automation-Verbindungsasset mit dem Namen `AzureRunAsConnection` im angegebenen Automation-Konto. Die Verbindungsressource enthält die Anwendungs-ID, die Mandanten-ID, die Abonnement-ID und den Zertifikatfingerabdruck.

### <a name="azure-classic-run-as-account"></a>Klassisches ausführendes Azure-Konto

Ein klassisches ausführendes Azure-Konto wird für die folgenden Aufgaben verwendet:

> [!NOTE]
> Sie müssen Co-Administrator für das Abonnement sein, um diese Art Konto zu erstellen oder zu erneuern.

* Erstellen eines Verwaltungszertifikats im Abonnement.

* Erstellen eines Automation-Zertifikatassets mit dem Namen `AzureClassicRunAsCertificate` im angegebenen Automation-Konto. Das Zertifikatasset enthält den privaten Zertifikatschlüssel, der vom Verwaltungszertifikat verwendet wird.

* Erstellen ein Automation-Verbindungsassets mit dem Namen `AzureClassicRunAsConnection` im angegebenen Automation-Konto. Das Verbindungsasset enthält den Abonnementnamen, die Abonnement-ID und den Namen des Zertifikatassets.

## <a name="service-principal-for-run-as-account"></a>Dienstprinzipal für ausführendes Konto

Der Dienstprinzipal für ein ausführendes Konto verfügt standardmäßig über keine Leseberechtigungen für Azure AD. Wenn Sie Berechtigungen zum Lesen oder Verwalten von Azure AD hinzufügen möchten, müssen Sie dem Dienstprinzipal diese Berechtigung unter **API-Berechtigungen** erteilen. Weitere Informationen finden Sie unter [Hinzufügen von Berechtigungen für den Zugriff auf Ihre Web-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Berechtigungen des ausführenden Kontos

In diesem Abschnitt werden Berechtigungen sowohl für normale ausführende Konten als auch für klassische ausführende Konten definiert.

* Ein Anwendungsadministrator in Azure Active Directory und ein Besitzer in einem Abonnement können sämtliche Aufgaben zum Erstellen oder Aktualisieren eines ausführenden Kontos erledigen.
* Zum Konfigurieren oder Erneuern klassischer ausführender Konten benötigen Sie die Rolle „Co-Administrator“ auf Abonnementebene. Weitere Informationen zu klassischen Abonnementberechtigungen finden Sie unter [Administratoren für klassische Azure-Abonnements](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

In der folgenden Tabelle werden die Aufgaben, das entsprechende Cmdlet und die erforderlichen Berechtigungen für Situationen mit Aufgabentrennung aufgeführt:

|Aufgabe|Cmdlet  |Mindestberechtigungen  |Hier legen Sie die Berechtigungen fest|
|---|---------|---------|---|
|Erstellen einer Azure AD-Anwendung|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rolle „Anwendungsentwickler“<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen |
|Hinzufügen von Anmeldeinformationen zur Anwendung|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | „Anwendungsadministrator“ oder „Globaler Administrator“<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen|
|Erstellen und Abrufen eines Azure AD-Dienstprinzipals|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | „Anwendungsadministrator“ oder „Globaler Administrator“<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > App-Registrierungen|
|Zuweisen oder Abrufen der Azure-Rolle für den angegebenen Prinzipal|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | „Benutzerzugriffsadministrator“ oder „Besitzer“, oder muss die folgenden Berechtigungen besitzen:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Start > Abonnements > \<subscription name\> -Access Control (IAM)|
|Erstellen oder Entfernen eines Automation-Zertifikats|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Mitwirkender der Ressourcengruppe         |Ressourcengruppe des Automation-Kontos|
|Erstellen oder Entfernen einer Automation-Verbindung|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Mitwirkender der Ressourcengruppe |Ressourcengruppe des Automation-Kontos|

<sup>1</sup> Benutzer Ihres Azure AD-Mandanten, die keine Administratoren sind, können [AD-Anwendungen registrieren](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app), wenn die Option **Benutzer können Anwendungen registrieren** des Azure AD-Mandanten auf der Seite **Benutzereinstellungen** auf **Ja** festgelegt ist. Wenn die Anwendungsregistrierungseinstellung **Nein** lautet, muss der Benutzer, der diese Aktion ausführt, eine der in dieser Tabelle definierten Rollen besitzen.

Wenn Sie kein Mitglied der Active Directory-Instanz des Abonnements sind, bevor Sie der Rolle „Globaler Administrator“ des Abonnements hinzugefügt werden, werden Sie als Gast hinzugefügt. In diesem Fall wird auf der Seite **„Automation-Konto hinzufügen“** die Warnung `You do not have permissions to create…` angezeigt.

So überprüfen Sie, ob die Situation, die die Fehlermeldung erzeugt, behoben wurde

1. Wählen Sie im Azure-Portal im Bereich „Azure Active Directory“ **Benutzer und Gruppen** aus.
2. Wählen Sie **Alle Benutzer**.
3. Wählen Sie Ihren Namen aus, und wählen Sie dann **Profil** aus.
4. Stellen Sie sicher, dass der Wert des Attributs **Benutzertyp** im Profil des Benutzers nicht auf **Gast** festgelegt ist.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Die rollenbasierte Zugriffssteuerung ist für Azure Resource Manager verfügbar, um einem Azure AD-Benutzerkonto und ausführenden Konto zulässige Aktionen zu gewähren und den Dienstprinzipal zu authentifizieren. Weitere Informationen zur Entwicklung Ihres Modells zum Verwalten von Automation-Berechtigungen finden Sie im Artikel [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md) .

Wenn Sie über strikte Sicherheitskontrollen für die Zuweisung von Berechtigungen in Ressourcengruppen verfügen, müssen Sie der Rolle **Mitwirkender** in der Ressourcengruppe die Mitgliedschaft beim ausführenden Konto zuweisen.

> [!NOTE]
> Wir empfehlen, die Rolle **Log Analytics-Mitwirkender** nicht zum Ausführen von Automation-Aufträgen zu verwenden. Erstellen Sie stattdessen die benutzerdefinierte Rolle „Azure Automation-Mitwirkender“, und verwenden Sie diese für Aktionen im Zusammenhang mit dem Automation-Konto. Weitere Informationen finden Sie unter der [Rolle „Benutzerdefinierter Azure Automation-Mitwirkender“](./automation-role-based-access-control.md#custom-azure-automation-contributor-role).

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbookauthentifizierung mit Hybrid Runbook Worker

Für Runbooks, die in Ihrem Rechenzentrum über einen Hybrid Runbook Worker oder für Computingdienste in anderen Cloudumgebungen wie AWS ausgeführt werden, kann nicht das gleiche Verfahren verwendet werden, das normalerweise für die Authentifizierung von Runbooks bei Azure-Ressourcen genutzt wird. Der Grund: Diese Ressourcen werden außerhalb von Azure ausgeführt, weshalb für die Authentifizierung bei Ressourcen, auf die lokal zugegriffen wird, ihre in Automation definierten Sicherheitsanmeldeinformationen verwendet werden müssen. Weitere Informationen zur Runbookauthentifizierung mit Runbook Workern finden Sie unter [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

Für Runbooks, die Hybrid Runbook Worker auf virtuellen Azure-Computern nutzen, können Sie anstelle von ausführenden Konten die [Authentifizierung von Runbooks mit verwalteten Identitäten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) für die Authentifizierung bei Ihren Azure-Ressourcen verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen eines Automation-Kontos über das Azure-Portal finden Sie unter [Erstellen eines eigenständigen Azure Automation-Kontos](automation-create-standalone-account.md).
* Wenn Sie Ihr Konto lieber mithilfe einer Vorlage erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines Automation-Kontos mithilfe einer Azure Resource Manager-Vorlage](quickstart-create-automation-account-template.md).
* Informationen zur Authentifizierung mithilfe von Amazon Web Services finden Sie unter [Authentifizieren von Azure Automation-Runbooks mit Amazon Web Services](automation-config-aws-account.md).
* Eine Liste der Azure-Dienste, die die Funktion für verwaltete Identitäten für Azure-Ressourcen unterstützen, finden Sie unter [Services that support managed identities for Azure resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen).