---
title: Erstellen einer IoT Central-Anwendung | Microsoft-Dokumentation
description: In diesem Artikel werden die Optionen zum Erstellen einer IoT Central-Anwendung beschrieben, z. B. über die Azure IoT Central-Website, das Azure-Portal und über eine Befehlszeilenumgebung.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: c9f9dec23209d8bc401313a7213239dff52a1023
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586201"
---
# <a name="create-an-iot-central-application"></a>Erstellen einer IoT Central-Anwendung

Sie haben mehrere Möglichkeiten, eine IoT Central-Anwendung zu erstellen. Sie können eine der GUI-basierten Methoden verwenden, wenn Sie einen manuellen Ansatz bevorzugen, oder eine der CLI- oder programmgesteuerten Methoden nutzen, wenn Sie den Prozess automatisieren möchten.

Unabhängig davon, welchen Ansatz Sie wählen, sind die Konfigurationsoptionen identisch, und der Prozess dauert in der Regel weniger als eine Minute.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>Optionen

In diesem Abschnitt werden die verfügbaren Optionen beschrieben, wenn Sie eine IoT Central-Anwendung erstellen. Je nach der von Ihnen festgelegten Methode müssen Sie möglicherweise die Optionen in einem Formular oder als Befehlszeilenparameter angeben:

### <a name="pricing-plans"></a>Tarife

Mit dem *kostenlosen* Tarif können Sie eine IoT Central-Anwendung erstellen, die Sie sieben Tage lang testen können. Kostenloser Tarif:

