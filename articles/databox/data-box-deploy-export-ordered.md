---
title: Tutorial zum Exportieren von Daten aus Azure Data Box | Microsoft-Dokumentation
description: Hier erfahren Sie, welche Voraussetzungen für die Bereitstellung gelten und wie Sie Daten aus einer Azure Data Box exportieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/21/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: db5b98170446e93737fd625671f5351cc11da337
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591917"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>Tutorial: Erstellen eines Exportauftrags für Azure Data Box

Azure Data Box ist eine Hybridlösung, mit der Sie Daten aus Azure in ihren Standort verschieben können. In diesem Tutorial wird beschrieben, wie Sie einen Exportauftrag für Azure Data Box erstellen. Der Hauptgrund für die Erstellung eines Exportauftrags ist die Notfallwiederherstellung, falls der lokale Speicher kompromittiert wird und eine Sicherungskopie wiederhergestellt werden muss.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen für den Export
> * Bestellen einer Data Box für den Export
> * Nachverfolgen des Exportauftrags
> * Stornieren des Exportauftrags

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Gerät bestellen, müssen die folgenden Konfigurationsvoraussetzungen für den Data Box-Dienst und das Data Box-Gerät erfüllt sein.

### <a name="for-service"></a>Für den Dienst

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Stellen Sie sicher, dass Sie über eine Ressourcengruppe verfügen, die Sie mit Ihrer Azure Data Box verwenden können.

