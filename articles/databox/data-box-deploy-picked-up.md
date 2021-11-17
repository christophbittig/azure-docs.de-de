---
title: Tutorial zur Rücksendung von Azure Data Box | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure Data Box zurücksenden. Die Informationen umfassen die Versandvorbereitung, den Versand von Data Box, die Überprüfung des Datenuploads sowie die Datenlöschung von Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/16/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c3dd0d64542dd9d25643fb811accde5d882af870
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556567"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Zurücksenden der Azure Data Box und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Zurücksenden der Data Box und Überprüfen des Datenuploads in Azure

::: zone-end

::: zone target="docs"

In diesem Tutorial wird beschrieben, wie Sie die Azure Data Box zurücksenden und die in Azure hochgeladenen Daten überprüfen.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten von der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben die Schritte ausgeführt in [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-deploy-copy-data.md).
* Die Durchführung der Kopieraufträge ist abgeschlossen, und auf der Seite **Verbindung herstellen und Daten kopieren** werden keine Fehler angezeigt. Die **Versandvorbereitung** kann nicht durchgeführt werden, wenn noch Kopieraufträge in Bearbeitung oder auf der Seite **Verbindung herstellen und Daten kopieren** Fehler aufgeführt sind.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Nach dem Datenkopiervorgang bereiten Sie das Gerät vor und versenden es. Wenn das Gerät das Azure-Datencenter erreicht, werden die Daten automatisch in Azure hochgeladen.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

Vergewissern Sie sich vor der Vorbereitung für den Versand, dass Kopieraufträge abgeschlossen sind.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zur Seite **Für den Versand vorbereiten**, und beginnen Sie mit der Versandvorbereitung.
2. Schalten Sie das Gerät auf der lokalen Webbenutzeroberfläche aus. Ziehen Sie die Kabel vom Gerät ab.

Die nächsten Schritte hängen davon ab, wo Sie das Gerät zurückgeben.

::: zone-end


## <a name="ship-data-box-back"></a>Zurücksenden der Data Box

Vergewissern Sie sich, dass die Daten vollständig auf das Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war. 

Die Vorgehensweise ist abhängig von der Region, in der das Gerät versendet wird. In vielen Ländern/Regionen können Sie den von [Microsoft verwalteten Versand](#microsoft-managed-shipping) oder den [selbstverwalteten Versand](#self-managed-shipping) verwenden.

### <a name="microsoft-managed-shipping"></a>Von Microsoft verwalteter Versand

Befolgen Sie die Richtlinien für die Region, aus der Sie versenden, wenn Sie den von Microsoft verwalteten Versand verwenden.

## <a name="us--canada"></a>[USA und Kanada](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-europe)

[!INCLUDE [data-box-shipping-in-eu](../../includes/data-box-shipping-in-eu.md)]

**Bei der Rücksendung an Azure-Rechenzentren in Deutschland oder der Schweiz** können Sie auch den [selbst verwalteten Versand](#self-managed-shipping) nutzen.

## <a name="uk"></a>[UK](#tab/in-uk)

[!INCLUDE [data-box-shipping-in-uk](../../includes/data-box-shipping-in-uk.md)]

## <a name="australia"></a>[Australien](#tab/in-australia)

[!INCLUDE [data-box-shipping-in-australia](../../includes/data-box-shipping-in-australia.md)]

## <a name="japan"></a>[Japan](#tab/in-japan)

[!INCLUDE [data-box-shipping-in-japan](../../includes/data-box-shipping-in-japan.md)]

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

[!INCLUDE [data-box-shipping-in-singapore](../../includes/data-box-shipping-in-singapore.md)]

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

[!INCLUDE [data-box-shipping-in-hk](../../includes/data-box-shipping-in-hk.md)]

## <a name="korea"></a>[Korea](#tab/in-korea)

[!INCLUDE [data-box-shipping-in-korea](../../includes/data-box-shipping-in-korea.md)]

## <a name="s-africa"></a>[Südafrika](#tab/in-sa)

[!INCLUDE [data-box-shipping-in-sa](../../includes/data-box-shipping-in-sa.md)]

## <a name="uae"></a>[Vereinigte Arabische Emirate](#tab/in-uae)

[!INCLUDE [data-box-shipping-in-uae](../../includes/data-box-shipping-in-uae.md)]

---

### <a name="self-managed-shipping"></a>Selbst verwalteter Versand

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box
 
Nachdem die Daten in Azure hochgeladen wurden, löscht die Data Box die Daten auf den Datenträgern gemäß den [NIST-Richtlinien (SP 800-88 Revision 1)](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end


::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

