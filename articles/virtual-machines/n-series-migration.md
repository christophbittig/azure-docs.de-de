---
title: Migrationshandbuch für GPU-Computeworkloads in Azure
description: Migrationshandbuch für die NC-, ND- und NCv2-Serie.
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: iafinder
ms.openlocfilehash: 574118d9bd8c400eccb48ed551d6059c0dfc7bf2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551679"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Migrationshandbuch für GPU-Computeworkloads in Azure

Da immer leistungsfähigere GPUs im Marketplace und in Microsoft Azure-Rechenzentren zur Verfügung stehen, empfiehlt es sich, die Leistung Ihrer Workloads neu zu bewerten und die Migration zu neueren GPUs in Betracht zu ziehen.

Aus diesem Grund und um ein hochwertiges und zuverlässiges Dienstangebot zu garantieren, wird die Hardware, die ältere VM-Größen unterstützt, von Azure in regelmäßigen Abständen aus dem Betrieb genommen. Bei den ersten GPU-Produkten, die aus dem Betrieb genommen werden, handelt es sich um die originalen VMs der NC-, NCv2- und ND-Serie, die auf NVIDIA Tesla K80-, P100- bzw. P40-GPU-Beschleunigern für Rechenzentren basieren. Diese Produkte werden nun am 31. August 2022 ausgemustert. Die ältesten VMs dieser Serie wurden bereits im Jahr 2016 eingeführt.

Seitdem haben GPUs gemeinsam mit der Deep Learning- und HPC-Branche enorme Fortschritte gemacht, wobei sich die Leistung von Generation zu Generation in der Regel verdoppelt. Seit der Einführung der NVIDIA K80-, P40- und P100-GPUs verfügt Azure über mehrere neuere Generationen und Kategorien von VM-Produkten, die auf GPU-beschleunigtes Computing und KI ausgerichtet sind, auf T4-, V100- und A100-GPUs von NVIDIA basieren und sich durch optionale Features wie z. B. InfiniBand-basierte Verbindungsfabrics unterscheiden. Wir empfehlen allen Kunden, diese Optionen als Migrationspfade in Betracht zu ziehen.

In den meisten Fällen verringert die drastische Leistungssteigerung durch neuere GPU-Generationen die Gesamtkosten (obwohl die Kosten pro GPU-Stunde variieren können), z. B. indem die Dauer von burstfähigen Aufträgen verringert wird, oder indem die Anzahl der gesamten GPU-fähigen VMs reduziert wird, die erforderlich sind, um einen festgelegten Bedarf an Computeressourcen abzudecken. Zusätzlich zu diesen Vorteilen können Kunden die Übergangsphase mithilfe von leistungsfähigeren VMs verbessern und die Integrität und Unterstützbarkeit ihrer Lösung optimieren, indem sie neuere Software, die CUDA-Runtime und Treiberversionen einsetzen.

## <a name="migration-vs-optimization"></a>Migration im Vergleich zur Optimierung

Wir wissen, dass Kunden eine Vielzahl von Anforderungen haben, durch die die Auswahl eines bestimmten GPU-VM-Produkts ggf. festgelegt ist. Dazu zählen Überlegungen zur GPU-Architektur, zu Verbindungen, zu Gesamtkosten, zur Übergangszeit und zur regionalen Verfügbarkeit, die jeweils auf den Compliance- oder Latenzanforderungen basieren. Einige dieser Anforderungen können sich sogar im Laufe der Zeit ändern.

Gleichzeitig handelt es sich bei der GPU-Beschleunigung um einen neuen und sich schnell entwickelnden Produktbereich.

Deshalb gibt es noch keinen generischen und allgemein verbindlichen Ansatz für diesen Bereich. Die Migration ist somit ein perfekter Zeitpunkt, um potenziell drastische Änderungen an einer Workload neu zu bewerten – z. B. den Wechsel von einem clusterbasierten Bereitstellungsmodell zu einem einzelnen großen virtuellen Computer mit 8 GPU oder umgekehrt, die Nutzung von Datentypen mit geringerer Genauigkeit, die Einführung von Features wie einer GPU mit mehreren Instanzen und vieles mehr.

