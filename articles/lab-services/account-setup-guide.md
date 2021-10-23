---
title: Leitfaden für beschleunigte Lab-Konto-Einrichtung für Azure Lab Services
description: Dieser Leitfaden unterstützt Administratoren beim schnellen Einrichten eines Lab-Kontos für die Verwendung innerhalb ihrer Bildungseinrichtung.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8b233464da7e6a5c5529e0057240d839282faf46
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177344"
---
# <a name="lab-account-setup-guide"></a>Einrichtungsleitfaden für Lab-Konten
Als Administrator müssen Sie vor dem Einrichten Ihrer Azure Lab Services-Umgebung zuerst ein *Lab-Konto* in Ihrem Azure-Abonnement erstellen. Ein Lab-Konto ist ein Container für eines oder mehrere Labs, dessen Einrichtung nur wenige Minuten in Anspruch nimmt.

Dieser Leitfaden umfasst drei Abschnitte:
-  Voraussetzungen
-  Planen der Einstellungen für das Lab-Konto
-  Einrichten Ihres Lab-Kontos

## <a name="prerequisites"></a>Voraussetzungen
In den folgenden Abschnitten wird erläutert, was Sie tun müssen, bevor Sie ein Lab-Konto einrichten können.


### <a name="access-your-azure-subscription"></a>Zugreifen auf Ihr Azure-Abonnement
Zum Erstellen eines Lab-Kontos benötigen Sie Zugriff auf ein Azure-Abonnement, das bereits für Ihre Schule eingerichtet wurde. Ihre Bildungseinrichtung kann über ein Abonnement oder mehrere Abonnements verfügen. Mit einem Abonnement verwalten Sie Abrechnung und Sicherheit für Ihre sämtlichen Azure-Ressourcen und -Dienste. Hierzu zählen auch Lab-Konten.  Azure-Abonnements werden in der Regel von Ihrer IT-Abteilung verwaltet.  Weitere Informationen finden Sie im Abschnitt „Abonnement“ im [Azure Lab Services-Administratorhandbuch](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Schätzen der benötigten VMs und VM-Größen
Sie müssen wissen, wie viele [virtuelle Computer (VMs) und welche VM-Größen](./administrator-guide.md#vm-sizing) das Lab Ihrer Bildungseinrichtung benötigt. 

Anleitungen zum Strukturieren von Labs und Images finden Sie im Blogbeitrag [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) (Von einem physischen Lab zu Azure Lab Services).

Weitere Anleitungen zum Strukturieren von Labs finden Sie im Abschnitt „Lab“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Grundlegendes zu VM-Einschränkungen und zur regionalen VM-Kapazität
Nachdem Sie die Anzahl der benötigten VMs sowie die erforderlichen VM-Größen für Ihre Labs geschätzt haben, müssen Sie folgendermaßen fortfahren:

- Stellen Sie sicher, dass die Kapazitätseinschränkung Ihres Azure-Abonnements die von Ihnen für die Verwendung in Ihren Labs geplante Anzahl von VMs und VM-Größen zulässt.
- Erstellen Sie ein Lab-Konto innerhalb einer Region, in der ausreichend VM-Kapazität verfügbar ist.

Weitere Informationen finden Sie unter [VM Subscription Limits and Regional Capacity](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553) (VM-Abonnementeinschränkungen und regionale Kapazität).

### <a name="decide-how-many-lab-accounts-to-create"></a>Festlegen der Anzahl zu erstellender Lab-Konten

Erstellen Sie ein einzelnes Lab-Konto innerhalb seiner eigenen Ressourcengruppe, um schnell loslegen zu können.  Später können Sie nach Bedarf weitere Lab-Konten und Ressourcengruppen erstellen. Beispielsweise bietet es sich möglicherweise an, ein Lab-Konto und eine Ressourcengruppe pro Abteilung zu erstellen, um so die Kosten klar zu trennen. 

Weitere Informationen zu Lab-Konten, Ressourcengruppen und die Kostenaufteilung finden Sie unter:
- Abschnitt „Ressourcengruppe“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#resource-group)
- Abschnitt „Lab-Konto“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#lab-account) 
- [Kostenverwaltung für Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planen der Einstellungen für das Lab-Konto

Zum Planen der Einstellungen für das Lab-Konto stellen Sie sich die folgenden Fragen:

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Wer sollten Besitzer und Mitwirkende für das Lab-Konto sein?

Die IT-Administratoren Ihrer Bildungseinrichtung besitzen normalerweise die Rollen „Besitzer“ und „Mitwirkender“ für ein Lab-Konto. Diese Rollen sind dafür verantwortlich, die Richtlinien zu verwalten, die für alle Labs im Lab-Konto gelten. Die Person, die das Lab-Konto erstellt, ist automatisch Besitzer des Kontos. Sie können weitere Besitzer und Mitwirkende aus dem mit Ihrem Abonnement verknüpften Azure AD-Mandanten (Azure Active Directory) hinzufügen. 

Weitere Informationen zu den Rollen „Besitzer“ und „Mitwirkender“ für Lab-Konten finden Sie im Abschnitt „Verwalten der Identität“ im [Azure Lab Services-Administratorhandbuch](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Lab-Benutzern wird innerhalb von Azure Lab Services nur eine einzelne Liste der VMs angezeigt, auf die sie über Azure AD-Mandanten hinweg Zugriff haben.

### <a name="who-will-be-allowed-to-create-labs"></a>Wer ist berechtigt, Labs zu erstellen?

Sie können festlegen, dass Ihr IT-Team oder Lehrkräfte Labs erstellen können. Zum Erstellen von Labs weisen Sie diese Personen dann der Rolle „Lab-Ersteller“ im Lab-Konto zu. Normalerweise weisen Sie diese Rolle aus dem Azure AD-Mandanten zu, der dem Abonnement Ihrer Bildungseinrichtung zugeordnet ist. Wenn ein Benutzer ein Lab erstellt, wird ihm automatisch die Rolle „Besitzer“ für das Lab zugewiesen.  

Weitere Informationen zur Rolle „Lab-Ersteller“ finden Sie im Abschnitt „Verwalten der Identität“ im [Azure Lab Services-Administratorhandbuch](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Wer ist berechtigt, Labs zu besitzen und zu verwalten?

Sie können auch festlegen, dass IT-Mitarbeiter und Lehrkräfte Labs besitzen bzw. verwalten können, *ohne* dass sie zum Erstellen von Labs berechtigt sind.  In diesem Fall wird Benutzern aus dem Azure AD-Mandanten Ihres Abonnements für vorhandene Labs entweder die Rolle Besitzer oder die Rolle Mitwirkender zugewiesen.  

Weitere Informationen zu den Rollen „Besitzer“ und „Mitwirkender“ für Labs finden Sie im Abschnitt „Verwalten der Identität“ im [Azure Lab Services-Administratorhandbuch](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Möchten Sie Images speichern und in verschiedenen Labs gemeinsam verwenden?

Shared Image Gallery ist ein Dienst, den Sie zum Speichern und Freigeben von Images verwenden können. Wenn Klassen dasselbe Image verwenden müssen, können Lab-Ersteller das Image erstellen und dann in Shared Image Gallery exportieren.  Nachdem ein Image in Shared Image Gallery exportiert wurde, kann es zum Erstellen neuer Labs verwendet werden.

Sie sollten die Images in Ihrer physischen Umgebung erstellen und dann in Shared Image Gallery importieren. Weitere Informationen finden Sie im Blogbeitrag [Import a Custom Image in a Shared Image Gallery](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353) (Importieren eines benutzerdefinierten Images in Shared Image Gallery).

Wenn Sie Shared Image Gallery verwenden möchten, müssen Sie eine Shared Image Gallery-Instanz erstellen oder an Ihr Lab-Konto anfügen. Sie können diese Entscheidung vorerst aufschieben, da Shared Image Gallery jederzeit an ein Lab-Konto angefügt werden kann.  

Weitere Informationen finden Sie unter:
- Abschnitt „Shared Image Gallery“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#shared-image-gallery)
- Abschnitt „Preise“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Welche Images in Azure Marketplace werden von Ihren Labs verwendet?

Azure Marketplace bietet Hunderte von Images, die Sie aktivieren können, damit Lab-Ersteller sie zum Erstellen des Labs verwenden können. Einige Images enthalten möglicherweise alles, was für ein Lab benötigt wird. In anderen Fällen können Sie ein Image als Ausgangspunkt verwenden, das der Lab-Ersteller durch die Installation zusätzlicher Anwendungen oder Tools anpassen kann.

Wenn Sie nicht wissen, welche Images Sie benötigen, können Sie diese auch später noch aktivieren. Sie können am besten feststellen, welche Images verfügbar sind, indem Sie zuerst ein Lab-Konto erstellen. Dadurch erhalten Sie Zugriff auf die Liste der verfügbaren Images und deren Inhalte und können diese prüfen.  

Weitere Informationen finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Benötigen die Lab-VMs Zugriff auf andere Azure- oder lokale Ressourcen?

Wenn Sie ein Lab-Konto einrichten, können Sie auch ein Peering mit einem virtuellen Netzwerk durchführen.  Beachten Sie, dass sich das virtuelle Netzwerk und das Lab-Konto in derselben Region befinden müssen.  Berücksichtigen Sie bei der Entscheidung, ob ein Peering mit einem virtuellen Netzwerk erforderlich ist, die folgenden Szenarien:

- **Zugriff auf einen Lizenzserver**
  
   Wenn Sie Azure Marketplace-Images verwenden, werden die Kosten für die Betriebssystemlizenz in den Preisen für Lab-Dienste gebündelt. Jedoch müssen Sie keine Lizenzen für das Betriebssystem selbst bereitstellen. Bei Installation zusätzlicher Software und Anwendungen müssen Sie ggf. eine entsprechende Lizenz bereitstellen.  So greifen Sie auf einen Lizenzserver zu
   - Sie können sich dazu entscheiden, eine Verbindung mit einem lokalen Lizenzserver herzustellen.  Zum Herstellen einer Verbindung mit einem lokalen Lizenzserver ist eine zusätzliche Einrichtung erforderlich.
   - Eine weitere Option, die schneller eingerichtet werden kann, ist das Erstellen eines Lizenzservers, den Sie auf einer Azure-VM hosten.  Die Azure-VM befindet sich in einem virtuellen Netzwerk, das Sie mittels Peering mit Ihrem Lab-Konto verknüpfen.

- **Zugriff auf andere lokale Ressourcen wie eine Dateifreigabe oder eine Datenbank**

   Sie erstellen in der Regel ein virtuelles Netzwerk, um den Zugriff auf lokale Ressourcen zu ermöglichen, über ein virtuelles Site-to-Site-Netzwerkgateway. Das Einrichten dieser Art von Umgebung nimmt zusätzliche Zeit in Anspruch.

- **Zugriff auf andere Azure-Ressourcen außerhalb eines virtuellen Netzwerks**

   Wenn Sie Zugriff auf Azure-Ressourcen benötigen, die *nicht* innerhalb eines virtuellen Netzwerks gesichert sind, können Sie über das öffentliche Internet darauf zugreifen, ohne ein Peering durchführen zu müssen.

   Weitere Informationen zu virtuellen Netzwerken finden Sie unter:
   - Abschnitt „Virtuelles Netzwerk“ in [Grundlegendes zur Architektur in Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Verbinden des Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk in Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Erstellen eines Labs mit einer freigegebenen Ressource in Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Einrichten Ihres Lab-Kontos

Nachdem Sie die Planung abgeschlossen haben, können Sie Ihr Lab-Konto einrichten. Sie können die gleichen Schritte auch für das Einrichten von [Azure Lab Services in Teams](./lab-services-within-teams-overview.md) anwenden.

1. **Erstellen Ihres Lab-Kontos**. Eine entsprechende Anleitung finden Sie unter [Erstellen eines Lab-Kontos](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Weitere Informationen zu Benennungskonventionen finden Sie im Abschnitt „Benennung“ des [Azure Lab Services-Administratorhandbuchs](./administrator-guide.md#naming).

1. **Hinzufügen von Benutzern zur Rolle „Lab-Ersteller“** . Anweisungen hierzu finden Sie unter [Hinzufügen von Benutzern zur Rolle „Lab-Ersteller“](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Herstellen einer Verbindung mit einem virtuellen Peernetzwerk**. Eine Anleitung finden Sie unter [Verbinden des Netzwerks Ihres Labs mit einem virtuellen Peernetzwerk](./how-to-connect-peer-virtual-network.md).

   Möglicherweise benötigen Sie auch die Anweisungen zum [Konfigurieren des Adressbereichs der Lab-VMs](./how-to-configure-lab-accounts.md).

1. **Aktivieren und Überprüfen von Images**. Anweisungen finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).

   Um die Inhalte der einzelnen Azure Marketplace-Images zu überprüfen, wählen Sie den Imagenamen aus. Im folgenden Screenshot werden beispielsweise die Details zum Image „Ubuntu Data Science VM“ gezeigt:

   ![Screenshot einer Liste der verfügbaren Images in Azure Marketplace](./media/setup-guide/review-marketplace-images.png)

   Wenn Sie eine Shared Image Gallery-Instanz an Ihr Lab-Konto angefügt haben und benutzerdefinierte Images für Lab-Ersteller freigeben möchten, müssen Sie ähnliche Schritte wie im folgenden Screenshot gezeigt durchführen:

   ![Screenshot einer Liste von aktivierten benutzerdefinierten Images in einer Shared Image Gallery](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten und Verwalten von Labs finden Sie unter:

- [Verwalten von Lab-Konten](how-to-manage-lab-accounts.md)  
- [Leitfaden für die Lab-Einrichtung](setup-guide.md)
