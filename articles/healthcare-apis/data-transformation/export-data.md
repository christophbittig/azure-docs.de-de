---
title: Ausführen des Exports durch Aufrufen $export Befehls im FHIR-Dienst
description: In diesem Artikel wird beschrieben, wie FHIR-Daten mithilfe von $export exportiert werden.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/25/2021
ms.author: cavoeg
ms.openlocfilehash: e161834e7b0d503e508f02260a150237f8345342
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355722"
---
# <a name="how-to-export-fhir-data"></a>Exportieren von FHIR-Daten


Mit der Funktion für Massenexport können Daten vom FHIR-Server gemäß der [FHIR-Spezifikation](https://hl7.org/fhir/uv/bulkdata/export/index.html) exportiert werden. 

Bevor Sie $export verwenden, müssen Sie sicherstellen, dass der FHIR-Dienst für die Verwendung konfiguriert ist. Informationen zum Konfigurieren von Exporteinstellungen und zum Erstellen eines Azure Storage-Kontos finden Sie auf der [Seite zum Konfigurieren von Exportdaten](configure-export-data.md).

## <a name="using-export-command"></a>Verwenden $export-Befehls

Nachdem Sie den FHIR-Dienst für den Export konfiguriert haben, können Sie den befehl $export verwenden, um die Daten aus dem Dienst zu exportieren. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Konfigurieren des Exports angegeben haben. Informationen zum Aufrufen des Befehls $export auf dem FHIR-Server finden Sie in der Dokumentation zur [$export-Spezifikation von HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Aufträge hängen in einem fehlerhaften Zustand**

In einigen Situationen besteht die Möglichkeit, dass ein Auftrag in einem fehlerhaften Zustand hängen bleibt. Dies kann insbesondere auftreten, wenn die Speicherkontoberechtigungen nicht ordnungsgemäß eingerichtet wurden. Eine Möglichkeit, zu überprüfen, ob ihr Export erfolgreich war, besteht darin, Ihr Speicherkonto zu überprüfen, um festzustellen, ob die entsprechenden Containerdateien (d. h. ndjson) vorhanden sind. Wenn sie nicht vorhanden sind und keine anderen Exportaufträge ausgeführt werden, besteht die Möglichkeit, dass der aktuelle Auftrag in einem fehlerhaften Zustand hängen bleibt. Sie sollten den Exportauftrag abbrechen, indem Sie eine Abbruchanforderung senden und versuchen, den Auftrag erneut in die Warteschlange zu stellen. Die Standardlaufzeit für einen Fehlerhaften Export beträgt 10 Minuten, bevor er beendet wird und zu einem neuen Auftrag wechselt oder den Export erneut versucht. 

Der FHIR-Dienst unterstützt $export auf den folgenden Ebenen:
* [System:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export) `GET https://<<FHIR service base URL>>/$export>>`
* [Patient:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients) `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Gruppe der Patienten*:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) Der FHIR-Dienst exportiert alle zugehörigen Ressourcen, exportiert jedoch nicht die Merkmale der Gruppe: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Beim Exportieren von Daten wird eine separate Datei für jeden Ressourcentyp erstellt. Um sicherzustellen, dass die exportierten Dateien nicht zu groß werden. Wir erstellen eine neue Datei, nachdem die Größe einer einzelnen exportierten Datei größer als 64 MB ist. Das Ergebnis ist, dass Sie möglicherweise mehrere Dateien für jeden Ressourcentyp erhalten, die aufzählt werden (d.h. Patient-1.ndjson, Patient-2.ndjson). 


> [!Note] 
> `Patient/$export` und `Group/[ID]/$export` exportieren möglicherweise doppelt vorhandene Ressourcen, wenn sich die Ressource in einem Depot mehrerer Ressourcen befindet oder in mehreren Gruppen vorhanden ist.

Darüber hinaus wird die Überprüfung des Exportstatus über die URL, die von der Standortkopfzeile während der Warteschlangendauer zurückgegeben wird, sowie das Abbrechen des eigentlichen Exportauftrags unterstützt.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportieren von FHIR-Daten nach ADLS Gen2

Derzeit wird „$export“ für ADLS Gen2-fähige Speicherkonten unterstützt. Hierbei gilt aber die folgende Einschränkung:

- Der Benutzer kann [hierarchische Namespaces](../../storage/blobs/data-lake-storage-namespace.md)nicht nutzen, aber es gibt keine Möglichkeit, den Export in ein bestimmtes Unterverzeichnis innerhalb des Containers als Ziel zu nutzen. Es kann nur ein bestimmter Container angegeben werden (wobei für jeden Export ein neuer Ordner erstellt wird).
- Nach Abschluss eines Exportvorgangs werden nicht noch einmal Daten in diesen Ordner exportiert, da für die nachfolgenden Exporte in denselben Container dann ein neu erstellter Ordner genutzt wird.


## <a name="settings-and-parameters"></a>Einstellungen und Parameter

### <a name="headers"></a>Header
Es gibt zwei erforderliche Headerparameter, die für $export-Aufträge festgelegt werden müssen. Die Werte werden durch die aktuelle [$export-Spezifikation](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers) definiert.
* **Accept:** application/fhir+json
* **Prefer:** respond-async

### <a name="query-parameters"></a>Abfrageparameter
Der FHIR-Dienst unterstützt die folgenden Abfrageparameter. die alle optional sind:

|Query parameter (Abfrageparameter)        | Definiert durch die FHIR-Spezifikation?    |  BESCHREIBUNG|
|------------------------|---|------------|
| \_outputFormat | Ja | Unterstützt derzeit drei Werte gemäß FHIR-Spezifikation: „application/fhir+ndjson“, „application/ndjson“ oder nur „ndjson“. Alle Exportaufträge geben `ndjson` zurück, und der übergebene Wert hat keine Auswirkung auf das Codeverhalten. |
| \_since | Ja | Ermöglicht nur das Exportieren von Ressourcen, die seit dem angegebenen Zeitpunkt geändert wurden |
| \_type | Ja | Ermöglicht die Angabe, welche Ressourcentypen eingeschlossen werden sollen. Beispielsweise gibt „\_type=Patient“ nur Patientenressourcen zurück.|
| \_typeFilter | Ja | Um eine differenziertere Filterung anzufordern, können Sie „\_typeFilter“ zusammen mit dem Parameter „\_type“ verwenden. Der Wert des Parameters „_typeFilter“ ist eine durch Trennzeichen getrennte Liste von FHIR-Abfragen, mit denen die Ergebnisse weiter eingegrenzt werden. |
| \_container | Nein |  Gibt den Container innerhalb des konfigurierten Speicherkontos an, in den die Daten exportiert werden sollen. Wenn ein Container angegeben wird, werden die Daten in einen Ordner in diesen Container exportiert. Wenn der Container nicht angegeben ist, werden die Daten in einen neuen Container exportiert. |

> [!Note]
> Nur Speicherkonten im selben Abonnement wie für den FHIR-Dienst dürfen als Ziel für $export Vorgänge registriert werden.

## <a name="secure-export-to-azure-storage"></a>Sicherer Export in Azure Storage

Der FHIR-Dienst unterstützt einen sicheren Exportvorgang. Wählen Sie eine der beiden folgenden Optionen aus:

* Zulassen, dass der FHIR-Dienst als vertrauenswürdiger Microsoft-Dienst auf das Azure-Speicherkonto zugreift.
 
* Zulassen, dass bestimmte IP-Adressen, die dem FHIR-Dienst zugeordnet sind, auf das Azure-Speicherkonto zugreifen können. Diese Option bietet zwei unterschiedliche Konfigurationen, je nachdem, ob sich das Speicherkonto am gleichen Standort wie befindet oder sich an einem anderen Speicherort als der des FHIR-Diensts befindet.

### <a name="allowing-fhir-service-as-a-microsoft-trusted-service"></a>Zulassen des FHIR-Diensts als vertrauenswürdiger Microsoft-Dienst

Wählen Sie im Azure-Portal ein Speicherkonto aus, und wählen Sie dann das Blatt **Netzwerk** aus. Wählen Sie auf der Registerkarte **Firewalls und virtuelle Netzwerke** die Option **Ausgewählte Netzwerke** aus.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie mithilfe der verwalteten Identität zugriffsberechtigungen für das Speicherkonto für den FHIR-Dienst erteilt haben. Weitere Informationen finden Sie unter [Konfigurieren der Exporteinstellung und Einrichten des Speicherkontos.](./configure-export-data.md)

  :::image type="content" source="media/export-data/storage-networking.png" alt-text="Netzwerkeinstellungen für Azure-Speicher" lightbox="media/export-data/storage-networking.png":::

Aktivieren Sie im Abschnitt **Ausnahmen** das Kontrollkästchen **Vertrauenswürdigen Microsoft-Dienste den Zugriff auf dieses Speicherkonto erlauben,** und speichern Sie die Einstellung. 

:::image type="content" source="media/export-data/exceptions.png" alt-text="Vertrauenswürdigen Microsoft-Dienste den Zugriff auf dieses Speicherkonto gestatten.":::

Sie können jetzt FHIR-Daten sicher in das Speicherkonto exportieren. Beachten Sie, dass sich das Speicherkonto in ausgewählten Netzwerken befindet und nicht öffentlich zugänglich ist. Um auf die Dateien zuzugreifen, können Sie entweder private Endpunkte für das Speicherkonto aktivieren und verwenden oder alle Netzwerke für das Speicherkonto für einen kurzen Zeitraum aktivieren.

> [!IMPORTANT]
> Die Benutzeroberfläche wird später aktualisiert, damit Sie den Ressourcentyp für den FHIR-Dienst und eine bestimmte Dienstinstanz auswählen können.

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-a-different-region"></a>Zulassen bestimmter IP-Adressen für das Azure-Speicherkonto in einer anderen Region

Wählen  Sie im Portal Netzwerk des Azure-Speicherkontos aus. 
   
Klicken Sie auf **Ausgewählte Netzwerke**. Geben Sie im Abschnitt Firewall die IP-Adresse im Feld **Adressbereich an.** Fügen Sie IP-Adressbereiche hinzu, um den Zugriff über das Internet oder Ihre lokalen Netzwerke zuzulassen. Sie finden die IP-Adresse in der folgenden Tabelle für die Azure-Region, in der der FHIR-Dienst bereitgestellt wird.

|**Azure-Region**         |**Öffentliche IP-Adresse** |
|:----------------------|:-------------------|
| Australien (Osten)       | 20.53.44.80       |
| Kanada, Mitte       | 20.48.192.84      |
| USA (Mitte)           | 52.182.208.31     |
| East US              | 20.62.128.148     |
| USA (Ost) 2            | 20.49.102.228     |
| USA, Osten 2 (EUAP)       | 20.39.26.254      |
| Deutschland, Norden        | 51.116.51.33      |
| Deutschland, Westen-Mitte | 51.116.146.216    |
| Japan, Osten           | 20.191.160.26     |
| Korea, Mitte        | 20.41.69.51       |
| USA Nord Mitte     | 20.49.114.188     |
| Nordeuropa         | 52.146.131.52     |
| Südafrika, Norden   | 102.133.220.197   |
| USA Süd Mitte     | 13.73.254.220     |
| Asien, Südosten       | 23.98.108.42      |
| Schweiz, Norden    | 51.107.60.95      |
| UK, Süden             | 51.104.30.170     |
| UK, Westen              | 51.137.164.94     |
| USA, Westen-Mitte      | 52.150.156.44     |
| Europa, Westen          | 20.61.98.66       |
| USA, Westen 2            | 40.64.135.77      |

> [!NOTE]
> Die obigen Schritte ähneln den Konfigurationsschritten, die im Dokument Konvertieren von Daten in FHIR (Vorschauversion) beschrieben werden. Weitere Informationen finden Sie unter [Hosten und Verwenden von Vorlagen.](./convert-data.md#host-and-use-templates)

### <a name="allowing-specific-ip-addresses-for-the-azure-storage-account-in-the-same-region"></a>Zulassen bestimmter IP-Adressen für das Azure-Speicherkonto in derselben Region

Der Konfigurationsprozess entspricht dem obigen Prozess, aber mit der Ausnahme, dass ein bestimmter IP-Adressbereich im CIDR-Format verwendet wird: 100.64.0.0/10. Der IP-Adressbereich (100.64.0.0 bis 100.127.255.255) muss angegeben werden, da die tatsächlich vom Dienst genutzte IP-Adresse variiert. Sie liegt für jede $export-Anforderung aber immer in diesem Bereich.

> [!Note] 
> Es ist möglich, dass stattdessen eine private IP-Adresse im Bereich 10.0.2.0/24 genutzt wird. In diesem Fall ist der $export Vorgang nicht erfolgreich. Sie können die $export Anforderung wiederholen, aber es gibt keine Garantie, dass eine IP-Adresse im Bereich von 100.64.0.0/10 beim nächsten Mal verwendet wird. Dies ist das bekannte standardmäßige Netzwerkverhalten. Die Alternative besteht darin, das Speicherkonto in einer anderen Region zu konfigurieren.
    
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie FHIR-Ressourcen mit $export Befehl exportieren. Weitere Informationen zum Exportieren von nicht identifizierten Daten finden Sie unter:
 
>[!div class="nextstepaction"]
>[Exportieren anonymisierter Daten](de-identified-export.md)