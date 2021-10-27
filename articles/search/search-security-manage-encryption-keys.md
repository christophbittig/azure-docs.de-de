---
title: Verschlüsseln von Daten mit kundenseitig verwalteten Schlüsseln
titleSuffix: Azure Cognitive Search
description: Ergänzung der serverseitigen Verschlüsselung über Indizes und Synonymzuordnungen in Azure Cognitive Search durch Schlüssel, die Sie in Azure Key Vault erstellen und verwalten.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9dc1244c6747ea5616bee59ccf9b972a89d0c4a0
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042180"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search

In Azure Cognitive Search werden Inhalte automatisch mit [dienstseitig verwalteten Schlüsseln](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components) verschlüsselt. Wenn ein weiterer Schutz erforderlich ist, können Sie die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie Schlüssel verwenden, die Sie in Azure Key Vault erstellen und verwalten. Zu den Objekten, die verschlüsselt werden können, gehören Indizes, Synonymlisten, Indexer, Datenquellen und Skillsets.

In diesem Artikel finden Sie die Schritte zum Einrichten der Verschlüsselung mit kundenseitig verwalteten Schlüsseln. Die folgenden Punkte müssen Sie beachten:

+ Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln erfordert [Azure Key Vault](../key-vault/general/overview.md). Sie können Ihre eigenen Verschlüsselungsschlüssel erstellen und in einem Schlüsseltresor speichern oder mit Azure Key Vault-APIs Verschlüsselungsschlüssel generieren.

+ Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln wird beim Erstellen von Objekten jeweils pro Objekt aktiviert. Bereits vorhandene Inhalte können nicht verschlüsselt werden.

Weil das Entschlüsseln der Verschlüsselung sehr rechenintensiv ist, werden nur sensible Inhalte verschlüsselt. Dies schließt den gesamten Inhalt in Indizes und Synonymlisten ein. Für Indexer, Datenquellen und Skillsets werden nur die Felder verschlüsselt, in denen Verbindungszeichenfolgen, Beschreibungen, Schlüssel und Benutzereingaben gespeichert sind. Skillsets verfügen beispielsweise über Cognitive Services-Schlüssel, und einige Skills akzeptieren Benutzereingaben, z. B. benutzerdefinierte Entitäten. Schlüssel und Benutzereingaben in Skills werden verschlüsselt.

## <a name="double-encryption"></a>Doppelte Verschlüsselung

Die doppelte Verschlüsselung ist eine Erweiterung von kundenseitig verwalteten Schlüsseln (CMK). Die CMK-Verschlüsselung gilt für die langfristige Speicherung, die auf einen Datenträger geschrieben wird. Der Begriff *doppelte Verschlüsselung* bezieht sich auf die zusätzliche Verschlüsselung der kurzfristigen Speicherung (von Inhalten, die auf temporäre Datenträger geschrieben werden). Es ist keine Konfiguration erforderlich. Wenn Sie CMK auf Objekte anwenden, wird die Mehrfachverschlüsselung automatisch aufgerufen.

Obwohl die Mehrfachverschlüsselung in allen Regionen verfügbar ist, wurde die Unterstützung in zwei Phasen eingeführt. Der erste Rollout erfolgte im August 2020 und umfasste die fünf unten aufgeführten Regionen. Beim zweiten Rollout im Mai 2021 wurde die Mehrfachverschlüsselung auf alle verbleibenden Regionen erweitert. Wenn Sie CMK für einen älteren Dienst verwenden und eine Mehrfachverschlüsselung wünschen, müssen Sie einen neuen Suchdienst in Ihrer bevorzugten Region erstellen.

