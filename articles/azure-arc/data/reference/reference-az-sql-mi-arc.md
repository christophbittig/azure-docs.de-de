---
title: Referenz zu „az sql mi-arc“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu „az sql mi-arc“-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355208"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Anzeigen und Verwalten von SQL-Endpunkten
[az sql mi-arc create](#az-sql-mi-arc-create) | Hiermit wird eine verwaltete SQL-Instanz erstellt.
[az sql mi-arc edit](#az-sql-mi-arc-edit) | Hiermit wird die Konfiguration einer verwalteten SQL-Instanz bearbeitet.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Hiermit wird eine verwaltete SQL-Instanz gelöscht.
[az sql mi-arc show](#az-sql-mi-arc-show) | Hiermit werden die Details einer verwalteten SQL-Instanz angezeigt.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi ab, und speichert es in einer Datei.
[az sql mi-arc list](#az-sql-mi-arc-list) | Hiermit werden verwaltete SQL-Instanzen aufgelistet.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Konfigurationsbefehle
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Erstellt oder löscht eine verteilte Verfügbarkeitsgruppe.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Um das Kennwort für die verwaltete SQL-Instanz festzulegen, legen Sie die Umgebungsvariable AZDATA_PASSWORD fest.
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>Beispiele
Hiermit wird eine verwaltete SQL-Instanz erstellt.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
Erstellen Sie eine verwaltete SQL-Instanz mit drei Replikaten in einem Szenario mit Hochverfügbarkeit.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verwalteten SQL-Instanz.
#### `--k8s-namespace -k`
Namespace, in dem die verwaltete SQL-Instanz bereitgestellt werden soll. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--path`
Der Pfad zur azext_arcdata-Datei für die JSON-Datei der verwalteten SQL-Instanz.
#### `--replicas`
Diese Option gibt die Anzahl der SQL Managed Instance-Replikate an, die zu Hochverfügbarkeitszwecken in Ihrem Kubernetes-Cluster bereitgestellt werden. Zulässige Werte sind „3“ oder „1“ (der Standardwert ist „1“).
#### `--cores-limit -c`
Das Limit für Kerne der verwalteten Instanz als Integer.
#### `--cores-request`
Die Anforderung für Kerne der verwalteten Instanz als Integer.
#### `--memory-limit -m`
Das Limit für die Kapazität der verwalteten Instanz als ganze Zahl (integer), gefolgt von „Gi“ (GB). Beispiel: 4Gi
#### `--memory-request`
Die Anforderung der Kapazität der verwalteten Instanz als ganze Zahl (integer), gefolgt von „Gi“ (GB). Beispiel: 4Gi
#### `--storage-class-data -d`
Die für Datendateien zu verwendende Speicherklasse (.mdf, .ndf). Wenn kein Wert angegeben wird, wird keine Speicherklasse angegeben, was dazu führt, dass Kubernetes die Standardspeicherklasse nutzt.
#### `--storage-class-logs -g`
Die für Protokolle zu verwendende Speicherklasse (/var/log). Wenn kein Wert angegeben wird, wird keine Speicherklasse angegeben, was dazu führt, dass Kubernetes die Standardspeicherklasse nutzt.
#### `--storage-class-datalogs`
Die für Datenbankprotokolle zu verwendende Speicherklasse (.ldf). Wenn kein Wert angegeben wird, wird keine Speicherklasse angegeben, was dazu führt, dass Kubernetes die Standardspeicherklasse nutzt.
#### `--storage-class-backups`
Die für Sicherungen zu verwendende Speicherklasse (/var/opt/mssql/backups). Wenn kein Wert angegeben wird, wird keine Speicherklasse angegeben, was dazu führt, dass Kubernetes die Standardspeicherklasse nutzt.
#### `--volume-size-data`
Dies ist die Größe des Speichervolumes, das für Daten verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--volume-size-logs`
Dies ist die Größe des Speichervolumes, das für Datenprotokolle verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--volume-size-datalogs`
Dies ist die Größe des Speichervolumes, das für Datenprotokolle verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--volume-size-backups`
Dies ist die Größe des Speichervolumes, das für Sicherungen verwendet werden soll, als positive Zahl, gefolgt von Ki (Kilobyte), Mi (Megabyte) oder Gi (Gigabyte).
#### `--no-wait`
Wenn dieser Wert vorhanden ist, wartet der Befehl nicht darauf, dass die Instanz bereit ist, bis eine Rückgabe erfolgt.
#### `--no-external-endpoint`
Falls ein Wert angegeben ist, wird kein externer Dienst erstellt. Andernfalls wird ein externer Dienst mit demselben Diensttyp wie dem des Datencontrollers erstellt.
#### `--cert-public-key-file`
Pfad zu der Datei, die einen öffentlichen PEM-formatierten Zertifikatschlüssel enthält, der für SQL Server verwendet werden soll.
#### `--cert-private-key-file`
Pfad zu der Datei, die einen privaten PEM-formatierten Zertifikatschlüssel enthält, der für SQL Server verwendet werden soll.
#### `--service-cert-secret`
Name des zu generierenden Kubernetes-Geheimnisses, das das SQL-Dienstzertifikat hostet oder hosten wird.
#### `--admin-login-secret`
Name des zu generierenden Kubernetes-Geheimnisses, das die Anmeldeinformationen der Administratoranmeldung des Hostbenutzers hostet oder hosten wird.
#### `--license-type -l`
Der Lizenztyp, der für diese verwaltete Instanz angewendet werden soll. Zulässige Werte sind: BasePrice, LicenseIncluded. Der Standardwert ist „LicenseIncluded“. Der Lizenztyp kann nicht geändert werden.
#### `--tier -t`
Der Tarif für die Instanz. Zulässige Werte: „BusinessCritical“ (Kurzform: bc) oder „GeneralPurpose“ (Kurzform: gp). Der Standardwert ist „GeneralPurpose“. Der Tarif kann nicht geändert werden.
#### `--dev`
Wenn dieser Wert angegeben wird, gilt die Instanz als Entwicklungsinstanz, und es fallen keine Kosten dafür an.
#### `--labels`
Durch Trennzeichen getrennte Liste von Bezeichnungen der verwalteten SQL-Instanz.
#### `--annotations`
Durch Trennzeichen getrennte Liste von Anmerkungen der verwalteten SQL-Instanz.
#### `--service-labels`
Durch Trennzeichen getrennte Liste von Bezeichnungen, die auf alle externen Dienste angewendet werden sollen.
#### `--service-annotations`
Durch Trennzeichen getrennte Liste von Anmerkungen, die auf alle externen Dienste angewendet werden sollen.
#### `--storage-labels`
Durch Trennzeichen getrennte Liste von Bezeichnungen, die auf alle PVCs angewendet werden sollen.
#### `--storage-annotations`
Durch Trennzeichen getrennte Liste von Anmerkungen, die auf alle PVCs angewendet werden sollen.
#### `--use-k8s`
Erstellen einer verwalteten SQL-Instanz mithilfe von lokalen Kubernetes-APIs.
#### `--collation`
Die SQL-Serversortierung für die Instanz.
#### `--language`
Das SQL Server-Gebietsschema zum jeweiligen unterstützten Gebietsschemabezeichner (LCID) für die Instanz.
#### `--agent-enabled`
Aktivieren Sie den SQL Server-Agent für die Instanz. Die Standardeinstellung ist „Deaktiviert“. Zulässige Werte sind „true“ oder „false“.
#### `--trace-flags`
Durch Trennzeichen getrennte Liste von Ablaufverfolgungsflags. Standardeinstellung ist ohne Flags.
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
Hiermit wird die Konfiguration einer verwalteten SQL-Instanz bearbeitet.
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Hiermit wird die Konfiguration einer verwalteten SQL-Instanz bearbeitet.
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verwalteten SQL-Instanz, die bearbeitet wird. Der Name, unter dem Ihre Instanz bereitgestellt wird, kann nicht geändert werden.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--k8s-namespace -k`
Namespace, in dem sich die verwaltete SQL-Instanz befindet. Wenn kein Namespace angegeben ist, wird der in kubeconfig definierte Namespace verwendet.
#### `--path`
Der Pfad zur azext_arcdata-Datei für die JSON-Datei der verwalteten SQL-Instanz.
#### `--cores-limit -c`
Das Limit für Kerne der verwalteten Instanz als Integer.
#### `--cores-request`
Die Anforderung für Kerne der verwalteten Instanz als Integer.
#### `--memory-limit -m`
Das Limit für die Kapazität der verwalteten Instanz als ganze Zahl (integer), gefolgt von „Gi“ (GB). Beispiel: 4Gi
#### `--memory-request`
Die Anforderung der Kapazität der verwalteten Instanz als ganze Zahl (integer), gefolgt von „Gi“ (GB). Beispiel: 4Gi
#### `--no-wait`
Wenn dieser Wert vorhanden ist, wartet der Befehl nicht darauf, dass die Instanz bereit ist, bis eine Rückgabe erfolgt.
#### `--dev`
Wenn dieser Wert angegeben wird, gilt die Instanz als Entwicklungsinstanz, und es fallen keine Kosten dafür an.
#### `--labels`
Durch Trennzeichen getrennte Liste von Bezeichnungen der verwalteten SQL-Instanz.
#### `--annotations`
Durch Trennzeichen getrennte Liste von Anmerkungen der verwalteten SQL-Instanz.
#### `--service-labels`
Durch Trennzeichen getrennte Liste von Bezeichnungen, die auf alle externen Dienste angewendet werden sollen.
#### `--service-annotations`
Durch Trennzeichen getrennte Liste von Anmerkungen, die auf alle externen Dienste angewendet werden sollen.
#### `--agent-enabled`
Aktivieren Sie den SQL Server-Agent für die Instanz. Die Standardeinstellung ist „Deaktiviert“.
#### `--trace-flags`
Durch Trennzeichen getrennte Liste von Ablaufverfolgungsflags. Standardeinstellung ist ohne Flags.
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Hiermit wird eine verwaltete SQL-Instanz gelöscht.
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Hiermit wird eine verwaltete SQL-Instanz gelöscht.
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verwalteten SQL-Instanz, die gelöscht werden soll.
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Hiermit werden die Details einer verwalteten SQL-Instanz angezeigt.
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Hiermit werden die Details einer verwalteten SQL-Instanz angezeigt.
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verwalteten SQL-Instanz, die angezeigt werden soll.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--path -p`
Ein Pfad, unter den die vollständige Spezifikation der verwalteten SQL-Instanz geschrieben werden soll. Wenn keine Angabe erfolgt, wird die Spezifikation in die Standardausgabe geschrieben.
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi ab, und speichert es in einer Datei.
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Ruft das Zertifikat des Spiegelungsendpunkts der Verfügbarkeitsgruppe von sqlmi1 ab, und speichert es in der Datei „fileName1“.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Der Name der verwalteten SQL-Instanz.
#### `--cert-file`
Der lokale Dateiname zum Speichern des abgerufenen Zertifikats im PEM-Format.
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Hiermit werden verwaltete SQL-Instanzen aufgelistet.
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>Beispiele
Hiermit werden verwaltete SQL-Instanzen aufgelistet.
```bash
az sql mi-arc list
```
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
