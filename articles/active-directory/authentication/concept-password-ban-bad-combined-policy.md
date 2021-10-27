---
title: Kombinierte Überprüfung von Kennwortrichtlinien und schwachen Kennwörtern in Azure Active Directory
description: Lernen Sie die kombinierte Überprüfung von Kennwortrichtlinien und schwachen Kennwörtern in Azure Active Directory kennen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: justinha
author: sajiang
manager: daveba
ms.reviewer: sajiang
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21fb52047822df5c14e8b5a21c017a2e4ca5f1d6
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138766"
---
# <a name="combined-password-policy-and-weak-password-check-in-azure-active-directory"></a>Kombinierte Überprüfung von Kennwortrichtlinien und schwachen Kennwörtern in Azure Active Directory

Ab Oktober 2021 enthält die Überprüfung auf Konformität mit Kennwortrichtlinien von Azure Active Directory (Azure AD) auch eine Überprüfung auf [bekannte schwache Kennwörter](concept-password-ban-bad.md) und deren Varianten. Wenn die kombinierte Überprüfung von Kennwortrichtlinien und gesperrten Kennwörter für Mandanten eingeführt wird, stellen Azure AD- und Office 365 Admin Center-Benutzer beim Erstellen, Ändern oder Zurücksetzen ihrer Kennwörter möglicherweise Unterschiede fest. In diesem Thema werden Details zu den Kriterien für Kennwortrichtlinien erläutert, die von Azure AD überprüft werden. 

## <a name="azure-ad-password-policies"></a>Azure AD-Kennwortrichtlinien

Eine Kennwortrichtlinie wird auf alle Benutzer- und Administratorkonten angewendet, die direkt in Azure AD erstellt und verwaltet werden. Sie können [schwache Kennwörter sperren](concept-password-ban-bad.md) und Parameter definieren, um nach wiederholten fehlerhaften Kennwortversuchen [das Konto zu sperren](howto-password-smart-lockout.md). Andere Kennwortrichtlinieneinstellungen können nicht geändert werden.

Die Azure AD-Kennwortrichtlinie gilt nicht für Benutzerkonten, die über Azure AD Connect aus einer lokalen AD DS-Umgebung synchronisiert werden, sofern Sie nicht EnforceCloudPasswordPolicyForPasswordSyncedUsers aktivieren.

Die folgenden Azure AD-Kennwortrichtlinienanforderungen gelten für alle Kennwörter, die in Azure AD erstellt, geändert oder zurückgesetzt werden. Die Anforderungen werden bei der Benutzerbereitstellung, der Kennwortänderung und bei Kennwortrücksetzungsflows angewendet. Sofern nicht anders angegeben, können Sie die folgenden Einstellungen nicht ändern.

| Eigenschaft | Requirements (Anforderungen) |
| --- | --- |
| Zulässige Zeichen |Großbuchstaben (A-Z)<br>Kleinbuchstaben (a-z)<br>Zahlen (0 bis 9)<br>Sonderzeichen:<br>- @ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ><br>- Leerzeichen |
| Unzulässige Zeichen | Unicode-Zeichen |
| Kennwortlänge |Kennwörter müssen enthalten<br>Mindestens 8 Zeichen<br>Maximal 256 Zeichen</li> |
| Kennwortkomplexität |Kennwörter müssen drei der folgenden vier Elemente enthalten:<br>Großbuchstaben<br>Kleinbuchstaben<br>Zahlen <br>Sonderzeichen<br> Hinweis: Für Education-Mandanten ist keine Kennwortkomplexitätsprüfung erforderlich. |
| Kennwort nicht kürzlich verwendet | Wenn ein Benutzer sein Kennwort ändert oder zurücksetzt, darf das neue Kennwort nicht mit dem aktuellen oder kürzlich verwendeten Kennwort identisch sein. |
| Kennwort ist nicht vom [Azure AD-Kennwortschutz](concept-password-ban-bad.md) gesperrt | Das Kennwort darf nicht in der globalen Liste der gesperrten Kennwörter für den Azure AD-Kennwortschutz oder in der anpassbaren Liste gesperrter Kennwörter enthalten sein, die spezifisch für Ihre Organisation ist. |

## <a name="password-expiration-policies"></a>Richtlinien zum Kennwortablauf

Die Richtlinien zum Ablauf von Kennwörtern bleiben unverändert, werden jedoch der Vollständigkeit halber in diesem Thema erwähnt. Ein *globaler Administrator* oder *Benutzeradministrator* kann mit dem [Microsoft Azure AD-Modul für Windows PowerShell](/powershell/module/Azuread/) festlegen, dass Benutzerkennwörter nicht ablaufen.

> [!NOTE]
> Standardmäßig können nur Kennwörter für Benutzerkonten, die nicht über Azure AD Connect synchronisiert werden, so konfiguriert werden, dass sie nicht ablaufen. Weitere Informationen zur Verzeichnissynchronisierung finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy).

Sie können auch PowerShell verwenden, um die Konfiguration für niemals ablaufende Kennwörter zu entfernen oder um Benutzerkennwörter anzuzeigen, für die kein Ablauf festgelegt ist.

Die folgenden Ablaufanforderungen gelten für andere Anbieter, die Azure AD für Identitäts- und Verzeichnisdienste wie Intune und Microsoft 365 verwenden. 

| Eigenschaft | Requirements (Anforderungen) |
| --- | --- |
| Zeitraum bis zum Ablauf des Kennworts (maximales Kennwortalter) |<ul><li>Standardwert: **90** Tage</li><li>Der Wert kann im Azure Active Directory-Modul für Windows PowerShell mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Benachrichtigung bei Ablauf des Kennworts (wenn Benutzer über den Ablauf des Kennworts benachrichtigt werden) |<ul><li>Standardwert: **14** Tage (bevor das Kennwort abläuft)</li><li>Der Wert kann mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Kennwortablauf (Kennwort läuft nie ab) |<ul><li>Standardwert: **false** (gibt an, dass Kennwörter ein Ablaufdatum aufweisen).</li><li>Der Wert kann mit dem Cmdlet `Set-MsolUser` für einzelne Benutzerkonten konfiguriert werden.</li></ul> |

## <a name="next-steps"></a>Nächste Schritte

- [Kennwortrichtlinien und Kontoeinschränkungen in Azure Active Directory](concept-sspr-policy.md)
- [Ausschließen von ungeeigneten Kennwörtern mit dem Azure Active Directory-Kennwortschutz](concept-password-ban-bad.md)
