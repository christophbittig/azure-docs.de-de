---
title: Erstellen und Verwalten von Anmeldeinformationen für Überprüfungen
description: In diesem Artikel werden die Schritte zum Erstellen und Verwalten von Anmeldeinformationen in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdce380d09cc2992f4e77f9385b1d176a6ae68eb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076659"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview

In diesem Artikel wird beschrieben, wie Sie in Azure Purview Anmeldeinformationen erstellen. Mithilfe dieser gespeicherten Anmeldeinformationen können Sie gespeicherte Authentifizierungsinformationen schnell wiederverwenden und auf Ihre Datenquellenüberprüfungen anwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Schlüsseltresor. Informationen zur Erstellung finden Sie unter [Schnellstart: Erstellen eines Schlüsseltresors über das Azure-Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Einführung

Bei Anmeldeinformationen handelt es sich um Informationen für die Authentifizierung, die von Azure Purview für die Authentifizierung bei Ihren registrierten Datenquellen genutzt werden können. Ein Objekt mit Anmeldeinformationen kann für verschiedene Arten von Authentifizierungsszenarien erstellt werden, z. B. die Standardauthentifizierung mit Benutzername und Kennwort. In den Anmeldeinformationen werden spezifische Informationen erfasst, die je nach der ausgewählten Authentifizierungsmethode für die Authentifizierung erforderlich sind. Für die Anmeldeinformationen werden Ihre vorhandenen Azure Key Vault-Geheimnisse verwendet, um bei der Erstellung der Anmeldeinformationen vertrauliche Authentifizierungsinformationen abzurufen.

In Azure Purview gibt es einige Authentifizierungsmethoden zum Überprüfen von Datenquellen, z. B. folgende:

- Verwaltete Identität von Azure Purview
- Kontoschlüssel (mit Key Vault)
- SQL-Authentifizierung (mit Key Vault)
- Dienstprinzipal (mit Key Vault)

Entscheiden Sie zunächst unter Berücksichtigung Ihrer Datenquellentypen und Netzwerkanforderungen, welche Authentifizierungsmethode für Ihr Szenario erforderlich ist. Ermitteln Sie anhand des folgenden Entscheidungsbaums, welche Anmeldeinformationen am besten geeignet sind:

   :::image type="content" source="media/manage-credentials/manage-credentials-decision-tree-small.png" alt-text="Entscheidungsbaum zum Management von Anmeldeinformationen" lightbox="media/manage-credentials/manage-credentials-decision-tree.png":::

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Verwenden einer verwalteten Purview-Identität zum Einrichten von Überprüfungen

Wenn Sie die verwaltete Purview-Identität zum Einrichten von Überprüfungen verwenden, müssen Sie nicht explizit Anmeldeinformationen erstellen und Ihren Schlüsseltresor für die Speicherung nicht mit Purview verknüpfen. Eine ausführliche Anleitung zum Hinzufügen der verwalteten Purview-Identität, um den Zugriff auf die Überprüfung Ihrer Datenquellen zu ermöglichen, finden Sie unten in den Abschnitten zur Authentifizierung von Datenquellen:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL-Datenbank](register-scan-azure-sql-database.md)
- [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Erstellen von Azure Key Vault-Verbindungen in Ihrem Azure Purview-Konto

Bevor Sie Anmeldeinformationen erstellen können, müssen Sie Ihrem Azure Purview-Konto mindestens eine Ihrer vorhandenen Azure Key Vault-Instanzen zuordnen.

1. Wählen Sie Ihr Azure Purview-Abonnement im [Azure-Portal](https://portal.azure.com) aus, und öffnen Sie [Purview Studio](https://web.purview.azure.com/resource/). Navigieren Sie im Studio zum **Verwaltungscenter** und dann zu den **Anmeldeinformationen**.

2. Wählen Sie auf der Seite **Anmeldeinformationen** die Option **Manage Key Vault Connections** (Key Vault-Verbindungen verwalten) aus.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Verwalten von Azure Key Vault-Verbindungen":::

3. Wählen Sie auf der Seite zum Verwalten Ihrer Key Vault-Verbindungen die Option **+ Neu** aus.

4. Geben Sie die erforderlichen Informationen ein, und wählen Sie anschließend **Erstellen** aus.

5. Vergewissern Sie sich, dass die Zuordnung Ihrer Key Vault-Instanz zu Ihrem Azure Purview-Konto wie im folgenden Beispiel erfolgreich war:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Anzeigen der Azure Key Vault-Verbindungen zur Bestätigung":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Gewähren des Zugriffs auf Ihre Azure Key Vault-Instanz für die verwaltete Purview-Identität

Derzeit unterstützt Azure Key Vault zwei Berechtigungsmodelle:

- Option 1: Zugriffsrichtlinien 
- Option 2: rollenbasierte Zugriffssteuerung 

Identifizieren Sie Ihr Azure Key Vault-Berechtigungsmodell unter den Key Vault-**Ressourcenzugriffsrichtlinien** im Menü, bevor Sie der verwalteten Purview-Identität Zugriffsrechte zuweisen. Führen Sie die folgenden, für das jeweilige Berechtigungsmodell relevanten Schritte aus.  

:::image type="content" source="media/manage-credentials/akv-permission-model.png" alt-text="Azure Key Vault-Berechtigungsmodell"::: 

### <a name="option-1---assign-access-using-key-vault-access-policy"></a>Option 1: Zuweisen des Zugriffs unter Verwendung einer Key Vault-Zugriffsrichtlinie  

Führen Sie diese Schritte nur aus, wenn das Berechtigungsmodell in Ihrer Azure Key Vault Ressource als **Tresorzugriffsrichtlinie** festgelegt ist:

1. Navigieren Sie zu Ihrer Azure Key Vault-Instanz.

2. Wählen Sie die Seite **Zugriffsrichtlinien** aus.

3. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv-2.png" alt-text="Hinzufügen des MSI für Purview zu AKV":::

4. Wählen Sie in der Dropdownliste **Secrets permissions** (Geheimnisberechtigungen) die Berechtigungen **Get** und **List** aus.

5. Wählen Sie für **Prinzipal auswählen** die verwaltete Purview-Identität aus. Sie können die Purview-MSI entweder mithilfe des Purview-Instanznamens **oder** der Anwendungs-ID der verwalteten Identität suchen. Verbundidentitäten (Name der verwalteten Identität + Anwendungs-ID) werden zurzeit nicht unterstützt.

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Zugriffsrichtlinie hinzufügen":::

6. Wählen Sie **Hinzufügen**.

7. Wählen Sie **Speichern** aus, um die Zugriffsrichtlinie zu speichern.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Speichern der Zugriffsrichtlinie":::

### <a name="option-2---assign-access-using-key-vault-azure-role-based-access-control"></a>Option 2: Zuweisen des Zugriffs durch die rollenbasierte Zugriffssteuerung in Azure Key Vault 

Führen Sie diese Schritte nur aus, wenn das Berechtigungsmodell in Ihrer Azure Key Vault-Ressource als **Rollenbasierte Zugriffssteuerung in Azure** festgelegt ist:

1. Navigieren Sie zu Ihrer Azure Key Vault-Instanz.

2. Wählen Sie im linken Navigationsmenü **Zugriffssteuerung (IAM)** aus.

3. Klicken Sie auf **+ Hinzufügen**.

4. Stellen Sie die **Rolle** auf **Key Vault-Geheimnisbenutzer**, und geben Sie im Eingabefeld **Auswählen** Ihren Azure Purview-Kontonamen ein. Wählen Sie dann Speichern aus, um diese Rollenzuweisung für Ihr Purview-Konto festzulegen.

   :::image type="content" source="media/manage-credentials/akv-add-rbac.png" alt-text="Azure Key Vault-RBAC":::


## <a name="create-a-new-credential"></a>Erstellen von neuen Anmeldeinformationen

Die folgenden Typen von Anmeldeinformationen werden in Purview unterstützt:

- Standardauthentifizierung: Sie fügen das **Kennwort** dem Schlüsseltresor als Geheimnis hinzu.
- Dienstprinzipal: Sie fügen den **Dienstprinzipalschlüssel** dem Schlüsseltresor als Geheimnis hinzu.
- SQL-Authentifizierung: Sie fügen das **Kennwort** dem Schlüsseltresor als Geheimnis hinzu.
- Kontoschlüssel: Sie fügen den **Kontoschlüssel** dem Schlüsseltresor als Geheimnis hinzu.
- Rollen-ARN: Fügen Sie für eine Amazon S3-Datenquelle Ihren **Rollen-ARN** in AWS hinzu. 

Weitere Informationen finden Sie unter [Hinzufügen eines Geheimnisses zu Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) und [Erstellen einer neuen AWS-Rolle für Purview](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

Nach dem Speichern Ihrer Geheimnisse in Key Vault:

1. Wechseln Sie in Azure Purview zur Seite „Anmeldeinformationen“.

2. Erstellen Sie Ihre neuen Anmeldeinformationen, indem Sie **+ Neu** auswählen.

3. Geben Sie die erforderlichen Informationen an. Wählen Sie die **Authentifizierungsmethode** und eine **Key Vault-Verbindung** aus, von der Sie ein Geheimnis auswählen möchten.

4. Wählen Sie **Erstellen** aus, nachdem Sie alle Details eingegeben haben.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Neue Anmeldeinformationen":::

5. Vergewissern Sie sich, dass Ihre neuen Anmeldeinformationen in der Liste angezeigt werden und einsatzbereit sind.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Anzeigen von Anmeldeinformationen":::

## <a name="manage-your-key-vault-connections"></a>Verwalten Ihrer Key Vault-Verbindungen

1. Suchen/Finden von Key Vault-Verbindungen anhand des Namens

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Schlüsseltresor suchen":::

2. Löschen von Key Vault-Verbindungen

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Löschen von Schlüsseltresoren":::

## <a name="manage-your-credentials"></a>Verwalten Ihrer Anmeldeinformationen

1. Suchen/Finden von Anmeldeinformationen anhand des Namens
  
2. Auswählen und Aktualisieren vorhandener Anmeldeinformationen

3. Löschen von Anmeldeinformationen

## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines Überprüfungsregelsatzes](create-a-scan-rule-set.md)
