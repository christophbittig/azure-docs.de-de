---
title: Integrierte und benutzerdefinierte Warnlisten
description: Hier erhalten Sie Informationen zu Sicherheitswarnungen und empfohlenen Abhilfemaßnahmen unter Verwendung von Features und Diensten von Defender für IoT Hub.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 89fb77bed6116fcbae847b19b79e90d4dff34a23
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293533"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Sicherheitswarnungen für Defender für IoT Hub

Defender für IoT analysiert Ihre IoT-Lösung ständig mithilfe von Advanced Analytics- und Threat Intelligence-Funktionen, um Sie vor schädlichen Aktivitäten zu warnen.
Darüber hinaus können Sie – basierend auf Ihrer Kenntnis des erwarteten Geräteverhaltens – benutzerdefinierte Warnungen erstellen.
Eine Warnung fungiert als Indikator für eine potenzielle Kompromittierung, die untersucht und behoben werden sollte.

In diesem Artikel finden Sie eine Liste von integrierten Warnungen, die für Ihren IoT Hub ausgelöst werden können.
Neben integrierten Warnungen ermöglicht Ihnen Defender für IoT, benutzerdefinierte Warnungen zu erstellen, die auf dem erwarteten IoT Hub- und/oder Geräteverhalten basieren.
Weitere Informationen finden Sie unter [Konfigurierbare Warnungen](concept-customizable-security-alerts.md).

## <a name="built-in-alerts-for-iot-hub"></a>Integrierte Warnungen für IoT Hub

### <a name="medium-severity"></a>Mittlerer Schweregrad

| Name | severity | Data source | BESCHREIBUNG | Vorschlag zur Problemlösung | AlertType |
|--|--|--|--|--|--|
| Neues Zertifikat zu einem IoT Hub hinzugefügt | Medium | IoT Hub | Einem IoT-Hub wurde ein Zertifikat hinzugefügt. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | 1. Stellen Sie sicher, dass das Zertifikat von einer autorisierten Partei hinzugefügt wurde. <br> 2. Wenn es nicht von einer autorisierten Partei hinzugefügt wurde, entfernen Sie das Zertifikat, und eskalieren Sie die Warnung an das Sicherheitsteam Ihrer Organisation. | IoT_CertificateSuccessfullyAddedToHub |
| Zertifikat von einem IoT Hub gelöscht | Medium | IoT Hub | Ein Zertifikat wurde von einem IoT-Hub gelöscht. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | 1. Stellen Sie sicher, dass das Zertifikat von einer autorisierten Partei entfernt wurde. <br> 2. Wenn das Zertifikat nicht von einer autorisierten Partei entfernt wurde, fügen Sie das Zertifikat erneut hinzu, und eskalieren Sie die Warnung an das Sicherheitsteam Ihrer Organisation. | IoT_CertificateSuccessfullyDeletedFromHub |
| Nicht erfolgreicher Versuch erkannt, ein Zertifikat zu einem IoT Hub hinzuzufügen | Medium | IoT Hub | Es wurde ein erfolgloser Versuch erkannt, einem IoT-Hub ein Zertifikat hinzuzufügen. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | Stellen Sie sicher, dass die Berechtigungen zum Ändern von Zertifikaten nur autorisierten Parteien gewährt werden. | Hub_CertificateFailedToBeAddedToHub |
| Nicht erfolgreicher Versuch erkannt, ein Zertifikat von einem IoT Hub zu löschen | Medium | IoT Hub | Es wurde ein erfolgloser Versuch erkannt, ein Zertifikat aus einem IoT-Hub zu löschen. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | Stellen Sie sicher, dass die Berechtigungen zum Ändern von Zertifikaten nur autorisierten Parteien gewährt werden. | IoT.Hub_CertificateFailedToBeDeletedFromHub |
| x.509-Gerätezertifikat-Fingerabdruck-Konflikt | Medium | IoT Hub | Der x.509-Zertifikatfingerabdruck für das Gerät stimmt mit der Konfiguration nicht überein. | Überprüfen Sie die Warnungen auf den Geräten. Es ist keine weitere Aktion erforderlich. | IoT_Cert_Print_Mismatch |
| x.509-Zertifikat abgelaufen | Medium | IoT Hub | Das X.509-Gerätezertifikat ist abgelaufen. | Hierbei kann es sich um ein legitimes Gerät handeln, dessen Zertifikat einfach abgelaufen ist, oder um einen Versuch, die Identität eines legitimen Geräts anzunehmen. Wenn das legitime Gerät zurzeit ordnungsgemäß kommuniziert, ist dies wahrscheinlich der Versuch eines Identitätswechsels. | IoT_Cert_Expired |

### <a name="low-severity"></a>Niedriger Schweregrad

| Name | severity | Data source | BESCHREIBUNG | Vorschlag zur Problemlösung | AlertType |
|--|--|--|--|--|--|
| Versuch erkannt, eine Diagnoseeinstellung für einen IoT Hub hinzuzufügen oder zu bearbeiten | Niedrig | IoT Hub | Ein Versuch wurde erkannt, eine Diagnoseeinstellung für einen IoT Hub hinzuzufügen oder zu bearbeiten. Mit Diagnoseeinstellungen können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | 1. Stellen Sie sicher, dass das Zertifikat von einer autorisierten Partei entfernt wurde. <br> 2. Wenn das Zertifikat nicht von einer autorisierten Partei entfernt wurde, fügen Sie das Zertifikat erneut hinzu, und eskalieren Sie die Warnung an das IT-Sicherheitsteam. | IoT_DiagnosticSettingAddedOrEditedOnHub |
| Versuch erkannt, eine Diagnoseeinstellung von einen IoT Hub zu löschen | Niedrig | IoT Hub | Ein Versuch wurde erkannt, eine Diagnoseeinstellung für einen IoT Hub hinzuzufügen oder zu bearbeiten. Mit Diagnoseeinstellungen können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. Wenn diese Aktion nicht durch eine autorisierte Person erfolgt ist, kann dies auf schädliche Aktivitäten hinweisen. | Stellen Sie sicher, dass die Berechtigungen zum Ändern von Diagnoseeinstellungen nur für eine autorisierte Partei erteilt werden. | IoT_DiagnosticSettingDeletedFromHub |
| SAS-Token abgelaufen | Niedrig | IoT Hub | Ein abgelaufenes SAS-Token wurde von einem Gerät verwendet. | Hierbei kann es sich um ein legitimes Gerät handeln, dessen Token einfach abgelaufen ist, oder um einen Versuch, die Identität eines legitimen Geräts anzunehmen. Wenn das legitime Gerät zurzeit ordnungsgemäß kommuniziert, ist dies wahrscheinlich der Versuch eines Identitätswechsels. | IoT_Expired_SAS_Token |
| Ungültige SAS-Tokensignatur | Niedrig | IoT Hub | Ein von einem Gerät verwendetes SAS-Token weist eine ungültige Signatur auf. Die Signatur stimmt entweder nicht mit dem primären oder nicht mit dem sekundären Schlüssel überein. | Überprüfen Sie die Warnungen auf den Geräten. Es ist keine weitere Aktion erforderlich. | IoT_Invalid_SAS_Token |

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md) über den Defender für IoT-Dienst
