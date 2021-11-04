---
title: Anfordern des Hostkontingents für Azure VMware Solution
description: Erfahren Sie, wie Sie das Hostkontingent bzw. die Hostkapazität für Azure VMware Solution anfordern. Sie können auch weitere Hosts in einer vorhandenen privaten Azure VMware Solution-Cloud anfordern.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 09/27/2021
ms.openlocfilehash: a95f152c5e55b1750aa34c9a4093e311eb27df29
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504491"
---
# <a name="request-host-quota-for-azure-vmware-solution"></a>Anfordern des Hostkontingents für Azure VMware Solution

In dieser Vorgehensweise fordern Sie das Hostkontingent bzw. die Hostkapazität für [Azure VMware Solution](introduction.md) an. Sie übermitteln ein Supportticket, damit Ihre Hosts zugeordnet werden, unabhängig davon, ob es sich um eine neue oder eine vorhandene Bereitstellung handelt. 

Wenn Sie über eine vorhandene private Azure VMware Solution-Cloud verfügen und weitere Hosts zugeordnet werden sollen, müssen Sie auf die gleiche Weise vorgehen.

>[!IMPORTANT]
>Das Zuordnen der Hosts kann abhängig von der angeforderten Anzahl bis zu fünf Werktage dauern.  Fordern Sie also an, was für die Bereitstellung erforderlich ist, damit weniger häufig eine Kontingenterhöhung angefordert werden muss.

## <a name="eligibility-criteria"></a>Wer kann Azure Government erwerben?

Sie benötigen ein Azure-Konto in einem Azure-Abonnement, das einem der folgenden Kriterien entspricht:

- Abonnement unter einem [Azure Konzernvertrag (Enterprise Agreement, EA)](../cost-management-billing/manage/ea-portal-agreements.md) mit Microsoft
- Von einem Cloudlösungsanbieter (Cloud Solution Provider, CSP) verwaltetes Abonnement unter einem CSP-Angebotsvertrag in Azure oder einem Azure-Plan
- [Eine Microsoft-Kundenvereinbarung](../cost-management-billing/understand/mca-overview.md) mit Microsoft

## <a name="request-host-quota-for-ea-customers"></a>Anfordern des Hostkontingents für EA-Kunden

1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen an:
   - **Problemtyp:** Technisch
   - **Abonnement:** Wählen Sie Ihr Abonnement aus.
   - **Dienst:** Alle Dienste > Azure VMware Solution
   - **Ressource:** Allgemeine Frage 
   - **Zusammenfassung:** Kapazität erforderlich
   - **Problemtyp:** Capacity Management Issues (Kapazitätsverwaltungsprobleme)
   - **Problemuntertyp:** Customer Request for Additional Host Quota/Capacity (Kundenanfrage für zusätzliches Hostkontingent/zusätzliche Hostkapazität)

1. Geben Sie in der **Beschreibung** des Supporttickets auf der Registerkarte **Details** folgende Informationen an:
 
   - Name der Region
   - Anzahl von Hosts
   - Weitere Details

   >[!NOTE]
   >Azure VMware Solution erfordert mindestens drei Hosts und empfiehlt eine Redundanz von N+1 Hosts. 

1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.


## <a name="request-host-quota-for-csp-customers"></a>Anfordern des Hostkontingents für CSP-Kunden 

CSPs müssen [Microsoft Partner Center](https://partner.microsoft.com) verwenden, um Azure VMware Solution für ihre Kunden zu aktivieren. In diesem Artikel wird der [CSP-Azure-Plan](/partner-center/azure-plan-lp) als Beispiel verwendet, um die Kaufabwicklung für Partner zu veranschaulichen.

Greifen Sie unter Verwendung der Prozedur **Administrator im Namen von** (Admin on Behalf Of, AOBO) von Partner Center aus auf das Azure-Portal zu.

>[!IMPORTANT] 
>Der Azure VMware Solution-Dienst bietet keine Mehrinstanzenfähigkeit. Hostingpartner, die dies erfordern, werden nicht unterstützt. 

1. Konfigurieren Sie den Azure CSP-Plan:

   1. Wählen Sie in **Partner Center** die Option **CSP** aus, um auf den Bereich **Kunden** zuzugreifen.
   
      :::image type="content" source="media/pre-deployment/csp-customers-screen.png" alt-text="Screenshot: Kundenbereich „Microsoft Partner Center“" lightbox="media/pre-deployment/csp-customers-screen.png":::
   
   1. Wählen Sie Ihren Kunden und anschließend **Produkte hinzufügen** aus.
   
      :::image type="content" source="media/pre-deployment/csp-partner-center.png" alt-text="Screenshot: Im Microsoft Partner Center ausgewählter Azure-Plan" lightbox="media/pre-deployment/csp-partner-center.png":::
   
   1. Wählen Sie **Azure-Plan** und anschließend **Zu Warenkorb hinzufügen** aus. 
   
   1. Überprüfen Sie die allgemeine Einrichtung des Abonnements mit Azure-Plan für Ihren Kunden, und schließen Sie sie ab. Weitere Informationen finden Sie in der [Dokumentation zu Microsoft Partner Center](/partner-center/azure-plan-manage).

1. Nachdem Sie den Azure-Plan konfiguriert und die erforderlichen [Azure RBAC-Berechtigungen](/partner-center/azure-plan-manage) für das Abonnement eingerichtet haben, fordern Sie das Kontingent für Ihr Abonnement mit Azure-Plan an. 

   1. Greifen Sie unter Verwendung der Prozedur **Administrator im Namen von** (Admin on Behalf Of, AOBO) von [Microsoft Partner Center](https://partner.microsoft.com) aus auf das Azure-Portal zu.
   
   1. Wählen Sie **CSP** aus, um auf den **Kundenbereich** zuzugreifen.
   
   1. Erweitern Sie die Kundendetails, und wählen Sie **Microsoft Azure-Verwaltungsportal** aus.
   
   1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen an:
      - **Problemtyp:** Technisch
      - **Abonnement:** Wählen Sie Ihr Abonnement aus.
      - **Dienst:** Alle Dienste > Azure VMware Solution
      - **Ressource:** Allgemeine Frage 
      - **Zusammenfassung:** Kapazität erforderlich
      - **Problemtyp:** Capacity Management Issues (Kapazitätsverwaltungsprobleme)
      - **Problemuntertyp:** Customer Request for Additional Host Quota/Capacity (Kundenanfrage für zusätzliches Hostkontingent/zusätzliche Hostkapazität)
   
   1. Geben Sie in der **Beschreibung** des Supporttickets auf der Registerkarte **Details** folgende Informationen an:
   
      - Name der Region
      - Anzahl von Hosts
      - Weitere Details
      - Sollen mehrere Kunden gehostet werden?
   
      >[!NOTE]
      >Azure VMware Solution erfordert mindestens drei Hosts und empfiehlt eine Redundanz von N+1 Hosts. 
   
   1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.


## <a name="next-steps"></a>Nächste Schritte

Vor der Bereitstellung von Azure VMware Solution müssen Sie zunächst den [Ressourcenanbieter mit Ihrem Abonnement registrieren](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider), um den Dienst zu aktivieren.   
