---
title: AS2-MDN-Bestätigungen
description: Im Folgenden erhalten Sie Informationen zu MDN-Bestätigungen (Message Disposition Notification) für AS2-Nachrichten in Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356102"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>MDN-Bestätigungen für AS2-Nachrichten in Azure Logic Apps

Sie können in Azure Logic Apps Workflows erstellen, die AS2-Nachrichten für die EDI-Kommunikation (Electronic Data Interchange) verarbeiten, wenn Sie **AS2**-Vorgänge verwenden. Beim EDI-Messaging geben Bestätigungen den Status der Verarbeitung eines EDI-Austauschs an. Beim Empfang eines Austauschs kann die [**AS2-Decodierungsaktion**](logic-apps-enterprise-integration-as2.md#decode) eine Meldungsdispositionsbenachrichtigung (Message Disposition Notification, MDN) oder eine Bestätigung an den Absender zurückgeben. Eine MDN überprüft die folgenden Elemente:

* Der Empfangspartner hat die ursprüngliche Nachricht erfolgreich empfangen.

  Die Sendepartei vergleicht die `MessageID` der ursprünglich gesendeten Nachricht mit dem Feld `original-message-id`, das der Empfänger in die MDN einfügt.

* Der Empfangspartner hat die Integrität der ausgetauschten Daten überprüft.

  Eine Nachrichtenintegritätsprüfung (Message Integrity Check, MIC) oder ein MIC-Digest wird aus der Nutzlast in der ursprünglich gesendeten Nachricht berechnet. Die Sendepartei vergleicht dieses MIC mit dem MIC, das der Empfänger aus der Nutzlast in der empfangenen Nachricht berechnet und im Feld `Received-content-MIC` in der MDN eingefügt hat, sofern diese signiert ist.

  > [!NOTE]
  > Eine MDN kann signiert, jedoch nicht verschlüsselt oder komprimiert werden.

* NRR (Non-Repudiation of Receipt)

  Die Sendepartei vergleicht die signierte MDN mithilfe des öffentlichen Schlüssels des Empfangspartners und stellt sicher, dass der zurückgegebene MIC-Wert in der MDN dem MIC-Wert für die Nutzlast der ursprünglichen Nachricht übereinstimmt, die in der Datenbank zur Nichtabsteitbarkeit gespeichert ist.

> [!NOTE]
> Wenn Sie das Senden einer MDN als Antwort aktivieren, versucht Ihre Logik-App, eine MDN zurücksenden, um den Status der AS2-Nachrichtenverarbeitung zu melden, auch wenn während der Verarbeitung ein Fehler auftritt. Die AS2-Übertragung ist erst abgeschlossen, wenn der Absender die MDN empfängt und überprüft.
> Eine synchrone MDN dient als HTTP-Antwort, z. B. als `200 OK`-Status.

In diesem Artikel finden Sie eine kurze Übersicht über die AS2 MDN-ACK, einschließlich der Eigenschaften, die zum Generieren der Bestätigung, der zu verwendenden MDN-Header und des MIC verwendet werden. Weiterführende Informationen finden Sie in der folgenden Dokumentation:

* [Austauschen von AS2-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps](logic-apps-enterprise-integration-as2.md)
* [AS2-Nachrichteneinstellungen](logic-apps-enterprise-integration-as2-message-settings.md)
* [Was ist Azure Logic Apps?](logic-apps-overview.md)

## <a name="mdn-generation"></a>MDN-Generierung

Die AS2-Decodierungsaktion generiert eine MDN basierend auf den AS2-Vereinbarungseigenschaften eines Handelspartners, wenn unter **Empfangseinstellungen** der Vereinbarung die Option **MDN senden** ausgewählt ist. In diesem Fall wird die **AS2-From**-Eigenschaft im Nachrichtenheader zum Generieren der MDN verwendet. Andere Eigenschaften und deren Werte werden jedoch aus den AS2-Vereinbarungseinstellungen des Partners übernommen.

Die eingehenden AS2-Nachrichtenheader werden standardmäßig zur Überprüfung und Generierung der MDN verwendet. Wählen Sie unter **Empfangseinstellungen** der Vereinbarung die Option **Nachrichteneigenschaften überschreiben** aus, wenn Sie stattdessen die Validierungs- und MDN-Einstellungen der Vereinbarung verwenden möchten. Andernfalls verwendet die AS2-Decodierungsaktion stattdessen die eingehenden Nachrichtenheader, wenn diese Option deaktiviert bleibt oder eine Vereinbarung nicht verfügbar ist.

## <a name="mdn-headers"></a>MDN-Header

Zum Korrelieren einer MDN mit der AS2-Nachricht als Antwort werden die Header `AS2-From` und `AS2-To` sowie die Kontexteigenschaft `MessageID` verwendet. Im MDN stammt der Header `Original-Message-ID` aus dem Header `Message-ID` in der AS2-Nachricht, für den die MDN die Antwort ist. Eine MDN enthält die folgenden Header:

| Header | BESCHREIBUNG |
|---------|-------------|
| HTTP und AS2 | Weitere Informationen finden Sie unter [AS2-Nachrichteneinstellungen](logic-apps-enterprise-integration-as2-message-settings.md).
| Übertragungsebene | Zu diesem Header zählen der `Content-Type`-Header, der die signierte mehrteilige Nachricht enthält, der Algorithmus für den MIC-Wert, das Signaturformatierungsprotokoll sowie die mehrteiligen Unterheader für die äußeren Begrenzungen. |
| Erster Teil | Der erste Teil der mehrteiligen signierten Nachricht ist die eingebettete MDN. Dieser Teil ist für Benutzer lesbar. |
| Zweiter Teil | Der zweite Teil der signierten mehrteiligen Nachricht enthält die digitale Signatur, einen Verweis auf die ursprüngliche Nachricht, den Dispositionstyp und -status sowie den MIC-Wert. Dieser Teil ist maschinenlesbar. |
|||

## <a name="mic-digest"></a>MIC-Digest

Der MIC-Digest oder die MIC überprüft, ob ein MDN mit der Nutzlast in der ursprünglich gesendeten Nachricht korreliert. Diese MIC ist im zweiten Teil der signierten mehrteiligen MDN-Nachricht im `Received-Content-MIC`-Erweiterungsfeld enthalten.

Die MIC ist base64-codiert und wird von der **MIC-Algorithmus**-Eigenschaft bestimmt, die aktiviert wird, wenn die Eigenschaften **MDN senden** und **Signierte MDN senden** auf der Seite **Empfangseinstellungen** der AS2-Vereinbarung ausgewählt werden. Sie können bei der MIC-Generierung zwischen den folgenden unterstützten Hashalgorithmen wählen:

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

Der folgende Screenshot zeigt beispielsweise die MDN-Eigenschaften auf der Seite **Empfangseinstellungen** der AS2-Vereinbarung:

![Screenshot, der das Azure-Portal mit den Empfangseinstellungen der AS2-Vereinbarung und den MDN-Bestätigungseinstellungen zeigt](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>Nächste Schritte

* [AS2-Nachrichteneinstellungen](logic-apps-enterprise-integration-as2-message-settings.md)
