---
title: Der Schwachstellen-Scanner von Defender für Cloud für Container-Images in CI/CD-Workflows
description: Erfahren Sie, wie Sie Container-Images in CI/CD-Workflows mit Microsoft Defender nach Container-Registrierungen scannen können
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: b7b30e6702b4c2c5f899f6ea1fb584dc9cbd0927
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526152"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>Identifizieren anfälliger Containerimages in CI/CD-Workflows

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite wird erläutert, wie Sie Ihre Azure Container Registry-basierten Containerimages mit dem integrierten Sicherheitsrisikoscanner überprüfen, wenn sie als Teil Ihrer GitHub-Workflows erstellt werden.

Um den Scanner einzurichten, müssen Sie den **Microsoft Defender für Container-Registrierungen** und die CI/CD-Integration aktivieren. Wenn Ihre CI/CD-Workflows Images an Ihre Registrierungen pushen, können Sie die Ergebnisse der Registrierungsüberprüfung und eine Zusammenfassung der CI/CD-Überprüfungsergebnisse anzeigen.

Die Ergebnisse der CI/CD-Überprüfungen sind eine Erweiterung der vorhandenen Registrierungsüberprüfungsergebnisse von Qualys. Das CI/CD-Scanning von Defender für Cloud wird von [Aqua Trivy](https://github.com/aquasecurity/trivy) unterstützt.

Sie erhalten Nachverfolgbarkeitsinformationen wie den GitHub-Workflow und die GitHub-Ausführungs-URL, um die Workflows zu identifizieren, die zu anfälligen Images führen.

> [!TIP]
> Die bei einer Überprüfung Ihrer Registrierung identifizierten Sicherheitsrisiken können sich von den Ergebnissen Ihrer CI/CD-Überprüfungen unterscheiden. Ein Grund für diese Unterschiede ist, dass die Registrierungsüberprüfungen [kontinuierlich](defender-for-container-registries-introduction.md#when-are-images-scanned) ausgeführt werden, während die CI/CD-Überprüfung unmittelbar vor dem Pushen des Images in die Registrierung durch den Workflow erfolgt.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:| **Diese CI/CD-Integration befindet sich in der Vorschauphase.**<br>Sie sollten daher nur mit Workflows experimentieren, die keine Produktionsworkflows sind.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Preise:|**Microsoft Defender für Container-Registrierungen** wird wie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/) angegeben berechnet|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre Images zu scannen, wenn sie von CI/CD-Workflows in Ihre Registrierungen verschoben werden, müssen Sie **Microsoft Defender für Container-Registrierungen** im Abonnement aktiviert haben.

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>Einrichten der Sicherheitsrisikoüberprüfung Ihrer CI/CD-Workflows

So aktivieren Sie Sicherheitsrisikoüberprüfungen von Images in Ihren GitHub-Workflows:

[Schritt 1. Aktivieren Sie die CI/CD-Integration in Defender für Cloud](#step-1-enable-the-cicd-integration-in-defender-for-cloud)

[Schritt 2: Hinzufügen der erforderlichen Zeilen zu Ihrem GitHub-Workflow](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-defender-for-cloud"></a>Schritt 1: Aktivieren Sie die CI/CD-Integration in Defender für Cloud

1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.
1. Wählen Sie das relevante Abonnement aus.
1. Klicken Sie auf der Randleiste der Seite „Einstellungen“ für dieses Abonnement auf **Integrationen**.
1. Wählen Sie im angezeigten Bereich ein Application Insights-Konto aus, um die CI/CD-Überprüfungsergebnisse aus Ihrem Workflow zu pushen.
1. Kopieren Sie das Authentifizierungstoken und die Verbindungszeichenfolge in Ihren GitHub-Workflow.

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="Aktivieren der CI/CD-Integration für Sicherheitsrisikoüberprüfungen von Containerimages in Ihren GitHub-Workflows" lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > Das Authentifizierungstoken und die Verbindungszeichenfolge werden verwendet, um die erfassten Sicherheitstelemetriedaten mit Ressourcen im Abonnement zu korrelieren. Wenn Sie ungültige Werte für diese Parameter verwenden, führt dies zu verworfenen Telemetriedaten.

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>Schritt 2: Hinzufügen der erforderlichen Zeilen zum GitHub-Workflow und Durchführen einer Überprüfung

1. Aktivieren Sie in Ihrem GitHub-Workflow die CI/CD-Überprüfung wie folgt:

    > [!TIP]
    > Es wird empfohlen, wie unten gezeigt zwei Geheimnisse in Ihrem Repository zu erstellen, auf die in Ihrer YAML-Datei verwiesen wird. Die Geheimnisse können gemäß Ihren eigenen Namenskonventionen benannt werden. In diesem Beispiel wird mit **AZ_APPINSIGHTS_CONNECTION_STRING** und **AZ_SUBSCRIPTION_TOKEN** auf die Geheimnisse verwiesen.

    > [!IMPORTANT]
    >  Die Übermittlung an das Register muss vor der Veröffentlichung der Ergebnisse erfolgen.

    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. Führen Sie den Workflow aus, der das Image in die ausgewählte Containerregistrierung pusht. Sobald das Image in die Registrierung übertragen wurde, wird ein Scan der Registrierung durchgeführt, und Sie können die CI/CD-Scanergebnisse zusammen mit den Ergebnissen des Registrierungsscans in Microsoft Defender für Cloud anzeigen.

1. [Zeigen Sie die Ergebnisse der CI/CD-Überprüfung an.](#view-cicd-scan-results)

## <a name="view-cicd-scan-results"></a>Anzeigen der CI/CD-Überprüfungsergebnisse

1. Auf der Seite **Empfehlungen** können Sie sich die Ergebnisse ansehen. Wenn Probleme gefunden wurden, wird die Empfehlung **Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden** angezeigt.

    ![Empfehlung zum Behandeln von Problemen](media/monitor-container-security/acr-finding.png)

1. Wählen Sie die Empfehlung aus.

    Die Detailseite der Empfehlung wird geöffnet und zeigt zusätzliche Informationen an. Diese Informationen umfassen die Liste der Registrierungen mit anfälligen Images („Betroffene Ressourcen“) und die Schritte zum Beheben des Problems.

1. Öffnen Sie die Liste der **betroffenen Ressourcen**, und wählen Sie eine fehlerhafte Registrierung aus, um die darin enthaltenen Repositorys mit anfälligen Images anzuzeigen.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="Auswählen einer fehlerhaften Registrierung":::

    Die Detailseite der Registrierung wird geöffnet und zeigt eine Liste der betroffenen Repositorys an.

1. Wählen Sie ein bestimmtes Repository aus, um die darin enthaltenen Repositorys mit möglicherweise gefährdeten Images anzuzeigen.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="Auswählen eines fehlerhaften Repositorys":::

    Die Detailseite für das Repository wird geöffnet. Sie zeigt die möglicherweise gefährdeten Images sowie eine Bewertung des Schweregrads der Ergebnisse an.

1. Wählen Sie ein bestimmtes Image aus, um die Sicherheitsrisiken anzuzeigen.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="Auswählen eines fehlerhaften Images":::

    Die Liste der Ergebnisse für das ausgewählte Image wird geöffnet.

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="Ergebnisse der Imageüberprüfung":::

1. Weitere Informationen dazu, mit welchem GitHub-Workflow diese anfälligen Images gepusht werden, finden Sie in der Informationssprechblase:

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="CI/CD-Ergebnisse zu bestimmten GitHub-Branches und -Commits":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich genauer über [die erweiterten Schutzpläne von Microsoft Defender für Cloud](defender-for-cloud-introduction.md).
