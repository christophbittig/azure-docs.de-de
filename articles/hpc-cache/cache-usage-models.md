---
title: Azure HPC Cache-Verwendungsmodelle
description: Beschreibt die verschiedenen Cache-Verwendungsmodelle und wie Sie zwischen ihnen wählen können, um Einstellungen für schreibgeschütztes oder Lese/Schreib-Zwischenspeichern festzulegen und andere Einstellungen für das Zwischenspeichern zu steuern
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: femila
ms.openlocfilehash: 84964e8a5f188d03fb9e5bcb98d4466610732c75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015236"
---
<!-- filename is referenced from GUI in aka.ms/hpc-cache-usagemodel -->

# <a name="understand-cache-usage-models"></a>Grundlegendes zu Cache-Verwendungsmodellen

Mit Cache-Verwendungsmodellen können Sie anpassen, wie Ihr Azure HPC Cache Dateien speichert, um Ihren Workflow zu beschleunigen.

## <a name="basic-file-caching-concepts"></a>Grundlegende Konzepte zum Zwischenspeichern von Dateien

Durch das Zwischenspeichern von Dateien werden Clientanforderungen von Azure HPC Cache beschleunigt. Dabei werden die folgenden grundlegenden Methoden verwendet:

* **Zwischenspeichern von Lesevorgängen**: Azure HPC Cache speichert eine Kopie von Dateien, die Clients vom Speichersystem anfordern. Wenn ein Client das nächste Mal dieselbe Datei anfordert, kann HPC Cache die Version im Cache bereitstellen, statt die Datei erneut aus dem Back-End-Speichersystem abrufen zu müssen.

* **Zwischenspeichern von Schreibvorgängen**: Optional kann Azure HPC Cache eine Kopie der von den Clientcomputern gesendeten geänderten Dateien speichern. Wenn mehrere Clients in einem kurzen Zeitraum Änderungen an derselben Datei vornehmen, können alle Änderungen im Cache erfasst werden, sodass nicht jede Änderung einzeln in das Back-End-Speichersystem geschrieben werden muss.

  Nach einer festgelegten Zeitspanne ohne Änderungen verschiebt der Cache die Datei in das langfristige Speichersystem.

  Wenn das Zwischenspeichern von Schreibvorgängen deaktiviert ist, speichert der Cache die geänderte Datei nicht, sondern schreibt sie sofort in das Back-End-Speichersystem.

* **Zurückschreibverzögerung**: Für einen Cache mit aktiviertem Zwischenspeichern von Schreibvorgängen ist die Zurückschreibverzögerung die Zeitspanne, die der Cache auf zusätzliche Dateiänderungen wartet, bevor die Datei in das Back-End-Speichersystem kopiert wird.

* **Back-End-Überprüfung**: Die Einstellung für die Back-End-Überprüfung bestimmt, wie häufig der Cache seine lokale Kopie einer Datei mit der Remoteversion auf dem Back-End-Speichersystem vergleicht. Wenn die Back-End-Kopie aktueller als die zwischengespeicherte Kopie ist, ruft der Cache die Remotekopie ab und speichert sie für zukünftige Anforderungen.

  Die Einstellung der Back-End-Überprüfung gibt an, wann der Cache Dateien *automatisch* mit Quelldateien im Remotespeicher vergleicht. Sie können jedoch erzwingen, dass Azure HPC Cache Dateien vergleicht, indem Sie einen Verzeichnisvorgang ausführen, der eine readdirplus-Anforderung enthält. Readdirplus ist eine standardmäßige NFS-API (erweiterter Lesevorgang), die Verzeichnismetadaten zurückgibt. Durch diesen Vorgang wird erzwungen, dass der Cache Dateien vergleicht und aktualisiert.

Die in Azure HPC Cache integrierten Verwendungsmodelle haben unterschiedliche Werte für diese Einstellungen, sodass Sie die optimale Kombination für Ihre Situation auswählen können.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Auswahl des richtigen Verwendungsmodells für Ihren Workflow

Sie müssen für jedes von Ihnen verwendete NFS-Protokollspeicherziel ein Nutzungsmodell auswählen. Azure Blob Storage-Ziele verfügen über ein integriertes Verwendungsmodell, das nicht angepasst werden kann.

