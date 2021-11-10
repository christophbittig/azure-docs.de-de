---
title: Behandeln von Problemen mit Überwachungsdaten bei Änderung der überprüften Domäne | Microsoft-Dokumentation
description: Erfahren Sie, welche Informationen in den Azure Active Directory-Aktivitätsprotokollen angezeigt werden, wenn Sie die überprüfte Domäne eines Benutzers ändern.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18c25f183d996ceb0829cad6d16633be98aa0707
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995709"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>Problembehandlung: Überprüfen von Daten bei der Änderung einer verifizierten Domäne 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>Ich habe viele Änderungen bei meinen Benutzern und bin mir nicht sicher, was die Ursache dafür ist.

### <a name="symptoms"></a>Symptome

Wenn ich die Azure AD-Überwachungsprotokolle überprüfe, sehe ich mehrere Benutzerupdates in meinem Azure AD-Mandanten. Diese Ereignisse vom Typ **Benutzer aktualisieren** enthalten keine Informationen zum **Akteur**, sodass ich nicht genau weiß, wer oder was die Massenänderungen an den Benutzern ausgelöst hat. 

### <a name="cause"></a>Ursache

 Ein häufiger Grund für Massenänderungen an Objekten ist ein nicht synchroner Back-End-Vorgang namens **ProxyCalc**.  **ProxyCalc** ist die Logik, mit der der richtige **UserPrincipalName** (Benutzerprinzipalname) und die **Proxyadressen** ermittelt werden, die in Azure AD-Benutzern, -Gruppen oder -Kontakten aktualisiert werden. Die Idee hinter **ProxyCalc** besteht darin, sicherzustellen, dass alle **UserPrincipalName**-Werte und **Proxyadressen** in Azure AD jederzeit konsistent sind. **ProxyCalc** muss durch eine explizite Änderung (z. B. der überprüften Domäne) ausgelöst werden und wird nicht dauerhaft als Task im Hintergrund ausgeführt. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>Was bedeutet Konsistenz beim Benutzerprinzipalnamen? 

Für reine Cloudbenutzer bedeutet Konsistenz, dass **UserPrincipalName** auf das Suffix einer überprüften Domäne festgelegt ist. Wenn ein inkonsistenter **UserPrincipalName** verarbeitet wird, konvertiert **ProxyCalc** ihn in das Standardsuffix „onmicrosoft.com“, z. B. username@Contoso.onmicrosoft.com. 

Bei synchronisierten Benutzern bedeutet Konsistenz, dass **UserPrincipalName** auf das Suffix einer überprüften Domäne festgelegt ist und mit dem lokalen Wert von **UserPrincipalName** (ShadowUserPrincipalName) übereinstimmt. Wenn ein inkonsistenter **UserPrincipalName** verarbeitet wird, setzt **ProxyCalc** diesen auf denselben Wert zurück wie der **ShadowUserPrincipalName** oder – wenn das Domänensuffix aus dem Mandanten entfernt wurde – konvertiert ihn in das standardmäßige „*.onmicrosoft.com“. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>Was bedeutet die Konsistenz bei Proxyadressen? 

Für reine Cloudbenutzer bedeutet Konsistenz, dass die Proxyadressen dem Suffix einer überprüften Domäne entsprechen. Wenn eine inkonsistente Proxyadresse verarbeitet wird, konvertiert **ProxyCalc** sie in das Standardsuffix „*.onmicrosoft.com“, z. B. SMTP:username@Contoso.onmicrosoft.com. 

