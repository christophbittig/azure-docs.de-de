---
title: 'Tutorial: Simulieren eines Fehlers beim Lesen von Daten aus der primären Region'
titleSuffix: Azure Storage
description: Simulieren Sie einen Fehler beim Lesen von Daten aus der primären Region, wenn das Speicherkonto für geozonenredundanten Speicher mit Lesezugriff (RA-GZRS) konfiguriert ist.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: fe955ede3c2936c0c842d19fbadb9a075830822c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603484"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Tutorial: Simulieren eines Fehlers beim Lesen von Daten aus der primären Region

Dieses Tutorial ist der zweite Teil einer Reihe. In diesem Teil erfahren Sie, welchen Nutzen ein [geozonenredundanter Speicher mit Lesezugriff](../common/storage-redundancy.md) (Read-Access Geo-Zone-Redundant Storage, RA-GZRS) bietet, indem Sie einen Fehler simulieren.

Zum Simulieren eines Fehlers können Sie [statisches Routing](#simulate-a-failure-with-an-invalid-static-route) oder [Fiddler](#simulate-a-failure-with-fiddler) verwenden. Beide Methoden ermöglichen es Ihnen, einen Fehler für Anforderungen an den primären Endpunkt Ihres RA-GZRS-Speicherkontos ([geozonenredundanter Speicher mit Lesezugriff](../common/storage-redundancy.md)) zu simulieren, sodass die Anwendung stattdessen vom sekundären Endpunkt liest.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> - Ausführen und Anhalten einer Anwendung
> - Simulieren eines Fehlers mit einer [ungültigen statischen Route](#simulate-a-failure-with-an-invalid-static-route) oder [Fiddler](#simulate-a-failure-with-fiddler)
> - Simulieren der Wiederherstellung des primären Endpunkts

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie vor Beginn dieses Tutorials das vorherige Tutorial durch: [Tutorial: Erstellen einer hochverfügbaren Anwendung mit Blobspeicher][previous-tutorial].

Zum Simulieren eines Fehlers mit statischem Routing verwenden Sie eine Eingabeaufforderung mit erhöhten Rechten.

Führen Sie den Download und die [Installation von Fiddler](https://www.telerik.com/download/fiddler) durch, um einen Fehler mit Fiddler zu simulieren.

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulieren eines Fehlers mit einer ungültigen statischen Route

Sie können eine ungültige statische Route für alle Anforderungen an den primären Endpunkt Ihres Speicherkontos vom Typ [Geozonenredundanter Speicher mit Lesezugriff](../common/storage-redundancy.md) (RA-GZRS) erstellen. In diesem Tutorial wird der lokale Host als Gateway für das Weiterleiten von Anforderungen an das Speicherkonto verwendet. Die Nutzung des lokalen Hosts als Gateway bewirkt, dass für alle Anforderungen an den primären Endpunkt Ihres Speicherkontos eine Schleife zurück zum Host erfolgt. Dies führt schließlich zu einem Fehler. Führen Sie die folgenden Schritte aus, um einen Fehler zu simulieren und die Wiederherstellung des primären Endpunkts mit einer ungültigen statischen Route durchzuführen.

### <a name="start-and-pause-the-application"></a>Starten und Anhalten der Anwendung

Verwenden Sie die Anleitung im [vorherigen Tutorial][previous-tutorial], um das Beispiel zu starten und die Testdatei herunterzuladen und sicherzustellen, dass sie aus dem primären Speicher stammt. Je nach Zielplattform können Sie das Beispiel dann manuell anhalten oder müssen an der Eingabeaufforderung warten.

### <a name="simulate-failure"></a>Simulieren eines Fehlers

Öffnen Sie bei angehaltener Anwendung eine Eingabeaufforderung unter Windows als Administrator, oder führen Sie das Terminal unter Linux als root-Benutzer aus.

Geben Sie an einer Eingabeaufforderung oder in einem Terminal den folgenden Befehl ein, um Informationen zur Domäne des primären Endpunkts des Speicherkontos abzurufen. Ersetzen Sie hierbei `STORAGEACCOUNTNAME` durch den Namen Ihres Speicherkontos.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopieren Sie die IP-Adresse Ihres Speicherkontos zur späteren Verwendung in einen Text-Editor.

Geben Sie zum Abrufen der IP-Adresse Ihres lokalen Hosts an der Windows-Eingabeaufforderung `ipconfig` bzw. im Linux-Terminal `ifconfig` ein.

Geben Sie zum Hinzufügen einer statischen Route für einen Zielhost den folgenden Befehl in einer Windows-Eingabeaufforderung oder einem Linux-Terminal ein, und ersetzen Sie `<destination_ip>` durch die IP-Adresse Ihres Speicherkontos und `<gateway_ip>` durch Ihre lokale Host-IP-Adresse.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Setzen Sie die Anwendung im Fenster mit dem ausgeführten Beispiel fort, oder betätigen Sie die entsprechende Schaltfläche, um die Beispieldatei herunterzuladen und zu bestätigen, dass sie aus dem sekundären Speicher stammt. Sie können das Beispiel dann erneut anhalten oder an der Eingabeaufforderung warten.

### <a name="simulate-primary-endpoint-restoration"></a>Simulieren der Wiederherstellung des primären Endpunkts

Löschen Sie die ungültige statische Route aus der Routingtabelle, um zu simulieren, dass der primäre Endpunkt wieder funktionsfähig ist. Dies ermöglicht, dass alle Anforderungen an den primären Endpunkt über das Standardgateway geleitet werden. Geben Sie den folgenden Befehl in einer Windows-Eingabeaufforderung oder im Linux-Terminal ein.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Sie können die Anwendung dann fortsetzen oder die entsprechende Schaltfläche betätigen, um die Beispieldatei erneut herunterzuladen und sich nun zu vergewissern, dass sie wieder aus dem primären Speicher stammt.

## <a name="simulate-a-failure-with-fiddler"></a>Simulieren eines Fehlers mit Fiddler

Zum Simulieren eines Fehlers mit Fiddler injizieren Sie eine fehlerhafte Antwort für Anforderungen an den primären Endpunkt Ihres RA-GZRS-Speicherkontos.

In den folgenden Abschnitten wird gezeigt, wie Sie mit Fiddler einen Fehler simulieren und die Wiederherstellung des primären Endpunkts durchführen.

### <a name="launch-fiddler"></a>Starten von Fiddler

Öffnen Sie Fiddler, und wählen Sie zuerst **Rules** (Regeln) und anschließend **Customize Rules** (Regeln anpassen) aus.

![Anpassen von Fiddler-Regeln](media/simulate-primary-region-failure/figure1.png)

Der Fiddler ScriptEditor wird gestartet und zeigt die Datei **SampleRules.js** an. Mit dieser Datei können Sie Fiddler anpassen.

Fügen Sie das folgende Codebeispiel in die Funktion `OnBeforeResponse` ein, und ersetzen Sie `STORAGEACCOUNTNAME` durch den Namen Ihres Speicherkontos. Je nach Beispiel müssen Sie ggf. auch `HelloWorld` durch den Namen der Testdatei (oder ein Präfix wie `sampleFile`) ersetzen, die heruntergeladen wird. Der neue Code ist auskommentiert, um sicherzustellen, dass er nicht sofort ausgeführt wird.

Wählen Sie anschließend **File** (Datei) und dann **Save** (Speichern) aus, um Ihre Änderungen zu speichern. Lassen Sie das ScriptEditor-Fenster geöffnet, damit es in den folgenden Schritten verwendet werden kann.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Einfügen einer benutzerdefinierten Regel](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Starten und Anhalten der Anwendung

Verwenden Sie die Anleitung im [vorherigen Tutorial][previous-tutorial], um das Beispiel zu starten und die Testdatei herunterzuladen und sicherzustellen, dass sie aus dem primären Speicher stammt. Je nach Zielplattform können Sie das Beispiel dann manuell anhalten oder müssen an der Eingabeaufforderung warten.

### <a name="simulate-failure"></a>Simulieren eines Fehlers

Wechseln Sie bei angehaltener Anwendung zurück zu Fiddler, und heben Sie die Auskommentierung der benutzerdefinierten Regel auf, die Sie in der Funktion `OnBeforeResponse` gespeichert haben. Achten Sie darauf, dass Sie **File** (Datei) und **Save** (Speichern) wählen, um Ihre Änderungen zu speichern, damit die Regel wirksam wird. In diesem Code wird nach Anforderungen an das RA-GZRS-Speicherkonto gesucht, und wenn der Pfad den Namen der Beispieldatei enthält, wird der Antwortcode `503 - Service Unavailable` zurückgegeben.

Setzen Sie die Anwendung im Fenster mit dem ausgeführten Beispiel fort, oder betätigen Sie die entsprechende Schaltfläche, um die Beispieldatei herunterzuladen und zu bestätigen, dass sie aus dem sekundären Speicher stammt. Sie können das Beispiel dann erneut anhalten oder an der Eingabeaufforderung warten.

### <a name="simulate-primary-endpoint-restoration"></a>Simulieren der Wiederherstellung des primären Endpunkts

Entfernen Sie die Regel in Fiddler, oder kommentieren Sie sie wieder aus. Wählen Sie **File** (Datei) und **Save** (Speichern), um sicherzustellen, dass die Regel nicht mehr wirksam ist.

Setzen Sie die Anwendung im Fenster mit dem ausgeführten Beispiel fort, oder betätigen Sie die entsprechende Schaltfläche, um die Beispieldatei herunterzuladen und zu bestätigen, dass sie nun wieder aus dem primären Speicher stammt. Sie können das Beispiel dann beenden.

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil dieser Reihe haben Sie gelernt, wie Sie einen Fehler simulieren, um den georedundanten Speicher mit Lesezugriff zu testen.

Im folgenden Artikel finden Sie weitere Informationen zur Funktionsweise von geozonenredundantem Speicher mit Lesezugriff (RA-GZRS) und zu den damit verbundenen Risiken:

> [!div class="nextstepaction"]
> [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