Mit den HPC Cache-Nutzungsmodellen können Sie zwischen kurzen Reaktionszeiten und dem Risiko veralteter Daten abwägen. Wenn Sie die Geschwindigkeit von Dateilesevorgängen optimieren möchten, spielt es für Sie möglicherweise keine Rolle, ob die Dateien im Cache mit den Back-End-Dateien abgeglichen werden. Wenn Sie jedoch sicherstellen möchten, dass Ihre Dateien immer auf dem neuesten Stand sind und den Dateien im Remotespeicher entsprechen, wählen Sie ein Modell aus, bei dem regelmäßig eine Überprüfung durchgeführt wird.

Die Optionen für das Verwendungsmodell sind:

* **Leseintensive, unregelmäßige Schreibvorgänge**: Verwenden Sie diese Option, wenn Sie den Lesezugriff auf Dateien beschleunigen möchten, die statisch sind oder selten geändert werden.

  Mit dieser Option werden Clientlesevorgänge zwischengespeichert, jedoch keine Schreibvorgänge. Schreibvorgänge werden sofort an den Back-End-Speicher weitergeleitet.
  
  Im Cache gespeicherte Dateien werden nicht automatisch mit den Dateien auf dem NFS-Speichervolume verglichen. (Lesen Sie die obige Beschreibung der Back-End-Überprüfung, um zu erfahren, wie Sie diese manuell vergleichen.)

  Verwenden Sie diese Option nicht, wenn das Risiko besteht, dass eine Datei direkt im Speichersystem geändert werden kann, ohne sie zuvor in den Cache zu schreiben. In diesem Fall ist die zwischengespeicherte Version der Datei nicht mit der Back-End-Datei synchron.

* **Mehr als 15 % Schreibvorgänge**: Diese Option beschleunigt die Lese-und Schreibleistung. Wenn diese Option verwendet wird, müssen alle Clients über den Azure HPC-Cache auf Dateien zugreifen, anstatt den Back-End-Speicher direkt einzubinden. Die zwischengespeicherten Dateien enthalten aktuelle Änderungen, die noch nicht in das Back-End kopiert wurden.

  Bei diesem Nutzungsmodell werden Dateien im Cache nur alle acht Stunden mit den Dateien im Back-End-Speicher verglichen. Es wird davon ausgegangen, dass die zwischengespeicherte Version der Datei aktueller ist. Eine geänderte Datei im Cache wird in das Back-End-Speichersystem geschrieben, nachdem sie sich eine Stunde lang ohne zusätzliche Änderungen im Cache befunden hat.

* **Clients umgehen den Cache und schreiben in das NFS-Ziel**: Wählen Sie diese Option aus, wenn Clients in Ihrem Workflow Daten direkt in das Speichersystem schreiben, ohne zuvor in den Cache zu schreiben, oder wenn Sie die Datenkonsistenz verbessern möchten. Dateien, die von Clients angefordert werden, werden zwischengespeichert (Lesevorgänge), aber alle Änderungen an diesen Dateien durch den Client (Schreibvorgänge) werden nicht zwischengespeichert. Sie werden direkt an das Back-End-Speichersystem weitergeleitet.

  Bei diesem Nutzungsmodell werden die Dateien im Cache häufig anhand der Back-End-Versionen auf Updates überprüft – alle 30 Sekunden. Bei dieser Überprüfung können Dateien außerhalb des Caches geändert werden, während die Datenkonsistenz gewahrt bleibt.

  > [!TIP]
  > Diese ersten drei grundlegenden Verwendungsmodelle können verwendet werden, um die Mehrzahl der Azure HPC Cache-Workflows zu verarbeiten. Die nächsten Optionen gelten für weniger häufige Szenarien.

* **Mehr als 15 % Schreibvorgänge, der Sicherungsserver wird alle 30 Sekunden auf Änderungen überprüft** und **Mehr als 15 % Schreibvorgänge, der Sicherungsserver wird alle 60 Sekunden auf Änderungen überprüft**: Diese Optionen sind für Workflows konzipiert, in denen Sie sowohl Lese- als auch Schreibvorgänge beschleunigen möchten, aber es besteht die Möglichkeit, dass ein anderer Benutzer direkt in das Back-End-Speichersystem schreibt. Wenn z. B. mehrere Gruppen von Clients von unterschiedlichen Standorten aus an denselben Dateien arbeiten, sind diese Verwendungsmodelle möglicherweise sinnvoll, um einen Ausgleich zwischen Bedarf an schnellem Dateizugriff und geringer Toleranz für veraltete Inhalte aus der Quelle zu schaffen.

