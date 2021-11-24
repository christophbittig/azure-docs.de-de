---
title: Empfehlungen zu bewährten Methoden für verwaltete Systemidentitäten
description: Empfehlungen zur Verwendung von benutzer- bzw. systemseitig zugewiesenen verwalteten Identitäten
services: active-directory
documentationcenter: ''
author: barclayn
manager: karenh444
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/09/2021
ms.author: barclayn
ms.openlocfilehash: 400fe7d940a97ddf9fb885302edd93c8780491f5
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134805"
---
# <a name="managed-identity-best-practice-recommendations"></a>Empfehlungen zu bewährten Methoden für verwaltete Identitäten

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="choosing-system-or-user-assigned-managed-identities"></a>Auswählen von system- oder benutzerseitig zugewiesenen verwalteten Identitäten

Vom Benutzer zugewiesene verwaltete Identitäten sind in einer größeren Bandbreite von Szenarien effizienter als vom System zugewiesene verwaltete Identitäten. In der folgenden Tabelle finden Sie einige Szenarien und Empfehlungen für benutzer- oder systemseitig zugewiesene Szenarien.

Vom Benutzer zugewiesene Identitäten können von mehreren Ressourcen verwendet werden, und ihre Lebenszyklen sind von den Lebenszyklen der Ressourcen, denen sie zugeordnet sind, abgekoppelt. [Erfahren Sie, welche Ressourcen verwaltete Identitäten unterstützen](./services-support-managed-identities.md).

Dank dieses Lebenszyklus können Sie Ihre Aufgaben bei der Ressourcenerstellung und der Identitätsverwaltung trennen. Vom Benutzer zugewiesene Identitäten und deren Rollenzuweisungen können vor den Ressourcen konfiguriert werden, für die sie erforderlich sind. Benutzer, welche die Ressourcen erstellen, benötigen nur den Zugriff zum Zuweisen einer benutzerseitig zugewiesenen Identität und brauchen keine neuen Identitäten oder Rollenzuweisungen zu erstellen.  

Da vom System zugewiesene Identitäten zusammen mit der Ressource erstellt und gelöscht werden, können Rollenzuweisungen nicht im voraus erstellt werden. Diese Abfolge kann beim Bereitstellen der Infrastruktur zu Fehlern führen, wenn der Benutzer, der die Ressource erstellt, nicht auch Zugriff zum Erstellen von Rollenzuweisungen hat.

Wenn es in Ihrer Infrastruktur erforderlich ist, dass mehrere Ressourcen auf die gleichen Ressourcen zugreifen müssen, kann ihnen eine einzelne vom Benutzer zugewiesene Identität zugewiesen werden. Der Verwaltungsaufwand wird reduziert, da weniger unterschiedliche Identitäten und Rollenzuweisungen verwaltet werden müssen.

Wenn jede Ressource über eine eigene Identität verfügen muss, oder wenn für Ressourcen ein eindeutiger Berechtigungssatz erforderlich ist und die Identität beim Löschen der Ressource ebenfalls gelöscht werden soll, sollten Sie eine vom System zugewiesene Identität verwenden.

