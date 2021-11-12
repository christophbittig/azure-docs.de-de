---
title: Erstellen eines Labs
description: Dieser Artikel beschreibt das Erstellen eines Labs mit dem Azure-Portal und Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/27/2021
ms.openlocfilehash: ffec989e93bcd394c175a2b4a19c4cb72978beb0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464461"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Erstellen eines Labs in Azure DevTest Labs

Azure DevTest Labs umfasst eine Gruppe von Ressourcen, wie z. B. virtuelle Azure-Computer (VMs) und Netzwerke. Mit dieser Infrastruktur können Sie diese Ressourcen besser verwalten, indem Sie Grenzwerte und Kontingente angeben. Dieser Artikel beschreibt das Erstellen eines Labs mit dem Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

ein Azure-Abonnement Weitere Informationen zu den Azure-Kaufoptionen finden Sie unter [Azure erwerben](https://azure.microsoft.com/pricing/purchase-options/) oder [Kostenlose Testversion (1 Monat)](https://azure.microsoft.com/pricing/free-trial/). Zum Erstellen des Labs müssen Sie der Besitzer des Abonnements sein.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Wenn Sie den folgenden Link auswählen, werden Sie zur Seite im Azure-Portal weitergeleitet, auf der Sie ein neues Lab in Azure DevTest Labs erstellen können.

[Minutenschnelles Ausführen der ersten Schritte mit Azure DevTest Labs](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="create-a-devtest-labs-resource"></a>Erstellen einer DevTest Labs-Ressource

Die Seite **DevTest Lab erstellen** enthält fünf Registerkarten. Die erste Registerkarte sind die **Grundeinstellungen**.

> [!TIP]
> Unten auf jeder Seite finden Sie einen Link, über den Sie eine **Automatisierungsvorlage herunterladen** können.

### <a name="basic-settings-tab"></a>Registerkarte „Grundeinstellungen“

Geben Sie die folgenden Informationen ein:

|Eigenschaft | BESCHREIBUNG |
|---|---|
|Subscription| Wählen Sie in der Dropdownliste das Azure-Abonnement aus, das für das Lab verwendet werden soll.|
|Ressource&nbsp;Gruppe| Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
|Labname| Geben Sie einen in Ihrem Abonnement eindeutigen Namen für das Lab ein.|
|Ort| Wählen Sie in der Dropdownliste einen Speicherort für das Lab aus.|
|Öffentliche Umgebungen| Das Repository der öffentlichen Umgebung enthält eine Liste zusammengestellter Azure Resource Manager-Vorlagen, die Lab-Benutzer zum Erstellen von PaaS-Ressourcen innerhalb von Labs verwenden können. Weitere Informationen finden Sie unter [Konfigurieren und Verwenden von Umgebungen](devtest-lab-configure-use-public-environments.md).|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-basic-settings.png" alt-text="Screenshot der Registerkarte „Grundeinstellungen“ für „DevTest Labs erstellen“.":::


### <a name="auto-shutdown-tab"></a>Registerkarte „Automatisch herunterfahren“

Mit dem automatischen Herunterfahren können Sie alle Computer in einem Lab täglich automatisch zu einem geplanten Zeitpunkt herunterfahren. Das Feature „Automatisches Herunterfahren“ ist in erster Linie dazu geeignet, um Kosten zu sparen. Lesen Sie [Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown), um zu lernen, wie Sie die Einstellungen zum automatischen Herunterfahren nach der Erstellung des Labs ändern können.

Geben Sie die folgenden Informationen ein:

|Eigenschaft | BESCHREIBUNG |
|---|---|
|Aktiviert| Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, oder **Aus**, um sie zu deaktivieren.|
|Geplantes&nbsp;Herunterfahren| Geben Sie einen Zeitpunkt ein, an dem alle VMs des aktuellen Labs heruntergefahren werden sollen.|
|Zeitzone| Wählen Sie in der Dropdownliste eine Zeitzone aus.|
|Benachrichtigung vor dem automatischen Herunterfahren senden? | Wählen Sie **Ja** oder **Nein** aus, um 30 Minuten vor der angegebenen Uhrzeit des automatischen Herunterfahrens eine Benachrichtigung zu senden. Wenn Sie **Ja** auswählen, geben Sie einen Webhook-URL-Endpunkt oder eine E-Mail-Adresse ein, der bzw. die angibt, wo die Benachrichtigung veröffentlicht bzw. an wen diese gesendet wird. Der Benutzer erhält eine Benachrichtigung und hat die Möglichkeit, das Herunterfahren zu verzögern.|
|Webhook-URL| Wenn das automatische Herunterfahren unmittelbar bevorsteht, wird eine Benachrichtigung an den angegebenen Webhookendpunkt gesendet.|
|E-Mail-Adresse| Geben Sie einen Satz durch Semikolon getrennte E-Mail-Adressen ein, um Benachrichtigungs-E-Mails zu Warnungen zu erhalten.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-auto-shutdown.png" alt-text="Screenshot: Details zur Planung des automatischen Herunterfahrens":::

### <a name="networking-tab"></a>Registerkarte „Netzwerk“

Es wird ein Standardnetzwerk für Sie erstellt (das später geändert/konfiguriert werden kann). Alternativ dazu können Sie ein vorhandenes virtuelles Netzwerk auswählen.

Geben Sie die folgenden Informationen ein:

|Eigenschaft | BESCHREIBUNG |
|---|---|
|Virtuelles&nbsp;Netzwerk| Behalten Sie die Standardeinstellung bei oder wählen Sie in der Dropdownliste ein vorhandenes Netzwerk aus. Virtuelle Netzwerke sind in Azure logisch voneinander isoliert. Standardmäßig können virtuelle Computer im gleichen virtuellen Netzwerk aufeinander zugreifen.|
|Subnet| Behalten Sie die Standardeinstellung bei oder wählen Sie in der Dropdownliste ein vorhandenes Netzwerk aus. Ein Subnetz ist ein IP-Adressbereich in Ihrem virtuellen Netzwerk, mit dem virtuelle Computer voneinander oder vom Internet isoliert werden können.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-networking.png" alt-text="Screenshot: Netzwerkdetails":::

### <a name="tags-tab"></a>Registerkarte „Tags“

Tags sind nützlich zum Verwalten und Organisieren von Lab-Ressourcen nach Kategorie. Weitere Informationen finden Sie unter [Hinzufügen von Tags zu einem Lab](devtest-lab-add-tag.md).

Geben Sie die folgenden Informationen ein:

|Eigenschaft | BESCHREIBUNG |
|---|---|
|Name| Bei Tagnamen ist die Groß-/Kleinschreibung nicht relevant. Sie sind auf 512 Zeichen beschränkt.|
|Wert| Bei Tagwerten ist die Groß-/Kleinschreibung relevant. Sie sind auf 256 Zeichen beschränkt.|

:::image type="content" source="./media/devtest-lab-create-lab/portal-create-tags.png" alt-text="Screenshot: Tagdetails":::

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Auf der Registerkarte **Überprüfen + erstellen** werden alle Konfigurationen überprüft. Wenn alle Einstellungen gültig sind, wird im oberen Bereich **Erfolgreich** angezeigt. Überprüfen Sie Ihre Einstellungen und wählen Sie anschließend **Erstellen** aus. Sie können den Status der Lab-Erstellung rechts oben auf der Seite des Portals im Bereich **Benachrichtigungen** überwachen. 

:::image type="content" source="./media/devtest-lab-create-lab/portal-review-and-create.png" alt-text="Screenshot der Details auf der Registerkarte „Überprüfen + erstellen“":::

## <a name="post-creation"></a>Nach der Erstellung

1. Wählen Sie nach Abschluss des Erstellungsprozesses in der Bereitstellungsbenachrichtigung die Option **Zu Ressource wechseln** aus.

    :::image type="content" source="./media/devtest-lab-create-lab/creation-notification.png" alt-text="Screenshot: DevTest Labs-Bereitstellungsbenachrichtigung":::

1. Die Seite **Übersicht** des Labs ähnelt der folgenden Abbildung:

    :::image type="content" source="./media/devtest-lab-create-lab/lab-home-page.png" alt-text="Screenshot: Seite „Übersicht“ von DevTest Labs":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem das Lab erstellt wurde, sollten Sie ggf. die folgenden Schritte ausführen:

* [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)
* [Sicherer Zugriff auf ein Lab](devtest-lab-add-devtest-user.md)
* [Festlegen von Labrichtlinien](devtest-lab-set-lab-policy.md)