* Stellen Sie sicher, dass Ihr Azure Storage-Konto, aus dem Sie Daten exportieren möchten, zu einem der in [Unterstützte Speicherkonten](data-box-system-requirements.md#supported-storage-accounts) beschriebenen Speicherkontotypen gehört.

### <a name="for-device"></a>Für das Gerät

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie verfügen über einen Hostcomputer, der mit dem Netzwerk des Datencenters verbunden ist. Sie kopieren die Daten aus der Azure Data Box auf diesen Computer. Auf dem Hostcomputer muss ein unterstütztes Betriebssystem ausgeführt werden (siehe [Azure Data Box – Systemanforderungen](data-box-system-requirements.md)).

* Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, kann auch eine 1-GbE-Datenverbindung verwendet werden, aber hierdurch wird die Geschwindigkeit der Kopiervorgänge beeinträchtigt.

## <a name="order-data-box-for-export"></a>Bestellen einer Data Box für den Export

Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Gerät zu bestellen.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen unter folgender URL an: [https://portal.azure.com](https://portal.azure.com).

2. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach *Azure Data Box*. Wählen Sie **Azure Data Box** aus.

   ![Erstellen einer Ressource](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. Klicken Sie auf **Erstellen**.

   ![Erstellen einer Azure Data Box-Ressource](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. Überprüfen Sie, ob der Azure Data Box-Dienst in Ihrer Region verfügbar ist. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend **Übernehmen** aus.

    |Einstellung  |Wert  |
    |---------|---------|
    |Übertragungstyp     | Wählen Sie **Nach Azure exportieren**.        |
    |Subscription     | Wählen Sie ein EA-, CSP- oder Azure Sponsorship-Abonnement für den Data Box-Dienst aus. <br> Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft.       |
    |Resource group     |    Wählen Sie eine vorhandene Ressourcengruppe aus. <br> Eine Ressourcengruppe ist ein logischer Container für die Ressourcen, die zusammen verwaltet oder bereitgestellt werden können.         |
    |Azure-Quellregion    |    Wählen Sie die Azure-Region aus, in der sich Ihre Daten zurzeit befinden.         |
    |Zielland     |     Wählen Sie das Land aus, wohin Sie das Gerät versenden möchten.        |

   ![Auswählen Ihrer Data Box-Einstellungen](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. Wählen Sie **Data Box** aus. Die maximal nutzbare Kapazität für eine einzelne Bestellung beträgt 80 TB. Sie können mehrere Bestellungen für größere Datenmengen erstellen.

   ![Auswählen der Data Box-Kapazität](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. Geben Sie in **Bestellung** die **Grundeinstellungen** für die Bestellung an. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus.

    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription     | Das Abonnement wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch eingetragen.|
    |Resource group | Die Ressourcengruppe, die Sie zuvor ausgewählt haben. |
    |Name des Exportauftrags     |  Geben Sie einen Anzeigenamen an, um die Bestellung nachzuverfolgen. <br> Der Name kann zwischen 3 und 24 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. <br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.      |

    ![Grundeinstellungen für den Exportauftrag](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    Klicken Sie auf **Weiter: Datenauswahl**, um fortzufahren.

7. Wählen Sie in **Datenauswahl** die Option **Speicherkonto und Exporttyp hinzufügen** aus.

    ![Hinzufügen von Speicherkonto und Exporttyp](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. Geben Sie in **Exportoption auswählen** die Exportoptiondetails an. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie anschließend **Hinzufügen** aus.

    |Einstellung  |Wert  |
    |---------|---------|
    |Speicherkonto     | Das Azure Storage-Konto, aus dem Sie Daten exportieren möchten. |
    |Exporttyp     | Gibt den Typ der aus **Alle Objekte** zu exportierenden Daten und **XML-Datei verwenden** an.<ul><li> **Alle Objekte**: Gibt an, dass der Auftrag alle Daten abhängig von Ihrer Auswahl für **Übertragungsoptionen** exportiert.</li><li> **XML-Datei verwenden**: Gibt eine XML-Datei an, die eine Reihe von Pfaden und Präfixen für Blobs und/oder Dateien enthält, die aus dem Speicherkonto exportiert werden sollen. Die XML-Datei muss sich im Container des ausgewählten Speicherkontos befinden, und die Auswahl aus Dateifreigaben wird derzeit nicht unterstützt. Die Datei muss eine nicht leere XML-Datei sein.</li></ul>        |
    |Übertragungsoptionen     |  Gibt die Datenübertragungsoptionen **Alle auswählen**, **Alle Blobs** und **Alle Dateien** an. <ul><li> **Alle auswählen**: Gibt an, dass alle Blobdateien und Azure Files exportiert werden. Wenn Sie ein Speicherkonto verwenden, das nur Blobs (Blobspeicherkonten) unterstützt, ist die Option **Alle Dateien** nicht auswählbar.</li><li> **Alle Blobs**: Gibt an, dass nur Block- und Seitenblobs exportiert werden.</li><li> **Alle Dateien**: Gibt an, dass alle Dateien außer Blobs exportiert werden. Der Typ Ihres Speicherkontos (GPv1 und GPv2, Storage Premium oder Blobspeicher) bestimmt, welche Datentypen Sie exportieren können. Weitere Informationen finden Sie unter [Unterstützte Speichertypen](../import-export/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Ausführliches Protokoll einschließen     | Gibt an, ob Sie eine ausführliche Protokolldatei mit einer Liste aller Dateien wünschen, die erfolgreich exportiert wurden. Weitere Informationen zu den Kopierprotokollen und ausführlichen Protokollen für einen Exportauftrag finden Sie unter [Anzeigen von Protokollen](data-box-export-logs.md#view-logs-during-data-copy). |

    > [!NOTE]
    > Wenn Sie **XML-Datei verwenden** für die Einstellung **Exporttyp** auswählen, müssen Sie sicherstellen, dass die XML-Datei gültige Pfade und/oder Präfixe enthält. Sie müssen die XML-Datei erstellen und bereitstellen. Wenn die Datei ungültig ist oder keine Daten mit den angegebenen Pfaden übereinstimmen, wird der Auftrag mit partiellen Daten oder ohne exportierte Daten beendet. Eine Anleitung finden Sie unter [Erstellen der XML-Datei](#create-xml-file).

    Informationen zum Hinzufügen einer XML-Datei zu einem Container finden Sie unter [Exportauftrag mit XML-Datei](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Auswählen der Exportoption](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   Ein Beispiel für die XML-Eingabe finden Sie unter [Erstellen der XML-Datei](#create-xml-file).

9. Überprüfen Sie Ihre Einstellungen in **Datenauswahl**, und wählen Sie **Weiter: Sicherheit>** , um den Vorgang fortzusetzen.

   ![Exportreihenfolge, Datenauswahl](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    Auf dem Bildschirm **Sicherheit** können Sie Ihren eigenen Verschlüsselungsschlüssel verwenden und die doppelte Verschlüsselung aktivieren.

    Alle Einstellungen auf dem Bildschirm **Sicherheit** sind optional. Wenn Sie keine der Einstellungen ändern, werden die Standardeinstellungen verwendet.

    ![Bildschirm „Sicherheit“ für den Assistenten für Data Box-Importaufträge](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. Erweitern Sie die Option **Verschlüsselungstyp**, wenn Sie Ihren eigenen kundenseitig verwalteten Schlüssel nutzen möchten, um den Hauptschlüssel zum Entsperren Ihrer neuen Ressource zu schützen.

    Das Konfigurieren eines kundenseitig verwalteten Schlüssels für Azure Data Box ist optional. Standardmäßig verwendet Data Box einen von Microsoft verwalteten Schlüssel zum Schützen des Hauptschlüssels für die Entsperrung.

    Ein kundenseitig verwalteter Schlüssel wirkt sich nicht darauf aus, wie Daten auf dem Gerät verschlüsselt werden. Der Schlüssel wird nur verwendet, um den Hauptschlüssel zum Entsperren des Geräts zu verschlüsseln.

    Wenn Sie keinen kundenseitig verwalteten Schlüssel verwenden möchten, fahren Sie mit Schritt 16 fort.

    ![Bildschirm „Sicherheit“ mit Einstellungen für den Verschlüsselungstyp](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. Wählen Sie **Kundenseitig verwalteter Schlüssel** als Schlüsseltyp aus. Wählen Sie anschließend die Option **Schlüsseltresor und Schlüssel auswählen** aus.
   
    ![Bildschirm „Sicherheit“ mit Einstellungen für einen kundenseitig verwalteten Schlüssel](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. Auf dem Bildschirm **Schlüssel aus Azure Key Vault auswählen** wird das Abonnementfeld automatisch aufgefüllt.

    - Für **Schlüsseltresor** können Sie einen vorhandenen Schlüsseltresor aus der Dropdownliste auswählen.

      ![Auswählen des Schlüssels auf dem Azure Key Vault-Bildschirm](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - Sie können auch **Neu erstellen** auswählen, um einen neuen Schlüsseltresor zu erstellen. Geben Sie auf dem Bildschirm **Schlüsseltresor erstellen** die Ressourcengruppe und einen Schlüsseltresornamen ein. Stellen Sie sicher, dass die Optionen **Vorläufiges Löschen** und **Löschschutz** aktiviert sind. Übernehmen Sie für alle anderen Einstellungen die Standardwerte, und wählen Sie dann **Überprüfen und erstellen** aus.

      ![Erstellen von neuen Azure Key Vault-Einstellungen](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      Überprüfen Sie die Informationen für Ihren Schlüsseltresor, und wählen Sie **Erstellen** aus. Warten Sie einige Minuten, bis die Erstellung des Schlüsseltresors abgeschlossen ist.

      ![Bildschirm mit Übersicht für neuen Schlüsseltresor](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. Auf dem Bildschirm **Schlüssel aus Azure Key Vault auswählen** können Sie einen Schlüssel auswählen, der im Schlüsseltresor vorhanden ist.

    ![Auswählen eines vorhandenen Schlüssels aus Azure Key Vault](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    Wählen Sie die Option **Neu erstellen** aus, wenn Sie einen neuen Schlüssel erstellen möchten. Sie müssen einen RSA-Schlüssel verwenden. Der Wert für die Größe kann „2048“ oder höher lauten. Geben Sie einen Namen für Ihren neuen Schlüssel ein, behalten Sie ansonsten die Standardeinstellungen bei, und wählen Sie **Erstellen** aus.

      ![Option zum Erstellen eines neuen Schlüssels](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      Sie werden benachrichtigt, wenn der Schlüssel in Ihrem Schlüsseltresor erstellt wurde.

14. Wählen Sie die **Version** des zu verwendenden Schlüssels und dann die Option **Auswählen** aus.

      ![Neu erstellter Schlüssel im Schlüsseltresor](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    Wählen Sie die Option **Neu erstellen** aus, wenn Sie eine neue Schlüsselversion erstellen möchten.

    ![Öffnen eines Dialogfelds zum Erstellen einer neuen Schlüsselversion](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    Wählen Sie auf dem Bildschirm **Neuen Schlüssel erstellen** Einstellungen für die neue Schlüsselversion und dann die Option **Erstellen** aus.

    ![Erstellen einer neuen Schlüsselversion](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    Auf dem Bildschirm **Sicherheit** werden in den Einstellungen unter **Verschlüsselungstyp** Ihr Schlüsseltresor und der Schlüssel angezeigt.

    ![Schlüssel und Schlüsseltresor für einen kundenseitig verwalteten Schlüssel](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. Wählen Sie eine Benutzeridentität aus, die Sie zum Verwalten des Zugriffs auf diese Ressource verwenden. Wählen Sie die Option **Benutzeridentität auswählen** aus. Wählen Sie im Bereich auf der rechten Seite das zu verwendende Abonnement und die verwaltete Identität aus. Wählen Sie anschließend **Auswählen** aus.

    Eine vom Benutzer zugewiesene verwaltete Identität ist eine eigenständige Azure-Ressource, die zum Verwalten mehrerer Ressourcen verwendet werden kann. Weitere Informationen finden Sie unter [Arten von verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md).  

    Befolgen Sie die Anleitung unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität über das Azure-Portal](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), wenn Sie eine neue verwaltete Identität erstellen müssen.
    
    ![Auswählen einer Benutzeridentität](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    Die Benutzeridentität wird in den Einstellungen unter **Verschlüsselungstyp** angezeigt.

    Sie können die Einstellungen unter **Verschlüsselungstyp** jetzt reduzieren.

    ![Ausgewählte Benutzeridentität in den Einstellungen unter „Verschlüsselungstyp“](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. Wenn Sie die softwarebasierte doppelte Verschlüsselung aktivieren möchten, erweitern Sie **Doppelte Verschlüsselung (für Hochsicherheitsumgebungen)** , und wählen Sie die Option **Doppelte Verschlüsselung für den Auftrag aktivieren** aus. 

    Die softwarebasierte Verschlüsselung wird zusätzlich zur AES-256-Bit-Verschlüsselung der Daten auf der Data Box ausgeführt.

    > [!NOTE]
    > Wenn Sie diese Option aktivieren, können die Auftragsverarbeitung und das Kopieren von Daten länger dauern. Nach dem Erstellen des Auftrags können Sie diese Option nicht mehr ändern.

    ![Bildschirm „Sicherheit“ für den Data Box-Import, doppelte Verschlüsselung](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    Klicken Sie auf **Weiter: Kontaktdetails**, um fortzufahren.

11. Wählen Sie in **Kontakt Details** die Option **+ Lieferadresse hinzufügen** aus, um Ihre Versandinformationen einzugeben.

    ![Lieferadresse hinzufügen](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. Geben Sie unter **Lieferadresse hinzufügen** Ihren Vor- und Nachnamen, den Namen und die Postanschrift des Unternehmens sowie eine gültige Telefonnummer an. Wählen Sie **Überprüfen** aus. Der Dienst überprüft anhand der Lieferadresse die Verfügbarkeit des Diensts. Wenn der Dienst für die angegebene Lieferadresse verfügbar ist, erhalten Sie eine entsprechende Benachrichtigung.

    ![Überprüfen der Lieferadresse](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    Wenn Sie in einer Region bestellen, wo der selbstverwaltete Versand verfügbar ist, können Sie diese Option auswählen. Weitere Informationen zum selbstverwalteten Versand finden Sie unter [Verwenden des selbstverwalteten Versands](data-box-portal-customer-managed-shipping.md).

13. Wählen Sie **Weiter** aus, wenn die Versanddetails erfolgreich überprüft wurden.

14. Überprüfen Sie in **Kontaktdetails** Ihre Lieferadresse und Ihre E-Mail-Adresse. Der Dienst sendet E-Mail-Benachrichtigungen in Bezug auf Aktualisierungen des Auftragsstatus an die angegebenen E-Mail-Adressen.

    Es wird empfohlen, eine E-Mail-Gruppenadresse zu verwenden, damit Sie weiterhin Benachrichtigungen erhalten, wenn ein Administrator die Gruppe verlässt.

    ![Kontaktdetails](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. Klicken Sie auf **Weiter: Überprüfen + bestellen**. Sie müssen die Geschäftsbedingungen akzeptieren, um mit der Auftragserstellung fortzufahren.

16. Wählen Sie **Bestellen** aus. Die Erstellung des Auftrags dauert einige Minuten.

    ![Committen des Auftrags](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>Exportauftrag mit XML-Datei

Wenn Sie **XML-Datei verwenden** auswählen, können Sie bestimmte Container und Blobs (Seite und Block) angeben, die Sie exportieren möchten. Die folgenden Schritte veranschaulichen, wie Sie die XML-Datei zum Exportieren von Daten verwenden. Eine Anleitung zum Erstellen der XML-Datei finden Sie unter [Erstellen der XML-Datei](#create-xml-file).

So verwenden Sie eine XML-Datei zum Exportieren Ihrer Daten:

1. Wählen Sie für **Exporttyp** die Option **XML-Datei verwenden** aus. Dies ist Ihre XML-Datei, die bestimmte Blobdateien und Azure-Dateien angibt, die Sie exportieren möchten. Um die XML-Datei hinzuzufügen, wählen Sie **Klicken Sie hier, um eine XML-Datei auszuwählen**.

     ![Auswählen der Exportoption, XML](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. Wählen Sie **+ Container**, um einen Container zu erstellen.

    ![Auswählen der Exportoption, Container](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Fügen Sie auf der Registerkarte **Neuer Container**, die auf der rechten Seite des Azure-Portals angezeigt wird, einen Namen für den Container hinzu. Der Name darf nur Kleinbuchstaben enthalten, und Sie können Zahlen und Bindestriche „-“ einschließen. Wählen Sie dann im Dropdownlistenfeld die **Öffentliche Zugriffsebene** aus. Sie sollten **Privat (nicht anonymer Zugriff)** auszuwählen, um andere Personen am Zugriff auf Ihre Daten zu hindern. Weitere Informationen zu Zugriffsebenen für Container finden Sie unter [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

   ![Auswählen der Exportoption, Einstellungen für neuen Container](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. Klicken Sie auf **Erstellen**.

   ![Auswählen der Exportoption, Erstellen des neuen Containers](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   Wenn Ihr Container erfolgreich erstellt wurde, wird die folgende Meldung angezeigt:

   ![Container erfolgreich erstellt](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. Wählen Sie den Container aus, den Sie erstellt haben, und doppelklicken Sie darauf.

   ![Anzeigen der Containerdetails](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. Wenn Sie auf den Container doppelklicken, werden die Containereigenschaften angezeigt. Nun können Sie Ihre XML-Datei anfügen (oder auswählen), die die Liste der zu exportierenden Blobs und/oder Azure Files enthält. Wählen Sie die Option **Hochladen**.

   ![Hochladen eines Blobs in einen Container](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. Die XML-Datei wurde dem Container erfolgreich hinzugefügt. Nur Blobs und Azure Files, die Sie in dieser XML-Datei angegeben haben, werden exportiert.

   ![Zum Container hinzugefügte XML-Datei](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="create-xml-file"></a>Erstellen der XML-Datei

Befolgen Sie die folgenden Richtlinien, um die XML-Datei zu erstellen, wenn Sie Blobs und Dateien für den Export mithilfe einer XML-Datei auswählen:
- **Registerkarte „XML-Beispieldatei“** : Kopieren Sie eine XML-Beispieldatei mit Beispielen für die einzelnen Tags.
- **Registerkarte „XML-Dateiübersicht“** : Überprüfen Sie die Taganforderungen für die XML-Datei.
- **Registerkarte „Präfixbeispiele“** : Sehen Sie sich Beispiele für gültige Präfixe an, die mehrere Blobs und Dateien für den Export auswählen.

### <a name="sample-xml-file"></a>[Beispiel für eine XML-Datei](#tab/sample-xml-file)

Diese XML-Beispieldatei enthält Beispiele für alle XML-Tags, die zum Auswählen von Blobs und Dateien für den Export in einem Data Box-Exportauftrag verwendet werden. 

- Informationen zu den XML-Dateianforderungen finden Sie auf der Registerkarte **XML-Dateiübersicht**.
- Weitere Beispiele für gültige Blob- und Dateipräfixe finden Sie auf der Registerkarte **Präfixbeispiele**.

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!--BlobList selects individual blobs (BlobPath) and multiple blobs (BlobPathPrefix) in Blob storage for export.-->
   <BlobList>
      <BlobPath>/container1/blob.txt</BlobPath> <!-- Exports /container1/blob.txt -->
      <BlobPathPrefix>/container2/</BlobPathPrefix> <!--Exports all blobs in container2 -->
      <BlobPathPrefix>/container</BlobPathPrefix>  <!-- Exports all containers beginning with prefix: "container" -->
      <BlobPathPrefix>/container1/2021Q2</BlobPathPrefix> <!-- Exports all blobs in container1 with prefix: "2021Q2" -->
   </BlobList>
   
   <!--AzureFileList selects individual files (FilePath) and multiple files (FilePathPrefix) in Azure File storage for export.-->
   <AzureFileList>
      <FilePath>/fileshare1/file.txt</FilePath> <!-- Exports /fileshare1/file.txt -->
      <FilePathPrefix>/fileshare1/</FilePath> <!-- Exports all directories and files in fileshare1 -->
      <FilePathPrefix>/fileshare</FilePathPrefix> <!-- Exports all directories and files in any fileshare with prefix: "fileshare" -->
      <FilePathPrefix>/fileshare2/contosowest</FilePathPrefix> <!-- Exports all directories and files in fileshare2 with prefix: "contosowest" -->
   </AzureFileList>
```

### <a name="xml-file-overview"></a>[XML-Dateiübersicht](#tab/xml-file-overview)

Befolgen Sie diese Richtlinien, wenn Sie die XML-Datei für Ihren Exportauftrag erstellen. Falsche Tagformate können zu Exportfehlern führen.

Schritte zum Hochladen der XML-Datei beim Erteilen eines Exportauftrags finden Sie unter [Exportauftrag mit einer XML-Datei](#export-order-using-xml-file).

#### <a name="path-vs-prefix"></a>Pfad im Vergleich zu Präfix

Um die XML-Tags in Ihrer XML-Datei ordnungsgemäß zu formen, müssen Sie den Unterschied zwischen einem Pfad und einem Präfix verstehen:

* Ein *Pfad* bietet die Möglichkeit, ein einzelnes Blob oder eine einzelne Datei auszuwählen und zu filtern.
* Ein *Präfix* bietet die Möglichkeit, mehrere Blobs oder Dateien auszuwählen und zu filtern.

Beispiele für ordnungsgemäß formatierte Präfixe finden Sie auf der Registerkarte **Präfixbeispiele**.

#### <a name="tag-usage"></a>Tagverwendung

Die folgenden XML-Tags werden in der XML-Datei für einen Data Box-Exportauftrag verwendet:

| XML-Tag           |BESCHREIBUNG |
|-------------------|------------|
|`<BlobList>`       |Übergeordnetes Tag für &lt;BlobPath&gt;- und &lt;BlobPathPrefix&gt;-Tags.|
|`<BlobPath>`       |Wählt ein einzelnes Blob aus. |
|`<BlobPathPrefix>` |Wählt Blobs mit einem gemeinsamen Präfix aus. Beispiele finden Sie auf der Registerkarte **Präfixbeispiele**.|
|`<AzureFileList>`  |Übergeordnetes Tag für &lt;FilePath&gt;- und &lt;FilePathPrefix&gt;-Tags.|
|`<FilePath>`       |Wählt eine einzelne Datei aus. |
|`<FilePathPrefix>` |Wählt Dateien mit einem gemeinsamen Präfix aus. Beispiele finden Sie auf der Registerkarte **Präfixbeispiele**.|

Um die Tags im Kontext anzuzeigen, wechseln Sie zur Registerkarte **XML-Beispieldatei.**

#### <a name="xml-tag-requirements"></a>XML-Taganforderungen

* Alle XML-Tags berücksichtigen Groß-/Kleinschreibung und müssen genau mit den Tags in der obigen Tabelle übereinstimmen.
* Öffnende und schließende Tags müssen einander entsprechen.
* Falsche XML-Tags oder Formatierungen führen möglicherweise zu Datenexportfehlern.
* Wenn Blob- oder Dateipräfixe ungültig sind, werden keine Daten exportiert. Beispiele für gültige Präfixe finden Sie auf der Registerkarte **Präfixbeispiele**.

### <a name="prefix-examples"></a>[Präfixbeispiele](#tab/prefix-examples)

Diese Beispielpfade zeigen verschiedene Möglichkeiten zum Erstellen eines Präfixes, um mehrere Blobs oder Dateien für den Export auszuwählen.

#### <a name="valid-blob-path-prefixes"></a>Gültige Blobpfadpräfixe

Die folgenden Beispielpfade werden mit dem &lt;BlobPathPrefix&gt;-Tag verwendet, um mehrere Blobs in Azure Blob Storage für den Export auszuwählen.

|Blobpfadpräfix        |BESCHREIBUNG                                                                     |Tagbeispiel                         |
|------------------------|--------------------------------------------------------------------------------|------------------------------------|
|/                       |Exportiert alle Blobs im Speicherkonto.                                       |`<BlobPathPrefix>/</BlobPathPrefix>`|
|/$root/                 |Exportiert alle Blobs im Stammcontainer.                                        |`<BlobPathPrefix>/$root/</BlobPathPrefix>`|
|/container2/            |Exportiert alle Blobs im Container **container2**.                              |`<BlobPathPrefix>/container1/</BlobPathPrefix>`|
|/container          |Exportiert alle Blobs in allen Containern, die mit dem Präfix **container** beginnen.      |`<BlobPathPrefix>/containers</BlobPathPrefix>`|
|/container1/2021Q2      |Exportiert alle Blobs im Container **container1**, die mit dem Präfix **2021Q2** beginnen.|`<BlobPathPrefix>/container1/2021Q2</BlobPathPrefix>`|

Um ein *einzelnes* Blob für den Export auszuwählen, verwenden Sie das &lt;BlobPath&gt;-Tag mit einem Containerpfad und einem Blobnamen. Wenn Sie beispielsweise **blob.txt** im Container **container1** auswählen möchten, verwenden Sie dieses Tag: `<BlobPath>/container1/blob.txt</BlobPath>`.

#### <a name="valid-file-path-prefixes"></a>Gültige Dateipfadpräfixe

Die folgenden Beispielpfade werden mit dem &lt;FilePathPrefix&gt;-Tag verwendet, um mehrere Azure-Dateien für den Export auszuwählen.

|Dateipfadpräfix        |BESCHREIBUNG                                                                                          |Tagbeispiel|
|------------------------|-----------------------------------------------------------------------------------------------------|-----------|
|/                       |Exportiert alle Dateien und Verzeichnisse im Speicherkonto. |`<FilePathPrefix>/</FilePath>Prefix`|
|/fileshare1/            |Exportiert alle Dateien und Verzeichnisse in der Freigabe namens **fileshare1**.                                                 |`<FilePathPrefix>/fileshare1/</FilePath>Prefix`|
|/fileshare              |Exportiert alle Dateien und Verzeichnisse in jeder Dateifreigabe, die mit dem Präfix **fileshare** beginnen. |`<FilePathPrefix>/fileshare</FilePath>Prefix`|
|/fileshare2/contosowest |Exportiert alle Dateien und Verzeichnisse in der Dateifreigabe **fileshare2**, die mit dem Präfix **contosowest** beginnen.|`<FilePathPrefix>/fileshare1/contosowest</FilePath>Prefix`|

Um eine *einzelne* Datei für den Export auszuwählen, verwenden Sie das &lt;FilePath&gt;-Tag mit einem Freigabepfad und einem Dateinamen. Wenn Sie beispielsweise **file.txt** in **fileshare1** auswählen möchten, verwenden Sie dieses Tag: `<FilePath>/fileshare1/file.txt</FilePath>`.

---

## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Nachdem Sie die Bestellung aufgegeben haben, können Sie ihren Status im Azure-Portal nachverfolgen. Navigieren Sie zu Ihrer Data Box-Bestellung, und navigieren Sie dann zu **Übersicht**, um den Status anzuzeigen. Die Bestellung wird im Portal mit dem Status **Bestellt** angezeigt.

Nachdem die Gerätevorbereitung abgeschlossen ist, beginnt das Kopieren der Daten aus den ausgewählten Speicherkonten. Im Portal wird der Auftrag mit dem Status **Daten werden kopiert** angezeigt.

![Data Box-Exportreihenfolge, Daten werden kopiert](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box kopiert Daten aus den Quellspeicherkonten. Nachdem das Kopieren der Daten abgeschlossen ist, wird Data Box gesperrt, und im Portal wird der Auftrag mit dem Status **Kopiervorgang abgeschlossen** angezeigt.

![Data Box-Exportreihenfolge, Datenvorgang abgeschlossen](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Beim Datenexport von Azure Storage in Ihre Data Box kann manchmal ein Fehler auftreten. Stellen Sie sicher, dass die Blobs keine Archivblobs sind, da der Export dieser Blobs nicht unterstützt wird. 

> [!NOTE]
> Für Archivblobs müssen Sie diese Blobs wieder aktivieren, bevor sie aus ihrem Azure Storage-Konto in Ihre Data Box exportiert werden können. Weitere Informationen finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene]( ../storage/blobs/storage-blob-rehydration.md).

Sollte das Gerät nicht verfügbar sein, erhalten Sie eine entsprechende Benachrichtigung. Wenn das Gerät verfügbar ist, identifiziert Microsoft das Gerät für den Versand und bereitet den Versand vor. Während der Vorbereitung des Geräts werden folgende Aktionen ausgeführt:

* Für jedes mit dem Gerät verknüpfte Speicherkonto werden SMB-Freigaben erstellt.
* Für jede Freigabe werden Anmeldeinformationen wie Benutzername und Kennwort generiert.
* Das Gerät ist gesperrt, und für den Zugriff ist das Kennwort zum Entsperren des Geräts erforderlich. Zum Abrufen des Kennworts müssen Sie sich bei Ihrem Azure-Portalkonto anmelden und **Gerätedetails** auswählen.

Microsoft bereitet dann Ihr Gerät vor und versendet es über einen regionalen Kurierdienst. Sobald das Gerät versandt wurde, erhalten Sie eine Nachverfolgungsnummer. Das Portal zeigt die Bestellung im Zustand **Versandt** an.

![Data Box-Exportauftrag wurde versandt](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

Wenn Sie den selbstverwalteten Versand ausgewählt haben, erhalten Sie eine E-Mail-Benachrichtigung mit den nächsten Schritten, wenn das Gerät aus dem Rechenzentrum abgeholt werden kann. Weitere Informationen zum selbstverwalteten Versand finden Sie unter [Verwenden des selbstverwalteten Versands für Azure Data Box im Azure-Portal](data-box-portal-customer-managed-shipping.md).

![Selbstverwalteter Versand: bereit für Abholung](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>Stornieren der Bestellung

Um die Bestellung zu stornieren, navigieren Sie im Azure-Portal zu **Übersicht** und wählen dann auf der Befehlsleiste die Option **Stornieren** aus.

Solange ein Auftrag noch nicht verarbeitet wird, können Sie ihn jederzeit stornieren.

Um eine stornierte Bestellung zu löschen, navigieren Sie zu **Übersicht** und wählen dann auf der Befehlsleiste die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
>
> * Voraussetzungen für den Export
> * Bestellen einer Data Box für den Export
> * Nachverfolgen des Exportauftrags
> * Stornieren des Exportauftrags

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box einrichten.

> [!div class="nextstepaction"]
> [Einrichten der Azure Data Box](./data-box-deploy-set-up.md)
