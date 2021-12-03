---
title: Tutorial zur Rücksendung von Azure Data Box im Exportauftrag | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure Data Box nach Abschluss des Export Auftrags an Microsoft senden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/29/2021
ms.author: alkohli
ms.openlocfilehash: 544454e4022da67db50fb194d7aea72a6bd716bd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449968"
---
# <a name="tutorial-return-azure-data-box"></a>Tutorial: Zurücksenden von Azure Data Box

In diesem Tutorial wird beschrieben, wie Azure Data Box zurückgesendet wird, und die Daten werden gelöscht, nachdem das Gerät in den Azure-Daten empfangen wurde.

In diesem Tutorial werden folgende Themen behandelt:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Löschen von Daten von der Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Sie haben die Schritte ausgeführt in [Tutorial: Kopieren von Daten aus Azure Data Box über SMB (Vorschau)](data-box-deploy-export-copy-data.md).
* Alle Kopieraufträge sind abgeschlossen. „Für Versand vorbereiten“ kann nicht ausgeführt werden, wenn noch Kopieraufträge ausgeführt werden.

## <a name="prepare-to-ship"></a>Vorbereiten des Versands

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Die nächsten Schritte hängen davon ab, wo Sie das Gerät zurückgeben.

## <a name="ship-data-box-back"></a>Zurücksenden der Data Box

Vergewissern Sie sich, dass die Daten vollständig vom Gerät kopiert wurden, und dass die **Versandvorbereitung** erfolgreich war.

Die Vorgehensweise ist abhängig von der Region, in der das Gerät versendet wird. In vielen Ländern/Regionen können Sie den von Microsoft verwalteten Versand oder den selbstverwalteten Versand verwenden.

### <a name="microsoft-managed-shipping"></a>Von Microsoft verwalteter Versand

Befolgen Sie die Richtlinien für die Region, aus der Sie versenden, wenn Sie den von Microsoft verwalteten Versand verwenden.

## <a name="us--canada"></a>[USA und Kanada](#tab/in-us-canada)

[!INCLUDE [data-box-shipping-in-us-canada](../../includes/data-box-shipping-in-us-canada.md)]

## <a name="eu"></a>[EU](#tab/in-eu)

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

### <a name="self-managed-shipping"></a>Selbst verwalteter Versand

[!INCLUDE [data-box-shipping-self-managed](../../includes/data-box-shipping-self-managed.md)]

---

## <a name="erasure-of-data-from-data-box"></a>Löschen von Daten von der Data Box

Sobald das Gerät das Azure-Rechenzentrum erreicht, löscht die Data Box die Daten auf ihren Datenträgern gemäß den [NIST-Richtlinien SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu verschiedenen Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Voraussetzungen
> * Vorbereiten des Versands
> * Senden der Data Box an Microsoft
> * Löschen von Daten von der Data Box

Im nächsten Artikel erfahren Sie, wie Sie Ihre Data Box verwalten.

> [!div class="nextstepaction"]
> [Verwalten von Data Box über das Azure-Portal](./data-box-portal-admin.md)
