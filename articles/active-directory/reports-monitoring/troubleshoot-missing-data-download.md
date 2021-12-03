---
title: 'Problembehandlung: Fehlende Daten in den heruntergeladenen Aktivitätsprotokollen | Microsoft-Dokumentation'
description: Bietet Ihnen eine Lösung für fehlende Daten in heruntergeladenen Azure Active Directory-Aktivitätsprotokollen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9084d30ad8fe978defa7f336030535f7b4a97a1a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995405"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Ich finde nicht alle Daten in den Azure Active Directory-Aktivitätsprotokollen, die ich heruntergeladen habe.

## <a name="symptoms"></a>Symptome

Ich habe die Aktivitätsprotokolle (Überwachung oder Anmeldungen) heruntergeladen, und für den ausgewählten Zeitraum werden nicht alle Datensätze angezeigt. Warum? 

 ![Berichterstellung](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Ursache

Beim Herunterladen von Aktivitätsprotokollen im Azure-Portal gilt eine Obergrenze von 250.000 Datensätzen, die nach Aktualität sortiert werden. 

## <a name="resolution"></a>Lösung

Mithilfe von [Azure AD-Berichterstellungs-APIs](concept-reporting-api.md) können jederzeit bis zu einer Millionen Datensätze abgerufen werden.

## <a name="next-steps"></a>Nächste Schritte

* [Azure Active Directory-Berichte: häufig gestellte Fragen](reports-faq.yml)