| Szenario| Empfehlung|Anmerkungen|
|---|---|---|
| Schnelle Erstellung von Ressourcen (z. B. kurzlebiges Computing) mit verwalteten Identitäten |  Vom Benutzer zugewiesene Identität | Wenn Sie versuchen, mehrere verwaltete Identitäten in kurzer Zeit zu erstellen (z. B. bei der Bereitstellung mehrerer virtueller Computer mit jeweils eigener systemseitig zugewiesener Identität), überschreiten Sie möglicherweise den Grenzwert für die Erstellung von Azure Active Directory-Objekten, und bei der Anforderung tritt ein HTTP 429-Fehler auf. <br/><br/>Wenn Ressourcen schnell erstellt oder gelöscht werden und Sie vom System zugewiesene Identitäten verwenden, überschreiten Sie möglicherweise auch den Grenzwert für die Anzahl der Ressourcen in Azure Active Directory. Auch wenn keine Ressource mehr auf eine gelöschte vom System zugewiesene Identität zugreifen kann, wird diese doch solange auf den Grenzwert angerechnet, bis sie nach 30 Tagen vollständig gelöscht wird.<br/><br/>Beim Bereitstellen von Ressourcen, die einer einzelnen vom Benutzer zugewiesenen Identität zugeordnet sind, muss nur ein Dienstprinzipal in Azure Active Directory erstellt werden, wodurch ein Überschreiten des Grenzwerts vermieden wird. Die Verwendung einer einzelnen Identität, die im voraus erstellt wird, reduziert auch das Risiko von Replikationsverzögerungen, die auftreten können, wenn mehrere Ressourcen mit jeweils eigener Identität erstellt werden.<br/><br/>Erfahren Sie mehr über die [Grenzwerte für den Azure-Abonnementdienst.](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) |
| Replizierte Ressourcen/Anwendungen | Vom Benutzer zugewiesene Identität  |  Für Ressourcen, die dieselbe Aufgabe ausführen (z. B. duplizierte Webserver oder identische Funktionen, die in einem App-Dienst und einer Anwendung auf einem virtuellen Computer ausgeführt werden), sind in der Regel dieselben Berechtigungen erforderlich. <br/><br/>Durch die Verwendung derselben benutzerseitig zugewiesenen Identität sind weniger Rollenzuweisungen erforderlich, was den Verwaltungsaufwand senkt. Die Ressourcen müssen nicht vom selben Typ sein. 
|Kompatibilität| Vom Benutzer zugewiesene Identität  | Wenn in Ihrer Organisation die gesamte Identitätserstellung einen Genehmigungsprozess durchlaufen muss, sind bei Verwendung einer einzelnen vom Benutzer zugewiesenen Identität für mehrere Ressourcen weniger Genehmigungen erforderlich als bei vom System zugewiesenen Identitäten, die zusammen mit neuen Ressourcen erstellt werden. |
Zugriff vor dem Bereitstellen einer Ressource erforderlich |Vom Benutzer zugewiesene Identität| Einige Ressourcen benötigen möglicherweise im Rahmen ihrer Bereitstellung Zugriff auf bestimmte Azure-Ressourcen.<br/><br/>In diesem Fall wird eine vom System zugewiesene Identität möglicherweise nicht rechtzeitig erstellt. Daher sollte eine bereits vorhandene vom Benutzer zugewiesene Identität verwendet werden.|
Überwachungsprotokollierung|Vom System zugewiesene Identität|Wenn Sie protokollieren müssen, welche Ressource (und nicht welche Identität) eine Aktion ausgeführt hat, verwenden Sie eine vom System zugewiesene Identität.|
Lebenszyklusverwaltung für Berechtigungen|Vom System zugewiesene Identität|Wenn die Berechtigungen für eine Ressource zusammen mit der Ressource entfernt werden müssen, verwenden Sie eine vom System zugewiesene Identität.

### <a name="using-user-assigned-identities-to-reduce-administration"></a>Verwenden von benutzerseitig zugewiesenen Identitäten zur Reduzierung der Verwaltung

Die Diagramme veranschaulichen den Unterschied zwischen system- und benutzerseitig zugewiesenen Identitäten, wenn diese verwendet werden, um mehreren virtuellen Computern den Zugriff auf zwei Speicherkonten zu ermöglichen. 

Das Diagramm enthält vier virtuelle Computer mit systemseitig zugewiesenen Identitäten. Jeder virtuelle Computer verfügt über dieselben Rollenzuweisungen, die Zugriff auf zwei Speicherkonten erlauben.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities.png" alt-text="Vier virtuelle Computer, die vom System zugewiesene Identitäten für den Zugriff auf ein Speicherkonto und einen Schlüsseltresor verwenden.":::

