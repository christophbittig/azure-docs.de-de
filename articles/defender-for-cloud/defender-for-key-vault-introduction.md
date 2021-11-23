---
title: 'Microsoft Azure Defender für Key Vault: Vorteile und Features'
description: Hier erfahren Sie etwas über die Vorteile und Features von Microsoft Defender für Key Vault.
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 2c77fa861a27cd0277ea501ae68f5d35e0cd59bf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526837"
---
# <a name="introduction-to-microsoft-defender-for-key-vault"></a>Einführung in Microsoft Defender für Key Vault

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). 

Aktivieren Sie **Microsoft Defender für Key Vault** für Azure-nativen, erweiterten Bedrohungsschutz für Azure Key Vault, wodurch eine zusätzliche Ebene von Security Intelligence bereitgestellt wird. 

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Microsoft Defender für Key Vault** wird gemäß den Angaben in der [Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-key-vault"></a>Welche Vorteile bietet Microsoft Defender für Key Vault?

Microsoft Defender für Key Vault erkennt ungewöhnliche und potenziell schädliche Versuche, auf Key Vault-Konten zuzugreifen oder diese missbräuchlich zu nutzen. Aufgrund dieser Schutzebene können Sie Bedrohungen begegnen, auch wenn Sie kein Sicherheitsexperte sind und ohne dass Drittanbietersysteme für die Sicherheitsüberwachung verwaltet werden müssen.

Wenn anomale Aktivitäten auftreten, zeigt Defender für Key Vault Warnungen an und sendet sie optional per E-Mail an die zuständigen Mitglieder Ihrer Organisation. Diese Warnungen enthalten Details zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Eindämmung von Bedrohungen. 

## <a name="microsoft-defender-for-key-vault-alerts"></a>Microsoft Defender für Key Vault-Warnungen
Wenn Sie eine Warnung von Microsoft Defender für Key Vault erhalten, wird empfohlen, dass Sie die Warnung untersuchen und darauf reagieren, wie unter [Reagieren auf Microsoft Defender für Key Vault](defender-for-key-vault-usage.md) beschrieben wird. Microsoft Defender für Key Vault schützt Anwendungen und Anmeldeinformationen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.

Die Warnungen werden auf der Seite **Sicherheit** von Key Vault, auf der Seite „Workloadschutz“ und auf der Warnungsseite von Defender für Cloud angezeigt.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Seite „Sicherheit“ von Azure Key Vault":::


> [!TIP]
> Sie können Microsoft Defender für Key Vault-Warnungen simulieren, indem Sie die Anleitungen unter [Überprüfen der Azure Key Vault-Bedrohungserkennung in Microsoft Defender für Cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336) ausführen.


## <a name="respond-to-microsoft-defender-for-key-vault-alerts"></a>Reagieren auf Microsoft Defender für Key Vault-Warnungen
Wenn Sie eine Warnung von [Microsoft Defender für Key Vault](defender-for-key-vault-introduction.md) erhalten, wird empfohlen, dass Sie die Warnung wie unten beschrieben untersuchen und darauf reagieren. Microsoft Defender für Key Vault schützt Anwendungen und Anmeldeinformationen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.  

Warnungen von Microsoft Defender für Key Vault enthalten die folgenden Elemente:

- Objekt-ID
- Benutzerprinzipalname oder IP-Adresse der verdächtigen Ressource 

Je nach *Typ* des ausgeführten Zugriffs sind einige Felder möglicherweise nicht verfügbar. Wenn auf Ihren Schlüsseltresor beispielsweise durch eine Anwendung zugegriffen wurde, wird kein zugeordneter Benutzerprinzipalname angezeigt. Wenn der Datenverkehr von außerhalb von Azure stammt, wird keine Objekt-ID angezeigt.

> [!TIP]
> Virtuellen Azure-Computern werden IP-Adressen von Microsoft zugewiesen. Dadurch ist es möglich, dass eine Warnung eine IP-Adresse von Microsoft enthält, obwohl sie sich auf eine außerhalb von Microsoft ausgeführte Aktivität bezieht. Daher sollten Sie auch bei einer Warnung, die eine IP-Adresse von Microsoft enthält, die auf dieser Seite beschriebenen Schritte ausführen.

### <a name="step-1-identify-the-source"></a>Schritt 1: Identifizieren der Quelle

1. Überprüfen Sie, ob der Datenverkehr aus Ihrem Azure-Mandanten stammt. Wenn die Key Vault-Firewall aktiviert ist, haben Sie wahrscheinlich dem Benutzer oder der Anwendung, der bzw. die diese Warnung ausgelöst hat, den Zugriff gewährt.
1. Wenn Sie die Quelle des Datenverkehrs nicht überprüfen können, fahren Sie mit [Schritt 2: Entsprechendes Reagieren](#step-2-respond-accordingly) fort.
1. Wenn Sie die Quelle des Datenverkehrs in Ihrem Mandanten ermitteln können, wenden Sie sich an den Benutzer oder den Besitzer der Anwendung. 

> [!CAUTION]
> Microsoft Defender für Key Vault dient zur Erkennung verdächtiger Aktivitäten, die durch gestohlene Anmeldeinformationen verursacht wurden. Verwerfen Sie die Warnung **nicht**, nur weil Sie den Benutzer oder die Anwendung kennen. Wenden Sie sich an den Besitzer der Anwendung oder den Benutzer, und vergewissern Sie sich, dass die Aktivität legitim war. Sie können ggf. eine Unterdrückungsregel erstellen, um Rauschen auszuschließen. Weitere Informationen finden Sie unter [Unterdrücken von Sicherheitswarnungen](alerts-suppression-rules.md).


### <a name="step-2-respond-accordingly"></a>Schritt 2: Entsprechendes Reagieren 
Wenn Sie den Benutzer oder die Anwendung nicht erkannt haben oder wenn Sie der Ansicht sind, dass der Zugriff nicht autorisiert werden sollte:

- Wenn der Datenverkehr von einer unbekannten IP-Adresse stammt:
    1. Aktivieren Sie die Azure Key Vault-Firewall, wie unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](../key-vault/general/network-security.md) beschrieben.
    1. Konfigurieren Sie die Firewall mit vertrauenswürdigen Ressourcen und virtuellen Netzwerken.

- Wenn die Quelle der Warnung eine nicht autorisierte Anwendung oder ein verdächtiger Benutzer war:
    1. Öffnen Sie die Einstellungen für die Zugriffsrichtlinien des Schlüsseltresors.
    1. Entfernen Sie den zugehörigen Sicherheitsprinzipal, oder schränken Sie die Vorgänge ein, die der Sicherheitsprinzipal ausführen darf.  

- Wenn die Quelle der Warnung über eine Azure Active Directory-Rolle in Ihrem Mandanten verfügt:
    1. Wenden Sie sich an den Administrator.
    1. Ermitteln Sie, ob Azure Active Directory-Berechtigungen verringert oder widerrufen werden müssen.

### <a name="step-3-measure-the-impact"></a>Schritt 3: Messen der Auswirkungen
Nachdem Sie das Ereignis behoben haben, untersuchen Sie die betroffenen Geheimnisse in Ihrer Key Vault-Instanz:
1. Öffnen Sie für Ihre Azure Key Vault-Instanz die Seite **Sicherheit**, und zeigen Sie die ausgelöste Warnung an.
1. Wählen Sie die spezifische Warnung aus, die ausgelöst wurde, und überprüfen Sie die Liste der Geheimnisse, auf die zugegriffen wurde, sowie den Zeitstempel.
1. Wenn Sie Key Vault-Diagnoseprotokolle aktiviert haben, überprüfen Sie optional die vorherigen Vorgänge für die entsprechende IP-Adresse, den Benutzerprinzipal oder die Objekt-ID des Aufrufers.  

### <a name="step-4-take-action"></a>Schritt 4: Ausführen einer Aktion 
Wenn Sie die Liste der Geheimnisse, Schlüssel und Zertifikate, auf die der verdächtige Benutzer oder die Anwendung zugegriffen hat, zusammengestellt haben, sollten Sie diese Objekte umgehend rotieren.

1. Betroffene Geheimnisse sollten deaktiviert oder aus Ihrem Schlüsseltresor gelöscht werden.
1. Wenn die Anmeldeinformationen für eine bestimmte Anwendung verwendet wurden:
    1. Wenden Sie sich an den Administrator der Anwendung, und bitten Sie ihn, die Umgebung auf die Verwendung der kompromittierten Anmeldeinformationen seit der Kompromittierung zu überprüfen.
    1. Wenn die kompromittierten Anmeldeinformationen verwendet wurden, sollte der Besitzer der Anwendung ermitteln, auf welche Informationen zugegriffen wurde, und die Auswirkungen minimieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Microsoft Defender für Key Vault erfahren.

Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Key Vault-Sicherheitswarnungen](alerts-reference.md#alerts-azurekv): Der „Key Vault“-Abschnitt der Referenztabelle für alle Microsoft Defender für Cloud-Warnungen
- [Fortlaufender Export von Defender für Cloud-Daten](continuous-export.md)
- [Unterdrücken von Sicherheitswarnungen](alerts-suppression-rules.md)
