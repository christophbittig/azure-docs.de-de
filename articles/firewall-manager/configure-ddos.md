---
title: Konfigurieren eines Plans für Azure DDoS Protection mithilfe von Azure Firewall Manager
description: Hier erfahren Sie, wie Sie Azure Firewall Manager zum Konfigurieren eines Plans für Azure DDoS Protection Standard verwenden.
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368054"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>Konfigurieren eines Plans für Azure DDoS Protection mithilfe von Azure Firewall Manager (Vorschau)

Azure Firewall Manager ist eine Plattform zum bedarfsorientierten Verwalten und Schützen Ihrer Netzwerkressourcen. Sie können Ihren virtuellen Netzwerken einen DDoS-Schutzplan in Azure Firewall Manager zuordnen.

> [!IMPORTANT]
> Die Verwendung von Azure Firewall Manager zum Konfigurieren eines Plans für Azure DDoS Protection befindet sich derzeit in der Vorschauphase.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind. 

> [!TIP]
> DDoS Protection Standard unterstützt derzeit keine virtuellen WANs. Sie können diese Einschränkung jedoch umgehen, indem Sie das Tunneln von Internetdatenverkehr an Azure Firewall in einem virtuellen Netzwerk erzwingen, dem ein DDoS-Schutzplan zugeordnet ist.

In einem einzelnen Mandanten können DDoS-Schutzpläne auf virtuelle Netzwerke in mehreren Abonnements angewendet werden. Weitere Informationen zu DDoS-Schutzplänen finden Sie unter [Übersicht über Azure DDoS Protection Standard](../ddos-protection/ddos-protection-overview.md).

Zum Verstehen der Funktionsweise erstellen Sie eine Firewallrichtlinie und dann ein virtuelles Netzwerk, das mit Azure Firewall geschützt ist. Anschließend erstellen Sie einen DDoS-Schutzplan und ordnen ihn dem virtuellen Netzwerk zu.

## <a name="create-a-firewall-policy"></a>Erstellen einer Firewallrichtlinie

Verwenden Sie Firewall Manager, um eine Firewallrichtlinie zu erstellen.

1. Öffnen Sie Firewall Manager über das [Azure-Portal](https://portal.azure.com).
1. Wählen Sie **Azure Firewall-Richtlinien** aus.
1. Wählen Sie die Option **Azure-Firewallrichtlinie erstellen** aus.
1. Wählen Sie für **Ressourcengruppe** die Gruppe **DDoS-Test-rg** aus.
1. Geben Sie unter **Richtliniendetails** für **Name** die Bezeichnung **fw-pol-01** ein.
1. Wählen Sie unter **Region** die Option **USA, Westen 2** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.


## <a name="create-a-secured-virtual-network"></a>Erstellen eines geschützten virtuellen Netzwerks

Verwenden Sie Firewall Manager, um ein geschütztes virtuelles Netzwerk zu erstellen.

1. Öffnen Sie Firewall Manager.
1. Wählen Sie **Virtuelle Netzwerke** aus.
1. Wählen Sie **Neues geschütztes virtuelles Netzwerk erstellen** aus.
1. Wählen Sie für **Ressourcengruppe** die Gruppe **DDoS-Test-rg** aus.
1. Wählen Sie unter **Region** die Option **USA, Westen 2** aus.
1. Geben Sie für **Name des Hub-VNET** die Bezeichnung **Hub-vnet-01** ein.
1. Geben Sie unter **Adressbereich** die Zeichenfolge **10.0.0.0/16** ein.
1. Wählen Sie **Weiter: Azure Firewall** aus.
1. Wählen Sie unter **Öffentliche IP-Adresse** die Option **Neu hinzufügen** aus, geben Sie als Name die Zeichenfolge **fw-pip** ein, und klicken Sie auf **OK**.
1. Geben Sie unter **Firewallsubnetz-Adressraum** die Zeichenfolge **10.0.0.0/24** ein.
1. Wählen Sie **fw-pol-01** für die **Firewallrichtlinie** aus.
1. Wählen Sie **Weiter: Überprüfen + erstellen**.
1. Wählen Sie **Erstellen** aus.

## <a name="create-a-ddos-protection-plan"></a>Erstellen eines DDoS-Schutzplans

Erstellen Sie mithilfe von Firewall Manager einen DDoS-Schutzplan. Auf der Seite **DDoS-Schutzpläne** können Sie Ihre Pläne für Azure DDoS Protection erstellen und verwalten.

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="Screenshot der Seite „DDoS-Schutzpläne“ im Firewall Manager":::

1. Öffnen Sie Firewall Manager.
1. Wählen Sie **DDoS-Schutzpläne** aus.
1. Wählen Sie **Erstellen** aus.
1. Wählen Sie für **Ressourcengruppe** die Option **Neu erstellen** aus.
1. Geben Sie **DDos-Test-rg** als Ressourcengruppennamen ein.
1. Geben Sie unter **Instanzendetails** für **Name** die Bezeichnung **DDoS-plan-01** ein.
1. Wählen Sie unter **Region** die Option **(USA) USA, Westen 2** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

## <a name="associate-a-ddos-protection-plan"></a>Zuordnen eines DDoS-Schutzplans

Nun können Sie den DDoS-Schutzplan dem geschützten virtuellen Netzwerk zuordnen.

1. Öffnen Sie Firewall Manager.
1. Wählen Sie **Virtuelle Netzwerke** aus.
1. Aktivieren Sie das Kontrollkästchen für **Hub-vnet-01**.
1. Wählen Sie **Sicherheit verwalten** und dann **DDoS-Schutzplan hinzufügen** aus.
1. Wählen Sie für **DDoS Protection Standard** die Option **Aktivieren** aus.
1. Wählen Sie unter **DDoS-Schutzplan** die Option **DDoS-plan-01** aus.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Aktualisieren** aus.

Sie sollten nun sehen, dass dem virtuellen Netzwerk ein DDoS-Schutzplan zugeordnet ist.

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="Screenshot des virtuellen Netzwerks mit DDoS-Schutzplan":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu DDoS-Schutzplänen finden Sie im folgenden Artikel:

- [Übersicht über Azure DDoS Protection Standard](../ddos-protection/ddos-protection-overview.md)