* **Mehr als 15 % Schreibvorgänge, Zurückschreiben auf den Server alle 30 Sekunden**: Diese Option ist für das Szenario konzipiert, in dem mehrere Clients dieselben Dateien aktiv ändern, oder wenn einige Clients direkt auf den Back-End-Speicher zugreifen, anstatt den Cache einzubinden.

  Die häufigen Back-End-Schreibvorgänge wirken sich auf die Cacheleistung aus. Daher sollten Sie das Verwendungsmodell **Größer als 15 % der Schreibvorgänge** verwenden, wenn ein niedriges Risiko für einen Dateikonflikt vorliegt, wenn Sie z. B. wissen, dass verschiedene Clients in verschiedenen Bereichen derselben Dateigruppe arbeiten.

* **Viele Lesezugriffe, Überprüfung des Sicherungsservers alle 3 Stunden**: Bei dieser Option werden schnelle Lesevorgänge auf Clientseite priorisiert. Außerdem werden zwischengespeicherte Dateien regelmäßig aus dem Back-End-Speichersystem aktualisiert, anders als im Verwendungsmodell **Umfangreiche, seltene Schreibvorgänge lesen**.

In dieser Tabelle werden die Unterschiede im Nutzungsmodell zusammengefasst:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Wenn Sie Fragen zum optimalen Verwendungsmodell für Ihren Azure HPC Cache-Workflow haben, wenden Sie sich an Ihren Azure-Vertreter, oder öffnen Sie eine Supportanfrage, um Hilfe zu erhalten.

## <a name="change-usage-models"></a>Ändern von Nutzungsmodellen

Sie können Nutzungsmodelle ändern, indem Sie das Speicherziel bearbeiten. Einige Änderungen sind jedoch nicht zulässig, da dadurch ein geringes Risiko von Dateiversionskonflikten entsteht.

Eine Änderung **in das** oder **aus dem** Modell **Viele Lesevorgänge, seltene Schreibvorgänge** ist nicht möglich. Um ein Speicherziel in dieses Nutzungsmodell zu ändern oder um es von diesem in ein anderes Nutzungsmodell zu ändern, müssen Sie das ursprüngliche Speicherziel löschen und ein neues erstellen.

Diese Einschränkung gilt auch für das Nutzungsmodell **Viele Lesezugriffe, Überprüfung des Sicherungsservers alle 3 Stunden**, das nicht so häufig verwendet wird. Außerdem können Sie zwischen den beiden Nutzungsmodellen des Typs „Viele Lesezugriff“ umschalten, aber nicht auf einen anderen Nutzungsmodellstil umsteigen.

Diese Einschränkung ist aufgrund der Art und Weise erforderlich, in der verschiedene Nutzungsmodelle Anforderungen des Netzwerksperrungs-Managers (Network Lock Manager, NLM) verarbeiten. Die Azure HPC Cache-Instanz befindet sich zwischen Clients und Back-End-Speichersystem. In der Regel übergibt der Cache NLM-Anforderungen an das Back-End-Speichersystem, aber in einigen Fällen bestätigt der Cache selbst die NLM-Anforderung und gibt einen Wert an den Client zurück. In Azure HPC Cache ist dies nur bei Verwendung des Nutzungsmodells **Viele Lesevorgänge, seltene Schreibvorgänge** oder **Viele Lesezugriffe, Überprüfung des Sicherungsservers alle 3 Stunden** der Fall (oder mit einem standardmäßigen Blobspeicherziel, für das keine konfigurierbaren Nutzungsmodelle verfügbar sind).

Wenn Sie zwischen **Viele Lesevorgänge, seltene Schreibvorgänge** und einem anderen Nutzungsmodell wechseln, gibt es keine Möglichkeit, den aktuellen NLM-Status vom Cache zum Speichersystem zu übertragen oder umgekehrt. Daher ist der Sperrstatus des Clients ungenau.

> [!NOTE]
> ADLS-NFS bietet keine Unterstützung für NLM. Sie müssen NLM deaktivieren, wenn Clients den Cluster einbinden, um auf ein ADLS-NFS-Speicherziel zuzugreifen.
>
> Verwenden Sie im Befehl ``mount`` die Option ``-o nolock``. Konsultieren Sie die Dokumentation zur Einbindung Ihres Clientbetriebssystems (man 5 nfs), um mehr über das genaue Verhalten der Option ``nolock`` für Ihre Clients zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md) zu Ihrer Azure HPC Cache-Instanz.
