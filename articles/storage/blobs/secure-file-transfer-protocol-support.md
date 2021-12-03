---
title: Unterstützung von Secure File Transfer Protocol (SFTP) für Azure Blob Storage (Vorschau) | Microsoft-Dokumentation
description: Blob Storage unterstützt jetzt Secure File Transfer Protocol (SFTP).
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 0cfd4a8dc9968ad9ef9e7538ac51414deb974fdb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557296"
---
# <a name="secure-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Unterstützung von Secure File Transfer Protocol (SFTP) für Azure Blob Storage (Vorschau)

Blob Storage unterstützt jetzt Secure File Transfer Protocol (SFTP). Diese Unterstützung bietet die Möglichkeit, über einen SFTP-Endpunkt eine sichere Verbindung mit Blob-Storage-Konten herzustellen, sodass Sie SFTP für Dateizugriff, Dateiübertragung und Dateiverwaltung nutzen können.  

> [!IMPORTANT]
> SFTP-Unterstützung befindet sich derzeit in der Vorschauversion und ist in [diesen Regionen](secure-file-transfer-protocol-support.md#regional-availability) verfügbar.
>
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/r/gZguN0j65Y).

Azure ermöglicht eine sichere Datenübertragung an Blob-Storage-Konten mithilfe der Azure Blob Service-REST-API, Azure SDKs und Tools wie AzCopy. Ältere Workloads verwenden jedoch häufig herkömmliche Dateiübertragungsprotokolle wie SFTP. Sie können benutzerdefinierte Anwendungen so aktualisieren, dass sie die REST-API und Azure SDKs verwenden, aber nur, indem Sie wichtige Codeänderungen vornehmen.

Wenn Sie SFTP zum Übertragen von Daten an Azure Blob Storage verwenden möchten, müssen Sie vor der Veröffentlichung dieser Funktion entweder ein Drittanbieterprodukt erwerben oder Ihre eigene Lösung orchestrieren. Sie müssten einen virtuellen Computer (VM) in Azure erstellen, um einen SFTP-Server zu hosten, und dann eine Möglichkeit zum Verschieben von Daten in das Speicherkonto finden. 

Mit SFTP-Unterstützung für Azure Blob Storage können Sie nun einen SFTP-Endpunkt für Blob-Storage-Konten mit einer einzigen Einstellung aktivieren. Anschließend können Sie lokale Benutzeridentitäten für die Authentifizierung einrichten, um Daten sicher zu übertragen, ohne zusätzliche Arbeit ausführen zu müssen. 

In diesem Artikel wird die SFTP-Unterstützung für Azure Blob Storage beschrieben. Informationen zum Aktivieren von SFTP für Ihr Speicherkonto finden Sie unter [Verbinden zu Azure Blob Storage mithilfe des SFTP (Secure File Transfer Protocol) (Vorschau)](secure-file-transfer-protocol-support-how-to.md).

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP und der hierarchische Namespace

Die Unterstützung von SFTP erfordert, dass Blobs in einem hierarchischen Namespace organisiert werden. Die Möglichkeit zur Verwendung eines hierarchischen Namespaces wurde durch Azure Data Lake Storage Gen2 eingeführt. Er organisiert Objekte (Dateien) genauso in eine Hierarchie von Verzeichnissen und Unterverzeichnissen, wie das Dateisystem auf Ihrem Computer organisiert ist. Der hierarchische Namespace wird linear skaliert und beeinträchtigt weder die Datenkapazität noch die Leistung. 

Verschiedene Protokolle werden aus dem hierarchischen Namespace erweitert. SFTP ist eines dieser verfügbaren Protokolle.

