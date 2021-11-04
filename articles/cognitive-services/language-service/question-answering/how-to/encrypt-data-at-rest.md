---
title: 'Benutzerdefinierte Fragen und Antworten: Verschlüsselung ruhender Daten'
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). In diesem Artikel erfahren Sie mehr zur Verschlüsselung ruhender Daten für benutzerdefinierte Fragen und Antworten und sowie zum Aktivieren und Verwalten kundenseitig verwalteter Schlüssel.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: egeaney
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8570a1160eddbed5b372daa529339ea3672482cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095644"
---
# <a name="custom-question-answering-encryption-of-data-at-rest"></a>Benutzerdefinierte Fragen und Antworten: Verschlüsselung ruhender Daten

Das Feature „Fragen und Antworten“ verschlüsselt automatisch Daten, wenn sie in der Cloud persistent gespeichert werden, und unterstützt Sie so bei der Einhaltung der Sicherheits- und Complianceziele Ihrer Organisation.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Standardmäßig verwendet Ihr Abonnement von Microsoft verwaltete Verschlüsselungsschlüssel. Es gibt auch die Option zum Verwalten Ihres Abonnements mit Ihren eigenen Schlüsseln (kundenseitig verwaltete Schlüssel, CMK). Kundenseitig verwaltete Schlüssel (CMK) bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. Wenn für Ihr Abonnement CMK konfiguriert ist, wird darüber hinaus die doppelte Verschlüsselung bereitgestellt, mit der eine zweite Schutzebene zur Verfügung steht, während Sie den Verschlüsselungsschlüssel über Ihren Azure Key Vault kontrollieren können.

Das Feature „Fragen und Antworten“ nutzt die [CMK-Unterstützung von Azure Search](../../../../search/search-security-manage-encryption-keys.md). Der bereitgestellte CMK wird automatisch zugeordnet, um die im Azure Search-Index gespeicherten Daten zu verschlüsseln.

> [!IMPORTANT]
> Die Azure Search-Dienstressource muss nach Januar 2019 erstellt worden sein und darf sich nicht im Free-Tarif (freigegeben) befinden. Die Konfiguration von kundenseitig verwalteten Schlüsseln wird im Azure-Portal nicht unterstützt.

## <a name="enable-customer-managed-keys"></a>Aktivieren von vom Kunden verwalteten Schlüsseln

Führen Sie die folgenden Schritte zum Aktivieren von CMKs aus:

1.  Wechseln Sie zur Registerkarte **Verschlüsselung** Ihrer Sprachressource, für die „Fragen und Antworten“ aktiviert sein muss.
2.  Wählen Sie die Option **Kundenseitig verwaltete Schlüssel** aus. Geben Sie die Details Ihrer [kundenseitig verwalteten Schlüssel](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) ein, und wählen Sie **Speichern** aus.

> [!div class="mx-imgBorder"]
> ![CMK für Fragen und Antworten](../media/encrypt-data-at-rest/question-answering-cmk.png)
   
3.  Bei einer erfolgreichen Speicherung wird der CMK verwendet, um die im Azure Search-Index gespeicherten Daten zu verschlüsseln.

> [!IMPORTANT]
> Es wird empfohlen, Ihren CMK in einem neuen Azure Cognitive Search-Dienst festzulegen, bevor Wissensdatenbanken erstellt werden. Wenn Sie den CMK in einer Sprachressource mit vorhandenen Wissensdatenbanken festlegen, verlieren Sie möglicherweise den Zugriff darauf. Weitere Informationen finden Sie in Azure Cognitive Search unter [Arbeiten mit verschlüsselten Inhalten](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content).

> [!NOTE]
> Wenn Sie die Möglichkeit haben möchten, von Kunden verwaltete Schlüssel zu verwenden, füllen Sie das [Formular zum Anfordern von kundenseitig verwalteten Schlüsseln für Cognitive Services](https://aka.ms/cogsvc-cmk) aus, und reichen Sie es ein.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Kundenseitig verwaltete Schlüssel sind in allen Azure Search-Regionen verfügbar.

## <a name="encryption-of-data-in-transit"></a>Verschlüsselung von Daten während der Übertragung

Das Language Studio-Portal wird im Browser des Benutzers ausgeführt. Jede Aktion löst einen direkten Aufruf der entsprechenden Cognitive Services-API aus. Daher ist „Fragen und Antworten“ für Daten während der Übertragung konform.

## <a name="next-steps"></a>Nächste Schritte

* [Verschlüsselung in Azure Search mithilfe von CMKs in Azure Key Vault](../../../../search/search-security-manage-encryption-keys.md)
* [Datenverschlüsselung ruhender Daten](../../../../security/fundamentals/encryption-atrest.md)
* [Weitere Informationen zu Azure Key Vault](../../../../key-vault/general/overview.md)
