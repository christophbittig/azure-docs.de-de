---
title: Automatisieren von Continuous Integration
description: Erfahren Sie, wie Sie Continuous Integration in Azure Data Factory mit Azure Pipelines-Releases automatisieren.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219491"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>Automatisieren von Continuous Integration mit Azure Pipelines-Releases

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Im Folgenden finden Sie eine Anleitung zum Einrichten eines Azure Pipelines-Release, mit dem die Bereitstellung einer Data Factory für mehrere Umgebungen automatisiert wird.

## <a name="requirements"></a>Requirements (Anforderungen)

-   Ein Azure-Abonnement, das mit Visual Studio Team Foundation Server oder Azure Repos verknüpft ist und für das der [Azure Resource Manager-Dienstendpunkt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager) verwendet wird

-   Eine Data Factory-Instanz mit konfigurierter Azure Repos Git-Integration

-   Ein [Azure-Schlüsseltresor](https://azure.microsoft.com/services/key-vault/) mit den Geheimnissen für jede Umgebung

## <a name="set-up-an-azure-pipelines-release"></a>Einrichten eines Azure-Pipelines-Release

1.  Öffnen Sie in [Azure DevOps](https://dev.azure.com/) das Projekt, das für Ihre Data Factory konfiguriert ist.

1.  Wählen Sie links auf der Seite die Option **Pipelines** und dann **Releases** aus.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="Auswählen von „Pipelines“ &gt; „Releases“":::

1.  Wählen Sie **Neue Pipeline** oder – falls Pipelines vorhanden sind – die Option **Neu** und dann **Neue Releasepipeline** aus.

1.  Wählen Sie die Vorlage **Leere Stufe** aus.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="Auswählen von „Leere Stufe“":::

1.  Geben Sie im Feld **Name der Stufe** den Namen der Umgebung ein.

1.  Wählen Sie **Artefakt hinzufügen** und dann das Git-Repository aus, das mit Ihrer Entwicklungs-Data Factory konfiguriert wurde. Wählen Sie den [Branch für die Veröffentlichung](source-control.md#configure-publishing-settings) des Repositorys für den **Standardbranch** aus. Dieser Branch für die Veröffentlichung lautet standardmäßig `adf_publish`. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="Hinzufügen eines Artefakts":::

1.  Fügen Sie eine Azure Resource Manager-Bereitstellungsaufgabe hinzu:

    a.  Wählen Sie in der Stufenansicht die Option **Stufenaufgaben anzeigen** aus.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="Stufenansicht":::

    b.  Erstellen Sie eine neue Aufgabe. Suchen Sie nach **AMR-Vorlagenbereitstellung**, und wählen Sie dann **Hinzufügen** aus.

    c.  Wählen Sie unter der Aufgabe „Bereitstellung“ das Abonnement, die Ressourcengruppe und den Speicherort für das Data Factory-Ziel aus. Geben Sie die Anmeldeinformationen an, falls dies erforderlich ist.

    d.  Wählen Sie in der Liste **Aktion** den Eintrag **Create or update resource group** (Ressourcengruppe erstellen oder aktualisieren) aus.

    e.  Wählen Sie neben dem Feld **Vorlage** die Schaltfläche mit den Auslassungszeichen ( **…** ) aus. Suchen Sie nach der Azure Resource Manager-Vorlage, die in Ihrem Branch für die Veröffentlichung des konfigurierten Git-Repositorys generiert wird. Suchen Sie im Ordner &lt;FactoryName&gt; von Branch „adf_publish“ nach der Datei `ARMTemplateForFactory.json`.

    f.  Wählen Sie **…** neben dem Feld **Vorlagenparameter** aus, um die Parameterdatei auszuwählen. Suchen Sie im Ordner &gt;FactoryName&lt; von Branch „adf_publish“ nach der Datei `ARMTemplateParametersForFactory.json`.

    g.  Wählen Sie **…** neben dem Feld **Vorlagenparameter überschreiben** aus, und geben Sie die gewünschten Parameterwerte für das Ziel „Data Factory“ ein. Geben Sie für Anmeldeinformationen aus Azure Key Vault den Namen des Geheimnisses in doppelten Anführungszeichen ein. Geben Sie als Wert hier beispielsweise Folgendes ein, wenn der Name des Geheimnisses „cred1“ lautet: **"$(cred1)"** .

    h. Wählen Sie unter **Bereitstellungsmodus** die Option **Inkrementell** aus.

    > [!WARNING]
    > Im vollständigen Bereitstellungsmodus werden Ressourcen, die in der Ressourcengruppe vorhanden, aber in der neuen Resource Manager-Vorlage nicht angegeben sind, **gelöscht**. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](../azure-resource-manager/templates/deployment-modes.md).

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="Data Factory-Bereitstellung in der Produktion":::

1.  Speichern Sie die Releasepipeline.

1. Wählen Sie zum Auslösen eines Release die Option **Release erstellen** aus. Informationen zum Automatisieren der Erstellung von Releases finden Sie unter [Azure DevOps-Releasetrigger](/azure/devops/pipelines/release/triggers).

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="Auswählen von „Release erstellen“":::

> [!IMPORTANT]
> In CI/CD-Szenarien muss der Typ der Integration Runtime (IR) in unterschiedlichen Umgebungen identisch sein. Wenn sich in Ihrer Entwicklungsumgebung beispielsweise eine selbstgehostete IR befindet, muss diese IR auch in anderen Umgebungen, z. B. Test- und Produktionsumgebungen, den Typ „Selbstgehostet“ aufweisen. Wenn Sie Integration Runtimes in mehreren Stufen freigeben, müssen Sie die Integration Runtimes in allen Umgebungen, z. B. Entwicklungs-, Test- und Produktionsumgebungen, als „Verknüpft selbstgehostet“ konfigurieren.

## <a name="get-secrets-from-azure-key-vault"></a>Abrufen von Geheimnissen aus Azure Key Vault

Wenn Sie Geheimnisse in einer Azure Resource Manager-Vorlage übergeben möchten, empfehlen wir Ihnen die Verwendung von Azure Key Vault mit dem Azure Pipelines-Release.

Es gibt zwei Möglichkeiten, um Geheimnisse zu verarbeiten:

- Fügen Sie die Geheimnisse der Parameterdatei hinzu. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/templates/key-vault-parameter.md).

    Erstellen Sie eine Kopie der Parameterdatei, die in den Branch für die Veröffentlichung hochgeladen wird. Legen Sie die Werte der Parameter, die Sie aus Key Vault abrufen möchten, in diesem Format fest:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Bei dieser Methode wird das Geheimnis per Pullvorgang automatisch aus dem Schlüsseltresor abgerufen.

    Die Parameterdatei muss sich auch im Branch für die Veröffentlichung befinden.

- Fügen Sie vor der im vorherigen Abschnitt beschriebenen Aufgabe für die Azure Resource Manager-Bereitstellung eine [Azure Key Vault-Aufgabe](/azure/devops/pipelines/tasks/deploy/azure-key-vault) hinzu:

    1.  Erstellen Sie auf der Registerkarte **Tasks** eine neue Aufgabe. Suchen Sie nach **Azure Key Vault**, und fügen Sie die Komponente hinzu.

    1.  Wählen Sie in der Key Vault-Aufgabe das Abonnement aus, unter dem Sie den Schlüsseltresor erstellt haben. Geben Sie bei Bedarf Anmeldeinformationen an, und wählen Sie anschließend den Schlüsseltresor aus.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="Hinzufügen einer Key Vault-Aufgabe":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Gewähren von Berechtigungen für den Azure Pipelines-Agent

Unter Umständen tritt bei der Azure Key Vault-Aufgabe ein Fehler vom Typ „Zugriff verweigert“ auf, wenn nicht die richtigen Berechtigungen festgelegt wurden. Laden Sie die Protokolle für das Release herunter, und suchen Sie nach der PS1-Datei, die den Befehl zum Erteilen von Berechtigungen für den Azure Pipelines-Agent enthält. Sie können den Befehl direkt ausführen. Alternativ können Sie die Prinzipal-ID aus der Datei kopieren und die Zugriffsrichtlinie manuell im Azure-Portal hinzufügen. Die Berechtigungen `Get` und `List` sind mindestens erforderlich.

## <a name="updating-active-triggers"></a>Aktualisieren von aktiven Triggern

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) befolgen.