> [!div class="mx-imgBorder"]
> ![hierarchischer Namespace](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>SFTP-Berechtigungsmodell

Azure Storage unterstützt keine SAS-Authentifizierung (Shared Access Signature) oder Azure Active Directory-Authentifizierung (Azure AD) zum Verbinden von SFTP-Clients. Stattdessen müssen SFTP-Clients entweder ein Kennwort oder Anmeldeinformationen für einen privaten SSH-Schlüssel (Secure Shell) verwenden.

Um einem Verbindungsherstellungsclient Zugriff zu gewähren, muss dem Speicherkonto eine Identität zugeordnet sein, die diesen Anmeldeinformationen zugeordnet ist. Diese Identität wird als lokaler Benutzer bezeichnet. Lokale Benutzer sind eine neue Form der Identitätsverwaltung, die mit SFTP-Unterstützung bereitgestellt wird. Sie können einem Speicherkonto 1.000 lokale Benutzer hinzufügen.

Um Zugriffsberechtigungen einzurichten, erstellen Sie einen lokalen Benutzer und wählen Authentifizierungsmethoden aus. Anschließend können Sie für jeden Container in Ihrem Konto die Zugriffsebene angeben, die Sie diesem Benutzer gewähren möchten.
 
> [!NOTE]
> Nachdem Ihre Daten in Azure Storage erfasst wurden, können Sie die gesamte Bandbreite der Azure-Speichersicherheitseinstellungen verwenden. Autorisierungsmechanismen wie rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) und Zugriffssteuerungslisten werden zwar nicht als Mittel zum Autorisieren eines sich verbindenden SFTP-Clients unterstützt, können jedoch zum Autorisieren des Zugriffs über Azure-Tools (z. B. Azure-Portal, Azure CLI, Azure PowerShell-Befehle und AzCopy) sowie Azure SDKs und Azure-REST-APIs verwendet werden. 
> 
> Weitere Informationen finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

## <a name="authentication-methods"></a>Authentifizierungsmethoden

Sie können einen SFTP-Client authentifizieren, der eine Verbindung herstellt, indem Sie ein Kennwort oder ein öffentliches/privates SSH-Schlüsselpaar (Secure Shell) verwenden. Sie können beide Authentifizierungsarten konfigurieren und verbindende Clients auswählen lassen, welche verwendet werden sollen. Die MFA, bei der sowohl ein gültiges Kennwort als auch ein gültiges Paar aus öffentlichem und privatem Schlüssel für eine erfolgreiche Authentifizierung erforderlich sind, wird jedoch nicht unterstützt. 

#### <a name="passwords"></a>Kennwörter

Kennwörter werden für Sie generiert. Wenn Sie die Kennwortauthentifizierung auswählen, wird Ihr Kennwort bereitgestellt, nachdem Sie die Konfiguration eines lokalen Benutzers abgeschlossen haben. Kopieren Sie dieses Kennwort, und speichern Sie es an einem Speicherort, an dem Sie es später finden können. Sie können dieses Kennwort nicht erneut aus Azure abrufen. Wenn Sie das Kennwort verlieren, müssen Sie ein neues Kennwort generieren. Aus Sicherheitsgründen können Sie das Kennwort nicht selbst festlegen.   

#### <a name="ssh-key-pairs"></a>SSH-Schlüsselpaare

