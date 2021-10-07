---
title: Bereitstellung und Konfiguration des direkten Azure-Routings mit Azure Communication Services
description: Erfahren Sie, wie Sie einen Sitzungsgrenzcontroller hinzufügen und das Sprachrouting für das Azure Communication Services Routing konfigurieren
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: b6fa8523a347f9191c607ce3ba50b32f2d088886
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636019"
---
# <a name="session-border-controllers-and-voice-routing"></a>Unterstützte Sitzungsgrenzcontroller (SBCs) und Sprachrouting
Mit dem Azure Communication Services direktem Routing können Sie Ihre vorhandene Infrastruktur der Telefonie mit Azure verbinden. In diesem Artikel werden die Schritte aufgeführt, die auf hoher Ebene zum Verbinden eines unterstützten Session Border Controller (SBC) zum direkten Routing erforderlich sind. Außerdem wird erläutert, wie das Sprachrouting für die aktivierte Communication-Ressource funktioniert. 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
Informationen dazu, ob Azure Communication Services direktes Routing die richtige Lösung für Ihr Unternehmen ist, finden Sie unter [Azure-Telefoniekonzepte](./telephony-concept.md). Informationen zu den Voraussetzungen und zur Planung Ihrer Bereitstellung finden Sie unter [Anforderungen an die Infrastruktur für das direkte Routing in Communication Services](./direct-routing-infrastructure.md).

## <a name="connect-the-sbc-with-azure-communication-services"></a>Verbinden von SBC mit Azure Communication Services

### <a name="configure-using-azure-portal"></a>Konfigurieren über das Azure-Portal 
1. Wählen Sie im linken Navigationsbereich unter Voice Calling – PSTN die Option Direktes Routing und dann auf der Registerkarte Session Border Controller die Option Konfigurieren aus.
1. Geben Sie einen vollqualifizierten Domänennamen und einen Signalport für den SBC ein.
 
Wenn Sie Office 365 verwenden, stellen Sie sicher, dass sich der Domänenteil des FQDN des SBC von der Domäne, die Sie im Office 365-Verwaltungsportal registriert haben, unter Domänen unterscheidet. 
- Wenn beispielsweise `contoso.com` eine registrierte Domäne in O365 ist, können Sie `sbc.contoso.com` nicht für Communication Services verwenden. Sie können jedoch eine Domäne der obersten Ebene verwenden, wenn sie in O365 nicht vorhanden ist: Sie können eine `acs.contoso.com`-Domäne erstellen und den FQDN `sbc.acs.contoso.com` als SBC-Namen verwenden.
- Das SBC-Zertifikat muss mit dem Namen übereinstimmen. Platzhalterzertifikate werden unterstützt.
- Die Domäne *.onmicrosoft.com kann nicht für den FQDN des SBC verwendet werden.
Die vollständige Liste der Anforderungen finden Sie unter [Infrastruktur für das direkte Routing in Azure](./direct-routing-infrastructure.md).

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="Hinzufügen eines Session Border Controllers":::.
- Klicken Sie abschließend auf Weiter.
Wenn alles ordnungsgemäß eingerichtet ist, sollte der Austausch von OPTIONS-Nachrichten zwischen Microsoft und Ihrem Session Border Controller angezeigt werden. Verwenden Sie ihre SBC-Überwachung/-Protokolle, um die Verbindung zu überprüfen.

## <a name="voice-routing-considerations"></a>Überlegungen zum Sprachrouting

Das direkte Routing von Azure Communication Services verfügt über einen Routingmechanismus, mit dem ein Anruf basierend auf dem angerufenen Zahlenmuster an einen bestimmten Session Border Controller (SBC) gesendet werden kann.
Wenn Sie einer Ressource eine Konfiguration für direktes Routing hinzufügen, versuchen alle Anrufe, die von den Instanzen (Identitäten) dieser Ressource vorgenommen werden, zuerst einen Trunk für direktes Routing. Das Routing basiert auf einer gewählten Nummer und einer Übereinstimmung in Sprachrouten, die für die Ressource konfiguriert sind. Wenn es eine Übereinstimmung gibt, durchläuft der Anruf den direkten Routing-Trunk. Wenn keine Übereinstimmung vorgibt, besteht der nächste Schritt im Verarbeiten des alternateCallerId-Parameters der callAgent.startCall-Methode. Wenn die Ressource für Voice Calling (PSTN) aktiviert ist und mindestens eine Nummer von Microsoft erworben wurde und die alternateCallerId einer der erworbenen Nummern für die Ressource entspricht, wird der Anruf über den Sprachanruf (PSTN) mithilfe der Microsoft-Infrastruktur geroutet. Wenn der alternateCallerId-Parameter nicht mit einer der erworbenen Nummern übereinstimmen sollte, wird der Anruf nicht ausgeführt. Das folgende Diagramm veranschaulicht die Sprachrouting-Logik des Azure Communication Services.

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="Ausgehendes Sprachrouting von Communication Services":::.

