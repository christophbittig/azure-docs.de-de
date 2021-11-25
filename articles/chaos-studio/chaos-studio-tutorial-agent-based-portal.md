---
title: Erstellen Sie ein Experiment, das einen Agent-basierten Fehler bei Azure Chaos Studio über das Portal nutzt.
description: Erstellen Sie eine Experiment, die einen agentbasierten Fehler verwendet, und konfigurieren Sie den Chaos-Agenten über das Portal
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: f83f43b92573aa575b432a7dd75e6f0ec060e475
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371360"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-to-add-cpu-pressure-to-a-linux-vm-with-the-azure-portal"></a>Erstellen Sie ein Chaos-Experiment, das einen agentbasierten Fehler verwendet, um einem virtuellen Linux-Computer über das Azure-Portal CPU-Druck zu verleihen.

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie ein Ereignis mit hoher CPU-Leistung auf einer virtuellen Linux-Maschine mithilfe eines Chaos-Experiments und Azure Chaos Studio auslösen. Die Durchführung dieses Experiments kann Ihnen helfen, eine Anwendung vor Ressourcenmangel zu schützen.

Dieselben Schritte können für die Einrichtung und Durchführung eines Experiments für jeden agentenbasierten Fehler verwendet werden. Ein **agentbasierter** Fault erfordert die Einrichtung und Installation des Chaos-Agenten, im Gegensatz zu einem service-direkten Fault, der direkt gegen eine Azure-Ressource läuft, ohne dass eine Instrumentierung erforderlich ist.


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Eine virtuelle Linux-Maschine. Wenn Sie noch keine virtuelle Maschine haben, können Sie [mit den folgenden Schritten eine erstellen](../virtual-machines/linux/quick-create-portal.md).
- Eine Netzwerkeinrichtung, die es Ihnen ermöglicht, [SSH in Ihre virtuelle Maschine](../virtual-machines/ssh-keys-portal.md)
- Eine vom Benutzer zugewiesene verwaltete Identität **, die der virtuellen Zielmaschine oder dem virtuellen Maschinenskalensatz** zugewiesen wurde. Wenn Sie keine benutzerzugewiesene verwaltete Identität haben, können Sie [mit den folgenden Schritten eine erstellen](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)


## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Aktivieren Sie Chaos Studio auf Ihrem virtuellen Rechner

Chaos Studio kann keine Fehler gegen eine virtuelle Maschine injizieren, wenn diese virtuelle Maschine nicht zuvor in Chaos Studio eingebunden wurde. Sie binden eine virtuelle Maschine in Chaos Studio ein, indem Sie ein [Target und capabilities](chaos-studio-targets-capabilities.md) auf der Ressource erstellen und dann den Chaos Agent installieren. Virtuelle Maschinen haben zwei Zieltypen - einen, der service-direkte Fehler ermöglicht (für den kein Agent erforderlich ist), und einen, der agentenbasierte Fehler ermöglicht (für den die Installation eines Agenten erforderlich ist). Der Chaos-Agent ist eine Anwendung, die auf Ihrer virtuellen Maschine als [Erweiterung der virtuellen Maschine](../virtual-machines/extensions/overview.md) installiert wird und es Ihnen ermöglicht, Fehler in das Gastbetriebssystem zu injizieren.

### <a name="install-stress-ng"></a>Installieren Sie stress-ng

Der Chaos Studio-Agent für Linux benötigt stress-ng, eine Open-Source-Anwendung, die verschiedene Stressereignisse auf einer virtuellen Maschine auslösen kann. Sie können stress-ng installieren, indem Sie [eine Verbindung zu Ihrer virtuellen Linux-Maschine](../virtual-machines/ssh-keys-portal.md) herstellen und den entsprechenden Installationsbefehl z.B. für Ihren Paketmanager ausführen:

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

oder

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-capabilities-and-agent"></a>Aktivieren Sie Chaos-Ziel, Fähigkeiten und Agenten

> [!IMPORTANT]
> Bevor Sie die folgenden Schritte ausführen, müssen Sie [eine dem Benutzer zugewiesene verwaltete Identität erstellen](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md) und sie dem virtuellen Zielcomputer oder dem Skalierungssatz des virtuellen Computers zuweisen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie in der Suchleiste nach **Chaos Studio (Vorschau)** .
3. Klicken Sie auf **Ziele** und navigieren Sie zu Ihrer virtuellen Maschine.
![Zielansicht im Azure-Portal](images/tutorial-agent-based-targets.png)
4. Markieren Sie das Kästchen neben Ihrer virtuellen Maschine und klicken Sie im Dropdown-Menü auf **Ziele aktivieren** und dann auf **Agentenbasierte Ziele aktivieren**.
![Aktivierung von Zielen im Azure-Portal](images/tutorial-agent-based-targets-enable.png)
5. Wählen Sie die **verwaltete Identität**, die Sie zur Authentifizierung des Chaos-Agenten verwenden werden, und aktivieren Sie optional Application Insights, um Experimentereignisse und Agentenprotokolle zu sehen.
![Auswahl einer verwalteten Identität](images/tutorial-agent-based-targets-enable-options.png)
6. Klicken Sie auf **Überprüfen + Aktivieren** und dann auf **Aktivieren**.
![Überprüfung der agentenbasierten Zielbefähigung](images/tutorial-agent-based-targets-enable-review.png)
7. Nach einigen Minuten erscheint eine Benachrichtigung, dass die ausgewählte(n) Ressource(n) erfolgreich aktiviert wurde(n). Das Azure-Portal fügt die dem Benutzer zugewiesene Identität zur virtuellen Maschine hinzu, aktiviert das Agentenziel und die Funktionen und installiert den Chaos-Agenten als Erweiterung der virtuellen Maschine.
![Benachrichtigung, dass das Ziel erfolgreich aktiviert wurde](images/tutorial-agent-based-targets-enable-confirm.png)

