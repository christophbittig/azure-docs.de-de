---
title: Referenz zu „az arcdata dc“
titleSuffix: Azure Arc-enabled data services
description: Referenzartikel zu az arcdata dc-Befehlen.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339775"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Befehle
| Befehl | BESCHREIBUNG|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Hiermit wird ein Datencontroller erstellt.
[az arcdata dc delete](#az-arcdata-dc-delete) | Hiermit wird ein Datencontroller gelöscht.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Endpunktbefehle
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Statusbefehle
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Konfigurationsbefehle
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Dient zum Debuggen des Datencontrollers.
[az arcdata dc export](#az-arcdata-dc-export) | Hiermit werden Metriken, Protokolle oder der Verbrauch exportiert.
[az arcdata dc upload](#az-arcdata-dc-upload) | Hiermit werden exportierte Datendateien hochgeladen.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Hiermit wird ein Datencontroller erstellt. Eine Kube-Konfiguration sowie die Umgebungsvariablen ['AZDATA_USERNAME', 'AZDATA_PASSWORD'] müssen in Ihrem System vorhanden sein.
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>Beispiele
Datencontrollerbereitstellung.
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem der Datencontroller bereitgestellt werden soll. Wenn er bereits vorhanden ist, wird er verwendet. Ist keiner vorhanden, wird zunächst versucht, ihn zu erstellen.
#### `--name -n`
Der Name des Datencontrollers.
#### `--connectivity-mode`
Die Konnektivität zu Azure – indirekt oder direkt, die für den Datencontroller gelten soll.
#### `--resource-group -g`
Die Azure-Ressourcengruppe, in der die Datencontrollerressource hinzugefügt werden soll.
#### `--location -l`
Der Speicherort in Azure, an dem die Metadaten des Datencontrollers gespeichert werden (z. B. eastus).
### <a name="optional-parameters"></a>Optionale Parameter
#### `--profile-name`
Der Name eines vorhandenen Konfigurationsprofils. Führen Sie `az arcdata dc config list` aus, damit verfügbare Optionen angezeigt werden. Einer der folgenden Werte: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-hci', 'azure-arc-aks-premium-storage'].
#### `--path -p`
Der Pfad zu einem Verzeichnis, das ein benutzerdefiniertes Konfigurationsprofil enthält, das benutzt werden kann. Führen Sie `az arcdata dc config init` aus, um ein benutzerdefiniertes Konfigurationsprofil zu erstellen.
#### `--storage-class`
Die Speicherklasse, die für alle persistenten Volumes für Daten und Protokolle für alle Datencontrollerpods verwendet werden soll, die sie erfordern.
#### `--infrastructure`
Die Infrastruktur, in der der Datencontroller ausgeführt wird. Zulässige Werte: ['aws', 'gcp', 'azure', 'alibaba', 'onpremises', 'other', 'auto']
#### `--labels`
Durch Komma getrennte Liste von Bezeichnungen, die auf alle Datencontrollerressourcen angewendet werden.
#### `--annotations`
Durch Komma getrennte Liste von Anmerkungen, die auf alle Datencontrollerressourcen angewendet werden.
#### `--service-annotations`
Durch Komma getrennte Liste von Anmerkungen, die auf alle externen Datencontrollerdienste angewendet werden.
#### `--service-labels`
Durch Komma getrennte Liste von Bezeichnungen, die auf alle externen Datencontrollerdienste angewendet werden.
#### `--storage-labels`
Durch Komma getrennte Liste von Bezeichnungen, die auf alle vom Datencontroller erstellten PVCs angewendet werden.
#### `--storage-annotations`
Durch Komma getrennte Liste von Anmerkungen, die auf alle vom Datencontroller erstellten PVCs angewendet werden.
#### `--use-k8s`
Erstellt den Datencontroller unter Verwendung der lokalen Kubernetes-APIs.
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Hiermit wird der Datencontroller gelöscht. Eine Kube-Konfiguration ist in Ihrem System erforderlich.
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>Beispiele
Datencontrollerbereitstellung.
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--name -n`
Name des Datencontrollers.
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem der Datencontroller vorhanden ist.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--force -f`
Erzwingt das Löschen des Datencontrollers und aller Datendienste.
#### `--yes -y`
Löscht den Datencontroller ohne Bestätigungsaufforderung.
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Hiermit werden Metriken, Protokolle oder der Verbrauch in eine Datei exportiert.
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--type -t`
Der Typ der Daten, die exportiert werden sollen. Optionen: Protokolle, Metriken und Verbrauch.
#### `--path -p`
Der vollständige oder relative Pfad einschließlich des Dateinamens der Datei, die exportiert werden soll.
#### `--k8s-namespace -k`
Der Kubernetes-Namespace, in dem der Datencontroller vorhanden ist.
### <a name="optional-parameters"></a>Optionale Parameter
#### `--force -f`
Hiermit wird die Erstellung der Ausgabedatei erzwungen. Hiermit werden vorhandene Dateien unter demselben Pfad überschrieben.
#### `--use-k8s`
Verwendet lokale Kubernetes-APIs, um diese Aktion auszuführen.
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Hiermit werden von einem Datencontroller exportierte Datendateien zu Azure hochgeladen.
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>Erforderliche Parameter
#### `--path -p`
Der vollständige oder relative Pfad einschließlich des Dateinamens der Datei, die hochgeladen werden soll.
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
