---
title: Ausführen von Skripts in einer Windows- oder Linux-VM in Azure mithilfe der Skriptausführung
description: Dieses Thema bietet einen Überblick über die Ausführung von Skripts innerhalb einer Azure-VM mithilfe des Features „Skriptausführung“.
ms.service: virtual-machines
author: cynthn
ms.author: cynthn
ms.date: 10/27/2021
ms.topic: how-to
ms.reviewer: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0442c97e07e89238bed6b670c1529a862ddb1ab0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478815"
---
# <a name="run-scripts-in-your-vm-by-using-run-command"></a>Ausführen von Skripts in Ihrer VM mithilfe der Skriptausführung

Das Feature „Skriptausführung“ verwendet den VM-Agent, um Skripts innerhalb einer Azure Windows- oder Linux-VM auszuführen. Diese Skripts können für die allgemeine Computer- oder Anwendungsverwaltung verwendet werden. Mit ihrer Hilfe können Sie Zugriffs- und Netzwerkprobleme eines virtuellen Computers schnell diagnostizieren und beheben und den virtuellen Computer wieder in einen funktionierenden Zustand versetzen. Skripts können in die Eigenschaften eingebettet werden, oder es kann auf ein vorab veröffentlichtes Katalogskript verwiesen werden. 


Der ursprüngliche Befehlssatz ist handlungsorientiert. Der aktualisierte Befehlssatz, der sich derzeit in der öffentlichen Vorschau befindet, ist verwaltungsorientiert, umfasst weniger Einschränkungen und ermöglicht es Ihnen, mehrere Skripts auszuführen. In diesem Artikel werden die Unterschiede zwischen den beiden Sätzen von Ausführungsbefehlen erläutert, und Sie können entscheiden, welcher Satz für Ihr Szenario der richtige ist.  

> [!IMPORTANT]
> Die **verwaltete Skriptausführung** ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="when-to-use-action-or-managed-commands"></a>Wann sollten Sie handlungs- oder verwaltungsorientierte Befehle verwenden?

Der ursprüngliche Befehlssatz ist handlungsorientiert. Sie sollten diesen Befehlssatz für Situationen verwenden, in denen Sie eines der folgenden Skripts ausführen müssen:
- Ein kleines Skript zum Abrufen von Inhalt aus einer VM 
- Ein Skript zum Konfigurieren einer VM (Festlegen von Registrierungsschlüsseln, Ändern der Konfiguration) 
- Ein einmaliges Skript für die Diagnose

Siehe [Aktionsbezogene Ausführungsbefehle für Linux](./linux/run-command.md) und [Aktionsbezogene Ausführungsbefehle für Windows](./windows/run-command.md) für verfügbare Aktionsbefehle und Anweisungen zu deren Anwendung. 

Der aktualisierte Befehlssatz, der sich derzeit in der öffentlichen Vorschau befindet, ist auf die Verwaltung ausgerichtet. Ziehen Sie die Verwendung von verwalteten Ausführungsbefehlen in Betracht, wenn Ihre Anforderungen den folgenden Beispielen entsprechen:
- Ein Skript muss im Rahmen der VM-Bereitstellung ausgeführt werden 
- Es ist eine wiederholte Skriptausführung erforderlich 
- Mehrere Skripts müssen nacheinander ausgeführt werden 
- Bootstrap einer VM durch Ausführen von Installationsskripts 
- Veröffentlichen eines benutzerdefinierten Skripts, das freigegeben und wiederverwendet werden soll 

Informationen zur Verwendung finden Sie unter [Verwaltete Skriptausführung für Linux](./linux/run-command-managed.md) und [Verwaltete Skriptausführung für Windows](./windows/run-command-managed.md).


## <a name="compare-feature-support"></a>Vergleich der Featureunterstützung

| Featureunterstützung  | Handlungsorientierter RunCommand  | Verwalteter RunCommand  |
|---|---|---|
| ARM-Vorlage  | Nein, es handelt sich um eine POST-Aktion  | Ja, es handelt sich um einen Ressourcentyp  |
| Zeitintensiv  | Grenzwert von 90 Minuten  | Vom Kunden angegebenes Timeout  |
| Ausführungskonto  | Systemkonto/Root  | Vom Kunden angegebener Benutzer  |
| Mehrere Ausführungsbefehle  | Nur einer aktiv  | Mehrere, parallel oder nacheinander  |
| Große Ausgabe  | Begrenzt auf 4K (im Statusblob)  | Upload in das Anfügeblob des Kunden  |
| Fortschrittsnachverfolgung  | Meldet nur den Endstatus  | Meldet den Status und die letzte 4K-Ausgabe während der Ausführung  |
| Asynchrone Ausführung  | Zielzustand/Bereitstellung wartet auf den Abschluss des Skripts  | Vom Kunden angegebenes asynchrones Flag, wenn die Bereitstellung auf das Skript wartet  |
| Unterstützung für VM-Skalierungsgruppe  | Nur auf VM-Instanz  | Unterstützung das VM-Skalierungsgruppenmodell und das Aufskalieren  |
| SAS-Generierung  | Keine Blobunterstützung  | Automatisiert: CRP generiert SAS für Kundenblobs und verwaltet diese  |
| Katalog (benutzerdefinierte Befehle)  | Nur integrierte commandIds  | Kunde kann Skripts veröffentlichen und freigeben  |


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg finden Sie unter [Verwaltete Skriptausführung für Linux](./linux/run-command-managed.md) und [Verwaltete Skriptausführung für Windows](./windows/run-command-managed.md). 