Wenn den vier virtuellen Computern eine vom Benutzer zugewiesene Identität zugeordnet ist, sind nur zwei Rollenzuweisungen erforderlich (im Gegensatz zu acht Rollenzuweisungen bei vom System zugewiesenen Identitäten). Wenn für die Identität der virtuellen Computer mehr Rollenzuweisungen erforderlich sind, werden diese allen Ressourcen gewährt, die dieser Identität zugeordnet sind.

:::image type="content" source="media/managed-identity-best-practice-recommendations/user-assigned-identities.png" alt-text="Vier virtuelle Computer, die eine vom Benutzer zugewiesene Identität für den Zugriff auf ein Speicherkonto und einen Schlüsseltresor verwenden.":::

Sicherheitsgruppen können ebenfalls verwendet werden, um die Anzahl der erforderlichen Rollenzuweisungen zu reduzieren. Dieses Diagramm enthält vier virtuelle Computer mit vom System zugewiesenen Identitäten, die einer Sicherheitsgruppe hinzugefügt wurden, wobei die Rollenzuweisungen der Gruppe und nicht den vom System zugewiesenen Identitäten hinzugefügt wurden. Das Ergebnis ist zwar ähnlich, aber diese Konfiguration bietet nicht die gleichen Resource Manager-Vorlagenfunktionen wie vom Benutzer zugewiesene Identitäten.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-assigned-identities-in-a-group.png" alt-text="Vier virtuelle Computer mit vom System zugewiesenen Identitäten, die einer Sicherheitsgruppe mit Rollenzuweisungen hinzugefügt wurden.":::

### <a name="multiple-managed-identities"></a>Mehrere verwaltete Identitäten

Ressourcen, die verwaltete Identitäten unterstützen, können sowohl über eine systemseitig als auch über eine oder mehrere benutzerseitig zugewiesene Identitäten verfügen. 

Dieses Modell ist so flexibel, dass Sie sowohl eine gemeinsam verwendete, vom Benutzer zugewiesene Identität nutzen als auch bei Bedarf präzise Berechtigungen anwenden können.

Im folgenden Beispiel können „Virtueller Computer 3“ und „Virtueller Computer 4“ auf Speicherkonten und Schlüsseltresore zugreifen, je nachdem, welche benutzerseitig zugewiesene Identität sie bei der Authentifizierung verwenden.

:::image type="content" source="media/managed-identity-best-practice-recommendations/multiple-user-assigned-identities.png" alt-text="Vier virtuelle Computer, davon zwei mit mehreren benutzerseitig zugewiesenen Identitäten.":::

Im folgenden Beispiel verfügt „Virtueller Computer 4“ über eine vom Benutzer zugewiesene Identität, die Zugriff auf Speicherkonten und Schlüsseltresore erlaubt, je nachdem, welche Identität bei der Authentifizierung verwendet wird. Die Rollenzuweisungen für die vom System zugewiesene Identität sind speziell diesem virtuellen Computer zugewiesen.

:::image type="content" source="media/managed-identity-best-practice-recommendations/system-and-user-assigned-identities.png" alt-text="Vier virtuelle Computer, davon einer mit system- und benutzerseitig zugewiesenen Identitäten.":::

## <a name="limits"></a>Grenzwerte 

Informieren Sie sich über die Grenzwerte für [verwaltete Identitäten](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits) und über [benutzerdefinierte Rollen und Rollenzuweisungen](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-rbac-limits).

## <a name="follow-the-principle-of-least-privilege-when-granting-access"></a>Befolgen des Prinzips der geringsten Rechte beim Gewähren von Zugriff

