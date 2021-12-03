---
title: LUIS RBAC (rollenbasierte Zugriffssteuerung)
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Ihrer LUIS-Ressource die Zugriffssteuerung hinzufügen.
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 2c1f9d8783ca293898ff77f851ac928c1b94413e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137890"
---
# <a name="luis-role-based-access-control"></a>LUIS RBAC (rollenbasierte Zugriffssteuerung)

LUIS unterstützt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), ein Autorisierungssystem zum Verwalten des individuellen Zugriffs auf Azure-Ressourcen. Mithilfe der Azure RBAC können Sie verschiedenen Teammitgliedern verschiedene Berechtigungsebenen für Ihre LUIS-Erstellungsressourcen zuweisen. Weitere Information finden Sie in der [Azure RBAC-Dokumentation](/azure/role-based-access-control/).

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Hinzufügen einer Rollenzuweisung zur Language Understanding-Erstellungsressource

Azure RBAC kann einer Language Understanding-Erstellungsressource zugewiesen werden. Wenn Sie Zugriff auf eine Azure-Ressource gewähren möchten, fügen Sie eine Rollenzuweisung hinzu.
1. Wählen Sie im [Azure-Portal](https://ms.portal.azure.com/) die Option **Alle Dienste**. 
2. Wählen Sie **Cognitive Services** aus, und navigieren Sie zu Ihrer spezifischen Language Understanding-Erstellungsressource.
   > [!NOTE]
   > Sie können die Azure RBAC auch für ganze Ressourcengruppen, Abonnements oder Verwaltungsgruppen einrichten. Wählen Sie hierzu die gewünschte Bereichsebene aus, und navigieren Sie dann zum gewünschten Element. Wählen Sie z. B. **Ressourcengruppen** aus, und navigieren Sie dann zu einer bestimmten Ressourcengruppe.

1. Klicken Sie im linken Navigationsbereich auf **Zugriffssteuerung (IAM)** .
1. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie im nächsten Bildschirm auf der Registerkarte **Rolle** eine Rolle aus, die Sie hinzufügen möchten.
1. Wählen Sie auf der Registerkarte **Mitglieder** einen Benutzer, eine Gruppe, einen Dienstprinzipal oder eine verwaltete Identität aus.
1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.

Innerhalb weniger Minuten wird dem Ziel die ausgewählte Rolle für den ausgewählten Bereich zugewiesen. Hilfe zu diesen Schritten finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="security"></a>Sicherheit 

LUIS unterstützt nur die Authentifizierung über Azure Active Directory (AAD). Weitere Informationen finden Sie unter [Authentifizieren mit Azure Active Directory](/azure/cognitive-services/authentication#authenticate-with-azure-active-directory).

## <a name="luis-role-types"></a>LUIS-Rollentypen

Verwenden Sie die folgende Tabelle, um die Zugriffsanforderungen für Ihre LUIS-Anwendung zu ermitteln.

Diese benutzerdefinierten Rollen gelten nur für die Erstellung (Language Understanding-Erstellung) und nicht für Vorhersageressourcen (Language Understanding).

### <a name="cognitive-services-luis-reader"></a>Cognitive Services: LUIS-Reader

Ein Benutzer, der nur LUIS-Anwendungen validieren und überprüfen sollte. Dies ist in der Regel ein Tester, um sicherzustellen, dass die Anwendung vor der Bereitstellung des Projekts ordnungsgemäß funktioniert. Sie möchten möglicherweise die Ressourcen der Anwendung (Äußerungen, Absichten, Entitäten) überprüfen, um die Entwickler der App über notwendige Änderungen zu informieren, verfügen aber nicht über den direkten Zugriff, um diese Änderungen durchzuführen.


:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API-Zugriff**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        * Lesen von Äußerungen
        * Absichten 
        * Entitäten
        * Testen der Anwendung
    :::column-end:::
    :::column span="":::
      Alle GET-APIs unter: 
        * [Programmgesteuerte LUIS-APIs v3.0 (Vorschau)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [Programmgesteuerte LUIS-APIs v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

      Alle APIs unter: 
        * [LUIS-Endpunkt-APIs v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
        * [LUIS-Endpunkt-APIs v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
        * [LUIS-Endpunkt-APIs v3.0 (Vorschau)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)

      Alle Batchtest-Web-APIs
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Cognitive Services: LUIS-Writer

Ein Benutzer, der als Projektmitarbeiter in einem größeren Team für das Erstellen und Ändern der LUIS-Anwendung verantwortlich ist. Der Projektmitarbeiter kann die LUIS-Anwendung auf beliebige Weise ändern, diese Änderungen trainieren und sie im Portal überprüfen/testen. Dieser Benutzer hätte jedoch keinen Zugriff auf die Bereitstellung dieser Anwendung für die Runtime, da er seine Änderungen versehentlich in einer Produktionsumgebung widerspiegeln könnte. Sie wären auch nicht in der Lage, die Anwendung zu löschen oder ihre Vorhersageressourcen und Endpunkteinstellungen zu ändern (Zuweisung oder Aufhebung der Zuweisung von Vorhersageressourcen, Veröffentlichung des Endpunkts). Dadurch wird verhindert, dass diese Rolle eine Anwendung ändert, die derzeit in einer Produktionsumgebung verwendet wird. Sie können auch neue Anwendungen unter dieser Ressource erstellen, jedoch mit den erwähnten Einschränkungen.

:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API-Zugriff**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Alle Funktionen unter Cognitive Services LUIS-Reader. 

      Die Fähigkeit zum Hinzufügen: 
        * Äußerungen
        * Absichten
        * Entitäten
    :::column-end:::
    :::column span="":::
      * Alle APIs unter LUIS-Reader

      Alle POST-, PUT- und DELETE-APIs unter:

        * [Programmgesteuerte LUIS-APIs v3.0 (Vorschau)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [Programmgesteuerte LUIS-APIs v2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

        Ausnahme:
        * [Löschen der Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
        * [Verschieben der App in eine andere Azure-Ressource für die LUIS-Erstellung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
        * [Veröffentlichen einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
        * [Einstellungen für die Updateanwendung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
        * [Zuweisen von LUIS-Azure-Konten zu einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
        * [Entfernen zugewiesener LUIS-Azure-Konten aus einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Cognitive Services: LUIS-Besitzer

Diese Benutzer sind die Gatekeeper für LUIS-Anwendungen in einer Produktionsumgebung. Sie sollten uneingeschränkten Zugriff auf alle zugrundeliegenden Funktionen haben und somit alles in der Anwendung sehen können und direkten Zugriff auf alle Änderungen sowohl für die Erstellungs- als auch die Laufzeitumgebung haben.

:::row:::
    :::column span="":::
        **Funktion**
    :::column-end:::
    :::column span="":::
        **API-Zugriff**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Alle Funktionen unter Cognitive Services LUIS-Writer
      * Bereitstellen eines Modells
      * Löschen einer Anwendung
    :::column-end:::
    :::column span="":::
      * Alle für LUIS verfügbaren APIs
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Azure-Ressourcen](/azure/cognitive-services/luis/luis-how-to-azure-subscription?branch=pr-en-us-171715&tabs=portal#authoring-resource)
