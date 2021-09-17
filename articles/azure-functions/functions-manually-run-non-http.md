---
title: Manuelles Ausführen einer Azure-Funktion ohne HTTP-Trigger
description: Ausführen einer Azure-Funktion ohne HTTP-Trigger mithilfe einer HTTP-Anforderung
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 96340d8ccd32c5056823c9dca3376488eb9250c1
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123104285"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Manuelles Ausführen einer Funktion ohne HTTP-Trigger

In diesem Artikel wird gezeigt, wie Sie eine Funktion ohne HTTP-Trigger manuell über eine speziell formatierte HTTP-Anforderung ausführen.

In bestimmten Kontexten müssen Sie unter Umständen bei Bedarf eine indirekt ausgelöste Azure-Funktion ausführen.  Zu Beispielen indirekter Trigger zählen u.a. [Funktionen eines Zeitplans](./functions-create-scheduled-function.md) und Funktionen, die als Folge einer [Aktion einer anderen Ressource](./functions-create-storage-blob-triggered-function.md) ausgeführt werden. 

Im folgenden Beispiel wird [Postman](https://www.getpostman.com/) verwendet. Sie können zum Senden von HTTP-Anforderungen aber auch [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) oder ein anderes Tool nutzen.

## <a name="define-the-request-location"></a>Festlegen des Anforderungsorts

Wenn Sie eine Funktion ohne HTTP-Trigger ausführen möchten, benötigen Sie eine Methode, mit der Sie die Ausführung der Funktion bei Azure anfordern können. Die zum Senden dieser Anforderung verwendete URL hat ein bestimmtes Format.

![Festlegen des Anforderungsorts: Hostname + Ordnerpfad + Funktionsname](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Hostname:** Der öffentliche Ort der Funktions-App, der sich aus dem Namen der Funktions-App und *azurewebsites.net* bzw. Ihrer benutzerdefinierten Domäne zusammensetzt.
- **Ordnerpfad:** Wenn Sie über eine HTTP-Anforderung auf Funktionen ohne HTTP-Trigger zugreifen möchten, müssen Sie die Anforderung über die Ordner *admin/functions* senden.
- **Funktionsname:** Der Name der Funktion, die Sie ausführen möchten.

Dieser Anforderungsort wird in Postman zusammen mit dem Hauptschlüssel der Funktion in der Azure-Anforderung zum Ausführen der Funktion verwendet.

> [!NOTE]
> Bei lokaler Ausführung wird der Hauptschlüssel der Funktion nicht benötigt. Sie können direkt [die Funktion aufrufen](#call-the-function) und den Header `x-functions-key` weglassen.

## <a name="get-the-functions-master-key"></a>Abrufen des Hauptschlüssels der Funktion

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App, und wählen Sie **App-Schlüssel** und dann den Schlüssel `_master` aus. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Suchen nach dem Hauptschlüssel, der kopiert werden soll." border="true":::

1. Kopieren Sie im Abschnitt **Schlüssel bearbeiten** den Schlüsselwert in die Zwischenablage, und wählen Sie dann **OK** aus.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Kopieren des Hauptschlüssels in die Zwischenablage." border="true":::

1. Klicken Sie nach dem Kopieren des *_master*-Schlüssels auf **Programmieren und testen**, und wählen Sie dann **Protokolle** aus. Hier werden Meldungen zur Funktion protokolliert, wenn Sie die Funktion manuell über Postman ausführen.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Screenshot: Seite „Programmieren und testen“ mit einer Meldung aus den angezeigten Protokollen" border="true":::

> [!CAUTION]  
> Aufgrund der erhöhten Berechtigungen, die der Hauptschlüssel in Ihrer Funktions-App gewährt, sollten Sie diesen Schlüssel nicht für Dritte freigeben oder in einer Anwendung verteilen. Der Schlüssel darf nur an einen HTTPS-Endpunkt gesendet werden.

## <a name="call-the-function"></a>Aufrufen der Funktion

Öffnen Sie Postman, und führen Sie die folgenden Schritte aus:

1. Geben Sie den **Anforderungsort in das Textfeld für die URL** ein.
1. Vergewissern Sie sich, dass die HTTP-Methode auf **POST** festgelegt ist.
1. Wählen Sie die Registerkarte **Headers** (Header) aus.
1. Geben Sie **x-functions-key** als ersten Schlüssel ein, und fügen Sie den Hauptschlüssel (aus der Zwischenablage) dann als Wert ein.
1. Geben Sie **Content-Type** als zweiten Schlüssel und **application/json** als Wert ein.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Header-Einstellungen in Postman." border="true":::

1. Wählen Sie die Registerkarte **Body (Hauptteil)** aus.
1. Geben Sie **{ "input": "test" }** als Anforderungstext ein.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Postman-Einstellungen für den Anforderungstext." border="true":::

1. Wählen Sie **Senden** aus.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Senden einer Anforderung mit Postman." border="true":::

    Postman meldet anschließend den Status **202 – Akzeptiert**.

1. Kehren Sie anschließend zu Ihrer Funktion im Azure-Portal zurück. Überprüfen Sie die Protokolle. In ihnen werden Meldungen aus dem manuellen Aufruf der Funktion angezeigt.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Anzeigen der Protokolle, um die Testergebnisse für den Hauptschlüssel anzuzeigen." border="true":::

## <a name="next-steps"></a>Nächste Schritte

- [Strategien zum Testen Ihres Codes in Azure Functions](./functions-test-a-function.md)
- [Lokales Debuggen von Azure-Funktionen mit Event Grid-Trigger](./functions-debug-event-grid-trigger-local.md)