Wenn Sie Identitäten, einschließlich einer verwalteten Identität, Berechtigungen für den Zugriff auf Dienste erteilen, erteilen Sie immer die geringsten Berechtigungen, die zum Ausführen der gewünschten Aktionen erforderlich sind. Wenn beispielsweise eine verwaltete Identität zum Lesen von Daten aus einem Speicherkonto verwendet wird, ist es nicht erforderlich, für diese Identität auch Berechtigungen zum Schreiben von Daten in das Speicherkonto zuzulassen. Durch das Erteilen zusätzlicher Berechtigungen wird die verwaltete Identität beispielsweise zu einem Mitwirkenden in einem Azure-Abonnement, wenn dies nicht erforderlich ist, was den Sicherheitsradius hinsichtlich der Identität erhöht. Der Sicherheitsradius muss immer minimiert werden, damit eine Kompromittierung dieser Identität nur zu geringen Schäden führt.

### <a name="consider-the-effect-of-assigning-managed-identities-to-azure-resources"></a>Berücksichtigen der Auswirkungen der Zuweisung verwalteter Identitäten zu Azure-Ressourcen

Beachten Sie, dass alle Berechtigungen, die der verwalteten Identität erteilt wurden, jetzt für die Azure-Ressource verfügbar sind, wenn einer Azure-Ressource, z. B. einer Azure-Logik-App, einer Azure-Funktion oder einem virtuellen Computer usw., eine verwaltete Identität zugewiesen wird. Dies ist besonders wichtig, denn wenn ein Benutzer Zugriff zum Installieren oder Ausführen von Code für diese Ressource hat, hat der Benutzer Zugriff auf alle Identitäten, die der Azure-Ressource zugewiesen/zugeordnet sind. Der Zweck der verwalteten Identität besteht darin, Code, der in einer Azure-Ressource ausgeführt wird, Zugriff auf andere Ressourcen zu geben, ohne dass Entwickler Anmeldeinformationen verarbeiten oder direkt im Code speichern müssen, um diesen Zugriff zu erhalten.

Wenn beispielsweise einer verwalteten Identität (ClientId = 1234) Lese-/Schreibzugriff für ***StorageAccount7755** _ erteilt wurde und sie _*_LogicApp3388_*_ zugewiesen wurde, kann Alice, die über keine direkten Berechtigungen für die verwaltete Identität oder das Speicherkonto verfügt, jedoch die Berechtigung zur Ausführung von Code innerhalb von _*_LogicApp3388_*_ hat, ebenfalls Daten aus bzw. in _ *_StorageAccount7755_** lesen bzw. schreiben, indem sie den Code ausführt, der die verwaltete Identität verwendet.

:::image type="content" source="media/managed-identity-best-practice-recommendations/security-considerations.png" alt-text="Sicherheitsszenario":::

Wenn Sie einem Benutzer Administratorzugriff auf eine Ressource gewähren, die Code ausführen kann (z. B. eine Logik-App) und über eine verwaltete Identität verfügt, sollten Sie im Allgemeinen überlegen, ob die dem Benutzer zugewiesene Rolle Code für die Ressource installieren oder ausführen kann, und wenn ja, sollten Sie diese Rolle nur zuweisen, wenn der Benutzer sie wirklich benötigt.


## <a name="maintenance"></a>Wartung

Vom System zugewiesene Identitäten werden automatisch gelöscht, wenn die Ressource gelöscht wird, während der Lebenszyklus einer vom Benutzer zugewiesenen Identität von keiner Ressource abhängig ist, der sie zugeordnet ist.

Sie müssen eine vom Benutzer zugewiesene Identität manuell löschen, wenn sie nicht mehr benötigt wird, selbst wenn ihr keine Ressourcen zugeordnet sind.

Rollenzuweisungen werden beim Löschen von system- oder benutzerseitig zugewiesenen verwalteten Identitäten nicht automatisch gelöscht. Diese Rollenzuweisungen sollten manuell gelöscht werden, damit der Grenzwert für Rollenzuweisungen pro Abonnement nicht überschritten wird. 

