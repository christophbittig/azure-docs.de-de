---
title: Datei einfügen
description: include file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 8b425646e9b416129d951cc78db3d05c26b0e6e8
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484693"
---
```console
robocopy /MT:128 /R:1 /W:1 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Schalter                | Bedeutung |
|-----------------------|---------|
| `/MT:n`               | Hiermit kann Robocopy mit mehreren Threads ausgeführt werden. Der Standardwert für `n` ist 8. Das Maximum beträgt 128 Threads. Beginnen Sie mit einer hohen Threadanzahl für eine erste Ausführung. Eine hohe Threadanzahl hilft, die verfügbare Bandbreite zu sättigen. Nachfolgende Ausführungen von `/MIR` werden zunehmend durch die verfügbare Computeleistung und nicht durch die verfügbare Netzwerkbandbreite beeinflusst. Stimmen Sie bei nachfolgenden Ausführungen den Wert für die Threadanzahl genauer auf die Prozessorkernanzahl und die Threadanzahl pro Kern ab. Überprüfen Sie, ob Kerne für andere Tasks reserviert werden müssen, über die ein Produktionsserver verfügen könnte. |
| `/R:n`                | Dies ist die maximale Anzahl der Wiederholungsversuche für eine Datei, bei der beim ersten Kopierversuch ein Fehler auftritt. Sie können die Geschwindigkeit einer Robocopy-Ausführung verbessern, indem Sie eine maximale Anzahl (`n`) von Wiederholungsversuchen angeben, bevor bei der Datei während der Ausführung dauerhaft ein Kopierfehler auftritt. Diese Option funktioniert am besten, wenn bereits klar ist, dass es weitere Robocopy-Ausführungen gibt. Falls die Datei in der aktuellen Ausführung nicht kopiert werden kann, wird dies beim nächsten Robocopy-Auftrag noch mal versucht. Wenn Sie diesen Ansatz verwenden, können Dateien schließlich erfolgreich kopiert werden, bei denen durch die Nutzung oder aufgrund von Timeoutproblemen zuvor ein Fehler aufgetreten ist. |
| `/W:n`                | Hiermit wird die Zeit angegeben, die Robocopy vor dem Versuch wartet, eine Datei zu kopieren, die bei einem vorherigen Versuch nicht erfolgreich kopiert wurde. `n` entspricht der Anzahl der zwischen Wiederholungsversuchen zu wartenden Sekunden. `/W:n` wird häufig in Verbindung mit `/R:n` verwendet. |
| `/B`                  | Führt Robocopy in dem Modus aus, den auch eine Sicherungsanwendung verwenden würde. Diese Option ermöglicht Robocopy das Verschieben von Dateien, für die der aktuelle Benutzer keine Berechtigungen hat. |
| `/MIR`                | (Quelle im Ziel spiegeln) Hiermit hat Robocopy die Möglichkeit, nur Deltas zwischen der Quelle und dem Ziel zu kopieren. Leere Unterverzeichnisse werden kopiert. Elemente (Dateien oder Ordner), die geändert wurden oder nicht im Ziel vorhanden sind, werden kopiert. Elemente, die im Ziel, aber nicht in der Quelle vorhanden sind, werden aus dem Ziel gelöscht. Wenn Sie diese Option verwenden, passen Sie die Quell- und Zielordnerstrukturen genau an. *Übereinstimmung* bedeutet, dass aus der richtigen Quelle und Ordnerebene in die entsprechende Ordnerebene im Ziel kopiert wird. Nur dann kann ein Kopiervorgang zum „Aufholen“ erfolgreich durchgeführt werden. Wenn die Quelle und das Ziel nicht übereinstimmen, können mithilfe von `/MIR` umfassende Lösch- und wiederholte Kopiervorgänge durchgeführt werden. |
| `/IT`                 | Sorgt dafür, dass die Genauigkeit in bestimmten Spiegelungsszenarien beibehalten wird. </br>Wenn beispielsweise bei einer Datei zwischen zwei Robocopy-Ausführungen eine ACL-Änderung und ein Attributupdate durchgeführt werden, wird diese als „ausgeblendet“ gekennzeichnet. Ohne `/IT` bemerkt Robocopy die ACL-Änderung möglicherweise nicht und führt daher auch keine Übertragung an den Zielspeicherort aus. |
|`/COPY:[copyflags]`    | Dies ist die Genauigkeit der Dateikopie. Standardwert: `/COPY:DAT`. Kopierflags: `D` = Daten, `A` = Attribute, `T` = Zeitstempel, `S` = Sicherheit = NTFS-ACLs, `O` = Besitzerinformationen, `U` = Überwachungsinformationen Überwachungsinformationen können nicht in einer Azure-Dateifreigabe gespeichert werden. |
| `/DCOPY:[copyflags]`  | Dies ist die Genauigkeit für die Kopie von Verzeichnissen. Standardwert: `/DCOPY:DA`. Kopierflags: `D` = Daten, `A` = Attribute, `T` = Zeitstempel. |
| `/NP`                 | Hiermit wird angegeben, dass der Kopierfortschritt für alle Dateien und Ordner nicht angezeigt wird. Das Anzeigen des Fortschritts reduziert die Kopierleistung erheblich. |
| `/NFL`                | Gibt an, dass Dateinamen nicht protokolliert werden. Hiermit wird die Kopierleistung verbessert. |
| `/NDL`                | Gibt an, dass Verzeichnisnamen nicht protokolliert werden. Hiermit wird die Kopierleistung verbessert. |
| `/UNILOG:<file name>` | Hiermit wird der Status als Unicode in die Protokolldatei geschrieben. (Überschreibt das vorhandene Protokoll.) |
| `/L`                  | **Nur für einen Testlauf** </br> Dateien sollen nur aufgelistet werden. Sie werden nicht kopiert, nicht gelöscht und nicht mit einem Zeitstempel versehen. Wird häufig mit `/TEE` für die Konsolenausgabe verwendet. Flags aus dem Beispielskript, z. B. `/NP`, `/NFL` und `/NDL`, müssen möglicherweise entfernt werden, um ordnungsgemäß dokumentierte Testergebnisse zu erzielen. |
| `/LFSM`               | **Nur für Ziele mit mehrstufigem Speicher vorgesehen** </br>Hiermit wird angegeben, dass Robocopy im Modus „Nicht genügend freier Speicherplatz“ ausgeführt wird. Diese Option ist nur für Ziele mit mehrstufigem Speicher nützlich, bei denen möglicherweise der lokale Speicherplatz aufgebraucht ist, bevor die Robocopy-Ausführung fertiggestellt wird. Sie wurde spezifisch für die Verwendung mit einem Ziel hinzugefügt, für das das Cloudtiering der Azure-Dateisynchronisierung aktiviert ist. Die Option kann unabhängig von der Azure-Dateisynchronisierung verwendet werden. In diesem Modus pausiert die Robocopy-Ausführung immer dann, wenn ein Dateikopiervorgang dazu führen würde, dass der freie Speicherplatz des Zielvolumes einen bestimmten Schwellenwert unterschreitet. Dieser Wert kann mit dem `/LFSM:n`-Formular des Flags festgelegt werden. Der Parameter `n` wird im Dualsystem festgelegt: `nKB`, `nMB` oder `nGB`. Wenn `/LFSM` ohne einen expliziten Schwellenwert festgelegt wird, wird der Schwellenwert auf 10 % der Größe des Zielvolumes festgelegt. Der Modus für nicht genügend freien Speicherplatz ist nicht mit `/MT`, `/EFSRAW`, `/B` oder `/ZB` kompatibel. |
| `/Z`                  | **Umsichtige Verwendung** </br>Hiermit werden Dateien im Neustartmodus kopiert. Diese Option wird nur in einer instabilen Netzwerkumgebung empfohlen. Sie reduziert die Kopierleistung aufgrund der zusätzlichen Protokollierung erheblich. |
| `/ZB`                 | **Umsichtige Verwendung** </br>Mit dieser Option wird der Neustartmodus verwendet. Wenn der Zugriff verweigert wird, wird der Sicherungsmodus verwendet. Diese Option reduziert die Kopierleistung aufgrund der Prüfpunkte erheblich. |

> [!IMPORTANT]
> Verwenden Sie einen Windows Server 2019 mit mindestens dem [OS-Update KB5005103](https://support.microsoft.com/topic/august-26-2021-kb5005103-os-build-18363-1766-preview-4e23362c-5e43-4d8f-95e5-9fdade60605f) vom 26. August 2021. Dieses Update enthält wichtige Korrekturen für bestimmte RoboCopy-Szenarien.
