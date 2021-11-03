---
title: QnA Maker-Verschlüsselung für ruhende Daten
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). In diesem Artikel erfahren Sie mehr über die Datenverschlüsselung im Ruhezustand für QnA Maker und wie Sie CMK aktivieren und verwalten können.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: egeaney
ms.custom: ignite-fall-2021
ms.openlocfilehash: 81267ba4c8b1d903e9ca83ff1333a5347c2c1463
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043754"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker-Verschlüsselung für ruhende Daten

QnA Maker verschlüsselt automatisch Daten, wenn sie in der Cloud persistent gespeichert werden, und unterstützt Sie so bei der Einhaltung der Sicherheits- und Complianceziele Ihrer Organisation.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Es gibt auch die Option zum Verwalten Ihres Abonnements mit Ihren eigenen Schlüsseln (kundenseitig verwaltete Schlüssel, CMK). Kundenseitig verwaltete Schlüssel (CMK) bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. Wenn für Ihr Abonnement CMK konfiguriert ist, wird darüber hinaus die doppelte Verschlüsselung bereitgestellt, mit der eine zweite Schutzebene zur Verfügung steht, während Sie den Verschlüsselungsschlüssel über Ihren Azure Key Vault kontrollieren können.

QnA Maker verwendet die CMK-Unterstützung von Azure Search. Konfigurieren Sie einen [CMK in Azure Search mithilfe von Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Diese Azure-Instanz muss dem QnA Maker-Dienst zugeordnet sein, damit sie CMK-fähig ist.


> [!IMPORTANT]
> Die Azure Search-Dienstressource muss nach Januar 2019 erstellt worden sein und darf sich nicht im Free-Tarif (freigegeben) befinden. Die Konfiguration von kundenseitig verwalteten Schlüsseln wird im Azure-Portal nicht unterstützt.

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln

Der QnA Maker-Dienst verwendet CMKs aus dem Azure Search-Dienst. Führen Sie die folgenden Schritte zum Aktivieren von CMKs aus:

1. Erstellen Sie eine neue Azure Search Instanz, und aktivieren Sie die Voraussetzungen, die in [Verschlüsselung ruhender Inhalte in Azure Cognitive Search mit von Kunden verwalteten Schlüsseln in Azure Key Vault unter „Voraussetzungen“](../../search/search-security-manage-encryption-keys.md#prerequisites) aufgeführt sind.

   ![Anzeigen der Verschlüsselungseinstellungen 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Wenn Sie eine QnA Maker-Ressource erstellen, wird sie automatisch einer Azure Search-Instanz zugeordnet. Diese Instanz kann nicht mit dem CMK verwendet werden. Zur Verwendung von CMKs müssen Sie die neu erstellte Instanz von Azure Search aus Schritt 1 zuordnen. Insbesondere müssen Sie `AzureSearchAdminKey` und `AzureSearchName` in der QnA Maker-Ressource aktualisieren.

   ![Anzeigen der Verschlüsselungseinstellungen 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Als nächstes erstellen Sie eine neue Anwendungseinstellung:
   * **Name**: Legen Sie den Wert `CustomerManagedEncryptionKeyUrl`
   * **Value**: Verwenden Sie den Wert, den Sie in Schritt 1 beim Erstellen der Azure Search-Instanz erhalten haben.

   ![Anzeigen der Verschlüsselungseinstellungen 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Wenn Sie fertig sind, starten Sie die Runtime neu. Ihr QnA Maker-Dienst ist nun CMK-fähig.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Kundenseitig verwaltete Schlüssel sind in allen Azure Search-Regionen verfügbar.

## <a name="encryption-of-data-in-transit"></a>Verschlüsselung von Daten während der Übertragung

Das QnA Maker-Portal wird im Browser des Benutzers ausgeführt. Jede Aktion löst einen direkten Aufruf der entsprechenden Cognitive Services-API aus. Daher ist QnA Maker mit Daten während der Übertragung kompatibel.
Der QnA Maker-Portaldienst wird jedoch in der Region „USA, Westen“ gehostet und ist daher nicht ideal für Kunden außerhalb der USA. 

## <a name="next-steps"></a>Nächste Schritte

* [Verschlüsselung in Azure Search mithilfe von CMKs in Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Datenverschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md)
* [Weitere Informationen zu Azure Key Vault](../../key-vault/general/overview.md)