## <a name="voice-routing-examples"></a>Beispiele für das Sprachrouting
In den folgenden Beispielen wird das Sprachrouting in einem Anruffluss angezeigt.

> [!NOTE]
> In allen Beispielen werden die SBCs in einer Route nach dem Zufallsprinzip ausprobiert, während die höhere Sprachroute eine höhere Priorität hat.

### <a name="one-route-example"></a>Ein Beispiel für eine Route:
Wenn Sie eine Sprachroute mit einem Muster `^\+1(425|206)(\d{7})$` erstellt und ihr `sbc1.contoso.biz` und `sbc2.contoso.biz` hinzugefügt haben, wird der Anruf, wenn der Benutzer ihn an `+1 425 XXX XX XX` oder `+1 206 XXX XX XX` tätigt, zunächst an SBC`sbc1.contoso.biz` oder `sbc2.contoso.biz` weitergeleitet. Wenn kein SBC verfügbar ist, wird der Anruf verworfen.

### <a name="two-routes-example"></a>Eine Beispiel für zwei Routen:
Wenn Sie eine Sprachroute mit einem Muster `^\+1(425|206)(\d{7})$` erstellt und mit `sbc1.contoso.biz` und `sbc2.contoso.biz` ergänzt haben, und dann eine zweite Route mit demselben Muster mit `sbc3.contoso.biz` und `sbc4.contoso.biz` erstellt haben. Wenn der Benutzer in diesem Fall einen Anruf an `+1 425 XXX XX XX` oder `+1 206 XXX XX XX` tätigt, wird der Aufruf zuerst an SBC `sbc1.contoso.biz` oder `sbc2.contoso.biz` geroutet. Wenn sowohl der SBC 1 als auch der SBC 2 nicht verfügbar sind, wird versucht, die Route mit einer niedrigerer Priorität (`sbc3.contoso.biz` und `sbc4.contoso.biz`) verwenden. Wenn keine der SBCs der zweiten Route verfügbar ist, wird der Aufruf verworfen.

### <a name="three-routes-example"></a>Eine Beispiel für drei Routen:
Wenn Sie eine Sprachroute mit einem Muster `^\+1(425|206)(\d{7})$` erstellt und mit `sbc1.contoso.biz` und `sbc2.contoso.biz` ergänzt haben, und dann eine zweite Route mit demselben Muster mit `sbc3.contoso.biz` und `sbc4.contoso.biz` sowie eine dritte Route mit `^+1(\d[10])$` und `sbc5.contoso.biz` erstellt haben. Wenn der Benutzer in diesem Fall einen Anruf an `+1 425 XXX XX XX` oder `+1 206 XXX XX XX` tätigt, wird der Aufruf zuerst an SBC `sbc1.contoso.biz` oder `sbc2.contoso.biz` geroutet. Wenn sowohl der SBC 1 als auch der SBC 2 nicht verfügbar sind, wird versucht, die Route mit einer niedrigerer Priorität (`sbc3.contoso.biz` und `sbc4.contoso.biz`) verwenden. Wenn keiner der SBCs einer zweiten Route verfügbar ist, wird die dritte Route ausprobiert. Wenn der SBC 5 ebenfalls nicht verfügbar ist, wird der Aufruf verworfen. Außerdem, wenn ein Benutzer `+1 321 XXX XX XX` wählt, geht der Anruf an `sbc5.contoso.biz` und wenn es nicht verfügbar ist, wird der Anruf verworfen.

> [!NOTE]
> Failover zum nächsten SBC im Sprachrouting funktioniert nur für die Antwortcodes 408, 503 und 504.