Rollenzuweisungen, die gelöschten verwalteten Identitäten zugeordnet sind, werden im Portal mit der Kennzeichnung „Identität nicht gefunden“ angezeigt. [Weitere Informationen](../../role-based-access-control/troubleshooting.md#role-assignments-with-identity-not-found).

:::image type="content" source="media/managed-identity-best-practice-recommendations/identity-not-found.png" alt-text="Identität für Rollenzuweisung nicht gefunden.":::

## <a name="limitation-of-using-azure-ad-groups-with-managed-identities-for-authorization"></a>Einschränkung der Verwendung von Azure AD-Gruppen mit verwalteten Identitäten für die Autorisierung

Die Verwendung von Azure AD-Gruppen zur Gewährung des Zugriffs auf Dienste ist eine hervorragende Möglichkeit, den Autorisierungsprozess zu vereinfachen. Die Idee ist einfach: Sie erteilen einer Gruppe Berechtigungen und fügen der Gruppe Identitäten hinzu, so dass sie dieselben Berechtigungen erben. Dies ist ein bewährtes Muster aus verschiedenen lokalen Systemen und funktioniert gut, wenn die Identitäten Benutzer darstellen. Bei nicht menschlichen Identitäten, wie z. B. Azure AD-Anwendungen und Verwaltete Identitäten, ist der genaue Mechanismus derzeit jedoch nicht gut geeignet. Die heutige Implementierung mit Azure AD und Azure Role Based Access Control (Azure RBAC) verwendet Zugriffstoken, die von Azure AD für die Authentifizierung der einzelnen Identitäten ausgestellt werden. Wenn die Identität jedoch zu einer Gruppe hinzugefügt wird, wird ihre Gruppenzugehörigkeit als Anspruch in dem von Azure AD ausgestellten Zugriffstoken ausgedrückt. Azure RBAC verwendet diesen Anspruch, um die Autorisierungsregeln zum Zulassen oder Verweigern des Zugriffs weiter zu bewerten.  

Da die Gruppenmitgliedschaft ein Anspruch im Zugriffstoken ist, werden Änderungen der Gruppenmitgliedschaft erst wirksam, wenn das Token aktualisiert wird. Ein menschlicher Benutzer kann ein neues Zugriffstoken abrufen, indem er sich abmelden und erneut anmelden kann. Token für verwaltete Identitäten werden aus Leistungs- und Resilienzgründen von der zugrunde liegenden Azure-Infrastruktur zwischengespeichert. Dies bedeutet, dass es mehrere Stunden dauern kann, bis Änderungen an der Gruppenmitgliedschaft einer verwalteten Identität wirksam werden. Derzeit ist es nicht möglich, zu erzwingen, dass das Token einer verwalteten Identität vor dessen Ablauf aktualisiert wird. Wenn Sie die Gruppenmitgliedschaft einer verwalteten Identität ändern, um Berechtigungen hinzuzufügen oder zu entfernen, müssen Sie daher möglicherweise mehrere Stunden warten, bis die Azure-Ressource, welche die Identität verwendet, den richtigen Zugriff hat – im Vergleich zu nur wenigen Minuten, wenn Sie Berechtigungen direkt für die Identität hinzufügen oder entfernen würden.

Um sicherzustellen, dass Änderungen an Berechtigungen für verwaltete Identitäten schnell wirksam werden, empfiehlt es sich, Azure-Ressourcen mithilfe einer [vom Benutzer zugewiesenen verwalteten Identität](how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azcli) mit Berechtigungen zu gruppieren, die direkt auf die Identität angewendet werden, anstatt verwaltete Identitäten einer Azure AD-Gruppe mit Berechtigungen hinzufügen oder daraus entfernen zu müssen. Eine vom Benutzer zugewiesene verwaltete Identität kann wie eine Gruppe verwendet werden, da sie einer oder mehrere Azure-Ressource(n) zugewiesen werden kann, um sie zu verwenden. Der Zuweisungsvorgang kann mithilfe der Rolle [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) und der [Operator-Rolle für verwaltete Identität gesteuert werden.](../../role-based-access-control/built-in-roles.md#managed-identity-operator)