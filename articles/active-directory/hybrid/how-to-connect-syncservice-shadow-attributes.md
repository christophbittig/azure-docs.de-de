---
title: Schattenattribute für den Azure AD Connect-Synchronisierungsdienst | Microsoft-Dokumentation
description: Beschreibt die Funktionsweise von Schattenattributen im Azure AD Connect-Synchronisierungsdienst.
services: active-directory
author: billmath
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/29/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b2274099803961fb477f0929c801e2fc341dd4b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355273"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Schattenattribute für den Azure AD Connect-Synchronisierungsdienst
Die meisten Attribute werden in Azure AD genauso wie in Ihrem lokalen Azure Directory dargestellt. Einige Attribute werden jedoch besonders verarbeitet, weshalb sich der Attributwert in Azure AD davon unterscheiden kann, was mit Azure AD Connect synchronisiert wird.

## <a name="introducing-shadow-attributes"></a>Was sind Schattenattribute?
Für einige Attribute gibt es in Azure AD zwei Darstellungen. Sowohl der lokale Wert als auch ein berechneter Wert werden gespeichert. Diese zusätzlichen Attribute werden Schattenattribute genannt. Die beiden gängigsten Attribute, bei denen dieses Verhalten erkennbar ist, heißen **userPrincipalName** und **proxyAddress**. Attributwerte werden geändert, wenn Werte in diesen Attributen vorhanden sind, die nicht bestätigte Domänen darstellen. Doch das Synchronisierungsmodul in Azure AD Connect liest den Wert im Schattenattribut aus seiner Perspektive dergestalt, dass Attribut von Azure AD bestätigt wurde.

Die Schattenattribute können nicht im Azure-Portal oder mit PowerShell angezeigt werden. Doch das Verstehen des Konzepts hilft, Probleme in bestimmten Szenarien zu beheben, in denen das Attribut lokal und in der Cloud unterschiedliche Werte aufweist.