Ein Paar aus öffentlichem und privatem Schlüssel ist die gängigste Form der Authentifizierung für Secure Shell (SSH). Der private Schlüssel ist geheim und nur Ihnen bekannt. Der öffentliche Schlüssel wird auf Azure gespeichert. Wenn ein SSH-Client eine Verbindung mit dem Speicherkonto herstellt, sendet er eine Nachricht mit dem privaten Schlüssel und der Signatur. Azure überprüft die Nachricht und überprüft, ob der Benutzer und der Schlüssel vom Speicherkonto erkannt werden. Weitere Informationen finden Sie in der [Übersicht über SSH und Schlüssel](/azure/virtual-machines/linux/ssh-from-windows##overview-of-ssh-and-keys).

Wenn Sie sich für die Authentifizierung mit einem Schlüsselpaar aus privatem und öffentlichem Schlüssel entscheiden, können Sie entweder einen Schlüssel generieren, einen bereits in Azure gespeicherten Schlüssel verwenden oder Azure den öffentlichen Schlüssel eines bestehenden Schlüsselpaars aus öffentlichem und privatem Schlüssel zur Verfügung stellen. 

## <a name="container-permissions"></a>Containerberechtigungen

In der aktuellen Version können Sie nur Berechtigungen auf Containerebene angeben. Berechtigungen auf Verzeichnisebene werden nicht unterstützt. Sie können auswählen, auf welche Container Sie Zugriff gewähren möchten und welche Zugriffsebene Sie bereitstellen möchten (Lesen, Schreiben, Auflisten, Löschen und Erstellen). Diese Berechtigungen gelten für alle Verzeichnisse und Unterverzeichnisse im Container. Sie können jedem lokalen Benutzer Zugriff auf bis zu 100 Container gewähren. Containerberechtigungen können auch nach dem Erstellen eines lokalen Benutzers aktualisiert werden. In der folgenden Tabelle werden die einzelnen Berechtigungen ausführlicher beschrieben.

| Berechtigung | Berechtigungscode | BESCHREIBUNG |
|---|---|---|
| Lesen | r | <li>Der Inhalt der Datei.</li> |
| Schreiben | w | <li>Hochladen der Datei</li><li>Erstellen eines Verzeichnisses</li><li>Hochladen der Verzeichnisse</li> |
| List | l | <li>Auflisten von Inhalten innerhalb des Containers</li><li>Auflisten von Inhalten in Verzeichnissen</li> |
| Löschen | T | <li>Löschen von Dateien/Verzeichnissen</li> |
| Erstellen | c | <li>Hochladen der Datei, wenn die Datei nicht vorhanden ist</li><li>Erstellen des Verzeichnisses, wenn es nicht vorhanden ist</li><li>Erstellen von Verzeichnissen</li>|

## <a name="home-directory"></a>Startverzeichnis

Beim Konfigurieren von Berechtigungen haben Sie die Möglichkeit, ein Stammverzeichnis für den lokalen Benutzer festzulegen. Wenn in einer SFTP-Verbindungsanforderung kein anderer Container angegeben ist, ist dies das Verzeichnis, mit dem der Benutzer standardmäßig eine Verbindung herstellt. Betrachten Sie beispielsweise die folgende Anforderung, die mit [Open SSH](/windows-server/administration/openssh/openssh_overview) erfolgt. Diese Anforderung gibt keinen Container- oder Verzeichnisnamen als Teil des Befehls `sftp` an.

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

Wenn Sie das Stammverzeichnis eines Benutzers auf `mycontainer/mydirectory` festlegen, stellt der Client eine Verbindung mit diesem Verzeichnis her. Anschließend wird die Datei `logfile.txt` in `mycontainer/mydirectory` hochgeladen. Wenn Sie das Stammverzeichnis nicht festgelegt haben, schlägt der Verbindungsversuch fehl. Stattdessen müssten Verbindungsclients einen Container zusammen mit der Anforderung angeben und dann SFTP-Befehle verwenden, um zum Zielverzeichnis zu navigieren, bevor eine Datei hochgeladen wird. Dies wird im folgenden Beispiel veranschaulicht:

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Im Artikel [Bekannte Probleme](secure-file-transfer-protocol-known-issues.md) finden Sie eine vollständige Liste der Probleme und Einschränkungen im aktuellen Release der SFTP-Unterstützung.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die Unterstützung von SFTP ist in den folgenden Regionen verfügbar. 

- USA Nord Mitte
- USA (Ost) 2
- USA, Osten 2 (EUAP)
- USA, Mitte (EUAP)
- Kanada, Osten
- Kanada, Mitte
- Europa, Westen
- Nordeuropa
- Australien (Osten)
- Schweiz, Norden
- Deutschland, Westen-Mitte
- Asien, Osten
- Frankreich, Mitte

## <a name="pricing-and-billing"></a>Preise und Abrechnung

> [!IMPORTANT]
> Während der öffentlichen Vorschau fallen für die Verwendung von SFTP keine zusätzlichen Gebühren an. Die Standardpreise für Transaktionen, Speicher und Netzwerke für das zugrunde liegende Azure Data Lake Store Gen2-Konto gelten jedoch weiterhin. Bei SFTP fallen möglicherweise zusätzliche Gebühren an, wenn die Funktion allgemein verfügbar wird.  

Transaktions- und Speicherkosten basieren auf Faktoren wie dem Speicherkontotyp und dem Endpunkt, den Sie zum Übertragen von Daten an das Speicherkonto verwenden. Mehr Informationen finden Sie unter [Grundlegende Informationen zum vollständigen Abrechnungsmodell für Azure Blob Storage](../common/storage-plan-manage-costs.md#understand-the-full-billing-model-for-azure-blob-storage).

## <a name="see-also"></a>Siehe auch

- [Verbinden mit Azure Blob Storage mithilfe des SFTP-Protokolls (Secure File Transfer) (Vorschau)](secure-file-transfer-protocol-support-how-to.md)
- [Bekannte Probleme bei der Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage (Vorschau)](secure-file-transfer-protocol-known-issues.md)