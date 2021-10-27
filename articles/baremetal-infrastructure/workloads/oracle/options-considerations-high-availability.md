---
title: Optionen für Oracle auf BareMetal-Infrastrukturservern
description: Erfahren Sie mehr über die Optionen und Überlegungen für Oracle auf BareMetal-Infrastrukturservern.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/12/2021
ms.openlocfilehash: 0c4442910ed40ece8147bca919039a7c4564a4f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999585"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Optionen für Oracle auf BareMetal-Infrastrukturservern

In diesem Artikel werden Optionen und Empfehlungen beschrieben, mit denen Sie bei der Ausführung von Oracle auf BareMetal-Infrastrukturservern den maximalen Schutz und die höchste Leistung erzielen. 

## <a name="data-guard-protection-modes"></a>Data Guard-Schutzmodi

Data Guard bietet einen Schutz, den Ihnen die ausschließliche Verwendung von Oracle Real Applications Cluster (RAC), logischer Replikation (z. B. GoldenGate) und speicherbasierter Replikation nicht bieten kann. 

| Schutzmodus | Beschreibung |
| --- | --- |
| **Maximale Leistung** | Dies ist der standardmäßige Schutzmodus. Er bietet die höchste Schutzebene, ohne die Leistung der primären Datenbank zu beeinträchtigen. Daten gelten als committet, sobald sie in den Datenstrom für Wiederholungsvorgänge der primären Datenbank geschrieben wurden. Anschließend werden sie asynchron in die Standbydatenbank repliziert. Normalerweise empfängt die Standbydatenbank die Daten innerhalb von Sekunden. Dafür gibt es jedoch keine Garantie. Dieser Modus ist (zusammen mit der Verzögerungsüberwachung) in der Regel ausreichend für geschäftliche Anforderungen und erfordert keine latenzarme Netzwerkkonnektivität zwischen dem primären Standort und dem Standbystandort.<br /><br />Er bietet die beste operative Persistenz, garantiert jedoch nicht, dass keine Datenverluste auftreten.   |
| **Maximale Verfügbarkeit** | Dieser Modus bietet die höchste Schutzebene, ohne die Verfügbarkeit der primären Datenbank zu beeinträchtigen. Daten gelten in der primären Datenbank erst dann als committet, wenn sie auch in mindestens einer Standbydatenbank committet wurden. Wenn die primäre Datenbank die zu wiederholenden Änderungen nicht in mindestens eine Standbydatenbank schreiben kann, wechselt sie wieder in den Modus „Maximale Leistung“, anstatt nicht mehr verfügbar zu sein. <br /><br />Dadurch kann der Dienst weiter ausgeführt werden, wenn der Standbystandort nicht verfügbar ist. Wenn nur ein Standort verfügbar ist, wird nur eine Kopie der Daten beibehalten, bis der zweite Standort online ist und die Synchronisierung wieder erfolgt. |
| **Maximaler Schutz** | Dieser Modus bietet eine ähnliche Schutzebene wie der Modus „Maximale Verfügbarkeit“. Die primäre Datenbank wird mit dem zusätzlichen Feature heruntergefahren, wenn sie die zu wiederholenden Änderungen nicht in mindestens eine Standbydatenbank schreiben kann. Dadurch wird sichergestellt, dass keine Datenverluste auftreten können, die Verfügbarkeit wird jedoch beeinträchtigt. |

>[!IMPORTANT]
>Wenn Sie eine Recovery Point Objective (RPO) von Null benötigen, empfehlen wir, die Konfiguration „Maximale Verfügbarkeit“ zu verwenden. Dadurch kann die RPO auch dann garantiert werden, wenn mehrere Fehler auftreten, beispielsweise bei einem Netzwerkausfall der primären Datenbank und dem anschließenden Ausfall der primären Datenbank während dieses Netzwerkausfalls.

### <a name="data-guard-deployment-patterns"></a>Data Guard-Bereitstellungsmuster

Oracle ermöglicht Ihnen die Konfiguration mehrerer Ziele für die Generierung von Wiederholungsvorgängen und somit die Verwendung mehrerer Standbydatenbanken. Die folgende Abbildung zeigt die gängigste Konfiguration: eine einzelne Standbydatenbank in einer anderen Region.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagramm der Data Guard-Standardbereitstellung von Oracle":::