Um das Verhalten besser zu verstehen, betrachten Sie dieses Beispiel für Fabrikam:  
![Der Screenshot zeigt das Active Directory-UPN-Suffix für mehrere Beispiele mit dem entsprechenden Azure AD-Domänenwert „Nicht hinzugefügt“, „Nicht überprüft“ und „Überprüft“.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Das Unternehmen hat mehrere UPN-Suffixe in seinem lokalen Active Directory, von denen aber nur eines bestätigt ist.

### <a name="userprincipalname"></a>userPrincipalName
Ein Benutzer hat die folgenden Attributwerte in einer nicht bestätigten Domäne:

| attribute | value |
| --- | --- |
| „userPrincipalName“ lokal | lee.sperry@fabrikam.com |
| „shadowUserPrincipalName“ in Azure AD | lee.sperry@fabrikam.com |
| „userPrincipalName“ in Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

Das Attribut „userPrincipalName“ ist der Wert, den Sie bei Verwenden von PowerShell sehen.

Da der tatsächliche lokale Attributwert in Azure AD gespeichert ist, aktualisiert Azure AD beim Bestätigen der Domäne „fabrikam.com“ das Attribut „userPrincipalName“ mit dem Wert von „shadowUserPrincipalName“. Sie müssen keine aus Azure AD Connect stammenden Änderungen synchronisieren, damit diese Werte aktualisiert werden.

### <a name="proxyaddresses"></a>proxyAddresses
Dasselbe Verfahren zum ausschließlichen Einbeziehen bestätigter Domänen erfolgt auch für „proxyAddresses“, jedoch mit zusätzlicher Logik. Die Suche nach bestätigten Domänen erfolgt nur für Postfachbenutzer. Ein E-Mail-aktivierter Benutzer oder Kontakt stellt einen Benutzer in einer anderen Exchange-Organisation dar. In „proxyAddresses“ können Sie diesen Objekten beliebige Werte hinzufügen.

Für einen Postfachbenutzer, ob lokal oder in Exchange Online, werden nur Werte für bestätigte Domänen angezeigt. Diese können wie folgt aussehen:

| attribute | value |
| --- | --- |
| „proxyAddresses“ lokal | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| „proxyAddresses“ in Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

In diesem Fall wurde **smtp:abbie.spencer\@fabrikam.com** entfernt, da diese Domäne nicht bestätigt wurde. Doch Exchange hat auch **SIP:abbie.spencer\@fabrikamonline.com** hinzugefügt. Fabrikam hat Lync/Skype nicht lokal genutzt, doch Azure AD und Exchange Online bereiten sich darauf vor.

Diese Logik für „proxyAddresses“ wird als **ProxyCalc** bezeichnet. „ProxyCalc“ wird bei jeder Änderung für einen Benutzer aufgerufen, wenn Folgendes gilt:

- Dem Benutzer wurde ein Serviceplan zugewiesen, der Exchange Online enthält, auch wenn der Benutzer nicht für Exchange lizenziert wurde. Beispiel: Dem Benutzer wurde die SKU Office E3 zugewiesen, doch ihm wurde nur SharePoint Online zugewiesen. Diese Bedingung gilt auch, wenn Ihr Postfach weiterhin lokal ist.
- Das Attribut „msExchRecipientTypeDetails“ hat einen Wert.
- Sie nehmen eine Änderung an „proxyAddresses“ oder „userPrincipalName“ vor.

ProxyCalc bereinigt eine Adresse, wenn ShadowProxyAddresses eine nicht verifizierte Domäne enthält und für den Cloudbenutzer eine der folgenden Eigenschaften konfiguriert ist. 
- Der Benutzer ist mit einem aktivierten EXO-Diensttypplan lizenziert (mit Ausnahme von MyAnalytics).  
- Für den Benutzer wurde „MSExchRemoteRecipientType“ festgelegt (nicht NULL).  
- Der Benutzer wird als freigegebene Ressource betrachtet.

Um als freigegebene Ressource betrachtet zu werden, muss für den Cloudbenutzer einer der folgenden Werte in „CloudMSExchRecipientDisplayType“ festgelegt werden. 

 |Objektanzeigetyp|Wert (dezimal)|
 |-----|-----|
 |MailboxUser|  0|
 |DistributionGroup|    1|
 |PublicFolder| 2|
 |DynamicDistributionGroup| 3|
 |Organization| 4|
 |PrivateDistributionList|  5|
 |RemoteMailUser|   6|
 |ConferenceRoomMailbox|    7|
 |EquipmentMailbox| 8|
 |ArbitrationMailbox|   10|
 |MailboxPlan|  11|
 |LinkedUser|   12|
 |RoomList| 15|
 |SyncedMailboxUser|    -2147483642|
 |SyncedUDGasUDG|   -2147483391|
 |SyncedUDGasContact|   -2147483386|
 |SyncedPublicFolder|   -2147483130|
 |SyncedDynamicDistributionGroup|   -2147482874|
 |SyncedRemoteMailUser| -2147482106|
 |SyncedConferenceRoomMailbox|  -2147481850|
 |SyncedEquipmentMailbox|   -2147481594|
 |SyncedUSGasUDG|   -2147481343|
 |SyncedUSGasContact|   -2147481338|
 |ACLableSyncedMailboxUser| -1073741818|
 |ACLableSyncedRemoteMailUser|  -1073740282|
 |ACLableSyncedUSGasContact|    -1073739514|
 |SyncedUSGasUSG|   -1073739511|
 |SecurityDistributionGroup|    1043741833|
 |SyncedUSGasUSG|   1073739511|
 |ACLableSyncedUSGasContact|    1073739514|
 |RBAC Role Group|  1073741824|
 |ACLableMailboxUser|   1073741824|
 |ACLableRemoteMailUser|    1073741830|


>[!NOTE]
> „CloudMSExchRecipientDisplayType“ ist auf der Azure AD-Seite nicht sichtbar und kann nur mithilfe des Exchange Online-Cmdlets [Get-Recipient](/powershell/module/exchange/get-recipient) oder einem ähnlichen Tool angezeigt werden.  
>
>Beispiel:
> ```PowerShell
>   Get-Recipient admin | fl *type*
> ```
>

„ProxyCalc“ benötigt eine gewisse Zeit zum Verarbeiten einer Änderung für einen Benutzer und ist nicht synchron mit dem Azure AD Connect-Exportvorgang.

> [!NOTE]
> Die „ProxyCalc“-Logik weist verschiedene zusätzliche Verhaltensweisen für erweiterte Szenarien auf, die in diesem Artikel nicht dokumentiert werden. Dieses Thema dient zum Erläutern des Verhaltens und nicht zum Dokumentieren der gesamten internen Logik.

### <a name="quarantined-attribute-values"></a>Isolierte Attributwerte
Schattenattribute werden auch verwendet, wenn Attributwerte doppelt vorhanden sind. Weitere Informationen finden Sie unter [Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Weitere Informationen
* [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
