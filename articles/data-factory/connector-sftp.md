---
title: Kopieren von Daten von einem und auf einen SFTP-Server
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Daten mithilfe von Azure Data Factory- und Azure Synapse Analytics-Pipelines von einem und auf einen SFTP-Server kopieren.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5e851a266dd74758987c5e6bdeef1d76e3d50db9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763954"
---
# <a name="copy-data-from-and-to-the-sftp-server-using-azure-data-factory-or-azure-synapse-analytics"></a>Kopieren von Daten von einem und auf einen SFTP-Server mit Azure Data Factory oder Azure Synapse Analytics

> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Diensts aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Aktuelle Version](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten von einem und auf einen SFTP-Server (Secure FTP) kopieren. Weitere Informationen finden Sie im Einführungsartikel zu [Azure Data Factory](introduction.md) oder [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der SFTP-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Der SFTP-Connector unterstützt insbesondere Folgendes:

- Kopieren der Dateien von einem auf einen anderen SFTP-Server mithilfe der **Basic-** , **öffentlichen SSH-Schlüssel-** oder **merhstufigen** Authentifizierung.
- Kopieren von Dateien im jeweiligen Zustand oder Analysieren oder Generieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-sftp-linked-service-using-ui"></a>Erstellen eines verknüpften SFTP-Diensts über die Benutzeroberfläche

Verwenden Sie die folgenden Schritte, um einen verknüpften SFTP-Dienst auf der Azure-Portal Benutzeroberfläche zu erstellen.

1. Navigieren Sie in Ihrem Azure Data Factory- oder Synapse-Arbeitsbereich zu der Registerkarte „Verwalten“, wählen Sie „Verknüpfte Dienste“ aus und klicken Sie dann auf „Neu“:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Screenshot: Erstellen eines neuen verknüpften Diensts über die Azure Data Factory-Benutzeroberfläche":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Ein Screenshot, der das Erstellen eines neuen verknüpften Diensts mit der Azure Synapse Benutzeroberfläche zeigt.":::

2. Suchen Sie nach SFTP und wählen Sie den „SFTP-Connector“ aus.

    :::image type="content" source="media/connector-sftp/sftp-connector.png" alt-text="Ein Screenshot der einen SFTP-Connector zeigt.":::    

1. Konfigurieren Sie die Dienstdetails, testen Sie die Verbindung und erstellen Sie den neuen verknüpften Dienst.

    :::image type="content" source="media/connector-sftp/configure-sftp-linked-service.png" alt-text="Ein Screenshot, der die Konfiguration für einen verknüpften SFTP-Dienst zeigt.":::

## <a name="connector-configuration-details"></a>Details zur Connector-Konfiguration

Die folgenden Abschnitte enthalten Details zu den Eigenschaften, die zum Definieren von Entitäten speziell für SFTP verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SFTP verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf *Sftp* festgelegt werden. |Ja |
| host | Der Name oder die IP-Adresse des SFTP-Servers. |Ja |
| port | Der Port, an dem der SFTP-Server lauscht.<br/>Der zulässige Wert ist eine ganze Zahl (Integer), und der Standardwert ist *22*. |Nein |
| skipHostKeyValidation | Angabe, ob die Überprüfung des Hostschlüssels übersprungen werden soll.<br/>Zulässige Werte sind *true* und *false* (Standard).  | Nein |
| hostKeyFingerprint | Angabe des Fingerabdrucks des Hostschlüssels. | Ja, sofern die „skipHostKeyValidation“ auf „false“ festgelegt ist.  |
| authenticationType | Gibt den Authentifizierungstyp an.<br/>Zulässige Werte sind *Basic*, *öffentlicher SSH-Schlüssel* und *mehrstufig*. Weitere Eigenschaften finden Sie im Abschnitt [Verwenden der Standardauthentifizierung](#use-basic-authentication). JSON-Beispiele finden Sie im Abschnitt [Verwenden der Authentifizierung mit öffentlichem SSH-Schlüssel](#use-ssh-public-key-authentication). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn die Integration Runtime nicht angegeben ist, verwendet der Dienst die Standard-Azure Integration Runtime. |Nein |

### <a name="use-basic-authentication"></a>Verwenden der Standardauthentifizierung

Um die Standardauthentifizierung zu verwenden, legen Sie die Eigenschaft *authenticationType* auf *Basic* fest, und geben zusätzlich zu den im vorherigen Abschnitt eingeführten generischen SFTP-Connectoreigenschaften die folgenden Eigenschaften an:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| userName | Der Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| password | Das Kennwort für den Benutzer (userName). Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Schlüsseltresor gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |

**Beispiel:**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Verwenden der Authentifizierung mit öffentlichem SSH-Schlüssel

Legen Sie zum Verwenden der Authentifizierung mit öffentlichem SSH-Schlüssel die Eigenschaft „authenticationType“ auf **SshPublicKey** fest, und geben Sie neben den im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors die folgenden Eigenschaften an:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| userName | Der Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| privateKeyPath | Geben Sie den absoluten Pfad der privaten Schlüsseldatei ein, auf die die Integration Runtime zugreifen kann. Dies ist nur anwendbar, wenn für „connectVia“ eine Integration Runtime vom Typ „selbstgehostet“ angegeben wird. | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an.  |
| privateKeyContent | Inhalt des Base64-codierten privaten SSH-Schlüssels. Der private SSH-Schlüssel sollte das Format „OpenSSH“ aufweisen. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Schlüsseltresor gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an. |
| passPhrase | Geben Sie die Passphrase zum Entschlüsseln des privaten Schlüssels ein, wenn Schlüsseldatei oder Schlüsselinhalt mit einer Passphrase geschützt sind. Markieren Sie dieses Feld als einen „SecureString“, um es sicher zu speichern, oder [verweisen Sie auf ein in Azure Schlüsseltresor gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, wenn die private Schlüsseldatei oder der Schlüsselinhalt mit einer Passphrase geschützt sind. |

> [!NOTE]
> Der SFTP-Connector unterstützt nur RSA/DSA OpenSSH-Schlüssel. Stellen Sie sicher, dass der Inhalt Ihrer Schlüsseldatei mit „-----BEGIN [RSA/DSA] PRIVATE KEY-----“ beginnt. Wenn die Datei mit dem privaten Schlüssel eine Datei im PPK-Format ist, verwenden Sie das Tool PuTTY für die Konvertierung aus dem PPK- in das OpenSSH-Format. 

**Beispiel 1: SshPublicKey-Authentifizierung unter Verwendung des Dateipfads des privaten Schlüssels**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: SshPublicKey-Authentifizierung unter Verwendung des Inhalts des privaten Schlüssels**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-multi-factor-authentication"></a>Verwenden der mehrstufigen Authentifizierung

Um die mehrstufige Authentifizierung zu verwenden, bei der es sich um eine Kombination aus grundlegenden und SSH-Authentifizierungen mit öffentlichem Schlüssel handelt, geben Sie den Benutzernamen, das Kennwort und die privaten Schlüsselinformationen an, wie oben beschrieben.

**Beispiel: mehrstufige Authentifizierung**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<host>",
            "port": 22,
            "authenticationType": "MultiFactor",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 encoded private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<passphrase for private key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Datasets](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für SFTP unter den `location`-Einstellungen im formatbasierten Dataset unterstützt:

| Eigenschaft   | Beschreibung                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die *type*-Eigenschaft unter `location` im Dataset muss auf *SftpLocation* festgelegt werden. | Ja      |
| folderPath | Diese Eigenschaft gibt den Pfad zum Ordner an. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie den Pfad in den Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen folderPath. Wenn Sie einen Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie den Dateinamen in den Aktivitätsquelleneinstellungen an. | Nein       |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SFTP-Quelle unterstützt werden.

### <a name="sftp-as-source"></a>SFTP als Quelle

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für SFTP unter den `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die *type*-Eigenschaft unter `storeSettings` muss auf *SftpReadSettings* festgelegt werden. | Ja                                           |
| ***Suchen nach den zu kopierenden Dateien*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Ordner/Dateipfad. Wenn Sie alle Dateien aus einem Ordner kopieren möchten, geben Sie zusätzlich für `wildcardFileName` den Wert `*` an. |  |
| OPTION 2: Platzhalter<br>– wildcardFolderPath | Der Ordnerpfad mit Platzhalterzeichen, um Quellordner zu filtern. <br>Zulässige Platzhalter sind `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 2: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen unter dem angegebenen folderPath/wildcardFolderPath für das Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 3: eine Liste von Dateien<br>– fileListPath | Diese Eigenschaft gibt an, dass eine angegebene Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile, mit dem relativen Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, geben Sie nicht den Dateinamen im Dataset an. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen*** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert oder dort erstellt. <br>Zulässige Werte sind *true* (Standard) und *false*.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                            |
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections | Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| Nein                                            |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-a-sink"></a>SFTP als Senke

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)]

Folgende Eigenschaften werden für SFTP unter den `storeSettings`-Einstellungen in einer formatbasierten Kopiersenke unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die Eigenschaft *type* unter `storeSettings` muss auf *SftpWriteSettings* festgelegt werden. | Ja      |
| copyBehavior             | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein       |
| maxConcurrentConnections | Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten. | Nein       |
| useTempFileRename | Geben Sie an, ob Sie temporäre Dateien hochladen und umbenennen oder direkt in den Zielordner oder Dateispeicherort schreiben möchten. Standardmäßig schreibt der Dienst zuerst in temporäre Dateien und benennt sie dann um, wenn der Upload abgeschlossen ist. Diese Sequenz hilft, (1) Konflikte zu vermeiden, die zu einer beschädigten Datei führen könnten, wenn andere Prozesse in dieselbe Datei schreiben, und (2) sicherzustellen, dass die Originalversion der Datei während der Übertragung vorhanden ist. Wenn Ihr SFTP-Server keine Umbenennungsvorgänge unterstützt, deaktivieren Sie diese Option, und stellen Sie sicher, dass keine gleichzeitigen Schreibvorgänge für die Zieldatei ausgeführt werden. Weitere Informationen finden Sie im Tipp zur Problembehandlung am Ende dieser Tabelle. | Nein. Der Standardwert lautet *true*. |
| operationTimeout | Bei der Wartezeit vor jeder Schreibanforderung an den SFTP-Server tritt ein Timeout auf. Der Standardwert ist 60 Minuten (01:00:00).|Nein |

>[!TIP]
>Wenn Sie beim Schreiben von Daten in SFTP den Fehler „UserErrorSftpPathNotFound“, „UserErrorSftpPermissionDenied“ oder „SftpOperationFail“ erhalten und der von Ihnen verwendete SFTP-Benutzer über die entsprechende Berechtigung *verfügt*, überprüfen Sie, ob Ihr Umbenennungsvorgang für die Unterstützungsdatei des SFTP-Servers funktioniert. Wenn dies nicht der Fall ist, deaktivieren Sie die Option **Upload with temp file** (`useTempFileRename`), und versuchen Sie es erneut. Weitere Informationen zu dieser Eigenschaft finden Sie in der obigen Tabelle. Wenn Sie eine selbstgehostete Integration Runtime für die Kopieraktivität verwenden, stellen Sie sicher, dass Sie Version 4.6 oder höher verwenden.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

In diesem Abschnitt wird das Verhalten beschrieben, das sich aus der Verwendung von Platzhalterfiltern mit Ordnerpfaden und Dateinamen ergibt.

| folderPath | fileName | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (empty, use default) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | (empty, use default) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In dieser Tabelle wird das aus der Verwendung eines Dateilistenpfads in der Quelle einer Kopieraktivität resultierende Verhalten beschrieben. Es wird angenommen, dass Sie die folgende Quellordnerstruktur verwenden und die Dateien kopieren möchten, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | Azure Data Factory-Konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Ordnerpfad: `root/FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `root/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile, mit dem relativen Pfad zu dem im Dataset konfigurierten Pfad. |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften der Lookup-Aktivität finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Weitere Informationen zu den Eigenschaften der GetMetadata-Aktivität finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Weitere Informationen zu den Eigenschaften der Löschaktivität finden Sie unter [Löschaktivität](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, das zuvor beschriebene neue Modell zu verwenden, da auf der Erstellungsbenutzeroberfläche nun das neue Modell generieren wird.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die *type*-Eigenschaft des Datasets muss auf *FileShare* festgelegt werden. |Ja |
| folderPath | Diese Eigenschaft gibt den Pfad zum Ordner an. Ein Platzhalterfilter wird unterstützt. Zulässige Platzhalter sind: `*` (entspricht 0 oder mehr Zeichen) und `?` (entspricht 0 oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiele: Stammordner/Unterordner/. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Ja |
| fileName |  **Name oder Platzhalterfilter** für die Dateien unter dem angegebenen Wert für „folderPath“. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind folgende Platzhalter zulässig: `*` (entspricht 0 oder mehr Zeichen) und `?` (entspricht 0 oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. |Nein |
| modifiedDatetimeStart | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und einen Dateifilter auf eine große Anzahl von Dateien anwenden möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und einen Dateifilter auf eine große Anzahl von Dateien anwenden möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen.<br/><br/>Für das Analysieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat* und *ParquetFormat*. Sie müssen die *type* -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Unterstützte Typen sind *GZip*, *Deflate*, *BZIP2* und *ZipDeflate*.<br/>Unterstützte Grade sind *Optimal* und *Schnellste*. |Nein |

>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur *folderPath* an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie *folderPath* mit der Ordnerkomponente und *fileName* mit dem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie *folderPath* mit dem Ordner und *fileName* mit einem Platzhalterfilter an.

>[!NOTE]
>Wenn Sie die *fileFilter*-Eigenschaft für den Dateifilter verwendet haben, wird sie weiterhin unterstützt, aber wir empfehlen, dass Sie von jetzt an die neue Filterfunktion verwenden, die zu *fileName* hinzugefügt wurde.

**Beispiel:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Legacy-Quellenmodell der Kopieraktivität

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die *type*-Eigenschaft der Quelle der Kopieraktivität muss auf *FileSystemSource* festgelegt werden. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn „recursive“ auf *true* festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, werden leere Ordner und Unterordner nicht in die Senke kopiert oder dort erstellt.<br/>Zulässige Werte sind *true* (Standard) und *false*. | Nein |
| maxConcurrentConnections |Die Obergrenze gleichzeitiger Verbindungen mit dem Datenspeicher während der Aktivitätsausführung. Geben Sie diesen Wert nur an, wenn Sie die Anzahl der gleichzeitigen Verbindungen begrenzen möchten.| Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Copy-Aktivität unterstützt werden, finden Sie hier [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
