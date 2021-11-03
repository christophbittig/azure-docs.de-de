---
title: Komponenten aus der Azure-Virtual-Network-Manager-Vorschau-Checkliste entfernen
description: Dieser Abschnitt stellt eine Checkliste zum Entfernen von Komponenten innerhalb des Azure Virtual Network Managers dar.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65987c576ff16d766cb8da88ec824f0bb5bdd40a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021520"
---
# <a name="remove-and-update-azure-virtual-network-manager-preview-components-checklist"></a>Komponenten aus der Azure-Virtual-Network-Manager-Vorschau-Checkliste entfernen und updaten

In diesem Abschnitt sehen Sie eine Checkliste an Schritten, die vonnöten sind, um eine Konfigurations-Komponente der Azure-Virtual-Network-Manager-Vorschau vollständig zu entfernen oder upzudaten.

## <a name="remove-components-checklist"></a><a name="remove"></a>Komponenten-Checkliste entfernen

| Aktion | Schritte | 
| ------ | ----- |
| Heben Sie die Konnektivitätskonfiguration auf | Wenden Sie **Keine**  Konnektivitätskonfiguration für Zielbereiche an. |
| Heben Sie die Konfiguration des Sicherheitsadministrators auf | Wenden Sie **Keine**  Konnektivitätskonfiguration für Zielbereiche an. |
| Entfernen Sie die Regeln des Sicherheitsadministrators | 1. Heben Sie die Konfiguration des Sicherheitsadministrators auf. </br> 2. Löschen Sie die Regeln des Sicherheitsadministrators, die mit der Sicherheitskonfiguration in Verbindung stehen. |
| Entfernen Sie die Sammlung von Sicherheitsregeln | 1. Heben Sie die Konfiguration des Sicherheitsadministrators auf. </br> 2. Löschen Sie die Regeln des Sicherheitsadministrators, die mit der Sicherheitskonfiguration in Verbindung stehen. </br> 3. Löschen Sie die Sammlung von Regeln. |
| Entfernen Sie die Konfiguration des Sicherheitsadministrators. | 1. Heben Sie die Konfiguration des Sicherheitsadministrators auf. </br> 2. Löschen Sie die Regeln des Sicherheitsadministrators, die mit der Sicherheitskonfiguration in Verbindung stehen. </br> 3. Löschen Sie die Sammlung von Regeln. </br> 4. Löschen Sie die Konfiguration des Sicherheitsadministrators. |
| Entfernen Sie die Konnektivitätskonfiguration. | 1. Heben Sie die Konnektivitätskonfiguration auf. </br> 2. Löschen Sie die Konnektivitätskonfiguration. |
| Eine Netzwerkgruppe entfernen | 1. Löschen Sie die Konnektivitätskonfiguration, die mit dieser Netzwerkgruppe im Zusammenhang steht </br> 2. Löschen Sie alle Sicherheitsregeln, die mit dieser Netzwerkgruppe im Zusammenhang stehen. </br> 3. Löschen Sie die Netzwerkgruppe. |
| Azure-Virtual-Network-Manager-Instanz löschen | 1. Löschen Sie alle Konnektivitäts- und Sicherheitsadministratorkonfigurationen. </br> 2. Löschen Sie alle Netzwerkgruppen. </br> 3. Löschen Sie die Azure-Virtual-Network-Manager-Instanz. |

## <a name="update-components-checklist"></a>Komponenten-Checkliste updaten

Die Informationen, die in diesem Abschnitt dargelegt werden, setzen voraus, dass Sie eine Konnektivitäts- oder Sicherheitsadministratorkonfiguration auf einen Zielbereich angewandt haben.

| Aktion | Schritte |
| ------ | ----- |
| Ein virtuelles Netzwerk hinzufügen oder entfernen, das eine statische Mitgliedschaft nutzt | 1. Fügen Sie ein virtuelles Netzwerk einer Netzwerkgruppe hinzu. </br> 2. Führen Sie eine Konnektivitäts- oder Sicherheitskonfigurtion, die die Netzwerkgruppe beinhaltet, durch. |
| Ein virtuelles Netzwerk mit Hilfe einer dynamischen Mitgliedschaft hinzufügen oder entfernen | Der Azure Virtual Network Manager nimmt automatisch Änderungen vor, um bedingte Anweisungen anzupassen. |
| Sicherheitsbestimmungen hinzufügen, entfernen oder updaten | * Wenn die Sicherheitskonfiguration auf eine Netzwerkgruppe angewandt wird, die statische Mitglieder hat, müssen Sie die Konfiguration erneut anwenden, um Wirkung zu erzielen. </br> * Netzwerkgruppen mit dynamischen Mitgliedern werden automatisch upgedated. |
| Regelsammlungen hinzufügen, entfernen oder updaten | * Wenn die Sicherheitskonfiguration auf eine Netzwerkgruppe angewandt wird, die statische Mitglieder hat, müssen Sie die Konfiguration erneut anwenden, um Wirkung zu erzielen. </br> * Netzwerkgruppen mit dynamischen Mitgliedern werden automatisch upgedated. |

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)-Instanz über das Azure-Portal.
