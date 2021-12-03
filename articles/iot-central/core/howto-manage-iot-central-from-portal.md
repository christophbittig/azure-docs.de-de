---
title: Verwalten und Überwachen von IoT Central im Azure-Portal | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal IoT Central-Anwendungen erstellen, verwalten und überwachen und verwaltete Identitäten aktivieren.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: dc9611eebdce1e988e760fda3b000cb52d0ff95a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488272"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>Verwalten und Überwachen von IoT Central über das Azure-Portal

Sie können das [Azure-Portal](https://portal.azure.com) zum Erstellen, Verwalten und Überwachen von IoT Central-Anwendungen nutzen.

## <a name="create-iot-central-applications"></a>Erstellen von IoT Central-Anwendungen

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Navigieren Sie zum Erstellen einer Anwendung zur Seite [IoT Central-Anwendung](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) im Azure-Portal:

![Erstellen eines IoT Central-Formulars](media/howto-manage-iot-central-from-portal/create-form.png)

* **Ressourcenname** ist ein eindeutiger Name, den Sie für Ihre IoT Central-Anwendung in Ihrer Azure-Ressourcengruppe auswählen können.

* **Anwendungs-URL** ist die URL, die Sie für den Zugriff auf Ihre Anwendung verwenden können.

* **Vorlage** ist der Typ der IoT Central-Anwendung, die Sie erstellen möchten. Sie können eine neue Anwendung entweder anhand der Liste mit den branchenrelevanten Vorlagen erstellen, um schnell einzusteigen, oder Sie starten von Grund auf neu, indem Sie eine Vorlage für eine **benutzerdefinierte Anwendung** verwenden.

* Der **Standort** beschreibt die [geografische Region](https://azure.microsoft.com/global-infrastructure/geographies/), in der Sie Ihre Anwendung erstellen möchten. Normalerweise wählen Sie den Standort aus, der Ihren Geräten physisch am nächsten liegt, um eine optimale Leistung zu erzielen. Azure IoT Central ist derzeit an den folgenden Standorten verfügbar:
    
    * Australien
    * East Central US (USA, Osten-Mitte)
    * East US
    * USA (Ost) 2
    * Japan, Osten
    * Nordeuropa
    * Asien, Südosten
    * UK, Süden
    * Europa, Westen
    * USA (Westen)

  Nachdem Sie einen Standort ausgewählt haben, können Sie Ihre Anwendung nicht zu einem späteren Zeitpunkt an einen anderen Standort verschieben.

Wählen Sie **Erstellen** aus, wenn Sie alle Felder ausgefüllt haben. Weitere Informationen finden Sie unter [Erstellen einer IoT Central-Anwendung](howto-create-iot-central-application.md).

## <a name="manage-existing-iot-central-applications"></a>Verwalten vorhandener IoT Central-Anwendungen

Wenn Sie bereits über eine Azure IoT Central-Anwendung verfügen, können Sie sie löschen oder aber im Azure-Portal in ein anderes Abonnement oder eine andere Ressourcengruppe verschieben.

> [!NOTE]
> Da für Anwendungen, die mit dem Tarif *Free* erstellt wurden, keine Azure-Abonnements erforderlich sind, werden sie auch nicht in Ihrem Azure-Abonnement im Azure-Portal aufgeführt. Kostenlose Apps können nur über das IoT Central-Portal angezeigt und verwaltet werden.

Um zu beginnen, suchen Sie mithilfe der Suchleiste oben im Azure-Portal nach Ihrer Anwendung. Sie können auch sämtliche Ihrer Anwendungen anzeigen, indem Sie nach _IoT Central-Anwendungen_ suchen und den Dienst auswählen:

![Screenshot: Suchergebnisse für „IoT Central-Anwendungen“ mit ausgewähltem ersten Dienst](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Wenn Sie eine Anwendung in den Suchergebnissen auswählen, wird im Azure-Portal die zugehörige Übersicht angezeigt. Sie können **URL der IoT Central-Anwendung** auswählen, um zur Anwendung zu navigieren:

![Der Screenshot zeigt die Seite „Übersicht“ mit hervorgehobenem „URL der IoT Central-Anwendung“.](media/howto-manage-iot-central-from-portal/highlight-application.png)

Um die Anwendung in eine andere Ressourcengruppe zu verschieben, wählen Sie neben der Ressourcengruppe **Ändern** aus. Wählen Sie auf der Seite **Ressourcen verschieben** die Ressourcengruppe aus, in die Sie diese Anwendung verschieben möchten:

![Der Screenshot zeigt die Seite „Übersicht“ mit hervorgehobenem „Ressourcengruppe (ändern)“.](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

Wenn Sie die Anwendung in ein anderes Abonnement verschieben möchten, wählen Sie neben dem Abonnement **Ändern** aus. Wählen Sie auf der Seite **Ressourcen verschieben** das Abonnement aus, in das Sie diese Anwendung verschieben möchten:

![Verwaltungsportal: Ressourcenverwaltung](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="configure-a-managed-identity"></a>Konfigurieren einer verwalteten Identität

Wenn Sie einen Datenexport in Ihrer IoT Central-Anwendung konfigurieren, können Sie die Verbindung mit dem Ziel mit einer *Verbindungszeichenfolge* oder einer [verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md) konfigurieren. Verwaltete Identitäten sind aus folgenden Gründen sicherer:

* Sie speichern die Anmeldeinformationen für Ihre Ressource nicht in einer Verbindungszeichenfolge in Ihrer IoT Central-Anwendung.
* Die Anmeldeinformationen sind automatisch an die Lebensdauer Ihrer IoT Central-Anwendung gebunden.
* Verwaltete Identitäten rotieren ihre Sicherheitsschlüssel automatisch regelmäßig.

IoT Central verwendet derzeit [systemseitig zugewiesene verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Die verwaltete Identität wird entweder über das Azure-Portal oder die REST-API erstellt.

> [!NOTE]
> Verwaltete Identitäten können nur IoT Central-Anwendungen hinzugefügt werden, die in einer Region erstellt wurden. Alle neuen Anwendungen werden in einer Region erstellt. Weitere Informationen finden Sie bei den [Updates](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/).

Wenn Sie eine verwaltete Identität konfigurieren, müssen Sie einen *Bereich* und eine *Rolle* festlegen:

* Mit dem Bereich wird definiert, wo die verwaltete Identität verwendet werden kann. Sie können zum Beispiel eine Azure-Ressourcengruppe als Bereich festlegen. In diesem Fall müssen sich die IoT Central-Anwendung und das Ziel in derselben Ressourcengruppe befinden.
* Mit der Rolle wird definiert, über welche Berechtigungen die IoT Central-Anwendung im Zieldienst verfügt. Damit eine IoT Central-Anwendung Daten an einen Event Hub senden kann, muss der verwalteten Identität beispielsweise die Rolle **Azure Event Hubs Data Sender** (Azure Event Hubs-Datensender) zugewiesen sein.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

Sie können Rollenzuweisungen über das Azure-Portal oder die Azure CLI konfigurieren:

* Weitere Informationen zum Konfigurieren von Rollenzuweisungen für bestimmte Ziele über das Azure-Portal finden Sie unter [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports](howto-export-data.md).
* Weitere Informationen zum Konfigurieren von Rollenzuweisungen für bestimmte Ziele über die Azure CLI finden Sie unter [Verwalten von IoT Central über die Azure CLI oder PowerShell](howto-manage-iot-central-from-cli.md).

## <a name="monitor-application-health"></a>Überwachen der Anwendungsintegrität

> [!NOTE]
> Metriken sind nur für IoT Central-Anwendungen der Version 3 verfügbar. Wenn Sie erfahren möchten, wie Sie Ihre Anwendungsversion überprüfen können, lesen Sie [Wie kann ich Informationen zu meiner Anwendung erhalten?](howto-faq.yml#how-do-i-get-information-about-my-application-).

Sie können mithilfe der von IoT Central bereitgestellten Metriken die Integrität von mit Ihrer IoT Central-Anwendung verbundenen Geräten und die Integrität Ihrer aktiven Datenexporte bewerten.

Metriken sind für Ihre IoT Central-Anwendung standardmäßig aktiviert, und Sie greifen über das [Azure-Portal](https://portal.azure.com/) darauf zu. Die [Azure Monitor-Datenplattform macht diese Metriken verfügbar](../../azure-monitor/essentials/data-platform-metrics.md) und bietet Ihnen mehrere Möglichkeiten zur Interaktion damit. So können Sie beispielsweise Diagramme im Azure-Portal, eine REST-API oder aber Abfragen in PowerShell oder in der Azure CLI verwenden.

> [!TIP]
> Weil Anwendungen, die den kostenlosen Testplan verwenden, kein Azure-Abonnement zugeordnet wird, unterstützen sie keine Azure Monitor-Metriken. Sie können [eine Anwendung in einen Standard-Tarif konvertieren](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-) und Zugriff auf diese Metriken erhalten.

### <a name="view-metrics-in-the-azure-portal"></a>Anzeigen von Metriken im Azure-Portal

Bei den folgenden Schritten wird davon ausgegangen, dass Sie über eine [IoT Central-Anwendung](./howto-create-iot-central-application.md) mit einigen [verbundenen Geräten](./tutorial-connect-device.md) oder über einen aktiven [Datenexport](howto-export-data.md) verfügen.

So zeigen Sie IoT Central-Metriken im Portal an:

1. Navigieren Sie im Portal zu Ihrer IoT Central-Anwendungsressource. IoT Central-Ressourcen befinden sich standardmäßig in der Ressourcengruppe **IOTC**.
1. Wenn Sie ein Diagramm aus den Metriken Ihrer Anwendung erstellen möchten, wählen Sie im Abschnitt **Überwachung** die Option **Metriken** aus.

![Azure-Metriken](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Azure-Portalberechtigungen

Der Zugriff auf Metriken im Azure-Portal wird von der [rollenbasierten Zugriffssteuerung in Azure](../../role-based-access-control/overview.md) verwaltet. Verwenden Sie das Azure-Portal, um der/dem IoT Central-Anwendung/-Ressourcengruppe/-Abonnement Benutzer hinzuzufügen, damit ihnen Zugriff gewährt wird. Sie müssen einen Benutzer im Portal auch dann hinzufügen, wenn er der IoT Central-Anwendung bereits hinzugefügt wurde. Verwenden Sie [integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md) für eine präzisere Zugriffssteuerung.

### <a name="iot-central-metrics"></a>IoT Central-Metriken

Eine Liste der aktuell für IoT Central verfügbaren Metriken finden Sie unter [Unterstützte Metriken von Azure Monitor](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps).

### <a name="metrics-and-invoices"></a>Metriken und Rechnungen

Metriken können von den Zahlen abweichen, die in Ihrer Azure IoT Central-Rechnung angezeigt werden. Für diese Situation gibt es verschiedene Ursachen, beispielsweise:

* Die [Standard-Tarife](https://azure.microsoft.com/pricing/details/iot-central/) von IoT Central enthalten zwei Geräte und kostenlose variierende Nachrichtenkontingente. Die kostenlosen Elemente sind in der Abrechnung nicht enthalten, werden in den Metriken aber dennoch gezählt.

* IoT Central generiert automatisch eine einzige Testgeräte-ID für jede Gerätevorlage in der Anwendung. Diese Geräte-ID wird auf der Seite **Testgerät verwalten** für eine Gerätevorlage angezeigt. Sie können Ihre Gerätevorlagen vor der Veröffentlichung überprüfen, indem Sie Code generieren, der diese Testgeräte-IDs verwendet. Diese Geräte sind in der Abrechnung nicht enthalten, werden in den Metriken aber dennoch gezählt.

* Während Metriken möglicherweise eine Teilmenge der Gerät-zu-Cloud-Kommunikation zeigen, wird die gesamte Kommunikation zwischen dem Gerät und der Cloud [als Nachricht zur Abrechnung gezählt](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="monitor-connected-iot-edge-devices"></a>Überwachen von verbundenen IoT Edge-Geräten

Wenn Sie erfahren möchten, wie Sie Ihre IoT Edge-Flotte mithilfe von Azure Monitor und integrierten Metriken überwachen können, lesen Sie [Sammeln und Transportieren von Metriken](../../iot-edge/how-to-collect-and-transport-metrics.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Azure IoT Central-Anwendungen über das Azure-Portal verwalten und überwachen, wird der folgende nächste Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)