Bei synchronisierten Benutzern bedeutet Konsistenz, dass die Proxyadressen den lokalen Proxyadressen entsprechen (d. h. ShadowProxyAddresses). Es wird erwartet, dass **ProxyAddresses** mit **ShadowProxyAddresses** synchron ist. Wenn dem synchronisierten Benutzer eine Exchange-Lizenz zugewiesen ist, müssen die Proxyadressen mit den Werten der lokalen Proxyadressen identisch sein. Außerdem müssen sie mit dem Suffix einer überprüften Domäne übereinstimmen. In diesem Szenario bereinigt **ProxyCalc** die inkonsistente Proxyadresse mit dem Suffix einer nicht überprüften Domäne und entfernt sie aus dem Objekt in Azure AD. Wenn diese nicht überprüfte Domäne später verifiziert wird, führt **ProxyCalc** eine Neuberechnung durch und fügt die Proxyadresse aus **ShadowProxyAddresses** wieder dem Objekt in Azure AD hinzu.  

> [!NOTE]
> Damit **ProxyCalc** bei synchronisierten Objekten keine unerwarteten Ergebnisse berechnet, sollten die Proxyadressen auf eine von Azure AD überprüfte Domäne im lokalen Objekt festgelegt werden.  

  
Eine der Verwaltungsaufgaben, die immer **ProxyCalc** auslöst, ist die Änderung einer überprüften Domäne. Diese Aufgabe tritt jedes Mal auf, wenn eine überprüfte Domäne in einem Azure AD-Mandanten hinzugefügt bzw. daraus entfernt wird. Dabei wird intern immer **ProxyCalc** ausgelöst.  

Wenn Sie Ihrem Mandanten „Contoso.onmicrosoft.com“ beispielsweise die überprüfte Domäne „Fabrikam.com“ hinzufügen, löst diese Aktion einen ProxyCalc-Vorgang für alle Objekte im Mandanten aus. Dieses Ereignis wird in den Azure AD-Überwachungsprotokollen als Ereignistyp **Benutzer aktualisieren** aufgezeichnet, dem ein Ereignis vom Typ **Überprüfte Domäne hinzufügen** vorausgeht. Wenn „Fabrikam.com“ andererseits aus dem Mandanten „Contoso.onmicrosoft.com“ entfernt wird, geht allen Ereignissen **Benutzer aktualisieren** ein Ereignis **Überprüfte Domäne entfernen** voraus.   

#### <a name="additional-notes"></a>Weitere Hinweise:

ProxyCalc bewirkt keine Änderungen an bestimmten Objekten, für die Folgendes gilt: 

- Sie verfügen nicht über eine aktive Exchange-Lizenz. 
- **MSExchRemoteRecipientType** ist auf NULL festgelegt. 
- Sie werden nicht als freigegebene Ressource betrachtet. Bei einer freigegebenen Ressource enthält **CloudMSExchRecipientDisplayType** einen der folgenden Werte: **MailboxUser (shared)** , **PublicFolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Group mailbox**, **Scheduling mailbox**, **ACLableMailboxUser** oder **ACLableTeamMailboxUser**. 
  
 Um mehr Korrelation zwischen diesen beiden unterschiedlichen Ereignissen zu erzielen, arbeitet Microsoft daran, die Informationen zum **Akteur** in den Überwachungsprotokollen zu aktualisieren, damit für diese Änderungen erkennbar ist, dass sie durch die Änderung einer überprüften Domäne ausgelöst wurden. Dies hilft bei der Überprüfung, ob eine Änderung an einer überprüften Domäne erfolgt ist und zu den Massenänderungen an den Objekte im Mandanten geführt hat. 

Außerdem gibt es in den meisten Fällen keine Änderungen an den Benutzern, da ihr **UserPrincipalName** und ihre **Proxyadressen** konsistent sind. Daher arbeiten wir daran, in den Überwachungsprotokollen nur die Updates anzuzeigen, die eine tatsächliche Änderung am Objekt verursacht haben. Dadurch wird das „Rauschen“ in den Überwachungsprotokollen vermindert, sodass Administratoren die verbleibenden Benutzeränderungen mit dem Änderungsereignis an der überprüften Domäne wie oben erläutert korrelieren können. 

## <a name="next-steps"></a>Nächste Schritte

[Schattenattribute für den Azure AD Connect-Synchronisierungsdienst](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
