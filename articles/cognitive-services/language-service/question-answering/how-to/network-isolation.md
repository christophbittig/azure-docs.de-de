---
title: Netzwerkisolation und Private Link – Fragen und Antworten
description: Benutzer können den öffentlichen Zugriff auf Ressourcen für Fragen und Antworten einschränken.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 9ab30b82a9bac9d06e2d5228b2004c36d465d392
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458207"
---
#  <a name="network-isolation-and-private-endpoints"></a>Netzwerkisolation und private Endpunkte

In den folgenden Schritten wird beschrieben, wie Sie den öffentlichen Zugriff auf Ressourcen für Fragen und Antworten einschränken und Azure Private Link aktivieren. Schützen Sie eine Cognitive Services-Ressource durch [Konfigurieren des virtuellen Netzwerks](../../../cognitive-services-virtual-networks.md?tabs=portal) vor öffentlichem Zugriff.

## <a name="private-endpoints"></a>Private Endpunkte

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet.  „Fragen und Antworten“ bietet jetzt Unterstützung für das Erstellen privater Endpunkte im Azure Search-Dienst.

Private Endpunkte werden durch [Azure Private Link](../../../../private-link/private-link-overview.md) als separater Dienst bereitgestellt. Weitere Informationen zu den Kosten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="steps-to-enable-private-endpoint"></a>Schritte zum Aktivieren des privaten Endpunkts

1. Weisen Sie der Sprachressource die Rolle *Mitwirkender* zu (je nach Kontext kann dies als Textanalyseressource angezeigt werden) in der Azure Search-Dienstinstanz. Dieser Vorgang erfordert die Zugriffsberechtigungen der Rolle *Besitzer* für das Abonnement. Wechseln Sie zur Registerkarte „Identität“ in der Dienstressource, um die Identität abzurufen.

> [!div class="mx-imgBorder"]
> ![Textanalyse: Identität](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. Fügen Sie die oben genannte Identität als *Contributer* (Mitwirkender) hinzu, indem Sie zur Registerkarte „Zugriffssteuerung (IAM)“ des Azure Search-Diensts navigieren.

![IAM für verwalteten Dienst](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Wählen Sie *Rollenzuweisungen hinzufügen* aus, fügen Sie die Identität hinzu, und wählen Sie dann *Speichern* aus.

![Rollenzuweisung für verwalteten Dienst](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Navigieren Sie nun in der Azure Search-Dienstinstanz zur Registerkarte *Netzwerk*, und ändern Sie die Einstellung für „Endpunktkonnektivität (Daten)“ von *Öffentlich* in *Privat*. Dieser Vorgang ist ein zeitintensiver Prozess und kann bis zu 30 Minuten in Anspruch nehmen. 

![Registerkarte „Netzwerk“ für verwalteten Azure Search-Dienst](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Wechseln Sie zur Registerkarte *Netzwerk* der Sprachressource, und wählen Sie unter *Zugriff erlauben von* die Option *Ausgewählte Netzwerke und private Endpunkte* aus. Wählen Sie anschließend *Speichern* aus.
 
> [!div class="mx-imgBorder"]
> ![Textanalyse Netzwerk](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

Dadurch wird eine private Endpunktverbindung zwischen der Sprachressource und der Azure Cognitive Search-Dienstinstanz hergestellt. Sie können die private Endpunktverbindung auf der Registerkarte *Netzwerk* der Azure Cognitive Search-Dienstinstanz überprüfen. Sobald der gesamte Vorgang abgeschlossen ist, können Sie Ihre Sprachressource mit aktivierten Fragen und Antworten verwenden.

![Registerkarte „Netzwerk“ für verwalteten Dienst](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)

## <a name="support-details"></a>Details zur Unterstützung
 * Wir unterstützen keine Änderungen am Azure Cognitive Suchdienst, nachdem Sie den privaten Zugriff auf Ihren Textanalyse-Dienst aktiviert haben. Wenn Sie den Azure Cognitive Suchdienst über die Registerkarte "Funktionen" ändern, nachdem Sie den privaten Zugriff aktiviert haben, wird die Sprachen-Funktion unbrauchbar.

 * .Wenn Sie nach der Einrichtung der privaten Endpunktverbindung die Vernetzung des Azure Cognitive Search Service auf "Öffentlich" umstellen, können Sie die Sprachen-Funktion nicht mehr verwenden. Sie müssen für das Netzwerk des Azure Search-Diensts die Einstellung „Privat“ verwenden, damit die private Endpunktverbindung funktioniert.

## <a name="restrict-access-to-cognitive-search-resource"></a>Einschränken des Zugriffs auf Cognitive Search-Ressourcen

Führen Sie die folgenden Schritte aus, um den öffentlichen Zugriff auf Sprachressourcen für Fragen und Antworten einzuschränken. Schützen Sie eine Cognitive Services-Ressource durch [Konfigurieren des virtuellen Netzwerks](../../../cognitive-services-virtual-networks.md?tabs=portal) vor öffentlichem Zugriff.

Nachdem Sie den Zugriff auf Cognitive Service-Ressourcen basierend auf VNet eingeschränkt haben, können Sie die Wissensdatenbanken im Language Studio-Portal über Ihr lokales Netzwerk oder Ihren lokalen Browser durchsuchen.
- Gewähren Sie Zugriff auf das [lokale Netzwerk](../../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Gewähren Sie Zugriff auf Ihren [lokalen Browser/Computer](../../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Fügen Sie auf der Registerkarte **Netzwerk** im Abschnitt **Firewall** die öffentliche IP-Adresse des Computers hinzu. `portal.azure.com` zeigt standardmäßig die öffentliche IP-Adresse des aktuellen Browsercomputers an. Wählen Sie diesen Eintrag aus, und wählen Sie dann **Speichern** aus.

     > [!div class="mx-imgBorder"]
     > [ ![Screenshot der Benutzeroberfläche zur Konfiguration von Firewall und virtuellen Netzwerken]( ../../../qnamaker/media/network-isolation/firewall.png) ](  ../../../qnamaker/media/network-isolation/firewall.png#lightbox)
