---
title: Referenz zu „az sql mi-arc dag“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc dag“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355974"
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
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Beispiel 1: Erstellen einer benutzerdefinierten Ressource „dagCr1“ einer verteilten Verfügbarkeitsgruppe, um die verteilte Verfügbarkeitsgruppe „dagName1“ zwischen der lokalen sqlmi-Instanz „sqlmi1“ und der sqlmi-Remoteinstanz „sqlmi2“ zu erstellen. Dies erfordert die primäre Spiegelung der sqlmi-Remoteinstanz „remotePrimary:5022“ sowie die Spiegelungsendpunkt-Zertifikatdatei „./sqlmi2.cer“ der sqlmi-Remoteinstanz.
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verteilten Verfügbarkeitsgruppenressource.
#### `--dag-name -d`
Der Name der verteilten Verfügbarkeitsgruppe für diese verwaltete SQL-Instanz. Sowohl die lokale als auch die Remoteinstanz muss denselben Namen verwenden.
#### `--local-instance-name -l`
Der Name der lokalen verwalteten SQL-Instanz.
#### `--local-primary -p`
„True“ gibt an, dass es sich bei der lokalen verwalteten SQL-Instanz um eine geoprimäre Instanz handelt. „False“ gibt an, dass es sich bei der lokalen verwalteten SQL-Instanz um eine geosekundäre Instanz handelt.
#### `--remote-instance-name -r`
Der Name der verwalteten SQL-Remoteinstanz oder der SQL-Remoteverfügbarkeitsgruppe
#### `--remote-mirroring-url -u`
Die URL des Spiegelungsendpunkts der verwalteten SQL-Remoteinstanz oder der SQL-Remoteverfügbarkeitsgruppe.
#### `--remote-mirroring-cert-file -f`
Der Dateiname des öffentlichen Zertifikats des Spiegelungsendpunkts für die verwaltete SQL-Remoteinstanz oder die SQL-Remoteverfügbarkeitsgruppe. Nur das PEM-Format wird unterstützt.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Namespace, in dem sich die verwaltete SQL-Instanz befindet. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--path`
Pfad zur benutzerdefinierten Ressourcenspezifikation, d. h. custom/spec.json
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
Löscht eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe in einer sqlmi-Instanz, um eine verteilte Verfügbarkeitsgruppe zu löschen. Der Befehl erfordert den Namen einer benutzerdefinierten Ressource.
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Beispiel 1: Löschen der Ressource namens „dagCr1“ einer verteilten Verfügbarkeitsgruppe.
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name`
Der Name der verteilten Verfügbarkeitsgruppenressource.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Namespace, in dem sich die verwaltete SQL-Instanz befindet. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
Zeigt eine benutzerdefinierte Ressource einer verteilten Verfügbarkeitsgruppe an. Der Befehl erfordert den Namen einer benutzerdefinierten Ressource.
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Beispiel 1: Anzeigen der Ressource namens „dagCr1“ einer verteilten Verfügbarkeitsgruppe.
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name`
Der Name der verteilten Verfügbarkeitsgruppenressource.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Namespace, in dem sich die verwaltete SQL-Instanz befindet. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--use-k8s`
Verwenden Sie lokale Kubernetes-APIs, um diese Aktion auszuführen.
### <a name="global-arguments"></a>Globale Argumente
#### `--debug`
Ausführlichkeit der Protokollierung erhöhen, um alle Debugprotokolle anzuzeigen.
#### `--help -h`
Zeigen Sie diese Hilfemeldung an, und schließen Sie sie.
#### `--output -o`
Ausgabeformat.  Zulässige Werte: „json“, „jsonc“, „none“, „table“, „tsv“, „yaml“, „yamlc“.  Standardwert: json.
#### `--query -q`
JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Der Name oder die ID des Abonnements. Sie können das standardmäßig verwendete Abonnement mittels `az account set -s NAME_OR_ID` konfigurieren.
#### `--verbose`
Ausführlichkeit der Protokollierung erhöhen. „--debug“ für vollständige Debugprotokolle verwenden.
