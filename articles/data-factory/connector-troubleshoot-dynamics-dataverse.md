---
title: Fehlerbehebung bei den Dynamics 365-, Dataverse- (Common Data Service) und Dynamics CRM-Konnektoren
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit den Dynamics 365-, Dataverse- (Common Data Service) und Dynamics CRM-Konnektoren in Azure Data Factory und Azure Synapse Analytics beheben können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8552cbcb79522933e0b2cf9ffe369cefdf900b79
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390552"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>Fehlerbehebung bei den Dynamics 365-, Dataverse- (Common Data Service) und Dynamics CRM-Konnektoren in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Fehlerbehebung bei allgemeinen Problemen mit den Dynamics 365-, Dataverse- (Common Data Service) und Dynamics CRM-Konnektoren in Azure Data Factory und Azure Synapse.

## <a name="error-code-dynamicscreateserviceclienterror"></a>Fehlercode: DynamicsCreateServiceClientError

- **Meldung**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Ursache:** Dies ist ein vorübergehendes Problem bei dem Dynamics-Server.

- **Empfehlung**:  Führen Sie die Pipeline erneut aus. Wenn erneut ein Fehler auftritt, versuchen Sie, die Parallelität zu verringern. Wenn das Problem weiterhin besteht, wenden Sie sich an den Dynamics-Support.


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Fehlende Spalten beim Importieren eines Schemas oder von Vorschaudaten

- **Symptome:** Einige Spalten fehlen beim Importieren eines Schemas oder von Vorschaudaten. Fehlermeldung: `The valid structure information (column name and type) are required for Dynamics source.`

- **Ursache**: Dieses Problem ist beabsichtigt, weil Data Factory- und Synapse-Pipelines keine Spalten anzeigen können, die keine Werte in den ersten 10 Datensätzen enthalten. Stellen Sie sicher, dass die hinzugefügten Spalten das richtige Format aufweisen. 

- **Empfehlung**: Fügen Sie die Spalten auf der Registerkarte „Zuordnung“ manuell hinzu.


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Fehlercode: DynamicsMissingTargetForMultiTargetLookupField

- **Meldung**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Ursache:** Die Zielspalte ist in der Quelle oder der Spaltenzuordnung nicht vorhanden.

- **Empfehlung**:  
  1. Stellen Sie sicher, dass die Quelle die Zielspalte enthält. 
  2. Fügen Sie die Zielspalte in der Spaltenzuordnung hinzu. Stellen Sie sicher, dass die Senkenspalte das Format *{fieldName}@EntityReference* aufweist.


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Fehlercode: DynamicsInvalidTargetForMultiTargetLookupField

- **Meldung**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Ursache:** Ein falscher Entitätsname wird als Zielentität eines Nachschlagefelds mit mehreren Zielen bereitgestellt.

- **Empfehlung**:  Geben Sie einen gültigen Entitätsnamen für das Nachschlagefeld mit mehreren Zielen an.


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Fehlercode: DynamicsInvalidTypeForMultiTargetLookupField

- **Meldung**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Ursache:** Der Wert in der Zielspalte ist keine Zeichenfolge.

- **Empfehlung**:  Geben Sie eine gültige Zeichenfolge in der Zielspalte für das Nachschlagen mit mehreren Zielen an.


## <a name="error-code-dynamicsfailedtorequetserver"></a>Fehlercode: DynamicsFailedToRequetServer

- **Meldung**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Ursache:** Der Dynamics-Server ist instabil, es kann nicht darauf zugegriffen werden, oder im Netzwerk treten Probleme auf.

- **Empfehlung**:  Überprüfen Sie die Netzwerkkonnektivität, oder suchen Sie im Dynamics-Serverprotokoll nach weiteren Details. Wenden Sie sich an den Dynamics-Support, um weitere Unterstützung zu erhalten.


