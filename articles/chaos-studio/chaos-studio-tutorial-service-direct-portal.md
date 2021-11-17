---
title: Erstellen eines Experiments, das einen service-direkten Fehler mit Azure Chaos Studio verwendet
description: Erstellen Sie ein Experiment, das einen service-direkten Fehler verwendet
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fd945f5a96228bc30c7d1f801fcc16ca34717cde
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373479"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-to-fail-over-an-azure-cosmos-db-instance"></a>Erstellen Sie ein Chaos-Experiment, das einen service-direkten Fehler verwendet, um eine Azure Cosmos DB-Instanz ausfallen zu lassen

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. In dieser Anleitung werden Sie ein Multi-Read, Single-Write Azure Comos DB Failover mit Hilfe eines Chaos-Experiments und Azure Chaos Studio durchführen. Die Durchführung dieses Experiments kann Ihnen helfen, sich vor Datenverlusten zu schützen, wenn ein Failover-Ereignis eintritt.

Dieselben Schritte können auch für die Einrichtung und Durchführung eines Experiments für einen beliebigen dienstbedingten Fehler verwendet werden. Ein **service-direct**-Fehler wird direkt gegen eine Azure-Ressource ausgeführt, ohne dass eine Instrumentierung erforderlich ist, im Gegensatz zu agentenbasierten Fehlern, die die Installation des Chaosagenten erfordern.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Ein Azure Cosmos DB-Konto. Wenn Sie noch kein Azure Cosmos DB-Konto haben, können Sie [die folgenden Schritte ausführen, um eines zu erstellen](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Mindestens eine Lese- und eine Schreibregion für Ihr Azure Cosmos DB-Konto eingerichtet.


## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Aktivieren Sie Chaos Studio auf Ihrem Azure Cosmos DB-Konto

Chaos Studio kann keine Fehler gegen eine Ressource injizieren, wenn diese Ressource nicht zuerst in Chaos Studio eingebunden wurde. Sie binden eine Ressource in Chaos Studio ein, indem Sie ein [Ziel und Fähigkeiten](chaos-studio-targets-capabilities.md) für die Ressource erstellen. Azure Cosmos DB-Konten haben nur einen Zieltyp (service-direkt) und eine Fähigkeit (Failover), aber andere Ressourcen können bis zu zwei Zieltypen - einen für service-direkte Fehler und einen für agentenbasierte Fehler - und viele Fähigkeiten haben.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie in der Suchleiste nach **Chaos Studio (Vorschau)** .
3. Klicken Sie auf **Ziele** und navigieren Sie zu Ihrem Azure Cosmos DB-Konto.
![Zielansicht im Azure-Portal](images/tutorial-service-direct-targets.png)
4. Markieren Sie das Kästchen neben Ihrem Azure Cosmos DB-Konto und klicken Sie im Dropdown-Menü auf **Ziele aktivieren** und dann auf **Service-Direktziele aktivieren**.
![Aktivierung von Zielen im Azure-Portal](images/tutorial-service-direct-targets-enable.png)
5. Es erscheint eine Meldung, dass die ausgewählte(n) Ressource(n) erfolgreich aktiviert wurde(n).
![Benachrichtigung, dass das Ziel erfolgreich aktiviert wurde](images/tutorial-service-direct-targets-enable-confirm.png)

Sie haben nun Ihr Azure Cosmos DB-Konto erfolgreich in Chaos Studio eingebunden. In der Ansicht **Ziele** können Sie auch die für diese Ressource aktivierten Fähigkeiten verwalten. Wenn Sie auf den Link **Aktionen verwalten** neben einer Ressource klicken, werden die für diese Ressource aktivierten Funktionen angezeigt.

## <a name="create-an-experiment"></a>Erstellen eines Experiments
Wenn Ihr Azure Cosmos DB-Konto nun eingebunden ist, können Sie Ihr Experiment erstellen. Ein Chaosexperiment definiert die Aktionen, die Sie mit den Zielressourcen durchführen wollen, unterteilt in Schritte, die sequentiell ablaufen, und Zweige, die parallel ablaufen.

1. Klicken Sie auf die Registerkarte **Experimente** in der Chaos Studio-Navigation. In dieser Ansicht können Sie alle Ihre Chaosexperimente sehen und verwalten. Klicken Sie auf **Experiment hinzufügen**
![Ansicht Experimente im Azure-Portal](images/tutorial-service-direct-add.png)
2. Geben Sie **Abonnement**, **Ressourcengruppe** und **Ort** ein, an dem Sie das Chaos-Experiment durchführen möchten. Gib deinem Experiment einen **Namen**. Klicken Sie auf **Weiter : Experimentdesigner >** 
![ Hinzufügen von grundlegenden Experimentdetails](images/tutorial-service-direct-add-basics.png)
3. Sie befinden sich nun im Chaos Studio Experiment Designer. Mit dem Experiment-Designer können Sie Ihr Experiment durch Hinzufügen von Schritten, Verzweigungen und Fehlern aufbauen. Geben Sie Ihrem **Schritt** und **Zweig** einen freundlichen Namen, dann klicken Sie auf **Fehler hinzufügen**.
![Versuchsplaner](images/tutorial-service-direct-add-designer.png)
4. Wählen Sie **CosmosDB Failover** aus dem Dropdown-Menü, geben Sie dann in **Duration** die Anzahl der Minuten ein, die der Ausfall dauern soll, und in **readRegion** die Leseregion Ihres Azure Cosmos DB-Kontos. Klicken Sie auf **Weiter: Zielressourcen >** 
![Fehlereigenschaften](images/tutorial-service-direct-add-fault.png)
5. Wählen Sie Ihr Azure Cosmos DB-Konto und klicken Sie auf **Weiter**
![Ziel hinzufügen](images/tutorial-service-direct-add-target.png)
6. Überprüfen Sie, ob Ihr Experiment korrekt aussieht, und klicken Sie dann auf **Überprüfen + Erstellen**, dann **Erstellen.** 
![Überprüfen und Experiment erstellen](images/tutorial-service-direct-add-review.png)

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Erteilen Sie Ihrem Azure Cosmos DB-Konto eine Experimentierberechtigung
Wenn Sie ein Chaosexperiment erstellen, erzeugt Chaos Studio eine dem System zugewiesene verwaltete Identität, die Fehler gegen Ihre Zielressourcen ausführt. Diese Identität muss mit [entsprechenden Rechten](chaos-studio-fault-providers.md) für die Zielressource ausgestattet sein, damit das Experiment erfolgreich durchgeführt werden kann.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto und klicken Sie auf **Zugriffskontrolle (IAM)** .
![Azure Cosmos DB Übersichtsseite](images/tutorial-service-direct-access-resource.png)
2. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.
![Übersicht über die Zugriffssteuerung](images/tutorial-service-direct-access-iam.png)
3. Suchen Sie nach **Cosmos DB Operator** und wählen Sie die Rolle. Klicken Sie auf **Weiter**
![Zuweisung der Rolle Azure Cosmos DB Operator](images/tutorial-service-direct-access-role.png)
4. Klicken Sie auf **Mitglieder auswählen** und suchen Sie nach Ihrem Experimentnamen. Wählen Sie Ihr Experiment und klicken Sie auf **Auswählen**. Wenn mehrere Experimente mit demselben Namen im selben Mieter vorhanden sind, wird der Name Ihres Experiments mit zufälligen Zeichen gekürzt.
![Hinzufügen eines Experiments zur Rolle](images/tutorial-service-direct-access-experiment.png)
5. Klicken Sie auf **Überprüfen + Zuweisen** und dann auf **Überprüfen + Zuweisen**.

## <a name="run-your-experiment"></a>Führen Sie Ihr Experiment durch
Sie sind nun bereit, Ihr Experiment durchzuführen. Um die Auswirkungen zu sehen, empfehlen wir, die Übersicht Ihres Azure Cosmos DB-Kontos zu öffnen und **Daten global replizieren** in einem separaten Browser-Tab aufzurufen. Durch regelmäßiges Aktualisieren während des Experiments wird der Regionstausch angezeigt.

1. Klicken Sie in der Ansicht **Experimente** auf Ihr Experiment, und klicken Sie auf **Start** und dann auf **OK**.
2. Wenn der **Status** auf **Laufend** wechselt, klicken Sie auf **Details** für den letzten Lauf unter **Historie**, um Details zum laufenden Experiment zu sehen.

## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie ein Azure Cosmos DB Service-Direkt-Experiment durchgeführt haben, können Sie loslegen:
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md)
- [Verwalte dein Experiment](chaos-studio-run-experiment.md)
