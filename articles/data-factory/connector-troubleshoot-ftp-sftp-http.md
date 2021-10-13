---
title: Problembehandlung für FTP-, SFTP- und HTTP-Connectors
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Probleme mit den FTP-, SFTP- und HTTP-Connectors in Azure Data Factory und Azure Synapse Analytics lösen können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390546"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für FTP-, SFTP- und HTTP-Connectors in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit den FTP-, SFTP- und HTTP-Connectors in Azure Data Factory und Azure Synapse.

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Fehlercode: FtpFailedToConnectToFtpServer

- **Meldung**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Ursache:** Möglicherweise wird ein falscher Typ von verknüpftem Dienst für den FTP-Server verwendet. Beispielsweise wird mit einem verknüpften SFTP-Dienst versucht, eine Verbindung mit einem FTP-Server herzustellen.

- **Empfehlung**:  Überprüfen Sie den Port des Zielservers. FTP verwendet Port 21.

### <a name="error-code-ftpfailedtoreadftpdata"></a>Fehlercode: FtpFailedToReadFtpData

- **Meldung**: `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **Ursache**: Der Portbereich zwischen 1024 und 65535 ist nicht für die Datenübertragung im passiven Modus geöffnet, der von der Data Factory- oder Synapse-Pipeline unterstützt wird.

- **Empfehlung**: Überprüfen Sie die Firewalleinstellungen des Zielservers. Öffnen Sie Port 1024-65535 oder den auf dem FTP-Server festgelegten Portbereich für die IP-Adresse der selbstgehosteten Integration Runtime/Azure IR.

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Fehlercode: SftpOperationFail

- **Meldung**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Ursache:** Ein Problem mit dem SFTP-Vorgang.

- **Empfehlung**:  Überprüfung Sie die Fehlerdetails aus SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Fehlercode: SftpRenameOperationFail

- **Meldung**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Ursache:** Der SFTP-Server unterstützt kein Umbenennen der temporären Datei.

- **Empfehlung**:  Legen Sie „useTempFileRename“ in der Kopiersenke auf „false“ fest, um das Hochladen in die temporäre Datei zu deaktivieren.


### <a name="error-code-sftpinvalidsftpcredential"></a>Fehlercode: SftpInvalidSftpCredential

- **Meldung**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Ursache:** Der Inhalt des privaten Schlüssels wird aus Azure Key Vault oder dem SDK abgerufen, ist aber nicht ordnungsgemäß codiert.

- **Empfehlung**:  

    Wenn der Inhalt des privaten Schlüssels aus Ihrem Schlüsseltresor stammt, kann die ursprüngliche Schlüsseldatei funktionieren, wenn Sie sie direkt in den verknüpften SFTP-Dienst hochladen.

    Weitere Informationen finden Sie unter [Copy data from and to the SFTP server by using data factory or Synapse pipelines](./connector-sftp.md#use-ssh-public-key-authentication) (Kopieren von Daten von einem und auf einen SFTP-Server mithilfe von Data Factory- oder Synapse-Pipelines). Der Inhalt des privaten Schlüssels ist der base64-codierte Inhalt eines privaten SSH-Schlüssels.

    Codieren Sie den *gesamten* Inhalt der ursprünglichen Datei mit dem privaten Schlüssel mit base64, und speichern Sie die codierte Zeichenfolge in Ihrem Schlüsseltresor. Die ursprüngliche Datei mit dem privaten Schlüssel funktioniert bei einem verknüpften SFTP-Dienst, wenn Sie **Hochladen** in der Datei auswählen.

    Es folgen einige Beispiele, die Sie verwenden können, um die Zeichenfolge zu generieren:

    - In C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - In Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Verwenden Sie ein base64-Konvertierungstool von Drittanbietern. Wir empfehlen das Tool [Encode to Base64 format](https://www.base64encode.org/).

- **Ursache:** Es wurde das falsche Format für den Schlüsselinhalt ausgewählt.

- **Empfehlung**:  

    Private SSH-Schlüssel im PKCS#8-Format (beginnt mit „-----BEGIN ENCRYPTED PRIVATE KEY-----“) werden derzeit nicht für den Zugriff auf den SFTP-Server unterstützt. 

    Führen Sie die folgenden Befehle aus, um den Schlüssel in ein herkömmliches SSH-Schlüsselformat zu konvertieren (beginnend mit „-----BEGIN RSA PRIVATE KEY-----“):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Ursache:** Die Anmeldeinformationen oder der Inhalt des privaten Schlüssels ist ungültig.

- **Empfehlung**:  Überprüfen Sie mit Tools wie WinSCP erneut, ob die Schlüsseldatei und das Kennwort richtig sind.

### <a name="sftp-copy-activity-failed"></a>Fehler bei der SFTP-Kopieraktivität

- **Symptome:** 
  * Fehlercode: UserErrorInvalidColumnMappingColumnNotFound 
  * Fehlermeldung: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Ursache:** Die Quelle enthält keine Spalte mit dem Namen „AccMngr“.

- **Lösung:** Um zu ermitteln, ob die Spalte „accmngr“ vorhanden ist, überprüfen Sie die Datasetkonfiguration erneut, indem Sie die Zieldatasetspalte zuordnen.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Fehlercode: SftpFailedToConnectToSftpServer

- **Meldung**: `Failed to connect to SFTP server '%server;'.`

- **Ursache:** Wenn die Fehlermeldung die Zeichenfolge „Socket read operation has timed out after 30,000 milliseconds“ enthält, ist eine mögliche Ursache, dass ein falscher Typ von verknüpftem Dienst für den SFTP-Server verwendet wird. Beispielsweise wird mit einem verknüpften FTP-Dienst versucht, eine Verbindung mit dem SFTP-Server herzustellen.

- **Empfehlung**:  Überprüfen Sie den Port des Zielservers. Standardmäßig verwendet SFTP Port 22.

- **Ursache:** Wenn die Fehlermeldung die Zeichenfolge „Server response does not contain SSH protocol identification“ enthält, ist eine mögliche Ursache, dass der SFTP-Server die Verbindung gedrosselt hat. Es werden mehrere Verbindungen erstellt, um parallele Downloads vom SFTP-Server durchführen zu können. Manchmal tritt dabei eine SFTP-Serverdrosselung auf. Normalerweise geben verschiedene Server unterschiedliche Fehler zurück, wenn Drosselung auftritt.

- **Empfehlung**:  

    Geben Sie die maximale Anzahl gleichzeitiger Verbindungen des SFTP-Datasets als 1 an, und führen Sie die Kopieraktivität dann erneut aus. Wenn die Aktivität erfolgreich ist, können Sie sicher sein, dass Drosselung die Ursache ist.

    Wenn Sie den niedrigen Durchsatz steigern möchten, wenden Sie sich an den SFTP-Administrator, damit dieser die Anzahl gleichzeitiger Verbindungen erhöht, oder führen Sie eine der folgenden Aktionen aus:

    * Fügen Sie bei Verwendung der selbstgehosteten IR der Positivliste die IP-Adresse des selbstgehosteten IR-Computers hinzu.
    * Wenn Sie Azure IR verwenden, fügen Sie [IP-Adressen von Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md) hinzu. Wenn Sie der Positivliste des SFTP-Servers keinen Bereich von IP-Adressen hinzufügen möchten, verwenden Sie stattdessen die selbstgehostete IR.


### <a name="error-code-sftppermissiondenied"></a>Fehlercode: SftpPermissionDenied

- **Meldung**: `Permission denied to access '%path;'`

- **Ursache**: Der angegebene Benutzer hat beim Betrieb keine Lese- oder Schreibberechtigung für den Ordner oder die Datei.

- **Empfehlung**: Erteilen Sie dem Benutzer die Berechtigung zum Lesen oder Schreiben in den Ordner oder die Dateien auf dem SFTP-Server.
 
### <a name="error-code-sftpauthenticationfailure"></a>Fehlercode: SftpAuthenticationFailure

- **Meldung**: `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **Ursache**: Die angegebenen Anmeldeinformationen (Ihr Kennwort oder Ihr privater Schlüssel) sind ungültig.

