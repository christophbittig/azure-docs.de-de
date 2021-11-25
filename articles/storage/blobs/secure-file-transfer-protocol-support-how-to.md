---
title: Verbinden zu Azure Blob Storage mithilfe des SFTP-Protokolls (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die SFTP-Unterstützung für Ihr Azure Blob Storage-Konto aktivieren, damit Sie mithilfe eines SFTP-Clients direkt eine Verbindung mit Ihrem Azure Storage-Konto herstellen können.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: b5249fa06dad80cb675723f724b12cf638c452fe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557265"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>Verbinden mit Azure Blob Storage mithilfe des SFTP-Protokolls (Secure File Transfer) (Vorschau)

Sie können eine sichere Verbindung mit dem Blob-Storage-Endpunkt eines Azure Storage-Kontos herstellen, indem Sie einen SFTP-Client verwenden und dann Dateien hochladen und herunterladen. In diesem Artikel erfahren Sie, wie Sie die SFTP-Protokollunterstützung aktivieren und dann mithilfe eines SFTP-Clients eine Verbindung mit Blob Storage herstellen. 

Weitere Informationen zur SFTP-Protokollunterstützung in Azure Blob Storage finden Sie unter [Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage](secure-file-transfer-protocol-support.md).

> [!IMPORTANT]
> Die SFTP-Protokollunterstützung befindet sich derzeit in der Vorschauversion und ist in [diesen Regionen](secure-file-transfer-protocol-support.md#regional-availability) verfügbar.
>
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/r/gZguN0j65Y).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Standard-Blockblob-Speicherkonto vom Typ „Allgemein v2“ oder „Premium“ Weitere Informationen zu diesen Speicherkontotypen finden Sie unter [Speicherkontoübersicht](../common/storage-account-overview.md).

- Die Kontoredundanzoption des Speicherkontos ist entweder auf lokal redundanten Speicher (LRS) oder zonenredundanten Speicher (ZRS) festgelegt.

- Das Feature für hierarchische Namespaces des Kontos muss aktiviert sein. Informationen zum Aktivieren des Features für hierarchische Namespaces finden Sie unter [Aktualisieren von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md).

- Wenn Sie eine Verbindung aus einem lokalen Netzwerk heraus herstellen, stellen Sie sicher, dass Ihr Client die ausgehende Kommunikation über den Port 22 zulässt. Das SFTP-Protokoll verwendet diesen Port.

## <a name="register-the-feature"></a>Registrieren der Funktion

Bevor Sie die SFTP-Unterstützung aktivieren können, müssen Sie das SFTP-Feature bei Ihrem Abonnement registrieren.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.

2. Öffnen Sie die Konfigurationsseite Ihres Abonnements.

3. Wählen Sie unter **Einstellungen** die Option **Previewfunktionen** aus.

   > [!div class="mx-imgBorder"]
   > ![Vorschaueinstellung](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. Wählen Sie auf der Seite **Previewfunktionen** das Feature **AllowSFTP** und dann **Registrieren** aus.

### <a name="verify-feature-registration"></a>Überprüfen der Featureregistrierung

Vergewissern Sie sich, dass das Feature registriert ist, bevor Sie mit den anderen Schritten in diesem Artikel fortfahren. 

1. Öffnen Sie die Seite **Previewfunktionen** Ihres Abonnements. 

2. Suchen Sie das **AllowSFTP**-Feature, und stellen Sie sicher, dass **Registriert** in der Spalte **Status** angezeigt wird.

## <a name="enable-sftp-support"></a>Aktivieren der SFTP-Unterstützung

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto.

2. Wählen Sie unter **Einstellungen** die Option **SFTP** aus.

   > [!NOTE]
   > Diese Option wird nur angezeigt, wenn das Feature für hierarchische Namespaces des Kontos aktiviert wurde. Informationen zum Aktivieren des Features für hierarchische Namespaces finden Sie unter [Aktualisieren von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md).

3. Wählen Sie **SFTP aktivieren** aus. 

   > [!div class="mx-imgBorder"]
   > ![Schaltfläche „SFTP aktivieren“](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > Wenn auf der SFTP-Konfigurationsseite keine lokalen Benutzer angezeigt werden, müssen Sie mindestens einen von ihnen hinzufügen. Informationen zum Hinzufügen lokaler Benutzer finden Sie im nächsten Abschnitt.

## <a name="configure-permissions"></a>Konfigurieren von Berechtigungen

Azure Storage unterstützt keine SAS-Authentifizierung (Shared Access Signature) oder Azure Active Directory-Authentifizierung (Azure AD) zum Zugriff auf den SFTP-Endpunkt. Stattdessen müssen Sie eine Identität namens lokaler Benutzer verwenden, die mit einem von Azure generierten Kennwort oder einem SSH-Schlüsselpaar (Secure Shell) geschützt werden kann. Dem Speicherkonto muss eine Identität zugeordnet sein, die mit diesem Kennwort oder Schlüsselpaar verknüpft ist, um einem Verbindungsherstellungsclient Zugriff zu gewähren. Diese Identität wird als *lokaler Benutzer* bezeichnet. 

In diesem Abschnitt erfahren Sie, wie Sie einen lokalen Benutzer erstellen, eine Authentifizierungsmethode auswählen und diesem lokalen Benutzer dann Berechtigungen zuweisen. 

Weitere Informationen zum SFTP-Berechtigungsmodell finden Sie unter [SFTP-Berechtigungsmodell](secure-file-transfer-protocol-support.md#sftp-permissions-model). 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto.

2. Wählen Sie unter **Einstellungen** den Eintrag **SFTP** und dann **Lokalen Benutzer hinzufügen** aus. 

   > [!div class="mx-imgBorder"]
   > ![Schaltfläche „Lokale Benutzer hinzufügen“](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. Fügen Sie im Konfigurationsbereich **Lokalen Benutzer hinzufügen** den Namen eines Benutzers hinzu, und wählen Sie dann aus, welche Authentifizierungsmethoden Sie diesem lokalen Benutzer zuordnen möchten. Sie können ein Kennwort und/oder einen SSH-Schlüssel zuordnen. 

   > [!IMPORTANT]
   > Sie können zwar beide Authentifizierungsarten aktivieren, SFTP-Clients können jedoch nur eine Verbindung herstellen. Die MFA, bei der sowohl ein gültiges Kennwort als auch ein gültiges Paar aus öffentlichem und privatem Schlüssel für eine erfolgreiche Authentifizierung erforderlich sind, wird jedoch nicht unterstützt.

   Wenn Sie **Mit Kennwort schützen** auswählen, wird Ihr Kennwort angezeigt, wenn Sie alle Schritte im Konfigurationsbereich **Lokalen Benutzer hinzufügen** abgeschlossen haben.

   Wenn Sie **Mit öffentlichem SSH-Schlüssel schützen** auswählen, wählen Sie **Schlüsselquelle hinzufügen** aus, um eine Schlüsselquelle anzugeben. 

   > [!div class="mx-imgBorder"]
   > ![Konfigurationsbereich „Lokaler Benutzer“](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   In der folgenden Tabelle werden die einzelnen Schlüsselquellenoptionen beschrieben:

   | Option | Anleitungen |
   |----|----|
   | Ein neues Schlüsselpaar generieren | Verwenden Sie diese Option, um ein neues Paar aus öffentlichem und privatem Schlüssel zu erstellen. Der öffentliche Schlüssel wird in Azure mit dem Schlüsselnamen gespeichert, den Sie angeben. Der private Schlüssel kann heruntergeladen werden, nachdem der lokale Benutzer erfolgreich hinzugefügt wurde. |
   | In Azure gespeicherten vorhandenen Schlüssel verwenden | Verwenden Sie diese Option, wenn Sie einen öffentlichen Schlüssel verwenden möchten, der bereits in Azure gespeichert ist. Informationen zum Suchen vorhandener Schlüssel in Azure finden Sie unter [Auflisten von Schlüsseln](/azure/virtual-machines/ssh-keys-portal#list-keys). Wenn SFTP-Clients eine Verbindung mit Azure Blob Storage herstellen, müssen diese Clients den privaten Schlüssel angeben, der diesem öffentlichen Schlüssel zugeordnet ist. |
   | Verwenden Sie den vorhandenen öffentlichen Schlüssel. | Verwenden Sie diese Option, wenn Sie einen öffentlichen Schlüssel hochladen möchten, der außerhalb von Azure gespeichert ist. Wenn Sie nicht über einen öffentlichen Schlüssel verfügen, aber einen außerhalb von Azure generieren möchten, finden Sie weitere Informationen unter [Generieren von Schlüsseln mit ssh-keygen](/azure/virtual-machines/linux/create-ssh-keys-detailed#generate-keys-with-ssh-keygen). |

4. Wählen Sie **Weiter** aus, um die Registerkarte **Containerberechtigungen** im Konfigurationsbereich zu öffnen.

5. Wählen Sie auf der Registerkarte **Containerberechtigungen** die Container aus, die Sie diesem lokalen Benutzer zur Verfügung stellen möchten. Wählen Sie dann aus, welche Arten von Vorgängen dieser lokale Benutzer ausführen soll.

   > [!div class="mx-imgBorder"]
   > ![Registerkarte Containerberechtigungen](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. Geben Sie im Bearbeitungsfeld **Startverzeichnis** den Namen des Containers oder den Verzeichnispfad (einschließlich des Containernamens) ein, der dem lokalen Benutzer als Standardspeicherort zugeordnet ist. 

   Weitere Informationen zum Stammverzeichnis finden Sie unter [Startverzeichnis](secure-file-transfer-protocol-support.md#home-directory).

7. Klicken Sie auf die **Hinzufügen-Schaltfläche**, um fortzufahren.

   Wenn Sie die Kennwortauthentifizierung aktiviert haben, wird das von Azure generierte Kennwort in einem Dialogfeld angezeigt, nachdem der lokale Benutzer hinzugefügt wurde. 

   > [!IMPORTANT]
   > Sie können dieses Kennwort später nicht abrufen. Achten Sie daher darauf, das Kennwort zu kopieren und es dann an einem Ort zu speichern, an dem Sie es finden.

   Wenn Sie ein neues Schlüsselpaar generieren möchten, werden Sie aufgefordert, den privaten Schlüssel dieses Schlüsselpaars herunterzuladen, nachdem der lokale Benutzer hinzugefügt wurde.

## <a name="connect-an-sftp-client"></a>Verbinden eines SFTP-Clients

Sie können einen beliebigen SFTP-Client verwenden, um eine sichere Verbindung herzustellen und dann Dateien zu übertragen. Der folgende Screenshot zeigt eine Windows PowerShell Sitzung, die die [Open SSH](/windows-server/administration/openssh/openssh_overview)- und Kennwortauthentifizierung verwendet, um eine Verbindung herzustellen und dann eine Datei mit dem Namen `logfile.txt` hochzuladen.  

> [!div class="mx-imgBorder"]
> ![Verbinden mit Open SSH](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> Möglicherweise werden Sie aufgefordert, einem Hostschlüssel zu vertrauen. Während der öffentlichen Vorschau werden gültige Hostschlüssel [hier](secure-file-transfer-protocol-host-keys.md) veröffentlicht.  

Nach Abschluss der Übertragung können Sie die Datei im Azure-Portal anzeigen und verwalten. 

> [!div class="mx-imgBorder"]
> ![Hochgeladene Datei wird im Speicherkonto angezeigt](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Der Azure-Portal verwendet die Blob-REST-API und die Data Lake Storage Gen2-REST-API. Die Interaktion mit einer hochgeladenen Datei im Azure-Portal veranschaulicht die Interoperabilität zwischen SFTP und REST.

Anleitungen zum Verbinden und Übertragen von Dateien finden Sie in der Dokumentation Ihres SFTP-Clients.

## <a name="see-also"></a>Siehe auch

- [Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage](secure-file-transfer-protocol-support.md)
- [Bekannte Probleme bei der Unterstützung des SFTP-Protokolls (Secure File Transfer) in Azure Blob Storage](secure-file-transfer-protocol-known-issues.md)
