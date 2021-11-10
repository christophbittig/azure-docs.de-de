---
title: Verwenden von Azure Key Vault-Geheimnissen in Pipelineaktivitäten
description: Erfahren Sie, wie Sie gespeicherte Anmeldeinformationen aus Azure Key Vault abrufen und während Data Factory-Pipelineausführungen verwenden.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 09a2c057cbefefc02e40d326eea094e37b092cc5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851152"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Verwenden von Azure Key Vault-Geheimnissen in Pipelineaktivitäten

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können Anmeldeinformationen oder Geheimniswerte in Azure Key Vault speichern und während der Pipelineausführung verwenden, um sie an Ihre Aktivitäten zu übergeben.

## <a name="prerequisites"></a>Voraussetzungen

Diese Funktion basiert auf der verwalteten Data Factory-Identität.  Informationen zur Funktionsweise finden Sie unter [Verwaltete Identität für Data Factory](./data-factory-service-identity.md). Stellen Sie sicher, dass Ihrer Data Factory eine verwaltete Identität zugeordnet ist.

## <a name="steps"></a>Schritte

1. Öffnen Sie die Eigenschaften Ihrer Data Factory, und kopieren Sie den Wert für die Anwendungs-ID der verwalteten Identität.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="Wert der verwalteten Identität":::

2. Öffnen Sie die Key Vault-Zugriffsrichtlinien, und fügen Sie die Berechtigungen für die verwaltete Identität zum Abrufen und Auflisten von Geheimnissen hinzu.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="Screenshot, der die Seite &quot;Zugangsrichtlinien&quot; mit der hervorgehobenen Aktion &quot;Zugangsrichtlinie hinzufügen&quot; zeigt.":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="Key Vault-Zugriffsrichtlinien":::

    Klicken Sie auf **Hinzufügen** und dann auf **Speichern**.

3. Navigieren Sie zu Ihrem Key Vault-Geheimnis, und kopieren Sie die Geheimnis-ID.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="Geheimnis-ID":::

    Notieren Sie sich den URI des Geheimnisses, den Sie während der Data Factory-Pipelineausführung abrufen möchten.

4. Fügen Sie in der Data Factory-Pipeline eine neue Webaktivität hinzu, und konfigurieren Sie diese wie folgt.  

    |Eigenschaft  |Wert  |
    |---------|---------|
    |Sichere Ausgabe     |True         |
    |URL     |[Ihr Geheimnis-URI]?api-version=7.0         |
    |Methode     |GET         |
    |Authentifizierung     |MSI         |
    |Resource        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="Webaktivität":::

    > [!IMPORTANT]
    > Sie müssen **?api-version=7.0** am Ende Ihres Geheimnis-URI hinzufügen.  

    > [!CAUTION]
    > Legen Sie die Option „Sichere Ausgabe“ auf „True“ fest, um zu verhindern, dass der Geheimniswert als Klartext protokolliert wird.  Für alle weiteren Aktivitäten, die diesen Wert verwenden, sollte die Option „Sichere Eingabe“ auf „True“ festgelegt werden.

5. Wenn Sie den Wert in einer anderen Aktivität nutzen möchten, verwenden Sie den Codeausdruck **\@activity(‚web1‘).output.value**.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="Codeausdruck":::

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwenden von Azure Key Vault zum Speichern von Anmeldeinformationen für Datenspeicher und Computeressourcen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md).