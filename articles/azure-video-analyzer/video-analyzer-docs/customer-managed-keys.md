---
title: Vom Kunden verwaltete Schlüssel – Azure Video Analyzer
description: 'Sie können in Azure Video Analyzer einen vom Kunden verwalteten Schlüssel (also: „Bring Your Own Key“) verwenden.'
author: anilmur
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.openlocfilehash: 749c70286af3c6d182dc1e8a925ad7fb4c8e815a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552675"
---
# <a name="customer-managed-keys-with-azure-video-analyzer"></a>Vom Kunden verwaltete Schlüssel mit Azure Video Analyzer

„Bring Your Own Key“ (BYOK) ist eine im gesamten Bereich von Azure unterstützte Initiative, die Kunden dabei unterstützt, ihre Workloads in die Cloud auszulagern. Vom Kunden verwaltete Schlüssel ermöglichen es Kunden, die Konformitätsbestimmungen ihrer Branche einzuhalten, und verbessern die Mandantenisolation eines Diensts. Die Kontrolle der Verschlüsselungsschlüssel in die Hand der Kunden zu legen, ist eine Möglichkeit, unnötigen Zugriff zu minimieren und die Kontrolle und Vertrauenswürdigkeit von Microsoft-Diensten zu stärken.

## <a name="keys-and-key-management"></a>Schlüssel und Schlüsselverwaltung

Für alle Video Analyzer-Konten wird ein Kontoschlüssel erstellt. Standardmäßig wird dieser Kontoschlüssel mit einem Systemschlüssel verschlüsselt, der sich im Besitz von Video Analyzer befindet (d. h. ein vom System verwalteter Schlüssel). Stattdessen können Sie ihren eigenen Schlüssel mit Azure Video Analyzer verwenden. In diesem Fall wird Ihr Kontoschlüssel mit Ihrem Schlüssel verschlüsselt. Zugriffsrichtlinien und Metadaten von Videoressourcen werden mithilfe des Kontoschlüssels verschlüsselt.

Video Analyzer verwendet eine vom Benutzer zugewiesene verwaltete Identität, um Ihren Schlüssel aus einem Schlüsseltresor zu lesen, dessen Besitzer Sie sind. Sie müssen die vom Benutzer zugewiesene verwaltete Identität beim Erstellen oder Aktualisieren des Video Analyzer-Kontos bereitstellen und die entsprechende [rollenbasierte Zugriffssteuerung in Azure](../../role-based-access-control/overview.md) dem Schlüsseltresor zuweisen. Für Video Analyzer ist es erforderlich, dass sich der Schlüsseltresor im gleichen Bereich wie das Konto befindet und dass vorläufiges Löschen und Schutz vor Bereinigung aktiviert sind.

Bei Ihrem Schlüssel kann es sich um einen 2048-, 3072- oder 4096-RSA-Schlüssel handeln, und sowohl HSM- als auch Softwareschlüssel werden unterstützt.

> [!NOTE]
> EC-Schlüssel werden nicht unterstützt.

Sie können wahlweise einen Schlüsselnamen und eine Schlüsselversion oder nur einen Schlüsselnamen angeben. Wenn Sie nur einen Schlüsselnamen verwendet, verwendet Video Analyzer die aktuellste Schlüsselversion. Neue Versionen von Kundenschlüsseln werden automatisch erkannt, und der Kontoschlüssel wird erneut verschlüsselt.

> [!WARNING]
> Video Analyzer überwacht den Zugriff auf den Kundenschlüssel. Wenn der Zugriff auf den Kundenschlüssel nicht möglich ist (beispielsweise, weil der Schlüssel gelöscht wurde, der Schlüsseltresor gelöscht wurde oder die Zugriffsberechtigung entzogen wurde), überführt Video Analyzer das Konto in den Status „Customer Key Inaccessible“ (Kein Zugriff auf den Kundenschlüssel – effektiv ist das Konto damit deaktiviert). Sie können entweder das Konto löschen oder den Kontoschlüssel wiederherstellen, um den Zugriff wieder zu ermöglichen.

## <a name="double-encryption"></a>Doppelte Verschlüsselung

Video Analyzer schützt Ihre vertraulichen Daten mithilfe der doppelten Verschlüsselung gemäß dem Azure-Standardverfahren. Weitere Informationen finden Sie unter [Mehrfachverschlüsselung in Azure](../../security/fundamentals/double-encryption.md). Bei ruhenden Daten wird von der ersten Verschlüsselungsebene abhängig von der Einstellung von `encryption` für das Konto ein kundenseitig verwalteter Schlüssel oder ein von Microsoft verwalteter Schlüssel verwendet. Die zweite Verschlüsselungsebene für ruhende Daten wird automatisch mit einem separaten, von Microsoft verwalteten Schlüssel bereitgestellt.

> [!NOTE]
> Die Mehrfachverschlüsselung wird automatisch für das Video Analyzer-Konto aktiviert. Sie müssen jedoch den kundenseitig verwalteten Schlüssel und die Mehrfachverschlüsselung in Ihrem Speicherkonto separat konfigurieren. Weitere Informationen finden Sie unter [Speicherverschlüsselung](../../storage/common/storage-service-encryption.md).


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [verwaltete Identität](managed-identity.md).
