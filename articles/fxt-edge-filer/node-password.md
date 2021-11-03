---
title: 'Tutorial: Initialisieren der Hardware – Azure FXT Edge Filer'
description: Hier erfahren Sie, wie Sie eine Verbindung mit dem Hardwareknoten herstellen und ein anfängliches Kennwort für Azure FXT Edge Filer-Knoten festlegen.
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: f78c7e60210d71aa94a20b4ce198a8d9d154a06f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033017"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: Festlegen von Hardwarekennwörtern

Wenn Sie einen Azure FXT Edge Filer-Knoten zum ersten Mal einschalten, müssen Sie ein Stammkennwort festlegen. Die Hardwareknoten werden nicht mit einem Standardkennwort ausgeliefert.

Die Netzwerkanschlüsse sind deaktiviert, bis das Kennwort festgelegt wurde und der Root-Benutzer sich anmeldet.

Führen Sie diesen Schritt nach der Installation und Verkabelung des Knotens, aber noch vor der Clustererstellung aus.

In diesem Tutorial erfahren Sie, wie Sie eine Verbindung mit dem Hardwareknoten herstellen und das Kennwort festlegen. Es wird auch beschrieben, wie Sie ein Kennwort für das BIOS-Setup zum Schützen des Knotens hinzufügen können.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Anschließen einer Tastatur und eines Monitors an den Knoten und Einschalten des Knotens
> * Einrichten eines Kennworts für das BIOS-Setup
> * Festlegen von Kennwörtern für den iDRAC-Anschluss und den Root-Benutzer dieses Knotens
> * Anmelden als Root-Benutzer

Wiederholen Sie diese Schritte für jeden Knoten, den Sie in Ihrem Cluster verwenden möchten.

Planen Sie für dieses Tutorial ca. 15 Minuten ein.

## <a name="prerequisites"></a>Voraussetzungen

Zur Vorbereitung dieses Tutorials müssen folgende Schritte ausgeführt werden:

* [Installieren](install.md) Sie die einzelnen Azure FXT Edge Filer-Knoten in einem Rack, und schließen Sie die Netz- und Netzwerkkabel an, wie in [diesem Tutorial](network-power.md) beschrieben.
* Halten Sie eine USB-Tastatur und einen Monitor mit VGA-Anschluss bereit, die Sie an die Hardwareknoten anschließen können. (Der serielle Anschluss des Knotens ist inaktiv, solange kein Kennwort festgelegt wurde.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Anschließen einer Tastatur und eines Monitors an den Knoten

Verbinden Sie einen Monitor und eine Tastatur physisch mit dem Azure FXT Edge Filer-Knoten.

* Schließen Sie den Monitor an den VGA-Anschluss an.
* Schließen Sie die Tastatur an einen der USB-Anschlüsse an.

Das folgende Referenzdiagramm zeigt die Anschlüsse auf der Gehäuserückseite.

> [!NOTE]
> Der serielle Anschluss ist inaktiv, solange kein Kennwort festgelegt wurde.

![Diagramm: Rückseite von Azure FXT Edge Filer mit Beschriftungen für seriellen Anschluss, VGA-Anschluss und USB-Anschlüsse](media/fxt-back-serial-vga-usb.png)

Wenn Sie mehrere Knoten mit den gleichen Peripheriegeräten verbinden möchten, können Sie einen KVM-Switch verwenden.

Schalten Sie den Knoten ein, indem Sie den Netzschalter auf der Vorderseite drücken.

![Diagramm: Vorderseite von Azure FXT Edge Filer. Der runde Netzschalter befindet sich rechts oben (siehe Beschriftung).](media/fxt-front-annotated.png)

## <a name="create-a-bios-setup-password"></a>Erstellen eines Kennworts für das BIOS-Setup

Mit einem Kennwort für das BIOS-Setup werden die BIOS-Einstellungen des Knotens vor versehentlichen oder nicht autorisierten Änderungen geschützt. Dieses Kennwort ist nicht unbedingt erforderlich, um einen Cluster zu erstellen. Die Verwendung im Rahmen Ihrer Strategie für die Clustersicherheit wird aber dringend empfohlen.

Erstellen Sie ein Kennwort für das BIOS-Setup wie folgt:

1. Aktivieren Sie den Knoten, oder starten Sie ihn neu, und drücken Sie sofort F2, um das Hilfsprogramm für die Systemeinrichtung zu öffnen.

1. Wählen Sie auf dem Bildschirm mit dem **Hauptmenü für die Systemeinrichtung** die Option **System BIOS** > **System Security** (System-BIOS > Systemsicherheit) aus.

1. Stellen Sie sicher, dass die Einstellung **Password Status** (Kennwortstatus) auf **Unlocked** (Entsperrt) festgelegt ist.

1. Verwenden Sie das Feld **Setup Password** (Setupkennwort) zum Festlegen des Kennworts. (Auf diesem Bildschirm können Sie auch ein System-BIOS-Kennwort festlegen, falls Sie dies wünschen.)

1. Drücken Sie die ESC-TASTE, um zum Bildschirm **System BIOS** (System-BIOS) zurückzukehren, und drücken Sie dann erneut die ESC-TASTE. In einer Meldung werden Sie aufgefordert, die Änderungen zu speichern. Wenn das System nicht automatisch neu gestartet wird, müssen Sie den Neustart durchführen, damit Sie zum regulären Startbildschirm gelangen.<!-- how to exit this mode/do you need to reboot to get to the initial setup screen? -->

## <a name="set-initial-passwords"></a>Festlegen anfänglicher Kennwörter

Der Azure FXT Edge Filer-Knoten gibt beim Start verschiedene Meldungen auf dem Monitor aus. Nach einigen Augenblicken wird ein Bildschirm für die Ersteinrichtung angezeigt, der in etwa wie folgt aussieht:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set the iDRAC and temporary root password.
Minimum password length is 8.

NOTE: This does not set a BIOS setup password. For security,
Microsoft recommends using a BIOS setup password, restricting
physical access to the node, and other measures. Learn more at
https://aka.ms/fxt-security.

Enter new password:

```

Das Kennwort, das Sie eingeben, wird für zwei Dinge verwendet:

* Als temporäres Stammkennwort für diesen Azure FXT Edge Filer-Knoten.

  Dieses Kennwort ändert sich, wenn Sie einen Cluster mit diesem Knoten erstellen oder den Knoten dem Cluster hinzufügen. Das Clusterverwaltungskennwort (das dem Benutzer ``admin`` zugeordnet ist) ist auch das Stammkennwort für alle Knoten in einem Cluster.

* Als langfristiges Kennwort für den iDRAC/IPMI-Hardwareverwaltungsanschluss.

  Merken Sie sich das Kennwort gut, damit Sie sich bei Bedarf zur Behandlung von Hardwareproblemen über IPMI anmelden können.

Geben Sie das Kennwort ein, und bestätigen Sie es:

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Nach Eingabe des Kennworts wird der Startvorgang des Systems fortgesetzt. Wenn der Startvorgang abgeschlossen ist, wird eine Anmeldeaufforderung (``login:``) angezeigt.

## <a name="sign-in-as-root"></a>Anmelden als Root-Benutzer

Melden Sie sich als ``root`` an, und verwenden Sie dabei das soeben festgelegte Kennwort.

```
login: root
Password:**********
```

Nachdem Sie sich als Root-Benutzer angemeldet haben, werden die Netzwerkanschlüsse aktiviert, und es wird eine Verbindung mit dem DHCP-Server hergestellt, um IP-Adressen zu beziehen.

## <a name="next-steps"></a>Nächste Schritte

Der Knoten kann nun in einen Cluster eingebunden werden. Sie können ihn zur Erstellung des Azure FXT Edge Filer-Clusters verwenden oder ihn [einem vorhandenen Cluster hinzufügen](add-nodes.md).

> [!div class="nextstepaction"]
> [Erstellen eines Clusters](cluster-create.md)
