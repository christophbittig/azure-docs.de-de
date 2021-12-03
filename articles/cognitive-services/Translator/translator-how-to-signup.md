---
title: Erstellen einer Translator-Ressource
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Cognitive Services-Übersetzerressource erstellen und einen Abonnementschlüssel sowie eine Endpunkt-URL abrufen.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 02d1e509068c2332164fea7236fe1fe554b3ef02
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439270"
---
# <a name="create-a-translator-resource"></a>Erstellen einer Translator-Ressource

In diesem Artikel erfahren Sie, wie Sie eine Übersetzerressource im Azure-Portal erstellen. Der [Übersetzer von Azure](translator-overview.md) ist ein cloudbasierter Dienst für maschinelle Übersetzung, der zur [Azure Cognitive Services](../what-are-cognitive-services.md)-Familie der REST-APIs gehört. Azure-Ressourcen sind Instanzen von Diensten, die Sie erstellen. Alle API-Anforderungen an Azure-Dienste erfordern eine **Endpunkt**-URL und einen schreibgeschützten **Abonnementschlüssel** zum Authentifizieren des Zugriffs.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/), um zu beginnen.  Wenn Sie kein Konto besitzen, können Sie [**ein kostenloses 12-monatiges Abonnement erstellen**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Übersetzerressourcentypen

Der Zugriff auf die Übersetzerressource ist über zwei verschiedene Ressourcentypen möglich:

* Ressourcentypen für **einzelne Dienste** ermöglichen den Zugriff auf einen API-Schlüssel und einen Endpunkt für einen einzelnen Dienst.  

* Ressourcentypen für **mehrere Dienste** ermöglichen den Zugriff auf mehrere Cognitive Services-Instanzen über einen einzelnen API-Schlüssel und Endpunkt. Die Cognitive Services-Ressource ist derzeit für die folgenden Dienste verfügbar:
  * Sprache ([Übersetzer](../translator/translator-overview.md), [Sprachenverständnis (LUIS - Language Understanding)](../luis/what-is-luis.md), [Sprachenservice](../text-analytics/overview.md))  
  * Vision ([Maschinelles Sehen](../computer-vision/overview.md), [Gesichtserkennung](../face/overview.md))  
  * Entscheidungsfindung ([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Erstellen Ihrer Ressource

* Navigieren Sie im Azure-Portal direkt zur Seite [**Create Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (Übersetzer erstellen), um Ihre Projektdetails anzugeben.

* Navigieren Sie im Azure-Portal direkt zur Seite [**Create Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) (Cognitive Services erstellen), um Ihre Projektdetails anzugeben.

>[!TIP]
>Wenn Sie es bevorzugen, können Sie den **Erstellprozess** auf der Homepage des Azure-Portals wie folgt beginnen:
>
> 1. Navigieren Sie zur Homepage des [**Azure-Portals**](https://ms.portal.azure.com/#home).
> 1. Klicken Sie im Menü für Azure-Dienste auf ➕**Ressource erstellen**.
>1. Geben Sie in das Suchfeld **Marketplace durchsuchen** **Übersetzer** (für eine einzelne Dienstressource) oder **Cognitive Services** (für mehrere Dienstressourcen) ein, und wählen Sie die jeweilige Option aus.  *Siehe [Auswählen eines Ressourcentyps](#create-your-resource) oben*.
> 1. Klicken Sie auf **Erstellen**. Daraufhin wird die Projektdetailseite geöffnet.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Ausfüllen Ihrer Projekt- und Instanzdetails

1. **Abonnement:** Wählen Sie eines Ihrer verfügbaren Azure-Abonnements aus.

1. **Ressourcengruppe:** Wählen Sie die Azure-Ressourcengruppe aus, die als virtueller Container für Ihre neue Ressource dienen soll. Sie können eine neue Ressourcengruppe erstellen oder Ihre Ressource zu einer bereits vorhandenen Ressourcengruppe hinzufügen, die denselben Lebenszyklus, dieselben Berechtigungen und dieselben Richtlinien aufweist.

1. **Ressourcenregion:** Wählen Sie die Option **Global** aus, es sei denn, Ihr Unternehmen oder Ihre Anwendung erfordert eine spezifische Region. Bei dem Übersetzer handelt es sich um einen Dienst, der keine Abhängigkeit von einer bestimmten Azure-Region aufweist. *Weitere Informationen finden Sie unter* [Regionen und Verfügbarkeitszonen in Azure](../../availability-zones/az-overview.md).

1. **Name**. Geben Sie den Namen ein, den Sie für Ihre Ressource ausgewählt haben. Der ausgewählte Name muss innerhalb von Azure eindeutig sein.

> [!NOTE]
> Wenn Sie ein Übersetzerfeature verwenden, das einen benutzerdefinierten Domänenendpunkt erfordert, entspricht der Wert, den Sie in das Feld „Name“ eingeben, dem benutzerdefinierten Domänennamenparameter für den Endpunkt.

5. **Tarif:** Wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/translator) aus, der Ihre Anforderungen erfüllt:

   * Jedes Abonnement verfügt über einen Free-Tarif.
   * Der kostenlose Tarif umfasst dieselben Features und Funktionen wie kostenpflichtige Tarife und läuft nicht ab.
   * Pro Konto ist nur ein kostenloses Abonnement zulässig.</li></ul>

1. Wenn Sie eine Ressource für mehrere Dienste erstellt haben, müssen Sie zusätzliche Informationen zur Nutzung über die Kontrollkästchen angeben.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Lesen Sie die Nutzungsbedingungen, und klicken Sie auf **Erstellen**, um Ihre Ressource bereitzustellen.

1. Nachdem Ihre Ressource erfolgreich bereitgestellt wurde, klicken Sie auf **Zu Ressource wechseln**.

### <a name="authentication-keys-and-endpoint-url"></a>Authentifizierungsschlüssel und Endpunkt-URL

Alle Cognitive Services-API-Anforderungen erfordern eine Endpunkt-URL und einen schreibgeschützten Schlüssel für die Authentifizierung.

* **Authentifizierungsschlüssel:** Bei dem Schlüssel handelt es sich um eine eindeutige Zeichenfolge, die mit jeder Anforderung an den Übersetzerdienst übergeben wird. Sie können Ihren Schlüssel über einen Abfragezeichenfolgenparameter übergeben, oder indem Sie ihn im HTTP-Anforderungsheader angeben.

* **Endpunkt-URL:** Verwenden Sie den globalen Endpunkt in Ihrer API-Anforderung, es sei denn, Sie benötigen eine spezifische Azure-Region. *Weitere Informationen finden Sie unter* [Basis-URLs](reference/v3-0-reference.md#base-urls). Die globale Endpunkt-URL lautet `api.cognitive.microsofttranslator.com`.

## <a name="get-your-authentication-keys-and-endpoint"></a>Abrufen Ihrer Authentifizierungsschlüssel und Ihres Endpunkts

1. Nachdem Ihre neue Ressource bereitgestellt wurde, klicken Sie auf **Zu Ressource wechseln**, oder navigieren Sie direkt zu Ihrer Ressourcenseite.
1. Klicken Sie in der linken Schiene unter *Ressourcenverwaltung* auf **Schlüssel und Endpunkt**.
1. Kopieren Sie Ihre Abonnementschlüssel und Endpunkt-URL an einen praktischen Speicherort, z. B. in den *Microsoft-Editor*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Abrufen der Schlüssel und des Endpunkts":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Löschen einer Ressource oder Ressourcengruppe

> [!Warning]
> Durch das Löschen einer Ressourcengruppe werden auch alle in der Gruppe enthaltenen Ressourcen gelöscht.

Zum Löschen einer Cognitive Services- oder Übersetzerressource können Sie **die Ressource löschen** oder **die Ressourcengruppe löschen**.

So löschen Sie die Ressource:

1. Navigieren Sie im Azure-Portal zu Ihrer Ressourcengruppe.
1. Wählen Sie die zu löschenden Ressourcen aus, indem Sie das zugehörige Kontrollkästchen aktivieren.
1. Klicken Sie im oberen Menü am rechten Rand auf **Löschen**.
1. Geben Sie *yes* (Ja) in das Dialogfeld **Gelöschte Ressourcen** ein.
1. Klicken Sie auf **Löschen**.

So löschen Sie die Ressourcengruppe:

1. Navigieren Sie im Azure-Portal zu Ihrer Ressourcengruppe.
1. Klicken Sie am linken Rand der oberen Menüleiste auf **Ressourcengruppe löschen**.
1. Bestätigen Sie die Löschanforderung, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="how-to-get-started-with-translator"></a>Erste Schritte mit dem Übersetzer

Im Schnellstart erfahren Sie, wie Sie den Übersetzerdienst mit REST-APIs verwenden.

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Übersetzer](quickstart-translator.md)

## <a name="more-resources"></a>Weitere Ressourcen

* [Codebeispiele für den Übersetzer von Microsoft](https://github.com/MicrosoftTranslator)  (Auf GitHub sind Codebeispiele in mehreren Sprachen für den Übersetzer verfügbar.)
* [Supportforum zur Microsoft-Sprachübersetzung](https://www.aka.ms/TranslatorForum)
* [Erste Schritte mit Azure (3-Minuten-Video)](https://azure.microsoft.com/get-started/?b=16.24)