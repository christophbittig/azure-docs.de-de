---
title: Erstellen und Verwalten von Azure IoT Central-Anwendungen über das CSP-Portal | Microsoft-Dokumentation
description: Sie erfahren, wie Sie als CSP eine Azure IoT Central-Anwendung im Auftrag des Kunden erstellen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/28/2021
ms.topic: how-to
ms.openlocfilehash: acbaa73fe51bd1de51084d0ec2752702ae96c66a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469957"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Erstellen und Verwalten einer Azure IoT Central-Anwendung über das CSP-Portal

Das Microsoft Cloud-Lösungsanbieter-Programm (CSP, Cloud Solution Provider) ist ein Programm für Microsoft-Vertriebspartner. Unseren Channelpartnern steht damit ein einziges Programm zum Verkauf aller kommerziellen Onlinedienste von Microsoft zur Verfügung. Erfahren Sie mehr über das [Cloud-Lösungsanbieter-Programm](https://partner.microsoft.com/cloud-solution-provider).

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Als CSP können Sie Microsoft Azure IoT Central-Anwendungen im Auftrag Ihrer Kunden über die [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home) erstellen und verwalten. Wenn Azure IoT Central-Anwendungen im Auftrag von CSP-Kunden erstellt werden, verwalten CSPs die Abrechnung für Kunden wie für andere CSP-verwaltete Azure-Dienste. Eine Gebühr für Azure IoT Central wird in Ihrer Gesamtrechnung im Microsoft Partner Center angezeigt.

Melden Sie sich zum Einstieg bei Ihrem Konto im Microsoft-Partnerportal an, und wählen Sie einen Kunden aus, für den Sie eine Azure IoT Central-Anwendung erstellen möchten. Navigieren Sie vom linken Navigationsbereich zur Dienstverwaltung für den Kunden.

![Microsoft Partner Center, Kundenansicht](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central wird als zur Verwaltung verfügbarer Dienst aufgeführt. Wählen Sie auf der Seite den Azure IoT Central-Link aus, um für diesen Kunden neue Anwendungen zu erstellen oder vorhandene Anwendungen zu verwalten.

![Azure IoT Central, verfügbar zur Verwaltung](media/howto-create-and-manage-applications-csp/image2.png)

Sie gelangen zur „Anwendungs-Manager“-Seite von Azure IoT Central. Azure IoT Central beachtet den Kontext, dass Sie vom Microsoft Partner Center gekommen sind, um diesen bestimmten Kunden zu verwalten. Dies wird in der Kopfzeile der Seite „Anwendungs-Manager“ bestätigt. Von hier aus können Sie entweder zu einer zuvor für diesen Kunden erstellten Anwendung navigieren, um sie zu verwalten, oder eine neue Anwendung für den Kunden erstellen.

![Erstellen des Managers für CSPs](media/howto-create-and-manage-applications-csp/image3.png)

Um eine Azure IoT Central-Anwendung zu erstellen, wählen Sie im linken Menü **Erstellen** aus. Wählen Sie eine der Branchenvorlagen aus, oder wählen Sie **Benutzerdefinierte App** aus, um eine Anwendung von Grund auf neu zu erstellen. Dadurch wird die Seite „Anwendungserstellung“ geladen. Sie müssen alle Felder auf dieser Seite ausfüllen und dann **Erstellen** auswählen. Unten finden Sie weitere Informationen zu den einzelnen Feldern.

![Screenshot: Seite „Eigene IoT-Anwendung erstellen“ mit ausgewählter Schaltfläche „Erstellen“](media/howto-create-and-manage-applications-csp/image4.png)

![Erstellen einer Anwendungsseite für CSPs](media/howto-create-and-manage-applications-csp/image4-1.png)

![Erstellen einer Anwendungsseite für CSP-Abrechnungsinformationen](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Tarif

Sie können nur Anwendungen erstellen, die einen Standardtarif als CSP verwenden. Um Ihrem Kunden Azure IoT Central vorzustellen, können Sie separat eine Anwendung erstellen, die den kostenlosen Tarif nutzt. Weitere Informationen zu den Tarifen „Free“ und „Standard“ finden Sie auf der Seite [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/).

Sie können nur Anwendungen erstellen, die einen Standardtarif als CSP verwenden. Um Ihrem Kunden Azure IoT Central vorzustellen, können Sie separat eine Anwendung erstellen, die den kostenlosen Tarif nutzt. Weitere Informationen zu den Tarifen „Free“ und „Standard“ finden Sie auf der Seite [Azure IoT Central – Preise](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Anwendungsname

Der Name Ihrer Anwendung wird auf der Seite **Anwendungs-Manager** und in jeder Azure IoT Central-Anwendung angezeigt. Für Ihre Azure IoT Central-Anwendung können Sie einen beliebigen Namen wählen. Wählen Sie einen Namen, der für Sie und Ihre Kollegen eindeutig ist.

## <a name="application-url"></a>Anwendungs-URL

Die Anwendungs-URL ist der Link zu Ihrer Anwendung. Sie können ein Lesezeichen in Ihrem Browser speichern oder mit anderen teilen.

Wenn Sie den Namen für Ihre Anwendung eingeben, wird die Anwendungs-URL automatisch generiert. Falls gewünscht, können Sie auch eine andere URL für Ihre Anwendung auswählen. Jede Azure IoT Central-URL muss in Azure IoT Central eindeutig sein. Eine Fehlermeldung wird angezeigt, wenn die von Ihnen gewählte URL bereits vergeben ist.

## <a name="directory"></a>Verzeichnis

Da Azure IoT Central den Kontext kennt, dass Sie den im Microsoft-Partnerportal ausgewählten Kunden verwalten möchten, sehen Sie im Feld „Verzeichnis“ nur den Azure Active Directory-Mandanten für diesen Kunden. 

Ein Azure Active Directory-Mandant enthält Benutzeridentitäten, Anmeldeinformationen und andere organisatorische Informationen. Mehrere Azure-Abonnements können einem einzelnen Azure Active Directory-Mandanten zugeordnet werden.

Weitere Informationen finden Sie unter [Azure Active Directory](../../active-directory/index.yml).

## <a name="azure-subscription"></a>Azure-Abonnement

Mit einem Azure-Abonnement können Sie Instanzen von Azure-Diensten erstellen. Azure IoT Central findet automatisch alle Azure-Abonnements des Kunden, auf die Sie Zugriff haben, und zeigt sie auf der Seite **Anwendung erstellen** in einem Dropdownmenü an. Wählen Sie ein Azure-Abonnement, um eine neue Azure IoT Central-Anwendung zu erstellen.

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie im Microsoft Partner Center eines erstellen. Nachdem Sie das Azure-Abonnement erstellt haben, navigieren Sie zurück zur Seite **Anwendung erstellen**. Ihr neues Abonnement wird in der Dropdownliste **Azure-Abonnement** angezeigt.

Weitere Informationen finden Sie unter [Azure-Abonnements](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Position

**Standort** beschreibt die [geografische Region](https://azure.microsoft.com/global-infrastructure/geographies/), in der Sie die Anwendung erstellen möchten. Normalerweise wählen Sie den Standort aus, der Ihren Geräten physisch am nächsten liegt, um eine optimale Leistung zu erzielen. Sie können eine IoT Central-Anwendung derzeit in den geografischen Regionen **Australien**, **Asien, Osten**, **Europa**, **USA**, **Vereinigtes Königreich** und **Japan** erstellen. Nachdem Sie einen Standort ausgewählt haben, können Sie Ihre Anwendung nicht zu einem späteren Zeitpunkt an einen anderen Standort verschieben.

## <a name="application-template"></a>Anwendungsvorlage

Wählen Sie die Anwendungsvorlage aus, die Sie für Ihre Anwendung verwenden möchten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie als CSP eine Azure IoT Central-Anwendung erstellen, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Verwalten Ihrer Anwendung](howto-administer.md)