- Erfordert kein Azure-Abonnement.
- Kann nur über die [Azure IoT Central](https://aka.ms/iotcentral)-Website erstellt und verwaltet werden.
- Ermöglicht den Anschluss von bis zu fünf Geräten.
- Wenn Sie die Anwendung behalten möchten, ist ein Upgrade auf einen Standardtarif möglich.

*Standardtarife*:

- Erfordern ein Azure-Abonnement. Sie sollten in Ihrem Azure-Abonnement mindestens über Zugriff als **Mitwirkender** verfügen. Wenn Sie das Abonnement selbst erstellt haben, sind Sie automatisch Administrator mit ausreichendem Zugriff. Weitere Informationen finden Sie in folgendem Artikel: [Was ist die rollenbasierte Zugriffssteuerung in Azure?](../../role-based-access-control/overview.md).
- Ermöglicht die Erstellung und Verwaltung von IoT Central-Anwendungen über eine der verfügbaren Methoden.
- Ermöglicht den Anschluss von beliebig vielen Geräten. Die Abrechnung erfolgt pro Gerät. Weitere Informationen finden Sie unter [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/).
- Kann nicht auf einen kostenlosen Tarif herabgestuft werden, aber ein Upgrade oder Downgrade auf andere Standardtarife ist möglich.

In der folgenden Tabelle werden die Unterschiede zwischen den drei Standardtarifen erläutert:

| Planname | Kostenlose Geräte | Nachrichten/Monat | Anwendungsfall |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | Einige Nachrichten pro Tag |
| S1        | 2            | 5\.000          | Einige Nachrichten pro Stunde |
| S2        | 2            | 30.000         | Nachrichten alle paar Minuten |

### <a name="application-name"></a>Anwendungsname

Der von Ihnen gewählte _Anwendungsname_ wird auf jeder Seite in Ihrer IoT Central-Anwendung in der Titelleiste angezeigt. Sie wird auch auf der Kachel Ihrer Anwendung auf der Seite **Meine Apps** auf der [Azure IoT Central](https://aka.ms/iotcentral)-Website angezeigt.

Die von Ihnen ausgewählte _Unterdomäne_ identifiziert Ihre Anwendung eindeutig. Die Unterdomäne ist Teil der URL, die Sie für den Zugriff auf die Anwendung verwenden. Die URL für eine IoT Central-Anwendung sieht wie `https://yoursubdomain.azureiotcentral.com` aus.

### <a name="application-template-id"></a>Anwendungsvorlagen-ID

Die von Ihnen gewählte Anwendungsvorlage bestimmt den ursprünglichen Inhalt Ihrer Anwendung, z. B. Dashboards und Gerätevorlagen. Vorlagen-ID: Für eine benutzerdefinierte Anwendung verwenden Sie `iotc-pnp-preview` als Vorlagen-ID.

Weitere Informationen zu benutzerdefinierten und branchenorientierten Anwendungsvorlagen finden Sie unter [Was sind Anwendungsvorlagen?](concepts-app-templates.md).

### <a name="billing-information"></a>Abrechnungsinformationen

Wenn Sie einen der Standardtarife auswählen, müssen Sie Abrechnungsinformationen angeben:

- Das Azure-Abonnement, das Sie verwenden.
- Das Verzeichnis, das das von Ihnen verwendete Abonnement enthält.
- Der Speicherort zum Hosten Ihrer Anwendung. IoT Central verwendet Azure-Geografien als Speicherorte: USA, Europa, Asien-Pazifik, Australien, Vereinigtes Königreich oder Japan.

## <a name="azure-iot-central-site"></a>Azure IoT Central-Website

Die einfachste Möglichkeit, um mit dem Erstellen von IoT Central-Anwendungen zu beginnen, stellt die [Azure IoT Central](https://aka.ms/iotcentral)-Website dar.

Im [Build](https://apps.azureiotcentral.com/build) können Sie die Anwendungsvorlage auswählen, die Sie verwenden möchten:

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="Screenshot der Buildseite, auf der Sie eine Anwendungsvorlage auswählen können":::

Wenn Sie **App erstellen** auswählen, können Sie die erforderlichen Informationen zum Erstellen einer Anwendung aus der Vorlage bereitstellen:

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="Screenshot der Seite „App erstellen“ für IoT Central":::

Auf der Seite **Meine Apps** werden alle IoT Central-Anwendungen aufgeführt, auf die Sie Zugriff haben. Die Liste enthält Anwendungen, die Sie erstellt haben, und Anwendungen, für die Ihnen der Zugriff erteilt wurde.

> [!TIP]
> Alle Anwendungen, die Sie mit einem Standardtarif auf der Azure IoT Central-Website erstellen, verwenden die **IOTC**-Ressourcengruppe in Ihrem Abonnement. Mit den im folgenden Abschnitt beschriebenen Ansätzen können Sie eine zu verwendende Ressourcengruppe auswählen.

## <a name="copy-an-application"></a>Kopieren einer Anwendung

Sie können eine Kopie jeder Anwendung erstellen, mit Ausnahme von Geräteinstanzen, Gerätedatenverlauf und Benutzerdaten. Die Kopie verwendet einen Standard-Tarif, der Ihnen in Rechnung gestellt wird. Es ist nicht möglich, eine Anwendung mit dem kostenlosen Tarif zu erstellen, indem Sie eine Anwendung kopieren.

Wählen Sie **Kopieren** aus. Geben Sie im Dialogfeld die Details für die neue Anwendung ein. Wählen Sie dann **Kopieren** aus, um zu bestätigen, dass Sie den Vorgang fortsetzen möchten. Weitere Informationen zu den Feldern im Formular finden Sie unter [Erstellen einer Anwendung](howto-create-iot-central-application.md).

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="Screenshot: Seite „Anwendungseinstellungen“":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="Screenshot: Seite „Anwendungseinstellungen kopieren“":::

Nach erfolgreichem Abschluss des App-Kopiervorgangs können Sie über den Link zur neuen Anwendung navigieren.

Beim Kopieren einer Anwendung wird auch die Definition von Regeln und E-Mail-Aktionen kopiert. Einige Aktionen, z.B. Flow und Logic Apps, sind über die Regel-ID an bestimmte Regeln gebunden. Wenn eine Regel in eine andere Anwendung kopiert wird, erhält sie eine eigene Regel-ID. In diesem Fall müssen Benutzer eine neue Aktion erstellen und diese dann der neuen Regel zuordnen. Generell empfiehlt es sich, die Regeln und Aktionen zu überprüfen, damit sie in der neuen App auf dem neuesten Stand sind.

> [!WARNING]
> Wenn ein Dashboard Kacheln enthält, in denen Informationen zu bestimmten Geräten angezeigt werden, erscheint in der neuen Anwendung die Meldung **Die angeforderte Ressource wurde nicht gefunden** in diesen Kacheln. Sie müssen diese Kacheln erneut konfigurieren, damit in der neuen Anwendung Informationen zu Geräten angezeigt werden.

## <a name="create-and-use-a-custom-application-template"></a>Erstellen und Verwenden einer benutzerdefinierten Anwendungsvorlage

Wenn Sie eine Azure IoT Central-Anwendung erstellen, können Sie unter verschiedenen integrierten Beispielvorlagen auswählen. Sie können auch eigene Anwendungsvorlagen von vorhandenen IoT Central-Anwendungen erstellen. Anschließend können Sie bei der Erstellung neuer Anwendung Ihre eigenen Anwendungsvorlagen verwenden.

Beim Erstellen einer Anwendungsvorlage werden die folgenden Elemente aus Ihrer vorhandenen Anwendung einbezogen:

- Das standardmäßige Anwendungsdashboard, einschließlich des Dashboardlayouts und aller von Ihnen definierten Kacheln.
- Gerätevorlagen, einschließlich Messungen, Einstellungen, Eigenschaften, Befehle und Dashboard.
- Regeln – Alle Regeldefinitionen werden eingeschlossen. Aktionen werden jedoch nicht eingeschlossen, mit Ausnahme von E-Mail-Aktionen.
- Gerätegruppen, einschließlich ihrer Abfragen.

> [!WARNING]
> Wenn ein Dashboard Kacheln enthält, in denen Informationen zu bestimmten Geräten angezeigt werden, erscheint in der neuen Anwendung die Meldung **Die angeforderte Ressource wurde nicht gefunden** in diesen Kacheln. Sie müssen diese Kacheln erneut konfigurieren, damit in der neuen Anwendung Informationen zu Geräten angezeigt werden.

Die folgenden Elemente sind nicht enthalten, wenn Sie eine Anwendungsvorlage erstellen:

- Geräte
- Benutzer
- Definitionen für den kontinuierlichen Datenexport

Diese Elemente müssen allen von einer Anwendungsvorlage erstellten Anwendungen manuell hinzugefügt werden.

So erstellen Sie eine Anwendungsvorlage von einer vorhandenen IoT Central-Anwendung:

1. Wechseln Sie in Ihrer Anwendung zum Abschnitt **Verwaltung**.
1. Wählen Sie die Option zum **Exportieren von Anwendungsvorlagen** aus.
1. Geben Sie auf der Seite zum **Exportieren von Anwendungsvorlagen** einen Namen und eine Beschreibung für Ihre Vorlage ein.
1. Wählen Sie die Schaltfläche **Exportieren** aus, um die Anwendungsvorlage zu erstellen. Jetzt können Sie den **Freigabefähigen Link** kopieren, mit dem andere Benutzer eine neue Anwendung von der Vorlage erstellen können:

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="Screenshot: Erstellen einer Anwendungsvorlage":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="Screenshot: Exportieren einer Anwendungsvorlage":::

### <a name="use-an-application-template"></a>Verwenden einer Anwendungsvorlage

Um eine Anwendungsvorlage zum Erstellen einer neuen IoT Central-Anwendung zu verwenden, benötigen Sie einen zuvor erstellten **Freigabefähigen Link**. Fügen Sie den **Freigabefähigen Link** in die Adressleiste Ihres Browsers ein. Die Seite **Anwendung erstellen** wird angezeigt. Dabei ist Ihre benutzerdefinierte Anwendungsvorlage ausgewählt:

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="Screenshot: Erstellen einer Anwendung aus einer Vorlage":::

Wählen Sie Ihren Tarif aus, und füllen Sie die übrigen Felder im Formular aus. Wählen Sie dann **Erstellen** aus, um von der Anwendungsvorlage eine neue IoT Central-Anwendung zu erstellen.

### <a name="manage-application-templates"></a>Verwalten von Anwendungsvorlagen

Auf der Seite zum **Exportieren von Anwendungsvorlagen** können Sie die Anwendungsvorlage löschen oder aktualisieren.

Wenn Sie eine Anwendungsvorlage löschen, ist es nicht mehr möglich, den zuvor generierten freigabefähigen Link zum Erstellen neuer Anwendungen zu verwenden.

Um Ihre Anwendungsvorlage zu aktualisieren, ändern Sie auf der Seite zum **Exportieren von Anwendungsvorlagen** den Namen oder die Beschreibung der Vorlage. Wählen Sie dann erneut die Schaltfläche **Exportieren** aus. Durch diese Aktion wird ein neuer **freigabefähiger Link** generiert. Dadurch werden vorherige URLs für **freigabefähige Links** ungültig.

## <a name="other-approaches"></a>Andere Ansätze

Sie können auch die folgenden Ansätze verwenden, um eine IoT Central-Anwendung zu erstellen:

- [Erstellen einer IoT Central-Anwendung über das Azure-Portal](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Erstellen einer IoT Central-Anwendung über die Befehlszeile](howto-manage-iot-central-from-cli.md#create-an-application)
- [Programmgesteuertes Erstellen einer IoT Central-Anwendung](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Azure IoT Central-Anwendungen über die Azure CLI verwalten, wird der folgende nächste Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)
