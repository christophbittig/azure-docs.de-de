---
title: Problembehandlung für Connectors
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier wird beschrieben, wie Sie Connectorprobleme in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: synapse
ms.openlocfilehash: 3271c2d37c9193fd9fb682359d0e0c57a6585ae4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504332"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Problembehandlung für Connectors in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Probleme mit Connectors in Azure Data Factory und Azure Synapse Analytics behandeln.  

## <a name="connector-specific-problems"></a>Connectorspezifische Probleme

Sie können sich die Problembehandlungsseiten für jeden Connector ansehen, um spezifische Probleme mit Erklärungen zu deren Ursachen und Empfehlungen zur Lösung anzuzeigen.

- [Azure Blob Storage](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB (einschließlich SQL-API-Connector)](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake (Gen1 und Gen2)](connector-troubleshoot-azure-data-lake.md)
- [Azure Database for PostgreSQL](connector-troubleshoot-postgresql.md)
- [Azure Files-Speicher](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics, Azure SQL-Datenbank und SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [Textformat mit Trennzeichen](connector-troubleshoot-delimited-text.md)
- [Dynamics 365, Dataverse (Common Data Service) und Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP, SFTP und HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Hive](connector-troubleshoot-hive.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [ORC-Format](connector-troubleshoot-orc.md)
- [Parquet-Format](connector-troubleshoot-parquet.md)
- [REST](connector-troubleshoot-rest.md)
- [SharePoint Online-Liste](connector-troubleshoot-sharepoint-online-list.md)
- [XML-Format](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>Fehler bei allgemeiner Kopieraktivität

Die folgenden Fehler gelten allgemein für die Kopieraktivität und können bei jedem Connector auftreten.

### <a name="error-code-jrenotfound"></a>Fehlercode: JreNotFound

- **Meldung**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Ursache:** Die selbstgehostete IR kann die Java-Laufzeit nicht finden. Die Java-Laufzeit ist zum Lesen bestimmter Quellen erforderlich.

- **Empfehlung**:  Überprüfen Sie Ihre Integration Runtime-Umgebung. Informationen dazu finden Sie unter [Verwenden einer selbstgehosteten Integration Runtime](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Fehlercode: WildcardPathSinkNotSupported

- **Meldung**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Ursache:** Das Senkendataset unterstützt keine Platzhalterwerte.

- **Empfehlung**:  Überprüfen Sie das Senkendataset, und korrigieren Sie den Pfad, ohne einen Platzhalterwert zu verwenden.


### <a name="fips-issue"></a>FIPS-Problem

- **Symptome:** Fehler bei der Kopieraktivität auf einem auf einem selbstgehosteten IR-Computer mit aktiviertem FIPS mit folgender Fehlermeldung: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Ursache:** Dieser Fehler kann auftreten, wenn Sie Daten mit Connectors wie Azure Blob, SFTP usw. kopieren. FIPS (Federal Information Processing Standards) definiert eine bestimmte Gruppe von Kryptografiealgorithmen, die verwendet werden können. Wenn der FIPS-Modus auf dem Computer aktiviert ist, werden einige Kryptografieklassen, von denen die Kopieraktivität abhängig ist, in einigen Szenarien blockiert.

- **Lösung:** Informieren Sie sich, [warum wir den „FIPS-Modus“ nicht mehr empfehlen](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), und ermitteln Sie, ob Sie FIPS auf Ihrem selbstgehosteten IR-Computer deaktivieren können.

    Wenn Sie jedoch nur FIPS umgehen und erfolgreiche Aktivitätsausführungen ermöglichen möchten, können Sie folgendermaßen vorgehen:

    1. Öffnen Sie den Ordner, in dem die selbstgehostete IR installiert ist. Der Pfad lautet in der Regel *C:\Programme\Microsoft Integration Runtime \<IR version>\Shared*.

    2. Öffnen Sie die Datei *diawp.exe.config*, und fügen Sie dann am Ende des `<runtime>`-Abschnitts `<enforceFIPSPolicy enabled="false"/>` wie hier gezeigt hinzu:

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="Screenshot eines Abschnitts der Datei „diawp.exe.config“ mit deaktiviertem FIPS.":::

    3. Speichern Sie die Datei, und starten Sie den Computer mit der selbstgehosteten IR neu.

### <a name="error-code-jniexception"></a>Fehlercode: JniException

- **Meldung**: `An error occurred when invoking Java Native Interface.`

- **Ursache**: Wenn die Fehlermeldung „Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]“ (JVM kann nicht erstellt werden: JNI-Rückgabecode [-6][Fehler bei JNI-Aufruf: ungültige Argumente.]) enthält, ist die mögliche Ursache, dass JVM nicht erstellt werden kann, da einige ungültige (globale) Argumente festgelegt sind.

- **Empfehlung**: Melden Sie sich beim Computer an, auf dem die *einzelnen Knoten* Ihrer selbstgehosteten Integration Runtime gehostet werden. Stellen Sie wie folgt sicher, dass die Systemvariable ordnungsgemäß festgelegt ist: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`. Starten Sie alle Integration Runtime-Knoten neu, und führen Sie dann die Pipeline erneut aus.

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>Fehlercode: GetOAuth2AccessTokenErrorResponse

- **Meldung**: `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **Ursache**: Ihre Client-ID oder Ihr geheimer Clientschlüssel ist ungültig, und bei der Authentifizierung auf dem Autorisierungsserver ist ein Fehler aufgetreten.

- **Empfehlung**: Korrigieren Sie alle Floweinstellungen für OAuth2-Clientanmeldeinformationen Ihres Autorisierungsservers.

### <a name="error-code-failedtogetoauth2accesstoken"></a>Fehlercode: FailedToGetOAuth2AccessToken

- **Meldung**: `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **Ursache**: Die Floweinstellungen für OAuth2-Clientanmeldeinformationen sind ungültig.

- **Empfehlung**: Korrigieren Sie alle Floweinstellungen für OAuth2-Clientanmeldeinformationen Ihres Autorisierungsservers.

### <a name="error-code-oauth2accesstokentypenotsupported"></a>Fehlercode: OAuth2AccessTokenTypeNotSupported

- **Meldung**: `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **Ursache**: Ihr Autorisierungsserver wird nicht unterstützt.

- **Empfehlung**: Verwenden Sie einen Autorisierungsserver, der Token mit unterstützten Tokentypen zurückgeben kann.

### <a name="error-code-oauth2clientidcolonnotallowed"></a>Fehlercode: OAuth2ClientIdColonNotAllowed

- **Meldung**: `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **Ursache**: Ihre Client-ID enthält einen Doppelpunkt (`:`). Dies ist ein ungültiges Zeichen.

- **Empfehlung**: Verwenden Sie eine gültige Client-ID.

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>Fehlercode: ManagedIdentityCredentialObjectNotSupported

- **Meldung**: `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Empfehlung**: Überprüfen Sie die unterstützte Version, und führen Sie für die Integration Runtime ein Upgrade auf eine höhere Version durch.

### <a name="error-code-querymissingformatsettingsindataset"></a>Fehlercode: QueryMissingFormatSettingsInDataset

- **Meldung**: `The format settings are missing in dataset %dataSetName;.`

- **Ursache**: Es ist ein binärer Datasettyp festgelegt. Dieser Typ wird nicht unterstützt.

- **Empfehlung**: Verwenden Sie stattdessen das DelimitedText-, Json-, Avro-, Orc- oder Parquet-Dataset.

- **Ursache**: Für den Dateispeicher fehlen die Formateinstellungen im Dataset.

- **Empfehlung**: Deaktivieren Sie die Option „Binärkopie“ im Dataset, und legen Sie die richtigen Formateinstellungen fest.

### <a name="error-code-queryunsupportedcommandbehavior"></a>Fehlercode: QueryUnsupportedCommandBehavior

- **Meldung**: `The command behavior "%behavior;" is not supported.`

- **Empfehlung**: Fügen Sie das Befehlsverhalten nicht als Parameter für die Vorschau- oder GetSchema-API-Anforderungs-URL hinzu.

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>Fehlercode: DataConsistencyFailedToGetSourceFileMetadata

- **Meldung**: `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **Ursache**: Es liegt ein vorübergehendes Problem im Senkendatenspeicher vor, oder das Abrufen von Metadaten aus dem Senkendatenspeicher ist nicht zulässig.

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>Fehlercode: DataConsistencyFailedToGetSinkFileMetadata

- **Meldung**: `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **Ursache**: Es liegt ein vorübergehendes Problem im Senkendatenspeicher vor, oder das Abrufen von Metadaten aus dem Senkendatenspeicher ist nicht zulässig.

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>Fehlercode: DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **Meldung**: `Data consistency validation is not supported in current copy activity settings.`

- **Ursache**: Die Datenkonsistenzüberprüfung wird nur im direkten Binärkopierszenario unterstützt.

- **Empfehlung**: Entfernen Sie die Eigenschaft „validateDataConsistency“ in den Nutzdaten der Kopieraktivität.

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>Fehlercode: DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **Meldung**: `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Empfehlung**: Überprüfen Sie die unterstützte Integration Runtime-Version, und führen Sie ein Upgrade auf eine höhere Version durch, oder entfernen Sie die Eigenschaft „validateDataConsistency“ aus Kopieraktivitäten.

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>Fehlercode: SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **Meldung**: `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Empfehlung**: Entfernen Sie „fileMissing“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>Fehlercode: SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **Meldung**: `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **Empfehlung**: Entfernen Sie „dataInconsistency“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>Fehlercode: SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **Meldung**: `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Empfehlung**: Entfernen Sie „fileForbidden“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>Fehlercode: SkipForbiddenFileNotSupportedForThisConnector

- **Meldung**: `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **Empfehlung**: Entfernen Sie „fileForbidden“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>Fehlercode: SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **Meldung**: `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Empfehlung**: Entfernen Sie „invalidFileName“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>Fehlercode: SkipInvalidFileNameNotSupportedForSource

- **Meldung**: `Skip invalid file name is not supported for '%connectorName;' source.`

- **Empfehlung**: Entfernen Sie „invalidFileName“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>Fehlercode: SkipInvalidFileNameNotSupportedForSink

- **Meldung**: `Skip invalid file name is not supported for '%connectorName;' sink.`

- **Empfehlung**: Entfernen Sie „invalidFileName“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>Fehlercode: SkipAllErrorFileNotSupportedForNonBinaryCopy

- **Meldung**: `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **Empfehlung**: Entfernen Sie „allErrorFile“ der skipErrorFile-Einstellung in den Nutzdaten der Kopieraktivität.

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>Fehlercode: DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **Meldung**: `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **Empfehlung**: Entfernen Sie die Einstellung „deleteFilesAfterCompletion“, oder verwenden Sie eine direkte Binärkopie.

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>Fehlercode: DeleteFilesAfterCompletionNotSupportedForThisConnector

- **Meldung**: `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **Empfehlung**: Entfernen Sie die Einstellung „deleteFilesAfterCompletion“ in den Nutzdaten der Kopieraktivität.

### <a name="error-code-failedtodownloadcustomplugins"></a>Fehlercode: FailedToDownloadCustomPlugins

- **Meldung**: `Failed to download custom plugins.`

- **Ursache**: Ungültige Downloadlinks oder vorübergehende Konnektivitätsprobleme.

- **Empfehlung**: Wiederholen Sie den Vorgang, wenn in der Meldung angezeigt wird, dass es sich um ein vorübergehendes Problem handelt. Sollte das Problem weiterhin bestehen, wenden Sie sich an das Supportteam.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
