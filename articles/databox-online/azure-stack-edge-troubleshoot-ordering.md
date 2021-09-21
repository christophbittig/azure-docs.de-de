---
title: Behandeln von Problemen mit Azure Stack Edge-Bestellungen über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Azure Stack Edge-Bestellprobleme behandelt werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 46a7d0ed41b8f10874c19c8d70ddc5d8c5d7f197
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608285"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>Behandeln von Problemen bei Azure Stack Edge-Bestellungen

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Azure Stack Edge-Bestellprobleme behandelt werden.

## <a name="unsupported-subscription-or-region"></a>Das Abonnement oder die Region wird nicht unterstützt

**Fehlerbeschreibung**: Sie erhalten die folgende Fehlermeldung im Azure-Portal:

*Das ausgewählte Abonnement oder die ausgewählte Region wird nicht unterstützt. Wählen Sie ein anderes Abonnement oder eine andere Region aus.*

![Das Abonnement oder die Region wird nicht unterstützt](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Vorgeschlagene Lösung**:  Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt. Weitere Informationen finden Sie unter [Ressourcenvoraussetzungen für Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Microsoft ermöglicht auf Einzelfallbasis gegebenenfalls ein Upgrade des Abonnementtyps. Wenden Sie sich an den [Microsoft-Support](https://azure.microsoft.com/support/options/), um Ihre Anforderungen zu besprechen und diese Grenzwerte entsprechend anzupassen.

## <a name="selected-subscription-type-not-supported"></a>Der ausgewählte Abonnementtyp wird nicht unterstützt

**Fehler:** Sie verfügen über ein EA-, CSP- oder gesponsertes Abonnement und erhalten die folgende Fehlermeldung:

*Der ausgewählte Abonnementtyp wird nicht unterstützt. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwenden. [Weitere Informationen](azure-stack-edge-deploy-prep.md#prerequisites) Wenn Sie einen unterstützten Abonnementtyp verwenden, stellen Sie folgende Punkte sicher:
 
- Der `Microsoft.DataBoxEdge`-Anbieter muss registriert sein, wenn Aufträge über das klassische Portal aufgegeben werden.
- Der `Microsoft.EdgeOrder`-Anbieter muss registriert sein, wenn Aufträge über Azure Edge Hardware Center (Vorschauversion) aufgegeben werden.
 
Informationen zur Registrierung finden Sie unter [Registrieren des Ressourcenanbieters](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Vorgeschlagene Lösung**: Führen Sie diese Schritte aus, um Ihren Azure Stack Edge-Ressourcenanbieter zu registrieren:

1. Navigieren Sie im Azure-Portal zu **Start** > **Abonnements**.

2. Wählen Sie das Abonnement aus, das Sie zum Bestellen Ihres Geräts verwenden möchten.

3. Wählen Sie **Ressourcenanbieter** aus, und suchen Sie dann nach **Microsoft.DataBoxEdge**.

    ![Registrieren des Ressourcenanbieters](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Wenn Sie nicht über die Zugriffsberechtigungen eines Besitzers oder Mitwirkenden zum Registrieren des Ressourcenanbieters verfügen, erhalten Sie die folgende Fehlermeldung: *Das Abonnement &lt;Abonnementname&gt; ist nicht berechtigt, die folgenden Ressourcenanbieter zu registrieren: Microsoft.DataBoxEdge.*

Weitere Informationen finden Sie unter [Registrieren von Ressourcenanbietern](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="resource-provider-not-registered-for-subscription"></a>Nicht für Abonnements registrierte Ressourcenanbieter

**Fehler:** Sie wählen im Azure-Portal ein Abonnement aus, um es für Azure Stack Edge oder Azure Data Box Gateway zu verwenden, und einer der folgenden Fehler tritt auf:

*Der Ressourcenanbieter „Microsoft.DataBoxEdge“ ist für das Abonnement &lt;Abonnementname&gt; nicht registriert, und Sie verfügen nicht über die Berechtigungen zum Registrieren eines Ressourcenanbieters für das Abonnement &lt;Abonnementname&gt;* .

*Resource provider(s): Microsoft.EdgeOrder are not registered for subscription &lt;subscription name&gt; and you don't have permissions to register a resource provider for subscription &lt;subscription name&gt;* . (Ressourcenanbieter: Microsoft.EdgeOrder ist für das Abonnement <Abonnementname> nicht registriert, und Sie verfügen nicht über die Berechtigungen zum Registrieren eines Ressourcenanbieters für das Abonnement <Abonnementname>)

**Vorgeschlagene Lösung**: Erhöhen Sie Ihre Zugriffsberechtigungen für das Abonnement, oder bitten Sie einen Benutzer mit Besitzer- oder Mitwirkenderzugriff, den Ressourcenanbieter zu registrieren.

## <a name="resource-disallowed-by-policy"></a>Die Ressource ist aufgrund einer Richtlinie unzulässig

**Fehler:** Sie versuchen, im Azure-Portal einen Ressourcenanbieter zu registrieren, und erhalten die folgende Fehlermeldung:

*Die Ressource &lt;Ressourcenname&gt; wurde durch eine Richtlinie abgelehnt. (Code: RequestDisallowedByPolicy). Initiative: Allgemein unerwünschte Ressourcentypen ablehnen. Richtlinie: Nicht zulässige Ressourcentypen.*

**Vorgeschlagene Lösung**: Dieser Fehler tritt aufgrund einer vorhandenen Azure Policy-Zuweisung auf, durch die das Erstellen der Ressource blockiert wird. Azure Policy-Definitionen und -Zuweisungen werden vom Systemadministrator einer Organisation festgelegt, um die Einhaltung von Vorgaben beim Verwenden oder Erstellen von Azure-Ressourcen sicherzustellen. Wenn die Erstellung von Azure Stack Edge-Ressourcen durch eine solche Richtlinienzuweisung blockiert wird, bitten Sie Ihren Systemadministrator, Ihre Azure Policy-Definition anzupassen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Behandeln von Problemen mit Ihrem Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md).
