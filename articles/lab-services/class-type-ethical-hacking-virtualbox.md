---
title: Einrichten eines Labs für ethisches Hacken mit Azure Lab Services in VirtualBox | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure Lab Services ein Lab einrichten, um ethisches Hacken mit VirtualBox zu vermitteln.
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: ef9ac58490ea4e917f76d1b0ee4874411dd7b4e2
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176926"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>Einrichten eines Labs für Kurse für ethisches Hacken mit VirtualBox

In diesem Artikel erfahren Sie, wie Sie einen Kurs einrichten, der sich auf die forensische Seite des ethischen Hackens konzentriert. Bei Penetrationstests, eine von der Community für ethisches Hacken verwendete Vorgehensweise, versucht eine Person, auf das System oder Netzwerk zuzugreifen, um Schwachstellen zu demonstrieren, die ein böswilliger Angreifer ausnutzen könnte.

In einem Kurs für ethisches Hacken erlernen die Kursteilnehmer moderne Techniken zum Schützen vor Schwachstellen kennen. Jeder Kursteilnehmer erhält einen virtuellen Hostcomputer mit drei geschachtelten virtuellen Computern: zwei virtuellen Computern mit einem [Seed](https://seedsecuritylabs.org/lab_env.html)-Image und einen anderen Computer mit einem [Kali Linux](https://www.kali.org/)-Image. Der virtuelle Seed-Computer wird angegriffen, und der virtuelle Kali-Computer stellt die Tools zur Verfügung, die zum Ausführen forensischer Aufgaben erforderlich sind.

Dieser Artikel besteht aus zwei Hauptteilen. Im ersten Abschnitt wird erläutert, wie das Kurs-Lab erstellt wird. Im zweiten Abschnitt wird erläutert, wie der Vorlagencomputer mit aktivierter geschachtelter Virtualisierung sowie mit den benötigten Tools und Images erstellt wird. In diesem Fall handelt es sich um zwei Seed-Images und ein Kali Linux-Image auf einem Computer, auf dem [VirtualBox](https://www.virtualbox.org/) zum Hosten der Images aktiviert ist.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie als Einstieg ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie entweder ein neues Lab-Konto in Azure Lab Services erstellen oder ein vorhandenes Konto verwenden. Sehen Sie sich das folgende Tutorial zum Erstellen eines neuen Lab-Kontos an: [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).

Absolvieren Sie [dieses Tutorial](tutorial-setup-classroom-lab.md), um ein neues Lab mit den folgenden Einstellungen zu erstellen:

| Größe des virtuellen Computers | Image |
| -------------------- | ----- |
| Mittel (geschachtelte Virtualisierung) | Windows Server 2019 Datacenter |
| Mittel (geschachtelte Virtualisierung) | Windows 10 |

## <a name="template-machine"></a>Vorlagencomputer

Nachdem der Vorlagencomputer erstellt wurde, starten Sie den Computer, und stellen Sie eine Verbindung mit ihm her, um die folgenden drei Hauptaufgaben auszuführen.

1. Richten Sie den Computer für die Verwendung von [VirtualBox](https://www.virtualbox.org/) für geschachtelte virtuelle Computer ein.
2. Richten Sie das [Kali](https://www.kali.org/) Linux-Image ein. Kali ist eine Linux-Distribution, die Tools für Penetrationstests und Sicherheitsüberwachung umfasst.
3. Richten Sie das Seed-Image ein. In diesem Beispiel wird das [Seed](https://seedsecuritylabs.org/lab_env.html)-Image verwendet. Dieses Image wird speziell für Sicherheitsschulungen erstellt.

Im restlichen Teil dieses Artikels werden die manuellen Schritte zum Durchführen der obigen Aufgaben beschrieben.

### <a name="installing-virtualbox"></a>Installieren von VirtualBox

1. Laden Sie die [VirtualBox-Plattformpakete](https://www.virtualbox.org/wiki/Downloads) herunter, indem Sie die Windows-Hostsoption auswählen.
2. Führen Sie die ausführbare Installationsdatei aus, und verwenden Sie die Standardoptionen, um die Installation abzuschließen.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Einrichten eines geschachtelten virtuellen Computers mit dem Kali Linux-Image

Kali ist eine Linux-Distribution, die Tools für Penetrationstests und Sicherheitsüberwachung umfasst.

1. Laden Sie das OVA-Image von [Kali Linux VM VirtualBox-Images](https://www.kali.org/get-kali/#kali-virtual-machines) herunter.  Wir empfehlen die 32-Bit-Version, beim Laden der 64-Bit-Version treten Fehler auf.  Merken Sie sich die Angaben zum Standardbenutzernamen und zum zugehörigen Kennwort auf der Downloadseite.
2. Öffnen Sie VirtualBox Manager, und [importieren Sie das OVA-Image](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html).  Der Kali-Lizenzvertrag muss überprüft und akzeptiert werden, um fortzufahren.

>[!Note]
>- Der VirtualBox-Standard-RAM für die Kali-VM beträgt 2 GB (2.048). Es wird empfohlen, den RAM je nach Ihren Anforderungen auf mindestens 4 GB (4.096) zu erhöhen.  Dies kann von den Kursteilnehmern auf ihren VMs geändert werden.  Wenn Sie die RAM-Größe in VirtualBox ändern, ändert sich die VM-Größe des Labs nicht.
>- Standardmäßig ist die Festplatte auf ein Limit von 80 GB festgelegt, jedoch erfolgt eine dynamische Zuordnung.  Lab-Dienst-Computer sind auf 128 GB Festplattenspeicher beschränkt. Achten Sie daher darauf, diese Datenträgergröße nicht zu überschreiten.
>- Für das Kali-Image ist USB 2.0 aktiviert, wofür das [Oracle VM VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads) erforderlich ist, oder legen Sie auf der Registerkarte „USB“ für den USB-Controller „1.0“ fest.

### <a name="setup-seed-lab-images"></a>Einrichten von Seed-Lab-Images

1. Laden Sie das [SEED-Labs-VM-Image](https://seedsecuritylabs.org/labsetup.html) herunter, und extrahieren Sie es.
2. Befolgen Sie die Anweisungen zum [Erstellen eines virtuellen Computers in VirtualBox](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md).
   Wenn Sie mehrere SEED-VMs benötigen, erstellen Sie Kopien der ISO-Datei für jeden Computer. Die Verwendung derselben ISO-Datei für verschiedene Computer funktioniert nicht ordnungsgemäß.

>[!IMPORTANT] 
>Stellen Sie sicher, dass alle geschachtelten virtuellen Computer ausgeschaltet sind, bevor Sie die Vorlage veröffentlichen.  Wenn sie eingeschaltet bleiben, treten unerwartete Nebeneffekte einschließlich der Schädigung der virtuellen Computer auf.

## <a name="cost"></a>Kosten  

Das folgende Beispiel dient der Einschätzung der Lab-Kosten:

Für einen Kurs mit 25 Teilnehmern, 20 planmäßigen Kursstunden und 10 Stunden Hausaufgaben bzw. Arbeitsaufträgen entstünden folgende Kosten für das Lab:

25 Kursteilnehmer · (20 + 10) Stunden · 55 Lab-Einheiten · 0,01 USD pro Stunde = 412,50 USD

>[!IMPORTANT]
>Diese Kostenschätzung dient ausschließlich zu Beispielzwecken. Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden die Schritte zum Erstellen eines Labs für einen Kurs zum ethischen Hacken erläutert. Er enthält Schritte zum Einrichten der geschachtelten Virtualisierung, um zwei virtuelle Computer auf dem virtuellen Hostcomputer für Penetrationstests zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Schritte sind die gleichen für sämtliche Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users).