| Region | Diensterstellungsdatum |
|--------|-----------------------|
| USA, Westen 2 | Nach dem 1. August 2020 |
| East US | Nach dem 1. August 2020 |
| USA Süd Mitte  | Nach dem 1. August 2020 |
| US Government, Virginia  | Nach dem 1. August 2020 |
| US Gov Arizona  | Nach dem 1. August 2020 |
| [Alle anderen unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | Nach dem 13. Mai 2021 |

## <a name="prerequisites"></a>Voraussetzungen

In diesem Szenario werden die folgenden Tools und Dienste verwendet.

+ [Azure Cognitive Search](search-create-service-portal.md) in einem [abrechenbaren Tarif](search-sku-tier.md#tier-descriptions) (mindestens Basic in einer beliebigen Region)
+ [Azure Key Vault](../key-vault/general/overview.md) Sie können einen Schlüsseltresor im [Azure-Portal](../key-vault//general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../key-vault//general/quick-create-cli.md) oder mit [Azure PowerShell](../key-vault//general/quick-create-powershell.md) erstellen. im selben Abonnement wie Azure Cognitive Search. **Vorläufiges Löschen** und **Löschschutz** müssen für den Schlüsseltresor aktiviert sein.
+ [Azure Active Directory:](../active-directory/fundamentals/active-directory-whatis.md) Falls Sie nicht über eine solche Instanz verfügen, [richten Sie einen neuen Mandanten ein](../active-directory/develop/quickstart-create-new-tenant.md).

Sie sollten über eine Suchanwendung verfügen, mit der das verschlüsselte Objekt erstellt werden kann. In diesem Code verweisen Sie auf einen Key Vault-Schlüssel und Active Directory-Registrierungsinformationen. Bei diesem Code kann es sich um eine funktionierende App oder einen Prototypcode wie das [C#-Codebeispiel „DotNetHowToEncryptionUsingCMK“](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) handeln.

> [!TIP]
> Sie können [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md) oder [Azure PowerShell](search-get-started-powershell.md) verwenden, um die REST-APIs aufzurufen, mit denen Indizes und Synonymzuordnungen erstellt werden, die einen Parameter für den Verschlüsselungsschlüssel enthalten. Sie können auch Azure SDKs verwenden. Das Hinzufügen eines Schlüssels zu Indizes oder Synonymzuordnungen über das Portal wird nicht unterstützt.

## <a name="key-vault-tips"></a>Tipps zu Key Vault

Wenn Sie noch nicht mit Azure Key Vault arbeiten, finden Sie in dieser Schnellstartanleitung Informationen zu grundlegenden Aufgaben: [Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe von PowerShell](../key-vault/secrets/quick-create-powershell.md). Im Folgenden finden Sie einige Tipps zur Verwendung von Key Vault:

+ Verwenden Sie beliebig viele Schlüsseltresore. Verwaltete Schlüssel können in verschiedenen Schlüsseltresoren gespeichert werden. Ein Suchdienst kann mehrere verschlüsselte Objekte haben, die jeweils mit anderen kundenseitig verwalteten Verschlüsselungsschlüsseln verschlüsselt werden, die in verschiedenen Schlüsseltresoren gespeichert sind.

+ [Aktivieren Sie die Protokollierung](../key-vault/general/logging.md) in Key Vault, damit Sie die Schlüsselverwendung überwachen können.

+ Halten Sie bei der Routinerotation von Schlüsseltresorschlüsseln und Active Directory-Anwendungsgeheimnissen und der Registrierung unbedingt strikte Verfahren ein. Aktualisieren Sie immer den gesamten [verschlüsselten Inhalt](search-security-get-encryption-keys.md), um neue Geheimnisse und Schlüssel zu verwenden, bevor Sie die alten löschen. Wenn Sie diesen Schritt nicht ausführen, können Ihre Inhalte nicht entschlüsselt werden.

## <a name="1---enable-purge-protection"></a>1: Aktivieren des Löschschutzes

Vergewissern Sie sich zunächst, dass [vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) und [Löschschutz](../key-vault/general/soft-delete-overview.md#purge-protection) auf dem Schlüsseltresor aktiviert sind. Aufgrund der Art der Verschlüsselung mit kundenseitig verwalteten Schlüsseln können Ihre Daten nicht abgerufen werden, wenn der Azure Key Vault-Schlüssel gelöscht wird. 

Um Datenverluste aufgrund versehentlich gelöschter Key Vault-Schlüssel zu vermeiden, müssen im Schlüsseltresor die Optionen „Vorläufiges Löschen“ und „Löschschutz“ aktiviert werden. Vorläufiges Löschen ist standardmäßig aktiviert, sodass nur dann Probleme auftreten, wenn Sie das Feature absichtlich deaktiviert haben. Der Löschschutz ist standardmäßig nicht aktiviert, er ist aber für die Verschlüsselung mit kundenseitig verwalteten Schlüsseln in Cognitive Search erforderlich. 

Sie können beide Eigenschaften mithilfe von Azure-Portal-, PowerShell- oder Azure CLI-Befehlen festlegen.

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und öffnen Sie die Übersichtsseite Ihrer Key Vault-Instanz.

1. Aktivieren Sie auf der Seite **Übersicht** unter **Zusammenfassung** die Optionen **Vorläufiges Löschen** und **Purge protection** (Löschschutz).

### <a name="using-powershell"></a>PowerShell

1. Führen Sie `Connect-AzAccount` aus, um Ihre Azure-Anmeldeinformationen einzurichten.

1. Führen Sie den folgenden Befehl aus, um eine Verbindung mit Ihrem Schlüsseltresor herzustellen, und ersetzen Sie `<vault_name>` durch einen gültigen Namen:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. In Azure Key Vault erfolgt die Erstellung mit aktiviertem vorläufigem Löschen. Wenn das Feature in Ihrem Tresor deaktiviert ist, führen Sie den folgenden Befehl aus:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Aktivieren Sie den Löschschutz:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Speichern Sie Ihre Änderungen:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

+ Wenn Sie die [Azure CLI installiert](/cli/azure/install-azure-cli) haben, können Sie den folgenden Befehl ausführen, um die erforderlichen Eigenschaften zu aktivieren.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2: Erstellen eines Schlüssels in Key Vault

Überspringen Sie die Schlüsselgenerierung, wenn Sie bereits über einen Schlüssel in Azure Key Vault verfügen, den Sie verwenden möchten, aber notieren Sie den Schlüsselbezeichner. Sie benötigen diese Informationen, wenn Sie ein verschlüsseltes Objekt erstellen.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und öffnen Sie die Übersichtsseite Ihrer Key Vault-Instanz.

1. Klicken Sie auf der linken Seite auf **Schlüssel** und dann auf **+ Generate/Import** (Generieren/Importieren).

1. Wählen Sie im Bereich **Schlüssel erstellen** in der Liste mit den **Optionen** die gewünschte Schlüsselerstellungsmethode aus. Sie können einen neuen Schlüssel **generieren**, einen vorhandenen Schlüssel **hochladen** oder **Sicherung wiederherstellen** verwenden, um eine Sicherung eines Schlüssels auszuwählen.

1. Geben Sie einen **Namen** für den Schlüssel ein, und wählen Sie optional andere Schlüsseleigenschaften aus.

1. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.

1. Notieren Sie sich den Schlüsselbezeichner, der sich aus dem **Schlüsselwert-URI**, dem **Schlüsselnamen** und der **Schlüsselversion** zusammensetzt. Sie benötigen den Bezeichner, um einen verschlüsselten Index in Azure Cognitive Search zu definieren.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Erstellen eines neuen Key Vault-Schlüssels":::

## <a name="3---register-an-app"></a>3: Registrieren einer App

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach der Azure Active Directory-Ressource für Ihr Abonnement.

1. Suchen Sie auf der linken Seite unter **Verwalten** nach **App-Registrierungen**, und klicken Sie dann auf **New registration** (Neue Registrierung).

1. Benennen Sie die Registrierung. Verwenden Sie dabei beispielsweise einen Namen, der dem Namen der Suchanwendung ähnelt. Wählen Sie **Registrieren**.

1. Sobald die App-Registrierung erstellt wurde, kopieren Sie die Anwendungs-ID. Sie müssen diese Zeichenfolge für Ihre Anwendung bereitstellen. 

   Wenn Sie [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) durchlaufen, fügen Sie diesen Wert in die Datei **appsettings.json** ein.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Anwendungs-ID im Abschnitt „Zusammenfassung“":::

1. Klicken Sie als Nächstes auf der linken Seite auf **Certificates & secrets** (Zertifikate und Geheimnisse).

1. Wählen Sie **Neuer geheimer Clientschlüssel**. Geben Sie dem Geheimnis einen Anzeigenamen, und klicken Sie auf **Hinzufügen**.

1. Kopieren Sie das Anwendungsgeheimnis. Wenn Sie das Beispiel durchlaufen, fügen Sie diesen Wert in die Datei **appsettings.json** ein.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Anwendungsgeheimnis":::

## <a name="4---grant-permissions"></a>4: Erteilen von Berechtigungen

In diesem Schritt erstellen Sie eine Zugriffsrichtlinie in Key Vault. Diese Richtlinie gewährt der registrierten Anwendung die Active Directory-Berechtigung zur Verwendung Ihres kundenseitig verwalteten Schlüssels.

Die Zugriffsberechtigungen können jederzeit aufgehoben werden. Nach dem Aufheben können alle Indizes oder Synonymzuordnungen des Suchdiensts, die diese Key Vault-Instanz verwenden, nicht mehr verwendet werden. Durch Wiederherstellen der Key Vault-Zugriffsberechtigungen zu einem späteren Zeitpunkt wird der Zugriff auf die Indizes oder Synonymzuordnungen wiederhergestellt. Weitere Informationen finden Sie unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/security-features.md).

1. Öffnen Sie dann im Azure-Portal die Seite **Übersicht** Ihrer Key Vault-Instanz. 

1. Wählen Sie auf der linken Seite **Zugriffsrichtlinien** und dann **+ Zugriffsrichtlinie hinzufügen** aus.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Hinzufügen einer neuen Key Vault-Zugriffsrichtlinie":::

1. Klicken Sie auf **Prinzipal auswählen**, und wählen Sie die Anwendung aus, die Sie in Active Directory registriert haben. Sie können nach dem Namen suchen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Auswählen des Prinzipals für die Key Vault-Zugriffsrichtlinie":::

1. Klicken Sie unter **Schlüsselberechtigungen** auf *Abrufen*, *Schlüssel entpacken* und *Schlüssel packen*.

1. Klicken Sie unter **Geheimnisberechtigungen** auf *Abrufen*.

1. Klicken Sie unter **Zertifikatberechtigungen** auf *Abrufen*.

1. Klicken Sie auf **Hinzufügen** und dann auf **Speichern**.

> [!Important]
> Verschlüsselte Inhalte in der kognitiven Azure-Suche sind zur Verwendung eines bestimmten Azure Key Vault-Schlüssels mit einer bestimmten **Version** konfiguriert. Wenn Sie den Schlüssel oder die Version ändern, muss der Index oder die Synonymzuordnung zur Verwendung des neuen Schlüssels oder der neuen Version geändert werden, **bevor** der vorherige Schlüssel bzw. die vorherige Version gelöscht wird. Andernfalls kann der Index oder die Synonymzuordnung nicht mehr verwendet werden, da Sie die Inhalte nicht mehr entschlüsseln können, wenn der Schlüsselzugriff verloren geht.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5: Verschlüsseln von Inhalten

Um einen kundenseitig verwalteten Schlüssel einem Index, einer Synonymzuordnung, einem Indexer, einer Datenquelle oder einem Skillset hinzuzufügen, verwenden Sie die [Search-REST-API](/rest/api/searchservice/) oder ein Azure SDK, um ein Objekt zu erstellen, für das die Verschlüsselung aktiviert ist. Das Portal macht keine Synonymzuordnungen oder Verschlüsselungseigenschaften verfügbar. 

1. Rufen Sie die Create-APIs auf, um die Eigenschaft **encryptionKey** anzugeben:

   + [Index erstellen](/rest/api/searchservice/create-index)
   + [Erstellen einer Synonymzuordnung](/rest/api/searchservice/create-synonym-map)
   + [Create Indexer](/rest/api/searchservice/create-indexer)
   + [Erstellen der Datenquelle](/rest/api/searchservice/create-data-source)
   + [Erstellen eines Skillsets](/rest/api/searchservice/create-skillset).

1. Fügen Sie das encryptionKey-Konstrukt in die Objektdefinition ein. Diese Eigenschaft ist eine Eigenschaft der ersten Ebene auf der gleichen Ebene wie Name und Beschreibung. Die [folgenden Beispiele](#rest-examples) zeigen die Platzierung der Eigenschaft. Wenn Sie denselben Schlüsseltresor, Schlüssel und dieselbe Version verwenden, können Sie dasselbe encryptionKey-Konstrukt in jedes Objekt einfügen, für das Sie die Verschlüsselung aktivieren.

   Das folgende JSON-Beispiel zeigt einen encryptionKey mit Platzhalterwerten für Azure Key Vault und Anwendungsregistrierung in Azure Active Directory:

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "accessCredentials": {
          "applicationId": "00000000-0000-0000-0000-000000000000",
          "applicationSecret": "myApplicationSecret"
        }
      }
    }
    ```

Nachdem Sie das verschlüsselte Objekt im Suchdienst erstellt haben, können Sie es wie jedes andere Objekt seines Typs verwenden. Die Verschlüsselung ist für Benutzer und Entwickler transparent.

> [!Note]
> Keine dieser Key Vault-Details gelten als geheim. Diese Angaben können durch Navigieren zu der entsprechenden Seite des Azure Key Vault-Schlüssels im Azure-Portal einfach abgerufen werden.

## <a name="rest-examples"></a>REST-Beispiele

In diesem Abschnitt wird der JSON-Code für mehrere Objekte angezeigt, damit Sie sehen können, wo `encryptionKey` in einer Objektdefinition zu finden ist.

### <a name="index-encryption"></a>Indexverschlüsselung

Die Angaben zum Erstellen eines neuen Index über die REST-API finden Sie unter [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index). Der einzige Unterschied besteht hier darin, dass die Angaben des Verschlüsselungsschlüssels als Teil der Indexdefinition angegeben werden:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zur Indexerstellung senden und anschließend den Index normal verwenden.

### <a name="synonym-map-encryption"></a>Verschlüsselung einer Synonymzuordnung

Erstellen Sie eine verschlüsselte Synonymzuordnung mit der [Azure Cognitive Search-REST-API „Synonymzuordnung erstellen“](/rest/api/searchservice/create-synonym-map). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen der Synonymzuordnung senden und anschließend die Synonymzuordnung normal verwenden.

### <a name="data-source-encryption"></a>Datenquellenverschlüsselung

Erstellen Sie eine verschlüsselte Datenquelle mit der [Azure Cognitive Search-REST-API zum Erstellen einer Datenquelle](/rest/api/searchservice/create-data-source). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen der Datenquelle senden und anschließend die Datenquelle normal verwenden.

### <a name="skillset-encryption"></a>Skillsetverschlüsselung

Erstellen Sie ein verschlüsseltes Skillset mit der [Azure Cognitive Search-REST-API zum Erstellen eines Skillsets](/rest/api/searchservice/create-skillset). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

Dann können Sie die Anforderung zum Erstellen des Skillsets senden und anschließend das Skillset normal verwenden.

### <a name="indexer-encryption"></a>Indexerverschlüsselung

Erstellen Sie einen verschlüsselten Indexer mit der [Azure Cognitive Search-REST-API zum Erstellen eines Indexers](/rest/api/searchservice/create-indexer). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen des Indexers senden und anschließend den Indexer normal verwenden.

>[!Important]
> Obwohl `encryptionKey` nicht vorhandenen Suchindizes oder Synonymzuordnungen hinzugefügt werden kann, ist eine Aktualisierung möglich, indem unterschiedliche Werte für eines der drei Key Vault-Details angegeben werden (z. B. Aktualisierung der Schlüsselversion). Wenn Sie einen neuen Key Vault-Schlüssel oder eine neue Schlüsselversion angeben, müssen alle Suchindizes oder Synonymzuordnungen, die den Schlüssel verwenden, zunächst für die Verwendung des neuen Schlüssels oder der neuen Version geändert werden, **bevor** der vorherige Schlüssel bzw. die vorherige Version gelöscht wird. Andernfalls kann der Index oder die Synonymzuordnung nicht mehr verwendet werden, da Sie die Inhalte nicht mehr entschlüsseln können, wenn der Schlüsselzugriff verloren geht. Allerdings kann durch Wiederherstellen der Key Vault-Zugriffsberechtigungen zu einem späteren Zeitpunkt der Zugriff auf die Inhalte wiederhergestellt werden.

## <a name="simpler-alternative-trusted-service"></a>Einfachere Alternative: Vertrauenswürdiger Dienst

Abhängig von der Mandantenkonfiguration und den Authentifizierungsanforderungen können Sie möglicherweise einen einfacheren Ansatz für den Zugriff auf einen Key Vault-Schlüssel implementieren. Anstatt eine Active Directory-Anwendung zu erstellen und zu verwenden, können Sie entweder einen Suchdienst zu einem vertrauenswürdigen Dienst machen, indem Sie eine vom System verwaltete Identität dafür aktivieren, oder Ihrem Suchdienst eine benutzerseitig zugewiesene verwaltete Identität zuweisen. Danach verwenden Sie entweder den vertrauenswürdigen Suchdienst oder die benutzerseitig zugewiesene verwaltete Identität anstelle einer bei AD registrierten Anwendung als Sicherheitsprinzipal für den Zugriff auf den Key Vault-Schlüssel.

Mit beiden Ansätzen können Sie die Schritte zur Anwendungsregistrierung und Anwendungsgeheimnisse weglassen und die Definition des Verschlüsselungsschlüssels vereinfachen.

### <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

Im Allgemeinen kann der Suchdienst über eine verwaltete Identität bei Azure Key Vault authentifiziert werden, ohne dass Anmeldeinformationen (ApplicationID oder ApplicationSecret) im Code gespeichert werden. Der Lebenszyklus dieses Typs einer verwalteten Identität ist an den Lebenszyklus des Suchdiensts gebunden, der nur eine verwaltete Identität enthalten kann. Weitere Informationen zur Funktion verwalteter Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

1. Machen Sie Ihren Suchdienst zu einem vertrauenswürdigen Dienst.
   ![Aktivieren einer systemseitig zugewiesenen verwalteten Identität](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivieren einer systemseitig zugewiesenen verwalteten Identität")

1. Wenn Sie eine Zugriffsrichtlinie in Azure Key Vault einrichten, wählen Sie den vertrauenswürdigen Suchdienst als Prinzip aus (anstelle der bei AD registrierten Anwendung). Weisen Sie wie im Schritt zum Gewähren von Zugriffsschlüsselberechtigungen die gleichen Berechtigungen zu (mehrere GETs, WRAP, UNWRAP).

1. Verwenden Sie eine vereinfachte Konstruktion von `encryptionKey`, die die Active Directory-Eigenschaften auslässt.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Folgende Bedingungen verhindern, dass Sie diesen vereinfachten Ansatz anwenden:

+ Sie können für Ihren Suchdienst nicht direkt Zugriffsberechtigungen für die Key Vault-Instanz erteilen (z. B. wenn der Suchdienst sich in einem anderen Active Directory-Mandanten befindet als die Azure Key Vault-Instanz).

+ Ein einzelner Suchdienst ist erforderlich, um mehrere verschlüsselte Indizes oder Synonymzuordnungen zu hosten, die jeweils einen anderen Schlüssel aus einem anderen Schlüsselspeicher verwenden, wobei jeder Schlüsselspeicher **eine andere Identität** zur Authentifizierung verwenden muss. Da ein Suchdienst nur über eine verwaltete Identität verfügen kann, ist der vereinfachte Ansatz für Ihr Szenario aufgrund der Anforderungen für mehrere Identitäten nicht geeignet.  

### <a name="user-assigned-managed-identity-preview"></a>Benutzerseitig zugewiesene verwaltete Identität (Vorschau)

> [!IMPORTANT] 
> Die Unterstützung von benutzerseitig zugewiesenen verwalteten Identitäten befindet sich unter [zusätzlichen Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) in der öffentlichen Vorschau.
> 
> Dieses Feature steht in der REST-API-Version 2021-04-30-Preview und [Verwaltungs-REST-API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) zur Verfügung.

Das Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zu Ihrem Suchdienst ermöglicht dem Suchdienst, sich bei Azure Key Vault zu authentifizieren, ohne dass Anmeldeinformationen (ApplicationID oder ApplicationSecret) im Code gespeichert werden. Der Lebenszyklus dieses Typs einer verwalteten Identität ist vom Lebenszyklus Ihres Suchdiensts unabhängig. Weitere Informationen zur Funktion verwalteter Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

1. Wenn Sie noch keine benutzerseitig zugewiesene verwaltete Identität erstellt haben, müssen Sie sie erstellen. Führen Sie die folgenden Schritte aus, um eine benutzerseitig zugewiesene verwaltete Identität zu erstellen:

    1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) an.
    1. Wählen Sie **+ Neue Ressource erstellen** aus.
    1. Suchen Sie in der Suchleiste „Dienste und Marketplace durchsuchen“ nach „Vom Benutzer zugewiesene verwaltete Identität“, und wählen Sie dann **Erstellen** aus.
    1. Geben Sie einen beschreibenden Namen für die Identität ein.

1. Weisen Sie dann dem Suchdienst eine benutzerseitig zugewiesene verwaltete Identität zu. Dies kann mithilfe der Verwaltungs-API [2021-04-01-preview](/rest/api/searchmanagement/management-api-versions) erfolgen.

    Die Identitätseigenschaft verwendet einen Typ und mindestens eine vollqualifizierte vom Benutzer zugewiesene Identität:
    
    * **type** ist der für die Ressource verwendete Identitätstyp. Der Typ „SystemAssigned, UserAssigned“ umfasst sowohl eine vom System erstellte Identität als auch einen Satz benutzerseitig zugewiesener Identitäten. Der Typ „None“ entfernt alle Identitäten aus dem Dienst.
    * **userAssignedIdentities** enthält die Details der benutzerseitig zugewiesenen verwalteten Identität.
        * Format der benutzerseitig zugewiesenen verwalteten Identität: 
            * „/subscriptions/**Abonnement-ID**/resourcegroups/**Name der Ressourcengruppe**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**Name der verwalteten Identität**“
    
    Beispiel für das Zuweisen einer benutzerseitig zugewiesenen verwalteten Identität zu einem Suchdienst:
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. Wenn Sie eine Zugriffsrichtlinie in Azure Key Vault einrichten, wählen Sie die benutzerseitig zugewiesene verwaltete Identität als Prinzipal aus (anstelle der bei AD registrierten Anwendung). Weisen Sie wie im Schritt zum Gewähren von Zugriffsschlüsselberechtigungen die gleichen Berechtigungen zu (mehrere GETs, WRAP, UNWRAP).

1. Verwenden Sie eine vereinfachte Konstruktion von `encryptionKey`, die die Active Directory-Eigenschaften auslässt, und fügen Sie eine Identitätseigenschaft hinzu. Verwenden Sie unbedingt die REST-API-Version 2021-04-30-preview.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

## <a name="work-with-encrypted-content"></a>Arbeiten mit verschlüsselten Inhalten

Bei der Verschlüsselung mit kundenseitig verwalteten Schlüsseln treten Wartezeiten bei der Indizierung und bei Abfragen aufgrund des zusätzlichen Aufwands für Verschlüsselung und Entschlüsselung auf. Die Verschlüsselungsaktivität wird von Azure Cognitive Search nicht protokolliert, Sie können jedoch den Schlüsselzugriff über die Key Vault-Protokollierung überwachen. Es wird empfohlen, die [Protokollierung](../key-vault/general/logging.md) schon bei der Konfiguration des Schlüsseltresors zu aktivieren.

Es wird erwartet, dass im Lauf der Zeit eine Schlüsselrotation erfolgt. Wenn Sie Schlüssel rotieren, ist es wichtig, dabei die folgende Sequenz einzuhalten:

1. [Ermitteln Sie den Schlüssel, der von einem Index oder einer Synonymzuordnung verwendet wird.](search-security-get-encryption-keys.md)
1. [Erstellen Sie einen neuen Schlüssel im Schlüsseltresor](../key-vault/keys/quick-create-portal.md), aber lassen Sie den ursprünglichen Schlüssel verfügbar.
1. [Aktualisieren Sie die Eigenschaften der encryptionKey-Eigenschaft](/rest/api/searchservice/update-index) für einen Index oder eine Synonymzuordnung, damit die neuen Werte verwendet werden. Nur Objekte, die ursprünglich mit dieser Eigenschaft erstellt wurden, können aktualisiert werden, um einen anderen Wert zu verwenden.
1. Deaktivieren oder löschen Sie den vorherigen Schlüssel aus dem Schlüsseltresor. Überwachen Sie den Schlüsselzugriff, um sicherzustellen, dass der neue Schlüssel verwendet wird.

Aus Leistungsgründen speichert der Suchdienst den Schlüssel für mehrere Stunden zwischen. Wenn Sie den Schlüssel deaktivieren oder löschen, ohne einen neuen bereitzustellen, funktionieren Abfragen weiter, bis der Cache abläuft. Wenn der Suchdienst die Inhalte aber nicht mehr entschlüsseln kann, wird diese Meldung angezeigt: „Zugriff untersagt. Der Abfrageschlüssel wurde möglicherweise widerrufen. Versuchen Sie es noch mal.“ 

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nicht mit der Azure-Sicherheitsarchitektur vertraut sind, finden Sie entsprechende Informationen in der [Dokumentation zur Azure-Sicherheit](../security/index.yml) und insbesondere in folgendem Artikel:

> [!div class="nextstepaction"]
> [Verschlüsselung ruhender Daten](../security/fundamentals/encryption-atrest.md)
