---
title: Azure Defender für Key Vault – Vorteile und Features
description: Erfahren Sie etwas über die Vorteile und Features von Azure Defender für Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 0b14b30e5ea749b53819d94fc69ff7f3d2eee0b4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743112"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Einführung in Azure Defender für Key Vault

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). 

Aktivieren Sie **Azure Defender für Key Vault**, um in Azure nativen erweiterten Bedrohungsschutz für Azure Key Vault zu erhalten und dadurch eine zusätzliche Ebene der Sicherheitsintelligenz zu erzielen. 

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|**Azure Defender für Key Vault** wird gemäß [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Sovereign (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Welche Vorteile bietet Azure Defender für Key Vault?

Azure Defender erkennt ungewöhnliche und potenziell schädliche Versuche, auf Key Vault-Konten zuzugreifen oder diese missbräuchlich zu nutzen. Aufgrund dieser Schutzebene können Sie Bedrohungen begegnen, ohne dass Sie ein Sicherheitsexperte sein oder Systeme von Drittanbietern für die Überwachung der Sicherheit verwalten müssen.  

Wenn anomale Aktivitäten auftreten, zeigt Azure Defender Warnungen an und sendet diese optional per E-Mail an die zuständigen Mitglieder Ihrer Organisation. Diese Warnungen enthalten Details zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Eindämmung von Bedrohungen. 

## <a name="azure-defender-for-key-vault-alerts"></a>Warnungen in Azure Defender für Key Vault
Wenn Sie eine Warnung von Azure Defender für Key Vault erhalten, empfiehlt es sich, die Warnung zu untersuchen und auf sie zu reagieren, wie unter [Reagieren auf Azure Defender für Key Vault](defender-for-key-vault-usage.md) beschrieben. Azure Defender für Key Vault schützt Anwendungen und Anmeldeinformationen. Selbst wenn Sie mit der Anwendung oder dem Benutzer, die bzw. der die Warnung ausgelöst hat, vertraut sind, sollten Sie bei jeder Warnung die Situation unbedingt überprüfen.

Die Warnungen werden auf der Seite **Sicherheit** von Key Vault, auf dem Azure Defender-Dashboard und auf der Seite „Warnungen“ von Security Center angezeigt.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Seite „Sicherheit“ von Azure Key Vault":::


> [!TIP]
> Sie können Azure Defender für Key Vault-Warnungen simulieren, indem Sie die Anweisungen unter [Validating Azure Key Vault threat detection in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336) (Überprüfen der Azure Key Vault-Bedrohungserkennung in Azure Security Center) befolgen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Azure Defender für Key Vault erfahren.

Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Key Vault-Sicherheitswarnungen:](alerts-reference.md#alerts-azurekv) der Key Vault-Abschnitt der Referenztabelle für alle Azure Security Center-Warnungen
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)
- [Unterdrücken von Warnungen von Azure Defender](alerts-suppression-rules.md)