- **Empfehlung**: Überprüfen Sie Ihre Anmeldeinformationen.

- **Ursache**: Der angegebene Authentifizierungstyp ist nicht zulässig oder reicht nicht aus, um die Authentifizierung auf Ihrem SFTP-Server abzuschließen.

- **Empfehlung**: Verwenden Sie die folgenden Optionen, um den richtigen Authentifizierungstyp zu verwenden:
    - Wenn Ihr Server ein Kennwort erfordert, verwenden Sie „Standard“.
    - Wenn Ihr Server einen privaten Schlüssel erfordert, verwenden Sie „Authentifizierung mit öffentlichem SSH-Schlüssel“.
    - Wenn Ihr Server sowohl ein Kennwort als auch einen privaten Schlüssel erfordert, verwenden Sie „Mehrstufige Authentifizierung“.

- **Ursache**: Ihr SFTP-Server erfordert eine Tastaturinteraktion für die Authentifizierung, aber Sie haben „Kennwort“ angegeben.

- **Empfehlung**: 

    Die Tastaturinteraktion (keyboard-interactive) ist eine spezielle Authentifizierungsmethode, die sich von der Methode „Kennwort“ unterscheidet. Dies bedeutet, dass Sie bei der Anmeldung bei einem Server das Kennwort manuell eingeben müssen und nicht das zuvor gespeicherte Kennwort verwenden können. Azure Data Factory (ADF) ist jedoch ein Dienst für geplante Datenübertragungen, bei dem kein Popupeingabefeld geöffnet wird, in das Sie das Kennwort zur Laufzeit eingeben können. <br/> 
    
    Eine mögliche Problemumgehung ist, die Eingabe im Hintergrund zu simulieren, sodass keine tatsächliche manuelle Eingabe erfolgt. Dies entspricht dem Ändern der Methode mit Tastaturinteraktion in die Kennwortmethode. Wenn dieses Sicherheitsrisiko für Sie akzeptabel ist, befolgen Sie die folgenden Schritte:<br/> 
    1. Zeigen Sie im ADF-Portal auf den verknüpften SFTP-Dienst, und öffnen Sie die zugehörigen Nutzdaten über die Codeschaltfläche.
    1. Fügen Sie `"allowKeyboardInteractiveAuth": true` im Abschnitt „typeProperties“ hinzu.

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Fehlercode: HttpFileFailedToRead

- **Meldung**: `Failed to read data from http server. Check the error from http server：%message;`

- **Ursache**: Dieser Fehler tritt auf, wenn eine Data Factory- oder Synapse-Pipeline mit dem HTTP-Server kommuniziert, beim HTTP-Anforderungsvorgang jedoch ein Fehler auftritt.

- **Empfehlung**:  Überprüfen Sie den HTTP-Statuscode in der Fehlermeldung, und beheben Sie das Remoteserverproblem.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
