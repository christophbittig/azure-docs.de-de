---
title: Verbinden von Hybridcomputern mit Azure im großen Stil
description: In diesem Artikel erfahren Sie, wie Sie Computer über Azure Arc-fähige Server unter Verwendung eines Dienstprinzipals mit Azure verbinden.
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fb78440bf03970616d3d608dcea2de1dc86681bd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356626"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Verbinden von Hybridcomputern mit Azure im großen Stil

Sie können Azure Arc-fähige Server je nach Anforderungen mithilfe verschiedener flexibler Optionen für mehrere Windows- oder Linux-Computer in Ihrer Umgebung aktivieren. Mithilfe des von uns bereitgestellten Vorlagenskripts können Sie jeden Schritt der Installation automatisieren, einschließlich des Einrichtens der Verbindung mit Azure Arc. Allerdings müssen Sie dieses Skript interaktiv mit einem Konto ausführen, das über erhöhte Berechtigungen auf dem Zielcomputer und in Azure verfügt.

Um die Computer mit Azure Arc-fähigen Servern zu verbinden, können Sie einen Azure Active Directory-[Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) verwenden, anstatt Ihre privilegierte Identität zum [interaktiven Verbinden des Computers](onboard-portal.md) zu nutzen. Bei einem Dienstprinzipal handelt es sich um eine spezielle eingeschränkte Verwaltungsidentität, der nur die Mindestberechtigung erteilt wird, die erforderlich ist, um Computer mithilfe des Befehls `azcmagent` mit Azure zu verbinden. Dies ist sicherer als die Verwendung eines Kontos mit höherer Berechtigung, wie z. B. einem Mandantenadministrator, und befolgt unsere bewährten Methoden für die Sicherheit der Zugriffssteuerung. Der Dienstprinzipal wird nur während des Onboardings verwendet und nicht für andere Zwecke.  

Die Methoden zum Installieren und Konfigurieren des Connected Machine-Agents erfordern, dass die von Ihnen verwendete automatisierte Methode über Administratorberechtigungen auf den Computern verfügt. Unter Linux muss hierfür das root-Konto verwendet werden. Unter Windows müssen Sie Mitglied der Gruppe „Lokale Administratoren“ sein.

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](agent-overview.md#prerequisites) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen auch diese Anforderungen erfüllen. Informationen zu unterstützten Regionen und andere Überlegungen finden Sie unter [Unterstützte Azure-Regionen](overview.md#supported-regions). Machen Sie sich anhand unseres Leitfadens unter [Planen und Bereitstellen von Servern mit Arc-Unterstützung](plan-at-scale-deployment.md) auch mit den Entwurfs- und Bereitstellungskriterien sowie mit unseren Verwaltungs- und Überwachungsempfehlungen vertraut.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Erstellen eines Dienstprinzipals für flexibles Onboarding

Sie können [Azure PowerShell](/powershell/azure/install-az-ps) verwenden, um einen Dienstprinzipal mit dem Cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) zu erstellen. Alternativ können Sie für diese Aufgabe auch die unter [Erstellen eines Dienstprinzipals mit dem Azure-Portal](../../active-directory/develop/howto-create-service-principal-portal.md) aufgeführten Schritte ausführen.

> [!NOTE]
> Wenn Sie einen Dienstprinzipal erstellen möchten, muss Ihr Konto der Rolle **Besitzer** oder **Benutzerzugriffsadministrator** in dem Abonnement angehören, das Sie für das Onboarding verwenden möchten. Falls Sie nicht über ausreichende Berechtigungen zum Konfigurieren von Rollenzuweisungen verfügen, wird der Dienstprinzipal zwar vielleicht erstellt, kann aber kein Onboarding für Computer durchführen.
>

Führen Sie die folgenden Schritte aus, um den Dienstprinzipal mithilfe von PowerShell zu erstellen:

1. Führen Sie den folgenden Befehl aus. Sie müssen die Ausgabe des Cmdlets [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) in einer Variablen speichern, da Sie sonst das in einem späteren Schritt benötigte Kennwort nicht abrufen können.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Führen Sie zum Abrufen des in der Variablen `$sp` gespeicherten Kennworts den folgenden Befehl aus:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Suchen Sie in der Ausgabe den Kennwortwert unter dem Feld **Kennwort**, und kopieren Sie ihn. Suchen Sie außerdem den Wert unter dem Feld **ApplicationId**, und kopieren Sie ihn ebenfalls. Speichern Sie beide zur späteren Verwendung an einem sicheren Ort. Sollten Sie Ihr Dienstprinzipalkennwort vergessen oder verlieren, können Sie es mithilfe des Cmdlets [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) zurücksetzen.

Die Werte der folgenden Eigenschaften werden mit Parametern verwendet, die an `azcmagent` übergeben werden:

* Der Wert der Eigenschaft **ApplicationId** wird für den Parameter `--service-principal-id` verwendet.
* Der Wert der Eigenschaft **password** wird für den Parameter `--service-principal-secret` verwendet, um eine Verbindung mit dem Agent herzustellen.

> [!NOTE]
> Stellen Sie sicher, dass Sie die Eigenschaft **ApplicationId** des Dienstprinzipals verwenden, nicht die Eigenschaft **Id**.
>