Diese Überlegungen sind äußerst workloadspezifisch, insbesondere unter Berücksichtigung der drastischen GPU-Leistungssteigerung von Generation zu Generation, bei der bereits ein einziges Feature (z. B. das Hinzufügen von TensorCores) die Leistung beträchtlich erhöhen kann.

Eine Kombination aus Migration und Anwendungsarchitektur kann enorme Nutzeffekte mit sich bringen und eine beträchtliche Kostenverbesserung sowie eine Verkürzung der Übergangsphase bewirken.

Dieses Dokument konzentriert sich jedoch nicht auf diese Art von Verbesserungen, sondern mehr auf direkte Äquivalenzklassen für generalisierte Workloads, die heutzutage von Kunden ausgeführt werden können. Auf diese Weise ist es möglich, die ähnlichsten VM-Optionen (sowohl in Bezug auf den Preis *als auch* in Bezug auf die Leistung pro GPU) für VM-Familien zu identifizieren, die bald außer Betrieb genommen werden.

Deshalb wird in diesem Dokument davon ausgegangen, dass der Benutzer möglicherweise keine Kenntnisse oder Kontrolle über workloadspezifische Eigenschaften wie die Anzahl der erforderlichen VM-Instanzen, GPUs, Verbindungen und mehr hat.

## <a name="recommended-upgrade-paths"></a>Empfohlene Upgrademöglichkeiten

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>VMs der NC-Serie mit NVIDIA K80-GPUs

Die VMs der [NC (v1)-Serie](./nc-series.md) sind der älteste VM-Typ für GPU-beschleunigtes Computing von Azure. Sie verfügen über 1 bis 4 NVIDIA Tesla K80-GPU-Beschleuniger für Rechenzentren, die mit Intel Xeon E5-2690 v3-Prozessoren (Haswell) gekoppelt sind. Dieser VM-Typ war früher vor allem für anspruchsvolle KI-, ML- und HPC-Anwendungen und bis spät in seinen Produktlebenszyklus hinein (insbesondere durch die attraktiven Angebotspreise für die NC-Serie) eine beliebte Wahl. Er wurden vor allem von Benutzern bevorzugt, die mehr Wert auf einen niedrigen Gesamtpreis pro GPU-Stunde anstatt auf einen höheren Durchsatz pro Dollar legten.

Angesichts der relativ geringen Computeleistung der veralteten NVIDIA K80-GPU-Plattform im Vergleich zu den VM-Serien mit neueren GPUs wird die NC-Serie heutzutage vor allem für Echtzeitrückschluss- und Analyseworkloads eingesetzt, bei denen eine beschleunigte VM in einem stabilen Zustand verfügbar sein muss, um Anforderungen von Anwendungen zu verarbeiten, sobald diese eintreffen. In diesen Fällen ist das Volumen oder die Batchgröße der Anforderungen möglicherweise nicht ausreichend, um wirklich von leistungsstärkeren GPUs zu profitieren. NC-VMs sind auch bei Entwicklern und Studenten beliebt, die sich mit der GPU-Beschleunigung beschäftigen oder Entwicklungen und Experimente in diesem Bereich durchführen. Dafür benötigen sie vor allem ein kostengünstiges cloudbasiertes CUDA-Bereitstellungsziel, auf dem Iterationen ausgeführt werden können, und das nicht die gleiche Leistung wie VMs auf Produktionsebene erbringen muss.

Im Allgemeinen sollten Kunden der NC-Serie direkt von NC-Größen zu [NC T4 v3](./nct4-v3-series.md)-Größen, d. h. der neuen GPU-beschleunigten, auf NVIDIA Tesla T4-GPUs basierenden Plattform von Azure für leichte Workloads, wechseln. Für Workloads, die auf InfiniBand-fähigen Größen der NC-Serie ausgeführt werden, sollten jedoch lieber andere VM-SKUs in Betracht gezogen werden.

