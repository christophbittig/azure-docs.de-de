---
title: Verschlüsselung für ruhende Daten des Speech-Diensts
titleSuffix: Azure Cognitive Services
description: Microsoft bietet von Microsoft verwaltete Verschlüsselungsschlüssel an und ermöglicht Ihnen auch die Verwaltung Ihrer Cognitive Services-Abonnements mit Ihren eigenen Schlüsseln, den so genannten kundenseitig verwalteten Schlüsseln (Customer Managed Keys, CMK). Dieser Artikel befasst sich mit der Verschlüsselung ruhender Daten für den Speech-Dienst.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: egeaney
ms.openlocfilehash: cdf8276904fda5098b3192779e0372b4a1bcc9d2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766622"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten des Speech-Diensts

Mit dem Speech-Dienst werden Ihre Daten beim Speichern in der Cloud automatisch verschlüsselt. Durch die Verschlüsselung des Speech-Diensts werden Ihre Daten ausreichend geschützt, um den Sicherheits- und Complianceanforderungen Ihrer Organisation gerecht zu werden.

## <a name="about-cognitive-services-encryption"></a>Informationen zur Cognitive Services-Verschlüsselung

Daten werden mittels [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)-konformer [256-Bit-AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)-Verschlüsselung ver- und entschlüsselt. Verschlüsselung und Entschlüsselung sind transparent, was bedeutet, dass die Verschlüsselung und der Zugriff für Sie verwaltet werden. Ihre Daten werden standardmäßig geschützt, und Sie müssen weder Code noch Anwendungen ändern, um die Verschlüsselung nutzen zu können.

## <a name="about-encryption-key-management"></a>Informationen zur Verwaltung von Verschlüsselungsschlüsseln

Wenn Sie Custom Speech und Custom Voice verwenden, speichert der Speech-Dienst möglicherweise die folgenden Daten in der Cloud:  

* Speech-Ablaufverfolgungsdaten – nur wenn Sie die Ablaufverfolgung für Ihren benutzerdefinierten Endpunkt aktivieren
* Hochgeladene Trainings- und Testdaten

Standardmäßig werden Ihre Daten im Speicher von Microsoft gespeichert, und Ihr Abonnement verwendet von Microsoft verwaltete Verschlüsselungsschlüssel. Sie haben auch die Möglichkeit, Ihr eigenes Speicherkonto vorzubereiten. Der Zugriff auf den Speicher wird durch die verwaltete Identität verwaltet, und der Speech-Dienst kann nicht direkt auf Ihre eigenen Daten zugreifen, wie z. B. Daten der Sprachablaufverfolgung, Daten der Trainingsanpassung und benutzerdefinierte Modelle.

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten?](../../active-directory/managed-identities-azure-resources/overview.md).

In der Zwischenzeit können Sie Ihr Abonnement mit eigenen Verschlüsselungsschlüsseln verwalten, wenn Sie benutzerdefinierte Befehle verwenden. Kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMK) werden auch als Bring Your Own Key (BYOK) bezeichnet und bieten eine größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Außerdem können Sie die zum Schutz Ihrer Daten verwendeten Verschlüsselungsschlüssel überwachen. Weitere Informationen zu benutzerdefinierten Befehlen und CMK finden Sie unter [Verschlüsselung ruhender Daten mithilfe benutzerdefinierter Befehle](custom-commands-encryption-of-data-at-rest.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Bring Your Own Storage (BYOS) für Anpassung und Protokollierung

Um Zugriff auf Bring Your Own Storage anzufordern, füllen Sie das  [Formular zum Anfordern von Bring Your Own Storage (BYOS) für den Speech-Dienst](https://aka.ms/cogsvc-cmk) aus, und übermitteln Sie es. Nach der Genehmigung müssen Sie ein eigenes Speicherkonto erstellen, um die Daten zu speichern, die für die Anpassung und Protokollierung erforderlich sind. Wenn Sie ein Speicherkonto hinzufügen, aktiviert die Speech-Dienstressource eine vom System zugewiesene verwaltete Identität.

> [!IMPORTANT]
> Dem Benutzerkonto, das Sie zum Erstellen einer Speech-Ressource mit aktivierter BYOS-Funktion verwenden, sollte [für den Bereich des Azure-Abonnements die Rolle „Besitzer“ zugewiesen sein](../../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator). Andernfalls wird bei der Ressourcenbereitstellung ein Autorisierungsfehler angezeigt.

Nachdem die vom System zugewiesene verwaltete Identität aktiviert ist, wird diese Ressource bei Azure Active Directory (AAD) registriert. Nach der Registrierung erhält die verwaltete Identität Zugriff auf das Speicherkonto. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Wenn Sie vom System zugewiesene verwaltete Identitäten deaktivieren, wird der Zugriff auf das Speicherkonto entfernt. Dies führt dazu, dass die Teile des Speech-Diensts nicht mehr funktionieren, die Zugriff auf das Speicherkonto benötigen.  

Der Sprachdienst unterstützt zurzeit keine Kunden-Lockbox. Kundendaten können jedoch mithilfe von BYOS gespeichert werden, sodass Sie eine ähnliche Datenkontrolle wie bei [Kunden-Lockbox](../../security/fundamentals/customer-lockbox-overview.md) erreichen können. Bedenken Sie, dass Daten des Sprachdiensts in der Region verarbeitet werden und verbleiben, in der die Speech-Ressource erstellt wurde. Dies betrifft alle Daten, sowohl ruhende Daten als auch übertragene Daten. Bei der Verwendung von Anpassungsfunktionen wie Custom Speech und Custom Voice werden alle Kundendaten in der gleichen Region übertragen, gespeichert und verarbeitet, in der Ihr BYOS (sofern verwendet) und die Sprachdienstressource gespeichert sind.

> [!IMPORTANT]
> Microsoft verwendet **keine** Kundendaten zur Verbesserung seiner Sprachmodelle. Wenn darüber hinaus die Endpunktprotokollierung deaktiviert ist und keine Anpassungen verwendet werden, werden keine Kundendaten gespeichert. 

## <a name="next-steps"></a>Nächste Schritte

* [Formular zum Anfordern von Bring Your Own Storage (BYOS) für den Speech-Dienst](https://aka.ms/cogsvc-cmk)
* [Was sind verwaltete Identitäten?](../../active-directory/managed-identities-azure-resources/overview.md)