> [!NOTE]
> Wenn die gewählte Nummer in keinem der Beispiele dem Muster entspricht, wird der Anruf verworfen. Das gilt nicht, wenn eine erworbene Nummer für die Kommunikationsressource vorhanden ist und diese Nummer als `alternateCallerId` in der Anwendung verwendet wurde. 

## <a name="configure-voice-routing"></a>Konfigurieren des Sprachroutings 

### <a name="configure-using-azure-portal"></a>Konfigurieren über das Azure-Portal

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="Konfigurieren des ausgehenden Sprachroutings von Communication Services":::.

Geben Sie Ihrer Voice Route einen Namen, geben Sie das Zahlenmuster mit regulären Ausdrücken an, und wählen Sie einen SBC für dieses Muster aus. Im Folgenden werden Beispiele für einfache reguläre Ausdrücke dargestellt:
- `^\+\d+$` – passt zu einer Telefonnummer mit einer oder mehreren Ziffern, die mit einem Pluszeichen beginnt
- `^+1(\d[10])$` – passt zu einer Telefonnummer mit zehn Ziffern nach einer `+1`
- `^\+1(425|206)(\d{7})$` – passt zu einer Telefonnummer, die mit `+1425` oder mit `+1206` beginnt, gefolgt von sieben Ziffern
- `^\+0?1234$` – passt zu beiden `+01234` und `+1234` Telefonnummern.

Weitere Informationen zu regulären Ausdrücken finden Sie in der Datei [.NET-Übersicht zu regulären Ausdrücken](/dotnet/standard/base-types/regular-expressions).

Sie können mehrere SBCs für ein einzelnes Muster auswählen. In einem solchen Fall wählt der Routingalgorithmus sie in einer zufälliger Reihenfolge aus. Sie können das genaue Zahlenmuster auch mehr als einmal angeben. Die höhere Zeile hat eine höhere Priorität, und wenn alle SBCs, die dieser Zeile zugeordnet sind, nicht verfügbar sind, wird die nächste Zeile ausgewählt. Auf diese Weise erstellen Sie komplexe Routing-Szenarien.

## <a name="delete-direct-routing-configuration"></a>Löschen einer direkten Routingkonfiguration

### <a name="delete-using-azure-portal"></a>Löschen mit dem Azure-Portal

#### <a name="to-delete-a-voice-route"></a>So löschen Sie eine Voice Route:
1. Navigieren Sie im linken Navigationsbereich unter „Voice Calling – PSTN“ zu „Direktes Routing“ und wählen Sie dann die Registerkarte „Voice Routes“ aus.
1. Wählen Sie die Route oder die Routen, die Sie löschen möchten, mithilfe eines Kontrollkästchens aus.
1. Wählen Sie „Entfernen“ aus.

#### <a name="to-delete-an-sbc"></a>So löschen Sie einen Session Border Controller:
1. Navigieren Sie im linken Navigationsbereich unter „Voice Calling – PSTN“ zu „Direktes Routing“.
1. Wählen Sie auf der Registerkarte „Session Border Controller“ die Option „Konfigurieren“ aus.
1. Löschen Sie die Felder „FQDN“ und „Port“ für den SBC, den Sie entfernen möchten, und wählen Sie dann „Weiter“ aus.
1. Überprüfen Sie auf der Registerkarte „Voice Routes“ die Konfiguration des Sprachroutings, und nehmen Sie bei Bedarf Änderungen vor. Wählen Sie „Speichern“ aus.

> [!NOTE]
> Wenn Sie einen SBC entfernen, der einer Sprachroute zugeordnet ist, können Sie auf der Registerkarte „Voice Routes“ einen anderen SBC für die Route auswählen. Die Sprachroute ohne SBC wird dann gelöscht.

## <a name="next-steps"></a>Nächste Schritte

### <a name="conceptual-documentation"></a>Dokumentation

- [Für das direkte Routing von Azure Communication Services zertifizierte Session Border Controller](./certified-session-border-controllers.md)
- [Preise](../pricing.md)

### <a name="quickstarts"></a>Schnellstarts

- [Schnellstart: Auf Telefon anrufen](../../quickstarts/voice-video-calling/pstn-call.md)