| Aktuelle VM-Größe | Zielgröße des virtuellen Computers | Unterschied bei der Spezifikation | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>oder<br>Standard_NC8as_T4 | CPU: Intel Haswell vs. AMD Rome<br>GPU-Anzahl: 1 (identisch)< br>GPU-Generation: NVIDIA Keppler vs. Turing (+2 Generationen, ~2x FP32 FLOPs)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (+4)<br>vCPU: 4 (-2) oder 8 (+2)<br>Arbeitsspeicher-GiB: 16 (-40) oder 56 (identisch)<br>Temporärer Speicher (SSD) GiB: 180 (-160) oder 360 (+20)<br>Max. Anzahl Datenträger: 8 (-4) oder 16 (+4)<br>Beschleunigter Netzwerkbetrieb: Ja (+)<br>Storage Premium: Ja (+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | CPU: Intel Haswell vs. AMD Rome<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Keppler vs. Turing (+2 Generationen, ~2x FP32 FLOPs)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (+4)<br>vCPU: 64 (+40)<br>Arbeitsspeicher-GiB: 440 (+216)<br>Temporärer Speicher (SSD) GiB: 2880 (+1440)<br>Max. Anzahl Datenträger: 32 (-32)<br>Beschleunigter Netzwerkbetrieb: Ja (+)<br>Storage Premium: Ja (+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>(Größen für InfiniBand-Clustering) | Standard_NC24rs_v3* | CPU: Intel Haswell vs. Intel Broadwell<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Keppler vs. Volta (+2 Generationen)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (+4)<br>vCPU: 24 (+0)<br>Arbeitsspeicher-GiB: 448 (+224)<br>Temporärer Speicher (SSD) GiB: 2948 (+1440)<br>Max. Anzahl Datenträger: 32 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+)<br>InfiniBand-Verbindung: Ja | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>VMs der ND-Serie mit NVIDIA Tesla P40-GPUs

Bei den virtuellen Computern der ND-Serie handelt es sich um eine Midrange-Plattform, die ursprünglich für KI- und Deep Learning-Workloads entwickelt wurde. Sie bieten eine hervorragende Leistung für Batchrückschlüsse, vor allem durch die im Vergleich zu ihren Vorgängern verbesserten Gleitkommaoperationen mit einfacher Genauigkeit, und basieren auf NVIDIA Tesla P40-GPUs und Intel Xeon E5-2690 v4-CPUs (Broadwell). Genau wie die NC- und NC v2-Serie bietet auch die ND-Serie eine Konfiguration mit einem sekundären, RDMA-basierten Netzwerk mit geringer Wartezeit und hohem Durchsatz sowie InfiniBand-Konnektivität, sodass Sie umfangreiche Trainingsaufträge über mehrere GPUs hinweg ausführen können.

| Aktuelle VM-Größe | Zielgröße des virtuellen Computers | Unterschied bei der Spezifikation | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>oder<br>Standard_NC8as_T4 | CPU: Intel Broadwell vs. AMD Rome<br>GPU-Anzahl: 1 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Turing (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (-8)<br>vCPU: 4 (-2) oder 8 (+2)<br>Arbeitsspeicher-GiB: 16 (-40) oder 56 (-56)<br>Temporärer Speicher (SSD) GiB: 180 (-552) oder 360 (-372)<br>Max. Anzahl Datenträger: 8 (-4) oder 16 (+4)<br>Beschleunigter Netzwerkbetrieb: Ja (+)<br>Storage Premium: Ja (+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | CPU: Intel Broadwell vs. AMD Rome<br>GPU-Anzahl: 1 (-1)<br>GPU-Generation: NVIDIA Pascal vs. Turing (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (-8)<br>vCPU: 16 (+4)<br>Arbeitsspeicher-GiB: 110 (-114)<br>Temporärer Speicher (SSD) GiB: 360 (-1,114)<br>Max. Anzahl Datenträger: 48 (+16)<br>Beschleunigter Netzwerkbetrieb: Ja (+)<br>Storage Premium: Ja (+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | CPU: Intel Broadwell vs. AMD Rome<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Turing (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (-8)<br>vCPU: 64 (+40)<br>Arbeitsspeicher-GiB: 440 (identisch)<br>Temporärer Speicher (SSD) GiB: 2880 (identisch)<br>Max. Anzahl Datenträger: 32 (identisch)<br>Beschleunigter Netzwerkbetrieb: Ja (+)<br>Storage Premium: Ja (+) | 
| Standard_ND24r | Standard_NC24rs_v3* | CPU: Intel Broadwell (identisch)<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Volta (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (-8)<br>vCPU: 24 (+0)<br>Arbeitsspeicher-GiB: 448 (identisch)<br>Temporärer Speicher (SSD) GiB: 2948 (identisch)<br>Max. Anzahl Datenträger: 32 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+)<br>InfiniBand-Verbindung: Ja (identisch) | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>VMs der NC v2-Serie mit NVIDIA Tesla P100-GPUs

Bei den virtuellen Computern der NC v2-Serie handelt es sich um eine führende Plattform, die ursprünglich für KI- und Deep Learning-Workloads entwickelt wurde. Sie basieren auf NVIDIA Tesla P100-GPUs und Intel Xeon E5-2690 v4-CPUs (Broadwell) und bieten eine hervorragende Leistung für Deep Learning-Training, mit einer Leistung pro GPU, die im Vergleich zur ursprünglichen NC-Serie ungefähr verdoppelt wurde. Genau wie die NC- und die ND-Serie bietet auch die NC v2-Serie eine Konfiguration mit einem sekundären, RDMA-basierten Netzwerk mit geringer Wartezeit und hohem Durchsatz sowie InfiniBand-Konnektivität, sodass Sie umfangreiche Trainingsaufträge über mehrere GPUs hinweg ausführen können.

| Aktuelle VM-Größe | Zielgröße des virtuellen Computers | Unterschied bei der Spezifikation | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | CPU: Intel Broadwell (identisch)<br>GPU-Anzahl: 1 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Volta (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (identisch)<br>vCPU: 6 (identisch)<br>Arbeitsspeicher-GiB: 112 (identisch)<br>Temporärer Speicher (SSD) GiB: 736 (identisch)<br>Max. Anzahl Datenträger: 12 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | CPU: Intel Broadwell (identisch)<br>GPU-Anzahl: 2 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Volta (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (identisch)<br>vCPU: 12 (identisch)<br>Arbeitsspeicher-GiB: 112 (identisch)<br>Temporärer Speicher (SSD) GiB: 1474 (identisch)<br>Max. Anzahl Datenträger: 24 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | CPU: Intel Broadwell (identisch)<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Volta (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (identisch)<br>vCPU: 24 (identisch)<br>Arbeitsspeicher-GiB: 448 (identisch)<br>Temporärer Speicher (SSD) GiB: 2948 (identisch)<br>Max. Anzahl Datenträger: 32 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | CPU: Intel Broadwell (identisch)<br>GPU-Anzahl: 4 (identisch)<br>GPU-Generation: NVIDIA Pascal vs. Volta (+1 Generation)<br>GPU-Arbeitsspeicher (GiB pro GPU): 16 (identisch)<br>vCPU: 24 (identisch)<br>Arbeitsspeicher-GiB: 448 (identisch)<br>Temporärer Speicher (SSD) GiB: 2948 (identisch)<br>Max. Anzahl Datenträger: 32 (identisch)<br>Beschleunigter Netzwerkbetrieb: Nein (identisch)<br>Storage Premium: Ja (+)<br>InfiniBand-Verbindung: Ja (identisch)| 


## <a name="migration-steps"></a>Schritte bei der Migration

### <a name="general-changes"></a>Allgemeine Änderungen

1.  Wählen Sie eine Reihe und eine Größe für die Migration aus. Nutzen Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) für weitere Informationen.

2.  Erhalten Sie das Kontingent für die Ziel-VM-Serie

3.  Ändern Sie die Größe der aktuellen VM der N\*-Serie auf die Zielgröße Dies ist möglicherweise ein guter Zeitpunkt, um das Betriebssystem Ihres VM-Images zu aktualisieren oder eines der HPC-Images mit vorinstallierten Treibern als Ausgangspunkt zu übernehmen.

    > [!IMPORTANT]
    > Ihr VM-Image wurde möglicherweise mit einer Version der CUDA-Runtime, des NVIDIA-Treibers und (falls zutreffend, nur für RDMA-fähige Größen) der Mellanox OFED-Treiber erstellt, die für Ihre neue GPU-VM-Serie zu alt sind. Sie können diese Elemente aktualisieren, indem Sie [die Anweisungen in der Azure-Dokumentation befolgen](./sizes-gpu.md).

### <a name="breaking-changes"></a>Aktuelle Änderungen

#### <a name="select-target-size-for-migration"></a>Auswählen der Zielgröße für die Migration

Nachdem Sie Ihre aktuelle Nutzungsweise bewertet haben, können Sie entscheiden, welche GPU-VM Sie benötigen. Abhängig von den Workloadanforderungen stehen Ihnen unterschiedliche Optionen zur Verfügung.

> [!NOTE]
> Eine bewährte Methode besteht in der Auswahl einer VM-Größe basierend auf Kosten und Leistung. Die Empfehlungen in diesem Leitfaden basieren auf einem allgemeinen 1:1-Vergleich der Leistungsmetriken und der nächstmöglichen Übereinstimmung in einer anderen VM-Serie. Bevor Sie sich für die richtige Größe entscheiden, können Sie mithilfe des Azure-Preisrechners einen Kostenvergleich durchführen.

> [!IMPORTANT]
> Alle älteren Größen der NC-, NC v2- und ND-Serie sind mit mehreren GPUs verfügbar, darunter z. B. Größen mit 4 GPUs mit und ohne InfiniBand-Verbindung für horizontal skalierte, eng gekoppelte Workloads, die mehr Computeleistung erfordern als eine einzelne 4-GPU-VM bzw. eine einzelne K80-, P40- oder P100-GPU bereitstellen kann. Die vorstehenden Empfehlungen sind zwar leicht umzusetzen, aber die Benutzer der hier genannten VM-Größen sollten dennoch erwägen, ihre Leistungsziele mit leistungsfähigeren NVIDIA V100 GPU-basierten VM-Serien wie der [NC v3-Serie](./ncv3-series.md) und [ND v2-Serie](./ndv2-series.md) zu erreichen. Diese ermöglichen in der Regel das gleiche Maß an Workloadleistung zu geringeren Kosten und mit einer verbesserten Verwaltbarkeit, da sie eine deutlich höhere Leistung pro GPU und pro VM bereitstellen, bevor Konfigurationen mit mehreren GPUs bzw. mit mehreren Knoten erforderlich werden.
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>Erhalten Sie das Kontingent für die Ziel-VM-Familie

Befolgen Sie die Anleitung zum [Anfordern einer Erhöhung des vCPU-Kontingents nach VM-Familie](../azure-portal/supportability/per-vm-quota-requests.md). Wählen Sie die Größe der Ziel-VM aus, die Sie für die Migration ausgewählt haben.

#### <a name="resize-the-current-virtual-machine"></a>Ändern Sie die Größe der aktuellen VM.

Sie können [die Größe des virtuellen Computers ändern](resize-vm.md). 

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Liste der GPU-fähigen VM-Größen finden Sie unter [Übersicht über GPU-beschleunigtes Computing](sizes-gpu.md).