>[!WARNING]
>Wenn Sie nicht die neuesten Versionen von PowerShell und des Data Factory-Moduls verwenden, treten beim Ausführen der Befehle möglicherweise Deserialisierungsfehler auf. 
>

Für die Bereitstellung kann ein Fehler auftreten, wenn Sie versuchen, aktive Trigger zu aktualisieren. Zum Aktualisieren von aktiven Triggern müssen Sie sie manuell beenden und nach der Bereitstellung wieder starten. Dies ist über eine Azure PowerShell-Aufgabe möglich:

1.  Fügen Sie auf der Registerkarte **Tasks** des Release eine **Azure PowerShell**-Aufgabe hinzu. Wählen Sie die Aufgabenversion der neuesten Azure PowerShell-Version aus. 

1.  Wählen Sie das Abonnement aus, in dem sich Ihre Factory befindet.

1.  Wählen Sie **Pfad zur Skriptdatei** als Skripttyp aus. Hierfür müssen Sie Ihr PowerShell-Skript in Ihrem Repository speichern. Das folgende PowerShell-Skript kann zum Beenden von Triggern verwendet werden:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Sie können ähnliche Schritte ausführen (mit der Funktion `Start-AzDataFactoryV2Trigger`), um die Trigger nach der Bereitstellung neu zu starten.

Das Data Factory-Team hat ein [Beispielskript für vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md) hinzugefügt. 

## <a name="next-steps"></a>Nächste Schritte

- [Continuous Integration und Continuous Delivery (CI/CD) – Übersicht](continuous-integration-delivery.md)
- [Manuelles Höherstufen einer Resource Manager-Vorlage für jede Umgebung](continuous-integration-delivery-manual-promotion.md)
- [Verwenden benutzerdefinierter Parameter mit einer Resource Manager-Vorlage](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Verknüpfte Resource Manager-Vorlagen](continuous-integration-delivery-linked-templates.md)
- [Verwenden einer Hotfix-Produktionsumgebung](continuous-integration-delivery-hotfix-environment.md)
- [Beispielskript für vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md)