Für eine Standardbereitstellung wird Data Guard im Modus „Maximale Leistung“ konfiguriert. Diese Konfiguration bietet über den asynchronen Datentransport für Wiederholungsvorgänge eine Datenreplikation, die nahezu in Echtzeit erfolgt. Die Standbydatenbank muss nicht innerhalb einer RAC-Bereitstellung ausgeführt werden. Es wird jedoch empfohlen, dass die Standbydatenbank die Leistungsanforderungen des primären Standorts erfüllt.

Für Umgebungen, in denen strenge Anforderungen an die Uptime gelten oder die eine RPO von 0 (null) erfordern, empfehlen wir eine Bereitstellung wie die in der folgenden Abbildung. Die Konfiguration der maximalen Verfügbarkeit besteht aus den folgenden Elementen:
- Eine lokale Standbydatenbank, die die Wiederholung im synchronen Modus anträgt
- Eine zweite Standbydatenbank, die in einer Remoteregion ausgeführt wird

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagramm der Data Guard-Bereitstellung „Maximale Verfügbarkeit“":::

Sie können eine lokale Standbydatenbank erstellen, wenn die Anwendungsleistung durch die Ausführung der Datenbank und Anwendungsserver in separaten Regionen beeinträchtigt wird. Eine lokale Standbydatenbank wird verwendet, wenn eine geplante oder ungeplante Wartung für den primären Cluster erforderlich ist. Da sich diese Datenbanken in derselben Region befinden, können Sie sie mit synchroner Replikation ausführen und dadurch sicherstellen, dass keine Daten verloren gehen.

### <a name="data-guard-configuration-considerations"></a>Überlegungen zur Data Guard-Konfiguration

Data Guard Broker sollte implementiert werden, da dieses Tool die Implementierung einer Data Guard-Konfiguration vereinfacht und sicherstellt, dass bewährte Methoden eingehalten werden. Es bietet Funktionen zur Leistungsüberwachung und vereinfacht die Prozeduren für Umstellung, Failover und erneute Instanziierung erheblich.

Data Guard ermöglicht die Ausführung eines Observer-Prozesses, der alle Datenbanken in einer Data Guard-Konfiguration überwacht, um die Verfügbarkeit der Datenbanken zu ermitteln. Wenn eine primäre Datenbank ausfällt, kann der Data Guard-Observer automatisch ein Failover zu einer Standbydatenbank in der Konfiguration starten. Sie können den Data Guard-Observer je nach Anzahl physischer Standorte mit mehreren Observer-Instanzen implementieren (maximal drei). 

Dieser Observer sollte sich in der Infrastruktur befinden, die die Anwendungsebene unterstützt. Der primäre Observer sollte sich immer an dem physischen Standort befinden, an dem sich die primäre Datenbank nicht befindet. Bei der Automatisierung von Failovervorgängen, die von einem Data Guard-Observer ausgelöst werden, ist besondere Vorsicht geboten. Ihre Anwendungen müssen dafür konzipiert und getestet werden, bei der Ausführung der Datenbank an einem separaten Standort eine akzeptable Leistung zu bieten.

Wenn die Anwendung nur lokal ausgeführt werden kann, muss das Failover zum sekundären Standort manuell erfolgen. In Umgebungen, die eine hohe Verfügbarkeit erfordern (99,99 % oder 99,999 % Uptime), sollten wie in der obigen Abbildung dargestellt sowohl eine lokale als auch eine Remotestandbydatenbank verwendet werden. In diesen Fällen wird der Parameter „FastStartFailoverTarget“ nur auf die lokale Standbydatenbank festgelegt.

Für alle Anwendungen, die standortübergreifenden Anwendungs-/Datenbankzugriff unterstützen, wird „FastStartFailoverTarget“ auf alle Standbydatenbanken in der Data Guard-Konfiguration festgelegt.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) ist eine Obermenge grundlegender Data Guard-Funktionen, die in Oracle Database Enterprise Edition enthalten sind. ADG bietet die folgenden zusätzlichen Features, die in der gesamten Oracle Exadata-Bereitstellung verwendet werden:

- Einzigartige Erkennung von Beschädigungen und automatische Reparatur
- Schnelles Failover zum synchronisierten Produktionsreplikat (manuell oder automatisch)
- Auslagern der Produktionsworkload in eine synchronisierte schreibgeschützt geöffnete Standbydatenbank
- Parallele Datenbankupgrades und Standbydatenbank Erstes Patchen mit der physischen Standbydatenbank
- Auslagern inkrementeller Sicherungen in die Standbydatenbank
- Datenwiederherstellung ohne Datenverluste unabhängig von der Entfernung ohne Beeinträchtigung der Leistung

