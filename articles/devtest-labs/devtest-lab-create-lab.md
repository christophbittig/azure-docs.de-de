---
title: Erstellen eines Labs
description: Dieser Artikel beschreibt das Erstellen eines Labs mit dem Azure-Portal und Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: 68b560125a4cacceee613b5c7c9bfaafc5c95c06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602895"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Erstellen eines Labs in Azure DevTest Labs

Ein Lab in Azure DevTest Labs ist die Infrastruktur, die eine Gruppe von Ressourcen wie z.B. virtuelle Computer (VMs) umfasst, mit der Sie diese Ressourcen besser verwalten können, indem Sie Einschränkungen und Kontingente angeben. Dieser Artikel beschreibt das Erstellen eines Labs mit dem Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen eines Labs benötigen Sie Folgendes:

* ein Azure-Abonnement Weitere Informationen zu den Azure-Kaufoptionen finden Sie unter [Azure erwerben](https://azure.microsoft.com/pricing/purchase-options/) oder [Kostenlose Testversion (1 Monat)](https://azure.microsoft.com/pricing/free-trial/). Zum Erstellen des Labs müssen Sie der Besitzer des Abonnements sein.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Minutenschnelles Ausführen der ersten Schritte mit Azure DevTest Labs

Wenn Sie auf den folgenden Link klicken, werden zur Seite im Azure-Portal weitergeleitet, auf der Sie ein neues Lab in Azure DevTest Labs erstellen können.

[Minutenschnelles Ausführen der ersten Schritte mit Azure DevTest Labs](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Konfigurieren der Einstellungen für Ihr neues Konto

Füllen Sie auf der Seite **DevTest Lab erstellen** die folgenden Einstellungen aus.

> [!TIP]
> Unten auf jeder Seite finden Sie einen Link, über den Sie eine **Automatisierungsvorlage herunterladen** können.

### <a name="basic-settings"></a>Grundlegende Einstellungen

Standardmäßig wird die Registerkarte **Grundeinstellungen** angezeigt. 

![Grundeinstellungen](./media/devtest-lab-create-lab/basic-settings.png)

Legen Sie die folgenden Werte fest:

|name|BESCHREIBUNG|
|---|---|
|**Abonnement** | Erforderlich. Wählen Sie das **Abonnement** aus, das mit dem Lab verknüpft werden soll.|
|**Ressourcengruppe**| Erforderlich. Geben Sie einen **Namen für die Ressourcengruppe** für das Lab ein. Erstellen Sie einen neuen, falls noch keiner vorhanden ist.|
|**Lab-Name**| Erforderlich. Geben Sie einen **Namen** für das Lab ein.|
|**Location**|Erforderlich. Wählen Sie einen Speicherort für das Lab aus.|
|**Öffentliche Umgebungen**| Weitere Informationen finden Sie unter [Konfigurieren und Verwenden von öffentlichen Umgebungen](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Einstellungen für das automatische Herunterfahren

Wechseln Sie zur Seite **Automatisch herunterfahren**, um die zugehörigen Einstellungen anzuzeigen. Mit dem automatischen Herunterfahren können Sie alle Computer in einem Lab täglich automatisch zu einem geplanten Zeitpunkt herunterfahren.

![Registerkarte „Automatisch herunterfahren“](./media/devtest-lab-create-lab/auto-shutdown.png)

Auf der Seite können Sie **Automatisch herunterfahren** aktivieren und die Parameter für das automatische Herunterfahren aller Lab-VMs definieren. Die Funktion zum automatischen Herunterfahren dient in erster Linie zum Kostensparen. Mit dieser Funktion können Sie angeben, wann der virtuelle Computer automatisch heruntergefahren werden soll. Nach dem Erstellen des Labs können Sie die Einstellungen zum automatischen Herunterfahren ändern, indem Sie die Schritte im Artikel [Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ausführen.

### <a name="networking"></a>Netzwerkfunktionen

Beim Erstellen eines neuen Labs, wird das Standardnetzwerk für Sie erstellt. Wechseln Sie zur Registerkarte **Netzwerk**, um die Einstellung wie gewünscht zu ändern/konfigurieren. Wählen Sie z. B. ein vorhandenes virtuelles Netzwerk aus.

![Registerkarte „Netzwerk“ ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Tags

Geben Sie **NAME** und **WERT** für **Tags** ein, wenn Sie ein benutzerdefiniertes Tagging erstellen möchten, das jeder im Lab erstellten Ressource hinzugefügt werden soll. Tags sind nützlich zum Verwalten und Organisieren von Lab-Ressourcen nach Kategorie. Weitere Informationen zu Tags, z.B. zum Hinzufügen von Tags nach der Erstellung des Labs, finden Sie unter [Hinzufügen von Tags zu einem Lab](devtest-lab-add-tag.md).

![Registerkarte „Tags“ ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Überprüfen und Erstellen

Wählen Sie anschließend die Option **Erstellen** aus. Sie können den Status der Lab-Erstellung rechts oben auf der Seite des Portals im Bereich **Benachrichtigungen** überwachen. 

![Registerkarte zum Erstellen](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Nach der Erstellung

Nachdem die Erstellung abgeschlossen ist, wird die Schaltfläche **Zu Ressource wechseln** unten auf der Seite und im Benachrichtigungsfenster angezeigt. Alternativ können Sie die Seite **DevTest Labs** aktualisieren, um das neu erstellte Lab in der Liste der Labs anzuzeigen.  

![Erstellen des Diensts](./media/devtest-lab-create-lab/create-2.png)

Klicken Sie auf die Schaltfläche **Zu Ressource wechseln**, und Sie gelangen auf die Startseite Ihres neuen DevTest Labs-Kontos.

![Resource](./media/devtest-lab-create-lab/go-to-resource.png)

Sie können auch im Azure-Portal nach **DevTest Labs** suchen. Wählen Sie in der Liste das neue Konto aus, und rufen Sie auf die Homepage auf. 

![Dienst erstellt](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem das Lab erstellt wurde, sollten Sie ggf. die folgenden Schritte ausführen:

* [Sicherer Zugriff auf ein Lab](devtest-lab-add-devtest-user.md)
* [Festlegen von Labrichtlinien](devtest-lab-set-lab-policy.md)
* [Erstellen einer Labvorlage](devtest-lab-create-template.md)
* [Erstellen benutzerdefinierter Artefakte für Ihre VMs](devtest-lab-artifact-author.md)
* [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)
