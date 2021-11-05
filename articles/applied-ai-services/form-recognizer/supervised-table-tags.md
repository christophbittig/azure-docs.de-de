---
title: Verwenden von Tabellentags zum Trainieren Ihres benutzerdefinierten Formularmodells – Formularerkennung
titleSuffix: Azure Applied AI Services
description: Hier erfahren Sie, wie Sie das überwachte Erstellen von Beschriftungen mit Tabellentags effizient nutzen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/23/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2d61c48204478cc19be51d5d2c0d2974c674661d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020842"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Verwenden von Tabellentags zum Trainieren Ihres benutzerdefinierten Formularmodells

In diesem Artikel erfahren Sie, wie Sie Ihr benutzerdefiniertes Formularmodell mit Tabellentags (Beschriftungen) trainieren. Einige Szenarios erfordern eine komplexere Beschriftung als ein einfaches Zuordnen von Schlüssel-Wert-Paaren. Diese Szenarios umfassen das Extrahieren von Informationen aus Formularen mit komplexen hierarchischen Strukturen oder das Auffinden von Elementen, die vom Dienst nicht automatisch erkannt und extrahiert werden. In diesen Fällen können Sie Tabellentags verwenden, um Ihr benutzerdefiniertes Formularmodell zu trainieren.

## <a name="when-should-i-use-table-tags"></a>Wann sollten Tabellentags verwendet werden?

Im Folgenden sind einige Beispiele dafür aufgeführt, wann Tabellentags verwendet werden können:

- Ihre Formulare enthalten Daten, die Sie extrahieren möchten, in Tabellen, und die Struktur der Tabellen ist von Bedeutung. Ein Beispiel wäre, dass jede Zeile der Tabelle ein Element darstellt und jede Spalte ein bestimmtes Feature dieses Elements. In diesem Fall könnten Sie ein Tabellentag verwenden, bei dem eine Spalte Features darstellt und eine Zeile Informationen zu den einzelnen Features.
- Es gibt Daten, die Sie extrahieren möchten und die nicht in bestimmten Formularfeldern dargestellt werden, sondern semantisch. In diesem Fall könnte ein zweidimensionales Raster für die Daten geeignet sein. Angenommen, Ihr Formular enthält eine Liste von Personen mit Vorname, Nachname und E-Mail-Adresse, und Sie möchten diese Informationen extrahieren. In diesem Fall könnten Sie ein Tabellentag mit Spalten für den Vornamen, den Nachnamen und die E-Mail-Adresse verwenden und die einzelnen Zeilen mit Informationen zu Personen auf Ihrer Liste auffüllen.

> [!NOTE]
> Die Formularerkennung findet und extrahiert automatisch alle Tabellen in Ihren Dokumenten, unabhängig davon, ob die Tabellen getaggt sind oder nicht. Daher müssen Sie nicht jede Tabelle des Formulars mit einem Tabellentag versehen, und die Tabellentags müssen nicht die Struktur jeder Tabelle in Ihrem Formular abbilden. Tabellen, die automatisch von der Formularerkennung extrahiert wurden, sind im Abschnitt pageResults der JSON-Ausgabe enthalten.

## <a name="create-a-table-tag-with-the-form-recognizer-sample-labeling-tool"></a>Erstellen eines Tabellentags mit dem Tool für die Beschriftung von Beispielen für die Formularerkennung
<!-- markdownlint-disable MD004 -->
* Legen Sie fest, ob Sie ein Tabellentag mit **dynamischer** oder **fester Größe** erstellen möchten. Wenn die Anzahl der Zeilen von Dokument zu Dokument variiert, verwenden Sie ein dynamisches Tabellentag. Wenn die Anzahl der Zeilen in Ihren Dokumenten immer gleich ist, verwenden Sie ein Tabellentag mit fester Größe.
* Wenn das Tabellentag dynamisch ist, definieren Sie die Spaltennamen sowie den Datentyp und das Format für die einzelnen Spalten.
* Wenn es sich um eine Tabelle mit fester Größe handelt, definieren Sie den Spaltennamen, den Zeilennamen, den Datentyp und das Format für jedes Tag.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Konfigurieren eines Tabellentags":::

## <a name="label-your-table-tag-data"></a>Beschriften Ihrer Tabellentagdaten

* Wenn Ihr Projekt über ein Tabellentag verfügt, können Sie das Beschriftungspanel öffnen und das Tag auffüllen, wie bei Schlüssel-Wert-Feldern.
:::image type="content" source="media/table-labeling.png" alt-text="Beschriften mit Tabellentagdaten":::

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den Schnellstart für das Trainieren und Verwenden Ihres benutzerdefinierten Modells für die Formularerkennung an:

> [!div class="nextstepaction"]
> [Trainieren eines benutzerdefinierten Modells mithilfe des Tools für die Beschriftung von Beispielen](label-tool.md)

## <a name="see-also"></a>Weitere Informationen

* [Trainieren eines benutzerdefinierten Modells mithilfe des Tools für die Beschriftung von Beispielen](label-tool.md)
>
