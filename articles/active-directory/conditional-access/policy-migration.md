---
title: 'Migrieren von Richtlinien für bedingten Zugriff: Azure Active Directory'
description: Wichtige Informationen zum Migrieren klassischer Richtlinien in das Azure-Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88244a527825a8cbaaa81dea64dd0c2f49a5fb72
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618170"
---
# <a name="conditional-access-classic-policy-migration"></a>Migration der klassischen Richtlinie für bedingten Zugriff

Der bedingte Zugriff ist das Tool, das von Azure Active Directory verwendet wird, um Signale zusammenzuführen, Entscheidungen zu treffen und Organisationsrichtlinien zu erzwingen. Der bedingte Zugriff ist der Kern der neuen identitätsbasierten Steuerungsebene. Während der Zweck immer noch derselbe ist, hat die Veröffentlichung des neuen Azure-Portals bedeutende Verbesserungen bei der Funktionsweise des bedingten Zugriffs mit sich gebracht.

Aus den folgenden Gründen sollten Sie eine Migration der Richtlinien in Erwägung ziehen, die Sie nicht im Azure-Portal erstellt haben:

- Sie können jetzt Szenarien berücksichtigen, die bisher nicht behandelt werden konnten.
- Sie können die Anzahl der zu verwaltenden Richtlinien reduzieren, indem Sie diese zusammenführen.
- Sie können alle Richtlinien für bedingten Zugriff zentral verwalten.
- Das klassische Azure-Portal wird eingestellt.

In diesem Artikel erhalten Sie wichtige Informationen zum Migrieren Ihrer vorhandenen Richtlinien für bedingten Zugriff zum neuen Framework.

## <a name="classic-policies"></a>Klassische Richtlinien

