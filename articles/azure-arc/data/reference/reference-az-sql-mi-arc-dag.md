---
title: Referenz zu „az sql mi-arc dag“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc dag“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f568f7086f2730bde53a1cd4dd31cf25092c5dd7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848646"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | Erstellen einer benutzerdefinierten Ressource einer verteilten Verfügbarkeitsgruppe
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | Löscht eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe in einer sqlmi-Instanz.
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | Zeigt eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe an.
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
Erstellt eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppen, um eine verteilte Verfügbarkeitsgruppe zu erstellen.
```bash
az sql mi-arc dag create 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Erstellen einer benutzerdefinierten Ressource „dagCr1“ einer verteilten Verfügbarkeitsgruppe, um die verteilte Verfügbarkeitsgruppe „dagName1“ zwischen der lokalen sqlmi-Instanz „sqlmi1“ und der sqlmi-Remoteinstanz „sqlmi2“ zu erstellen. Dies erfordert die primäre Spiegelung der sqlmi-Remoteinstanz „remotePrimary:5022“ sowie die Spiegelungsendpunkt-Zertifikatdatei „./sqlmi2.cer“ der sqlmi-Remoteinstanz.
```bash
az sql mi-arc dag create --name dagCr1 --dag-name dagName1 --local-instance-name sqlmi1 --local-primary local --remote-instance-name sqlmi2 --remote-mirroring-url remotePrimary:5022 --remote-mirroring-cert-file ./sqlmi2.cer --use-k8s
```
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: json, jsonc, table, tsv.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. Verwenden Sie `--debug`, um vollständige Debugprotokolle zu erhalten.
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Löscht eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe in einer sqlmi-Instanz, um eine verteilte Verfügbarkeitsgruppe zu löschen. Der Befehl erfordert den Namen einer benutzerdefinierten Ressource.
```bash
az sql mi-arc dag delete 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Löschen der Ressource namens „dagCr1“ einer verteilten Verfügbarkeitsgruppe.
```bash
az sql mi-arc dag delete --name dagCr1 --use-k8s
```
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: json, jsonc, table, tsv.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. Verwenden Sie `--debug`, um vollständige Debugprotokolle zu erhalten.
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Zeigt eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe an. Der Befehl erfordert den Namen einer benutzerdefinierten Ressource.
```bash
az sql mi-arc dag show 
```
### <a name="examples"></a>Beispiele
Beispiel 1: Anzeigen der Ressource namens „dagCr1“ einer verteilten Verfügbarkeitsgruppe.
```bash
az sql mi-arc dag show --name dagCr1 --use-k8s
```
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: json, jsonc, table, tsv.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org/](http://jmespath.org).
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. Verwenden Sie `--debug`, um vollständige Debugprotokolle zu erhalten.
