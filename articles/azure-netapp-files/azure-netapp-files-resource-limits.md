---
title: Ressourceneinschränkungen für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt Limits für Azure NetApp Files-Ressourcen und wie eine Erhöhung des Ressourcenlimits angefordert wird.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 4a97b89957a4f451aaa9c7305535f87980234db2
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094289"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Ressourcenlimits für Azure NetApp Files

Grundlegende Informationen zu Ressourceneinschränkungen für Azure NetApp Files unterstützen Sie bei der Verwaltung Ihrer Volumes.

## <a name="resource-limits"></a>Ressourceneinschränkungen

In der folgenden Tabelle werden die Ressourcengrenzwerte für Azure NetApp Files beschrieben:

|  Resource  |  Standardlimit  |  Über Supportanfrage anpassbar  |
|----------------|---------------------|--------------------------------------|
|  [Regionales Kapazitätskontingent pro Abonnement](#regional-capacity-quota)   |  25 TiB  |  Ja  |
|  Anzahl von NetApp-Konten pro Azure-Region und Abonnement  |  10    |  Ja   |
|  Anzahl der Kapazitätspools pro NetApp-Konto   |    25     |   Ja   |
|  Anzahl von Volumes pro Abonnement   |    500     |   Ja   |
|  Anzahl der Volumes pro Kapazitätspool     |    500   |    Ja     |
|  Anzahl von Momentaufnahmen pro Volume       |    255     |    Nein        |
|  Anzahl der an Azure NetApp Files (Microsoft.NetApp/volumes) delegierten Subnetze pro virtuellem Azure-Netzwerk    |   1   |    Nein    |
|  Anzahl verwendeter IP-Adressen innerhalb eines VNets (einschließlich VNets mit sofortigem Peering) mit Azure NetApp Files   |    1000   |    Nein   |
|  Mindestgröße eines einzelnen Kapazitätspools   |  4 TiB     |    Nein  |
|  Maximale Größe eines einzelnen Kapazitätspools    |  500 TiB   |   Nein   |
|  Mindestgröße eines einzelnen Volumes    |    100 GB    |    Nein    |
|  Maximale Größe eines einzelnen Volumes     |    ca. 100 TiB    |    Nein    |
|  Maximale Größe einer einzelnen Datei     |    16 TiB    |    Nein    |    
|  Maximale Größe der Verzeichnismetadaten in einem einzelnen Verzeichnis      |    320 MB    |    Nein    |    
|  Maximale Anzahl an Dateien in einem einzelnen Verzeichnis  | *Ca.* 4 Millionen. <br> Siehe [Bestimmen, ob sich ein Verzeichnis der Begrenzungsgröße nähert](#directory-limit).  |    Nein    |   
|  Maximale Anzahl von Dateien ([maxfiles](#maxfiles)) pro Volume     |    100 Mio.    |    Ja    |    
|  Maximale Anzahl von Exportrichtlinienregeln pro Volume     |    5  |    Nein    | 
|  Minimaler zugewiesener Durchsatz für ein Volume für manuelle QoS     |    1 MiB/s   |    Nein    |    
|  Maximaler zugewiesener Durchsatz für ein Volume für manuelle QoS     |    4\.500 MiB/s    |    Nein    |    
|  Anzahl der regionsübergreifenden Datenschutzvolumes für die regionsübergreifende Replikation (Zielvolumes)     |    10    |    Ja    |     
|  Maximale Anzahl der richtlinienbasierten (geplanten) Sicherungen pro Volume  | <ul><li> Tägliche Aufbewahrungsanzahl: 1 (Minimum) bis 1019 (Maximum) </li> <li> Wöchentliche Aufbewahrungsanzahl: 1 (Minimum) bis 1019 (Maximum) </li> <li> Monatliche Aufbewahrungsanzahl: 1 (Minimum) bis 1019 (Maximum) </ol></li> <br> Die maximale Anzahl der täglichen, wöchentlichen und monatlichen Sicherungsaufbewahrungen beträgt *zusammen* 1019.  |  N  |
|  Maximale Größe des geschützten Volumes  |  ca. 100 TiB  |  N  |
|  Maximale Anzahl von Volumes, die pro Abonnement gesichert werden können   |  5  |  J  |
|  Maximale Anzahl manueller Sicherungen pro Volume und Tag |  5  |  J  |

Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Kapazitätsverwaltung](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>Feststellen, ob sich ein Verzeichnis der Begrenzungsgröße nähert <a name="directory-limit"></a>  

Sie können den Befehl `stat` von einem Client aus verwenden, um festzustellen, ob sich ein Verzeichnis der maximal zulässigen Größe für Verzeichnismetadaten (320 MB) nähert.   

Bei einem Verzeichnis mit 320 MB beträgt die Anzahl der Blöcke 655.360, wobei jeder Block eine Größe von 512 Bytes hat.  (Berechnung: 320 · 1.024 · 1.024 : 512.) Daraus ergibt sich für ein Verzeichnis mit 320 MB ein Maximum von etwa vier Millionen Dateien. Die tatsächliche maximale Anzahl von Dateien ist jedoch ggf. geringer. Dies hängt von Faktoren wie etwa der Anzahl von Dateien mit ASCII-fremden Zeichen im Verzeichnis ab. Verwenden Sie daher den Befehl `stat` wie folgt, um zu ermitteln, ob der Grenzwert Ihres Verzeichnisses bald erreicht ist.  

Beispiele:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Maxfiles-Limits <a name="maxfiles"></a> 

Azure NetApp Files-Volumes besitzen ein Limit namens *maxfiles*. Das maxfiles-Limit ist die Anzahl von Dateien, die ein Volume enthalten kann. Linux-Dateisysteme beziehen sich auf das Limit als *I-Knoten*. Das maxfiles-Limit für ein Azure NetApp Files-Volume wird basierend auf der Größe (dem Kontingent) des Volumes indiziert. Das maxfiles-Limit für ein Volume erhöht oder verringert sich mit einer Rate von 20 Millionen Dateien pro TiB bereitgestellter Volumegröße. 

Der Dienst passt das maxfiles-Limit für ein Volume basierend auf seiner bereitgestellten Größe dynamisch an. Beispielsweise hätte ein Volume, das anfänglich mit einer Größe von 1 TiB konfiguriert wurde, ein maxfiles-Limit von 20 Millionen. Nachfolgende Änderungen an der Größe des Volumes führten zu einer automatischen Neuanpassung des maxfiles-Limits basierend auf den folgenden Regeln: 

|    Volumegröße (Kontingent)     |  Automatische Neuanpassung des maxfiles-Limits    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 Mio.     |
|    > 1 TiB, aber <= 2 TiB    |    40 Mio.     |
|    > 2 TiB, aber <= 3 TiB    |    60 Mio.     |
|    > 3 TiB, aber <= 4 TiB    |    80 Mio.     |
|    > 4 TiB                 |    100 Mio.    |

Wenn Sie für ein Volume bereits ein Kontingent von mindestens 4 TiB zugeordnet haben, können Sie eine [Supportanfrage](#request-limit-increase) initiieren, um das maxfiles-Limit (I-Knoten) auf über 100 Millionen zu erhöhen. Für jede Erhöhung um 100 Millionen Dateien (oder einen Bruchteil davon) müssen Sie das entsprechende Volumekontingent um 4 TiB erhöhen.  Wenn Sie z. B. den Grenzwert „maxfiles“ von 100 Millionen Dateien auf 200 Millionen Dateien (oder eine beliebige Zahl dazwischen) erhöhen, müssen Sie das Volumenkontingent von 4 TiB auf 8 TiB erhöhen.

Sie können das maxfiles-Limit auf 500 Millionen erhöhen, wenn das Volumenkontingent mindestens 20 TiB beträgt. <!-- ANF-11854 --> 

## <a name="regional-capacity-quota"></a>Regionales Kapazitätskontingent

Sie können in den Einstellungen für Azure NetApp Files auf **Kontingent** klicken, um die aktuellen und standardmäßigen Kontingentgrößen für die Region anzuzeigen. 

Zum Beispiel: 

![Screenshot: Anzeigen von Kontingentinformationen](../media/azure-netapp-files/quota-display.png) 

Sie können eine [Supportanfrage](#request-limit-increase) zur Erhöhung eines regionalen Kapazitätskontingent einreichen, ohne dass Ihnen zusätzliche Kosten entstehen. Die von Ihnen übermittelte Supportanfrage wird zur Verarbeitung an das Azure-Team für die Kapazitätsverwaltung gesendet. Sie erhalten in der Regel innerhalb von zwei Arbeitstagen eine Antwort. Wenn Sie eine umfangreiche Erhöhung anfordern, werden Sie möglicherweise vom Azure-Team für die Kapazitätsverwaltung kontaktiert.  

Eine Erhöhung des regionalen Kapazitätskontingents führt nicht zu einer Erhöhung der Abrechnung. Die Abrechnung basiert weiterhin auf den bereitgestellten Kapazitätspools.
Wenn Sie beispielsweise derzeit über eine bereitgestellte Kapazität von 25 TiB verfügen, können Sie eine Erhöhung des Kontingents auf 35 TiB anfordern.  Innerhalb von zwei Werktagen wird Ihre Kontingenterhöhung auf die angeforderte Region angewendet. Wenn die Kontingenterhöhung angewendet wird, zahlen Sie weiterhin nur für die derzeit bereitgestellte Kapazität (25 TiB). Wenn Sie jedoch die zusätzlichen 10 TiB tatsächlich bereitstellen, werden Ihnen 35 TiB in Rechnung gestellt.

Die aktuellen [Ressourcenlimits](#resource-limits) für Azure NetApp Files bleiben unverändert. Sie können weiterhin einen 500-TiB-Kapazitätspool bereitstellen. Vorher muss das regionale Kapazitätskontingent jedoch auf 500 TiB erhöht werden.

## <a name="request-limit-increase"></a>Anfordern einer Erhöhung der Grenzwerte

Sie können eine Supportanfrage an den Azure-Support stellen, um die anpassbaren Grenzwerte aus der Tabelle [Ressourcenlimits](#resource-limits) zu erhöhen. 

1. Wechseln Sie unter **Support + Problembehandlung** zu **Neue Supportanfrage**.
1. Geben Sie auf der Registerkarte **Problembeschreibung** die angeforderten Informationen an.
1. Klicken Sie auf der Registerkarte **Zusätzliche Details** im Feld „Anforderungsdetails“ auf **Details eingeben**.  

    ![Screenshot: Registerkarte „Details“ mit dem Feld „Details eingeben“](../media/azure-netapp-files/quota-additional-details.png)

1. Gehen Sie im angezeigten Fenster „Kontingentdetails“ folgendermaßen vor:  

    1. Wählen Sie unter „Kontingenttyp“ den Ressourcentyp aus, für den Sie eine Erhöhung anfordern möchten.  
        Zum Beispiel:  
        * *Regionales Kapazitätskontingent pro Abonnement (TiB)*
        * *Anzahl von NetApp-Konten pro Azure-Region und Abonnement*
        * *Anzahl von Volumes pro Abonnement*

    1. Wählen Sie unter „Angeforderte Region“ Ihre Region aus.   
        Unter „Kontingentstatus“ werden die aktuelle Größe und die Standardgröße angezeigt.
    1. Geben Sie einen Wert ein, um eine Erhöhung für den angegebenen Kontingenttyp anzufordern.
    
    ![Screenshot: Anzeigen und Anfordern einer Erhöhung des regionalen Kontingents](../media/azure-netapp-files/quota-details-regional-request.png)

1. Klicken Sie auf **Weiter** und **Überprüfen + erstellen**, um die Anforderung zu erstellen.

## <a name="next-steps"></a>Nächste Schritte  

- [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