Einen Überblick über die oben genannten Features, die in der folgenden Abbildung dargestellt sind, finden Sie im Whitepaper unter [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf).

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagramm mit einer Übersicht der Active Data Guard-Features von Oracle":::

## <a name="backup-recommendations"></a>Sicherungsempfehlungen

Stellen Sie sicher, dass Ihre Datenbanken gesichert werden. Verwenden Sie die Wiederherstellungsfunktionen, um eine Datenbank auf demselben oder einem anderen System oder um Datenbankdateien wiederherzustellen.

Um Oracle Database Appliance-Datenbanken vor Datenverlusten zu schützen, ist eine Sicherungs- und Wiederherstellungsstrategie unerlässlich. Beispielsweise kann ein physisches Problem mit einem Datenträger, das bei einem Lese- oder Schreibvorgang in einer zum Ausführen der Datenbank erforderlichen Datenträgerdatei einen Fehler verursacht, zu einem Datenverlust führen. Datenverluste können auch durch Benutzerfehler verursacht werden. Das Sicherungsfeature ermöglicht die **Zeitpunktwiederherstellung (Point in Time Restore, PITR) der Datenbank sowie die Wiederherstellung von Systemänderungsnummern (System Change Number, SCN) und des letzten Wiederherstellungspunkts.** Sie können über die Benutzeroberfläche des Browsers oder die Befehlszeilenschnittstelle eine Sicherungsrichtlinie erstellen.

Folgende Sicherungsoptionen sind verfügbar:

- Sicherung auf einem NFS-Speichervolume (Bereich für schnelle Wiederherstellung [Fast Recovery Area, FRA], /u98)
- Verwendung von Azure NetApp Files SnapCenter – Momentaufnahme

Folgende Prozesse sind zu beachten:

- Manuelle oder automatische Sicherungen.
- Automatische Sicherungen werden in NFS-Speichervolumes geschrieben (z. B. /u98).
- Sicherungen werden zwischen 0:00 und 6:00 Uhr in der Zeitzone des Datenbanksystems ausgeführt.
- Die aktuellen Aufbewahrungszeiträume sind 7, 15, 30, 45 und 60 Tage.

- Wiederherstellen einer Datenbank aus einer im Objektspeicher gespeicherten Sicherung:
  - Letzter bekannter fehlerfreier Zustand mit dem geringstmöglichen Datenverlust
  - Unter Verwendung des angegebenen Zeitstempels
  - Unter Verwendung der angegebenen SCN
  - BackupReport: _Verwendet anstelle der angegebenen SCN die SCN aus dem Sicherungsbericht_

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagramm, das die Sicherung der Kundendatenbank im Bereich für schnelle Wiederherstellung (/u98) und einem nicht für die schnelle Wiederherstellung vorgesehenen Bereich (/u95) zeigt":::

### <a name="backup-policy"></a>Sicherungsrichtlinie

Die Sicherungsrichtlinie definiert die Sicherungsdetails. Beim Erstellen einer Sicherungsrichtlinie legen Sie das Ziel für Datenbanksicherungen, d. h. den Bereich für schnelle Wiederherstellung (NFS-Speicherort), und das Wiederherstellungsfenster fest.

Standardmäßig wird der Komprimierungsalgorithmus BASIC verwendet. Bei Verwendung der Komprimierungsalgorithmen LOW, MEDIUM oder HIGH für die Datenträger- oder NFS-Sicherungsrichtlinie müssen verschiedene Überlegungen im Zusammenhang mit der Lizenz beachtet werden.

### <a name="backup-levels"></a>Sicherungsebenen

Geben Sie beim Erstellen einer Sicherung die Sicherungsebene an.

- Ebene 0 – Vollständig
- Ebene 1 – Inkrementell
- LongTerm/Archivelog: Verwenden Sie mit Ausnahme der Aufbewahrungsrichtlinie für Sicherungen einen Speicherort, der kein Bereich für schnelle Wiederherstellung ist (z. B. /u95).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihre Oracle-Datenbank im Fall eines Fehlers wiederherstellen:

> [!div class="nextstepaction"]
> [Wiederherstellen Ihrer Oracle-Datenbank auf der Azure BareMetal-Infrastruktur](oracle-high-availability-recovery.md)