Im [Azure-Portal](https://portal.azure.com) finden Sie Richtlinien für bedingten Zugriff unter **Azure Active Directory** > **Sicherheit**  > **Bedingter Zugriff**. In Ihrer Organisation können auch ältere Richtlinien für bedingten Zugriff vorhanden sein, die nicht mithilfe dieser Seite erstellt wurden. Diese Richtlinien werden als *klassische Richtlinien* bezeichnet. Klassische Richtlinien sind Richtlinien für bedingten Zugriff, die Sie an diesen Orten erstellt haben:

- Klassisches Azure-Portal
- Klassisches Intune-Portal
- Intune-App-Schutz-Portal

Auf der Seite **Bedingter Zugriff** können Sie auf Ihre klassischen Richtlinien zugreifen, indem Sie im Bereich **Verwalten** auf [**Klassische Richtlinien**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) klicken. 

![Bedingter Zugriff in Azure AD mit der Ansicht für klassische Richtlinien](./media/policy-migration/71.png)

Die Ansicht **Klassische Richtlinien** bietet Optionen für diese Aktionen:

- Filtern Ihrer klassischen Richtlinien.
- Deaktivieren von klassischen Richtlinien.
- Überprüfen Sie die Einstellungen einer klassischen Richtlinie, und deaktivieren Sie sie.

   ![Details der klassischen Richtlinie einschließlich der vorhandenen Richtlinienkonfiguration](./media/policy-migration/74.png)

> [!WARNING]
> Nach der Deaktivierung kann eine klassische Richtlinie nicht erneut aktiviert werden.

Die Detailansicht einer klassischen Richtlinie ermöglicht es Ihnen, die Einstellungen zu dokumentieren, die enthaltenen oder ausgeschlossenen Gruppen zu ändern und die Richtlinie zu deaktivieren.

![Richtliniendetails: einzuschließende oder auszuschließende Gruppen](./media/policy-migration/75.png)

Indem Sie die ausgewählten Gruppen ändern oder bestimmte Gruppen ausschließen, können Sie die Wirkung einer deaktivierten klassischen Richtlinie an einigen Testbenutzern ausprobieren, bevor Sie die Richtlinie für alle enthaltenen Benutzer und Gruppen deaktivieren.
 
## <a name="migration-considerations"></a>Überlegungen zur Migration

In diesen Artikel werden Azure AD-Richtlinien für bedingten Zugriff auch als *neue Richtlinien* bezeichnet.
Ihre klassischen Richtlinien funktionieren weiterhin parallel zu Ihren neuen Richtlinien, bis Sie sie deaktivieren oder löschen. 

Die folgenden Aspekte sind im Zusammenhang mit der Konsolidierung von Richtlinien wichtig:

- Während klassische Richtlinien an eine bestimmte Cloud-App gebunden sind, können Sie in einer neuen Richtlinie beliebig viele Cloud-Apps auswählen.
- In den Steuerelementen einer klassischen Richtlinie müssen in Verbindung mit einer neuen Richtlinie für eine Cloud-Apps alle Steuerelemente (*UND*) erfüllt sein. 
- Eine neue Richtlinie eröffnet Ihnen folgende Möglichkeiten:
   - Kombinieren mehrerer Bedingungen, wenn das für Ihr Szenario erforderlich ist. 
   - Auswählen mehrerer Erteilungsanforderungen als Zugriffssteuerung, die mit logischem *ODER* (eins der ausgewählten Steuerelemente muss erfüllt sein) oder mit logischen *UND* (alle aktivierten Steuerelemente müssen erfüllt sein) kombiniert werden können.

### <a name="exchange-online"></a>Exchange Online

Wenn Sie klassische Richtlinien für **Exchange Online** migrieren möchten, die **Exchange Active Sync** als Bedingung für Client-Apps beinhalten, ist eine Konsolidierung in einer neuen Richtlinie eventuell nicht möglich. 

Dies ist beispielsweise dann der Fall, wenn Sie alle Typen von Client-Apps unterstützen möchten. In einer neuen Richtlinie, die **Exchange Active Sync** als Bedingung für Client-Apps beinhaltet, können keine weiteren Client-Apps ausgewählt werden.

![Bedingter Zugriff: Auswählen von Client-Apps](./media/policy-migration/64.png)

Eine Konsolidierung in einer neuen Richtlinie ist auch dann nicht möglich, wenn Ihre klassischen Richtlinien mehrere Bedingungen enthalten. Eine neue Richtlinie, in der **Exchange Active Sync** als Bedingung für Client-Apps konfiguriert ist, unterstützt keine weiteren Bedingungen:   

![Exchange ActiveSync unterstützt die ausgewählten Bedingungen nicht](./media/policy-migration/08.png)

Wenn Sie über eine neue Richtlinie verfügen, in der **Exchange Active Sync** als Client-App-Bedingung konfiguriert ist, müssen Sie sich vergewissern, dass keine weiteren Bedingungen konfiguriert sind. 

![Bedingungen für bedingten Zugriff](./media/policy-migration/16.png)
 
App-basierte klassische Richtlinien für Exchange Online, die **Exchange Active Sync** als Bedingung für Client-Apps enthalten, lassen **unterstützte** und **nicht unterstützte** Geräteplattformen zu. Zwar können in einer zugeordneten neuen Richtlinie keine einzelnen Geräteplattformen konfiguriert werden, jedoch können Sie die Unterstützung auf [unterstützte Geräteplattformen](concept-conditional-access-conditions.md#device-platforms) einschränken. 

![Bedingter Zugriff: Auswählen von Exchange ActiveSync](./media/policy-migration/65.png)

Sie können mehrere klassische Richtlinien konsolidieren, die **Exchange Active Sync** als Bedingung für Client-Apps enthalten, wenn diese Punkte zutreffen:

- Nur **Exchange Active Sync** ist als Bedingung festgelegt 
- Es sind mehrere Anforderungen für das Erteilen des Zugriffs konfiguriert

Ein häufiges Szenario ist die Konsolidierung von:

- einer gerätebasierten klassischen Richtlinie aus dem klassischen Azure-Portal 
- einer App-basierten klassischen Richtlinie in Intune-App-Schutz-Portal 
 
In diesem Fall können Sie Ihre klassischen Richtlinien in einer neuen Richtlinie konsolidieren, für die beide Anforderungen aktiviert sind.

![Bedingter Zugriff: Gewähren der Steuerung](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Geräteplattformen

Klassische Richtlinien mit App-basierten Steuerelementen sind bereits mit iOS und Android als Bedingung für die Geräteplattform konfiguriert. 

In einer neuen Richtlinie müssen Sie die zu unterstützenden [Geräteplattformen](concept-conditional-access-conditions.md#device-platforms) einzeln auswählen.

![Bedingter Zugriff: Auswahl der Geräteplattformen](./media/policy-migration/41.png)

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden des Modus „Nur Bericht“ für bedingten Zugriff zum Ermitteln der Auswirkungen neuer Richtlinienentscheidungen](concept-conditional-access-report-only.md)
- Wenn Sie wissen möchten, wie Sie eine Richtlinie für bedingten Zugriff konfigurieren, finden Sie weitere Informationen unter [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md).
- Wenn Sie bereit sind, Richtlinien für bedingten Zugriff für Ihre Umgebung zu konfigurieren, lesen Sie den Artikel [Vorgehensweise: Planen Ihrer Bereitstellung für bedingten Zugriff in Azure Active Directory](plan-conditional-access.md). 