## <a name="error-code-dynamicsfailedtoconnect"></a>Fehlercode: DynamicsFailedToConnect 
 
 - **Meldung**: `Failed to connect to Dynamics: %message;` 
 
 - **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Es wird `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` oder andernfalls `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'` angezeigt, wenn Ihr Anwendungsfall **alle** drei folgenden Bedingungen erfüllt: <li>Sie stellen eine Verbindung mit Dynamics 365, Common Data Service oder Dynamics CRM her.</li><li>Sie verwenden die Office 365-Authentifizierung.</li><li>Ihr Mandant und der Benutzer sind in Azure Active Directory für den [bedingten Zugriff](../active-directory/conditional-access/overview.md) konfiguriert, und/oder eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist erforderlich (weitere Informationen hierzu finden Sie unter diesem [Link](/powerapps/developer/data-platform/authenticate-office365-deprecation) zur Dataverse-Dokumentation).</li>  Unter diesen Umständen war die Verbindung vor dem 08.06.2021 erfolgreich. Ab dem 09.06.2021 kann die Verbindung aufgrund der Außerbetriebnahme des regionalen Ermittlungsdiensts nicht hergestellt werden (weitere Informationen hierzu finden Sie unter diesem [Link](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)).| Wenn Mandant und Benutzer in Azure Active Directory für den [bedingten Zugriff](../active-directory/conditional-access/overview.md) konfiguriert sind und/oder eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist, müssen Sie nach dem 08.06.2021 den Azure AD-Dienstprinzipal für die Authentifizierung verwenden. Ausführliche Schritte finden Sie unter diesem [Link](./connector-dynamics-crm-office-365.md#prerequisites).|
    |Wenn `Office 365 auth with OAuth failed` in der Fehlermeldung angezeigt wird, bedeutet dies, dass Ihr Server möglicherweise einige Konfigurationen aufweist, die nicht mit OAuth kompatibel sind.| <li>Wenden Sie sich an das Dynamics-Supportteam, um Hilfe zu erhalten.</li><li>Verwenden Sie die Dienstprinzipalauthentifizierung. Weitere Informationen finden Sie in diesem Artikel: [Beispiel: Dynamics Online mit Azure AD Dienstprinzipal- und Zertifikatauthentifizierung](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication).</li>
    |Wenn `Unable to retrieve authentication parameters from the serviceUri` in der Fehlermeldung angezeigt wird, bedeutet dies, dass Sie entweder die falsche Dynamics-Dienst-URL angegeben haben oder einen Proxy/eine Firewall verwenden, um den Datenverkehr abzufangen. |<li>Stellen Sie sicher, dass Sie den richtigen Dienst-URI im verknüpften Dienst eingerichtet haben.</li><li>Wenn Sie die selbst gehostete IR verwenden, stellen Sie sicher, dass die Firewall/der Proxy die Anforderungen an den Dynamics-Server nicht abfängt.</li> |
    |Wenn `An unsecured or incorrectly secured fault was received from the other party` in der Fehlermeldung angezeigt wird, bedeutet dies, dass unerwartete Antworten vom Server erhalten wurden.  | <li>Stellen Sie sicher, dass Ihr Benutzername und Ihr Kennwort korrekt sind, wenn Sie die Office 365-Authentifizierung verwenden. </li><li> Stellen Sie sicher, dass Sie den richtigen Dienst-URI eingegeben haben.</li><li>Wenn Sie die regionale CRM-URL verwenden (URL hat eine Zahl nach „crm“), stellen Sie sicher, dass Sie den richtigen regionalen Bezeichner verwenden.</li><li>Wenden Sie sich an das Dynamics-Supportteam, um Hilfe zu erhalten.</li>|
    |Wenn `No Organizations Found` in der Fehlermeldung angezeigt wird, bedeutet dies, dass entweder der Organisationsname falsch ist oder Sie einen falschen CRM-Regionsbezeichner in der Dienst-URL verwendet haben.|<li>Stellen Sie sicher, dass Sie den richtigen Dienst-URI eingegeben haben.</li><li>Wenn Sie die regionale CRM-URL verwenden (URL hat eine Zahl nach „crm“), stellen Sie sicher, dass Sie den richtigen regionalen Bezeichner verwenden.</li><li>Wenden Sie sich an das Dynamics-Supportteam, um Hilfe zu erhalten.</li>|
    | Wenn `401 Unauthorized` und eine AAD-bezogene Fehlermeldung angezeigt wird, bedeutet dies, dass ein Problem mit dem Dienstprinzipal vorliegt. |Befolgen Sie die Anweisungen in der Fehlermeldung, um das Problem mit dem Dienstprinzipal zu beheben. |
   |Bei anderen Fehlern liegt das Problem in der Regel auf der Seite des Servers. |Verwenden Sie [XrmToolBox](https://www.xrmtoolbox.com/), um eine Verbindung herzustellen. Wenn der Fehler weiterhin auftritt, wenden Sie sich an das Dynamics-Supportteam, um Hilfe zu erhalten. |

## <a name="error-code-dynamicsoperationfailed"></a>Fehlercode: DynamicsOperationFailed 
 
- **Meldung**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Ursache**: Der Vorgang ist auf Serverseite fehlgeschlagen. 

- **Empfehlung**: Extrahieren Sie den Fehlercode des Dynamics-Vorgangs aus der Fehlermeldung : `Dynamics operation failed with error code: {code}`. Ausführlichere Informationen finden Sie im Artikel [Webdienstfehlercodes](/powerapps/developer/data-platform/org-service/web-service-error-codes). Sie können sich bei Bedarf an das Dynamics-Supportteam wenden. 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>Fehlercode: DynamicsInvalidFetchXml 
  
- **Meldung**: `The Fetch Xml query specified is invalid.` 

- **Ursache**: In der XML-Abrufdatei ist ein Fehler aufgetreten.  

- **Empfehlung**: Beheben Sie den Fehler im XML-Abruf. 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>Fehlercode: DynamicsMissingKeyColumns 
 
- **Meldung**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Ursache**: Die Quelldaten enthalten nicht die Schlüsselspalte für die Senkenentität. 

- **Empfehlung**: Vergewissern Sie sich, dass sich Schlüsselspalten in den Quelldaten befinden, oder ordnen Sie der Schlüsselspalte in der Senkenentität eine Quellspalte zu. 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>Fehlercode: DynamicsPrimaryKeyMustBeGuid 
 
- **Meldung**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **Ursache**: Der Typ der Primärschlüsselspalte ist nicht „Guid“. 
 
- **Empfehlung**: Stellen Sie sicher, dass die Primärschlüsselspalte in den Quelldaten vom Typ „Guid“ ist. 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>Fehlercode: DynamicsAlternateKeyNotFound 
 
- **Meldung**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Ursache**: Der bereitgestellte alternative Schlüssel ist nicht vorhanden, was durch falsche Schlüsselnamen oder unzureichende Berechtigungen verursacht werden kann. 
 
- **Empfehlung**: <br/> 
    - Korrigieren Sie Tippfehler im Schlüsselnamen.<br/> 
    - Stellen Sie sicher, dass Sie über ausreichende Berechtigungen für die Entität verfügen. 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>Fehlercode: DynamicsInvalidSchemaDefinition 
 
- **Meldung**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Ursache**: Senkenspalten in der Spaltenzuordnung haben keine Type-Eigenschaft. 
 
- **Empfehlung**: Sie können die Type-Eigenschaft diesen Spalten in der Spaltenzuordnung hinzufügen, indem Sie den JSON-Editor im Portal verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
