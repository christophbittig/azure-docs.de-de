---
title: Problembehandlung für Ihre Verbindungen in Azure Purview
description: In diesem Artikel werden die Schritte zum Durchführen der Problembehandlung für Ihre Verbindungen in Azure Purview beschrieben.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 18f3a98874fcfb98978778cf40ebe2cd876826d0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076147"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Problembehandlung für Ihre Verbindungen in Azure Purview

In diesem Artikel wird beschrieben, wie Sie die Problembehandlung für Verbindungsfehler durchführen, während Sie Überprüfungen für Datenquellen in Azure Purview einrichten.

## <a name="permission-the-credential-on-the-data-source"></a>Zuweisen einer Berechtigung für Anmeldeinformationen auf der Datenquelle

Wenn Sie eine verwaltete Identität oder einen Dienstprinzipal als Authentifizierungsmethode für Überprüfungen verwenden, müssen Sie diese Identitäten zulassen, damit Zugriff auf Ihre Datenquelle besteht.

Für jeden Quellentyp gilt eine bestimmte Anleitung:

- [Mehrere Azure-Quellen](register-scan-azure-multiple-sources.md#authentication-for-registration)
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#authentication-for-registration)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#prerequisites-for-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#prerequisites-for-scan)
- [Azure SQL-Datenbank](register-scan-azure-sql-database.md)
- [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)
- [SQL Server](register-scan-on-premises-sql-server.md#authentication-for-registration)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-s3-scan)

## <a name="verifying-azure-role-based-access-control-to-enumerate-azure-resources-in-azure-purview-studio"></a>Überprüfen der rollenbasierten Zugriffssteuerung in Azure zum Auflisten von Azure-Ressourcen in Azure Purview Studio

### <a name="registering-single-azure-data-source"></a>Registrieren einer einzelnen Azure-Datenquelle
Um eine einzelne Datenquelle (etwa eine Azure Blog Storage- oder Azure SQL-Datenbank-Instanz) in Azure Purview zu registrieren, muss Ihnen mindestens die Rolle **Leser** für die Ressource zugewiesen oder von einem höheren Bereich wie einer Ressourcengruppe oder einem Abonnement vererbt worden sein. Beachten Sie, dass einige Azure RBAC-Rollen, z. B. die Rolle „Sicherheitsadministrator“, keinen Lesezugriff zum Anzeigen von Azure-Ressourcen auf Steuerungsebene haben.  

Überprüfen Sie dies, indem Sie die folgenden Schritte ausführen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Ressource, die Sie in Azure Purview registrieren möchten. Wenn Sie die Ressource anzeigen können, verfügen Sie wahrscheinlich bereits mindestens über die Rolle „Leser“ für die Ressource. 
2. Wählen Sie **Zugriffssteuerung (IAM)**  > **Rollenzuweisungen** aus.
3. Suchen Sie nach dem Namen oder der E-Mail-Adresse des Benutzers, der versucht, Datenquellen in Azure Purview zu registrieren.
4. Überprüfen Sie, ob Rollenzuweisungen wie „Leser“ in der Liste vorhanden sind, oder fügen Sie bei Bedarf eine neue Rollenzuweisung hinzu.

### <a name="scanning-multiple-azure-data-sources"></a>Überprüfen mehrerer Azure-Datenquellen
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Abonnement oder zur Ressourcengruppe.  
2. Wählen Sie im linken Menü  **Zugriffskontrolle (IAM)**  aus. 
3. Wählen Sie **+ Hinzufügen** aus. 
4. Wählen Sie im Feld **Eingabe auswählen** die Rolle **Leser** aus, und geben Sie den Namen Ihres Azure Purview-Kontos (der dessen MSI-Namen darstellt) ein. 
5. Klicken Sie auf **Speichern**, um die Rollenzuweisung abzuschließen.
6. Wiederholen Sie die obigen Schritte, um die Identität des Benutzers hinzuzufügen, der versucht, eine neue Überprüfung für mehrere Datenquellen in Azure Purview zu erstellen.

## <a name="scanning-data-sources-using-private-link"></a>Überprüfen von Datenquellen mithilfe von Private Link 
Wenn der öffentliche Endpunkt auf Ihre Datenquellen beschränkt ist, müssen Sie zum Überprüfen von Azure-Datenquellen mithilfe von Private Link eine selbstgehostete Integration Runtime einrichten und Anmeldeinformationen erstellen. 

> [!IMPORTANT]
> Beim Überprüfen mehrerer Datenquellen, die Datenbanken wie etwa Azure SQL-Datenbank mit der Option _Zugriff auf öffentliches Netzwerk verweigern_ enthalten, tritt ein Fehler auf. Verwenden Sie zum Überprüfen dieser Datenquellen mithilfe eines privaten Endpunkts stattdessen die Option zum Registrieren einer einzelnen Datenquelle.

Weitere Informationen zum Einrichten einer selbstgehosteten Integration Runtime finden Sie unter [Bereitstellen einer selbstgehosteten Integration Runtime (IR) und Überprüfen Ihrer Datenquellen](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources).

Weitere Informationen zum Erstellen neuer Anmeldeinformationen in Azure Purview finden Sie unter [Erstellen von Azure Key Vault-Verbindungen in Ihrem Azure Purview-Konto](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Speichern Ihrer Anmeldeinformationen in Ihrem Schlüsseltresor und Verwenden der richtigen Angaben für Geheimnisname und -version

Darüber hinaus müssen Sie Ihre Anmeldeinformationen auf Ihrer Azure Key Vault-Instanz speichern und den richtigen Geheimnisnamen und die entsprechende Version verwenden.

Überprüfen Sie dies, indem Sie die folgenden Schritte ausführen:

1. Navigieren Sie zu Ihrer Key Vault-Instanz.
1. Wählen Sie **Settings** > **Secrets** (Einstellungen > Geheimnisse) aus.
1. Wählen Sie das Geheimnis aus, das Sie für die Authentifizierung bei Ihrer Datenquelle für Überprüfungen verwenden.
1. Wählen Sie die gewünschte Version aus, und überprüfen Sie die Richtigkeit des Kennworts bzw. des Kontoschlüssels, indem Sie **Geheimniswert anzeigen** auswählen. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Überprüfen der Berechtigungen für die verwaltete Purview-Identität auf Ihrer Azure Key Vault-Instanz

Vergewissern Sie sich, dass zum Zugreifen auf Ihre Azure Key Vault-Instanz die richtigen Berechtigungen für die verwaltete Purview-Identität konfiguriert wurden.

Führen Sie die folgenden Schritte aus, um dies zu überprüfen:

1. Navigieren Sie zu Ihrem Schlüsseltresor und dann zum Abschnitt **Zugriffsrichtlinien**.

1. Vergewissern Sie sich, dass für Ihre verwaltete Purview-Identität im Abschnitt *Aktuelle Zugriffsrichtlinien* mindestens die Berechtigung zum Abrufen (**Get**) und Auflisten (**List**) von Geheimnissen angezeigt wird.

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Abbildung der Dropdownliste zum Auswählen der Berechtigungen „Get“ und „List“":::

Falls Ihre verwaltete Purview-Identität nicht aufgeführt ist, sollten Sie die Schritte unter [Anmeldeinformationen für die Quellenauthentifizierung in Azure Purview](manage-credentials.md) ausführen, um sie hinzuzufügen. 

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