Sie haben nun Ihre virtuelle Linux-Maschine erfolgreich in Chaos Studio eingebunden. In der Ansicht **Ziele** können Sie auch die für diese Ressource aktivierten Fähigkeiten verwalten. Wenn Sie auf den Link **Aktionen verwalten** neben einer Ressource klicken, werden die für diese Ressource aktivierten Funktionen angezeigt.

## <a name="create-an-experiment"></a>Erstellen eines Experiments
Wenn Ihre virtuelle Maschine nun an Bord ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Klicken Sie auf die Registerkarte **Experimente** in der Chaos Studio-Navigation. In dieser Ansicht können Sie alle Ihre Chaosexperimente sehen und verwalten. Klicken Sie auf **Experiment hinzufügen**
![Ansicht Experimente im Azure-Portal](images/tutorial-agent-based-add.png)
2. Geben Sie **Abonnement**, **Ressourcengruppe** und **Ort** ein, an dem Sie das Chaos-Experiment durchführen möchten. Gib deinem Experiment einen **Namen**. Klicken Sie auf **Weiter : Experimentdesigner >** 
![ Hinzufügen von grundlegenden Experimentdetails](images/tutorial-agent-based-add-basics.png)
3. Sie befinden sich nun im Chaos Studio Experiment Designer. Mit dem Experiment-Designer können Sie Ihr Experiment durch Hinzufügen von Schritten, Verzweigungen und Fehlern aufbauen. Geben Sie Ihrem **Schritt** und **Zweig** einen freundlichen Namen, dann klicken Sie auf **Fehler hinzufügen**.
![Versuchsplaner](images/tutorial-agent-based-add-designer.png)
4. Wählen Sie **CPU-Druck** aus dem Dropdown-Menü, geben Sie dann in **Dauer** die Anzahl der Minuten ein, die der Druck angewendet werden soll, und in **Druckstufe** die Höhe des anzuwendenden CPU-Drucks. Lassen Sie **virtualMachineScaleSetInstances** leer. Klicken Sie auf **Weiter: Zielressourcen >** 
![Fehlereigenschaften](images/tutorial-agent-based-add-fault.png)
5. Wählen Sie Ihre virtuelle Maschine, und klicken Sie auf **Weiter**
![Ziel hinzufügen](images/tutorial-agent-based-add-targets.png)
6. Überprüfen Sie, ob Ihr Experiment korrekt aussieht, und klicken Sie dann auf **Überprüfen + Erstellen**, dann **Erstellen.** 
![Überprüfen und Experiment erstellen](images/tutorial-agent-based-add-review.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Erteilen Sie Ihrer virtuellen Maschine eine Experimentiergenehmigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann.

1. Navigieren Sie zu Ihrer virtuellen Maschine und klicken Sie auf **Zugriffskontrolle (IAM)** .
![Übersichtsseite für virtuelle Maschinen](images/tutorial-agent-based-access-resource.png)
2. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.
![Übersicht über die Zugriffssteuerung](images/tutorial-agent-based-access-iam.png)
3. Suchen Sie nach **Leser** und wählen Sie die Rolle. Klicken Sie auf **Weiter**
![Zuweisung der Rolle "Virtual Machine Contributor"](images/tutorial-agent-based-access-role.png)
4. Klicken Sie auf **Mitglieder auswählen** und suchen Sie nach Ihrem Experimentnamen. Wählen Sie Ihr Experiment und klicken Sie auf **Auswählen**. Wenn mehrere Experimente mit demselben Namen im selben Mieter vorhanden sind, wird der Name Ihres Experiments mit zufälligen Zeichen gekürzt.
![Hinzufügen eines Experiments zur Rolle](images/tutorial-agent-based-access-experiment.png)
5. Klicken Sie auf **Überprüfen + Zuweisen** und dann auf **Überprüfen + Zuweisen**.

## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir, [ein Azure Monitor-Metriken-Diagramm](../azure-monitor/essentials/tutorial-metrics.md) mit der CPU-Auslastung Ihrer virtuellen Maschine in einem separaten Browser-Tab zu öffnen.

1. Klicken Sie in der Ansicht **Experimente** auf Ihr Experiment, und klicken Sie auf **Start** und dann auf **OK**.
![Beginn des Experiments](images/tutorial-agent-based-start.png)
2. Wenn der **Status** auf **Laufend** wechselt, klicken Sie auf **Details** für den letzten Lauf unter **Historie**, um Details zum laufenden Experiment zu sehen.

## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie ein agentenbasiertes Experiment durchgeführt haben, können Sie loslegen:
- [Erstellen Sie ein Experiment, das dienstbezogene Fehler verwendet](chaos-studio-tutorial-service-direct-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)