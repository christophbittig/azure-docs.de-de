---
title: 'Tutorial: Zurücksenden von Azure Data Box Heavy | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Data Box Heavy zurücksenden. Die Informationen umfassen die Versandvorbereitung, den Versand von Data Box Heavy, die Überprüfung des Datenuploads sowie die Datenlöschung.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: aad9a66c37b4d9d4ba590e339cd2188e5e194c64
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449923"
---
::: zone target = "docs"

# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Tutorial: Zurücksenden von Azure Data Box Heavy und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target = "chromeless"

## <a name="return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Zurücksenden von Azure Data Box Heavy und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target = "docs"

In diesem Tutorial erfahren Sie, wie Sie Azure Data Box Heavy zurücksenden und die in Azure hochgeladenen Daten überprüfen.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Versenden von Data Box Heavy an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von Data Box Heavy

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie haben die Schritte unter [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-heavy-deploy-copy-data.md).
- Alle Kopieraufträge sind abgeschlossen. „Für Versand vorbereiten“ kann nicht ausgeführt werden, wenn noch Kopieraufträge ausgeführt werden.


## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

::: zone-end

::: zone target = "chromeless"

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

Vergewissern Sie sich vor der Vorbereitung für den Versand, dass Kopieraufträge abgeschlossen sind.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zur Seite „Für den Versand vorbereiten“, und beginnen Sie mit der Versandvorbereitung.
2. Schalten Sie das Gerät auf der lokalen Webbenutzeroberfläche aus. Ziehen Sie die Kabel vom Gerät ab.

Sie sind jetzt bereit, Ihr Gerät zurückzusenden.

::: zone-end

## <a name="ship-data-box-heavy-back"></a>Zurücksenden von Data Box Heavy

1. Stellen Sie sicher, dass das Gerät ausgeschaltet ist und alle Kabel entfernt wurden. Wickeln Sie die vier Netzkabel auf, und legen Sie sie in die Ablage (auf der Rückseite des Geräts).
2. Das Gerät versendet LTL-Fracht per FedEx in die USA und per DHL in die EU.

    1. Wenden Sie sich an [Data Box Operations](mailto:DataBoxOps@microsoft.com), um sie über die Abholung zu informieren und das Adressetikett für den Rückversand zu erhalten.
    2. Vereinbaren Sie unter der lokalen Telefonnummer Ihres Transportdienstleisters einen Abholtermin.
    3. Achten Sie darauf, dass der Adressaufkleber gut sichtbar auf der Außenseite der Sendung angebracht ist.
    4. Achten Sie darauf, dass die alten Adressaufkleber des vorherigen Versands vom Gerät entfernt wurden.
3. Nachdem Data Box Heavy von Ihrem Transportdienstleister abgeholt und gescannt wurde, ändert sich der Auftragsstatus im Portal in **Abgeholt**. Außerdem wird eine Nachverfolgungs-ID angezeigt.

::: zone target = "docs"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Nachdem das Gerät bei Microsoft eingegangen ist und gescannt wurde, wird der Status der Bestellung in **Empfangen** geändert. Das Gerät wird dann physisch auf Schäden oder Anzeichen einer Manipulation überprüft.

Nach der Überprüfung wird Data Box Heavy mit dem Netzwerk im Azure-Datencenter verbunden. Das Kopieren der Daten wird automatisch gestartet. Je nach Datengröße kann der Kopiervorgang einige Stunden oder auch einige Tage dauern. Sie können den Status des Kopiervorgangs im Portal verfolgen.

Nachdem der Kopiervorgang abgeschlossen ist, wird der Auftragsstatus in **Completed** (Abgeschlossen) geändert.

Stellen Sie sicher, dass Ihre Daten in Azure hochgeladen wurden, bevor Sie sie aus der Quelle löschen. Ihre Daten können sich an folgenden Orten befinden:

- In Ihren Azure Storage-Konten. Wenn Sie die Daten in Data Box kopieren, werden die Daten abhängig vom Typ in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen:

  - Blockblobs und Seitenblobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativ hierzu können Sie auch im Azure-Portal auf Ihr Azure-Speicherkonto zugreifen und von dort aus entsprechend navigieren.

- In Ihren Ressourcengruppen für verwaltete Datenträger. Beim Erstellen von verwalteten Datenträgern werden die VHDs als Seitenblobs hochgeladen und dann in verwaltete Datenträger konvertiert. Die verwalteten Datenträger werden an die Ressourcengruppen angefügt, die zum Zeitpunkt der Auftragserstellung angegeben waren. 

    - Wenn der Kopiervorgang auf verwaltete Datenträger in Azure erfolgreich war, können Sie im Azure-Portal zu **Auftragsdetails** navigieren und sich die Ressourcengruppen notieren, die für verwaltete Datenträger angegeben sind.

        ![Identifizieren von Ressourcengruppen für verwaltete Datenträger](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Wechseln Sie zu der Ressourcengruppe, die Sie notiert haben, und suchen Sie Ihre verwalteten Datenträger.

        ![An Ressourcengruppen angefügter verwalteter Datenträger](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Wenn Sie eine VHDX oder eine dynamische/differenzierende VHD kopiert haben, wird die VHDX bzw. VHD als Seitenblob in das Stagingspeicherkonto hochgeladen, aber die VHD kann nicht in einen verwalteten Datenträger konvertiert werden. Wechseln Sie zu Ihrem **Stagingspeicherkonto > Blobs**, und wählen Sie den geeigneten Container aus: SSD Standard, HDD Standard oder SSD Premium. Die VHDs werden als Seitenblobs in Ihr Stagingspeicherkonto hochgeladen.
    
::: zone-end

::: zone target = "chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Wenn das Data Box Heavy-Gerät mit dem Netzwerk des Azure-Datencenters verbunden wird, beginnt das Hochladen der Daten in Azure automatisch. Sie werden vom Data Box-Dienst über das Azure-Portal benachrichtigt, dass der Kopiervorgang für die Daten abgeschlossen ist.

- Prüfen Sie die Fehlerprotokolle auf Fehler, und ergreifen Sie ggf. entsprechende Maßnahmen.
- Stellen Sie sicher, dass sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen.

::: zone-end

## <a name="erasure-of-data-from-data-box-heavy"></a>Löschen von Daten von Data Box Heavy
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Nach Abschluss des Löschvorgangs können Sie den [Auftragsverlauf herunterladen](data-box-portal-admin.md#download-order-history).

::: zone target = "docs"

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Versenden von Data Box Heavy an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von Data Box Heavy

Im nächsten Artikel erfahren Sie, wie Sie Data Box Heavy über die lokale Webbenutzeroberfläche verwalten.

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten von Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