Die Rolle **Onboarding von Azure Connected Machine** umfasst nur die Berechtigungen, die für das Onboarding eines Computers erforderlich sind. Sie können die Dienstprinzipalberechtigung zuweisen, damit deren Umfang eine Ressourcengruppe oder ein Abonnement umfasst. Informationen zum Hinzufügen von Rollenzuweisungen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md) sowie unter [Zuweisen von Azure-Rollen mithilfe der Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generieren des Installationsskripts über das Azure-Portal

Das Skript zum Automatisieren des Download- und Installationsvorgangs sowie zum Herstellen der Verbindung mit Azure Arc ist über das Azure-Portal verfügbar. Gehen Sie für den Prozess wie folgt vor:

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

1. Wählen Sie auf der Seite **Server - Azure Arc** oben links **Hinzufügen** aus.

1. Wählen Sie auf der Seite **Methode auswählen** die Kachel zum Hinzufügen **mehrerer Server** und anschließend **Skript generieren** aus.

1. Wählen Sie auf der Seite **Skript generieren** das Abonnement und die Ressourcengruppe für die Verwaltung des Computers in Azure aus. Wählen Sie einen Azure-Standort zum Speichern der Computermetadaten aus. Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.

1. Lesen Sie sich die Informationen auf der Seite **Voraussetzungen** durch, und wählen Sie dann **Weiter: Ressourcendetails** aus.

1. Geben Sie auf der Seite **Ressourcendetails** Folgendes an:

    1. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, über die der Computer verwaltet wird.
    1. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.
    1. Wählen Sie in der Dropdownliste **Betriebssystem** das Betriebssystem aus, für das das Skript konfiguriert ist.
    1. Wenn der Computer über einen Proxyserver kommuniziert, um eine Verbindung mit dem Internet herzustellen, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die der Computer für die Kommunikation mit dem Proxyserver verwenden wird. Bei dieser Konfiguration kommuniziert der Agent mithilfe des HTTP-Protokolls über den Proxyserver. Geben Sie den Wert im Format `http://<proxyURL>:<proxyport>` ein.
    1. Wählen Sie **Weiter: Authentifizierung** aus.

1. Wählen Sie auf der Seite **Authentifizierung** in der Dropdownliste **Dienstprinzipal** die Option **Arc-for-servers** aus.  Wählen Sie anschließend **Weiter: Tags** aus.

1. Überprüfen Sie auf der Seite **Tags** die vorgeschlagenen standardmäßigen **physischen Speicherorttags**, und geben Sie einen Wert ein, oder geben Sie mindestens ein **benutzerdefiniertes Tag** an, um Ihre Standards zu unterstützen.

1. Klicken Sie auf **Weiter: Skript herunterladen und ausführen**.

1. Überprüfen Sie die auf der Seite **Skript herunterladen und ausführen** die Zusammenfassungsinformationen, und wählen Sie dann **Herunterladen** aus. Sollten noch Änderungen erforderlich sein, wählen Sie **Zurück** aus.

Sie werden aufgefordert, `OnboardingScript.ps1` (Windows) bzw. `OnboardingScript.sh` (Linux) auf Ihrem Computer zu speichern.

## <a name="install-the-agent-and-connect-to-azure"></a>Installieren des Agents und Herstellen einer Verbindung mit Azure

Mithilfe der zuvor erstellten Skriptvorlage können Sie den Connected Machine-Agent auf mehreren hybriden Linux- und Windows-Computern unter Verwendung des bevorzugten Automatisierungstools Ihrer Organisation installieren und konfigurieren. Das Skript führt ähnliche Schritte aus, wie im Artikel [Verbinden von Hybridcomputern mit Azure über das Azure-Portal](onboard-portal.md) beschrieben. Der Unterschied besteht im letzten Schritt, in dem Sie mit dem Befehl `azcmagent` unter Verwendung des Dienstprinzipals eine Verbindung mit Azure Arc herstellen.

Im Folgenden finden Sie die Einstellungen, die Sie für den Befehl `azcmagent` konfigurieren, damit dieser sie für den Dienstprinzipal verwendet.

* `service-principal-id`: Der eindeutige Bezeichner (GUID), der die Anwendungs-ID des Dienstprinzipals darstellt.
* `service-principal-secret`: Das Kennwort des Dienstprinzipals.
* `tenant-id` : Die eindeutige ID (GUID), die Ihre dedizierte Instanz von Azure AD darstellt.
* `subscription-id` : Die Abonnement-ID (GUID) Ihres Azure-Abonnements, in das Computer aufgenommen werden sollen.
* `resource-group` : Der Name der Ressourcengruppe, zu der Ihre verbundenen Computer gehören sollen.
* `location` : Siehe [Unterstützte Azure-Regionen](overview.md#supported-regions). Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.
* `resource-name` : (*Optional*) Wird für die Azure-Ressourcendarstellung Ihres lokalen Computers verwendet. Ohne Angabe dieses Werts wird der Hostname des Computers verwendet.

Weitere Informationen zum `azcmagent`-Befehlszeilentool finden Sie in der [Azcmagent-Referenz](./manage-agent.md).

>[!NOTE]
>Das Windows PowerShell-Skript unterstützt nur die Ausführung in einer 64-Bit-Version von Windows PowerShell.
>

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Zeigen Sie Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

![Erfolgreiche Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

- Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.

- Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) und vieles mehr.
