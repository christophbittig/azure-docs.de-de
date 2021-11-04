---
title: Blockieren von Netzwerkverkehr mit Azure Virtual Network Manager (Vorschau) - Azure-Portal
description: Erfahren Sie, wie Sie den Netzwerkverkehr mithilfe von Sicherheitsregeln in Azure Virtual Network Manager mit dem Azure-Portal blockieren können.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 20adb2a748f839d63122a1fc5904746a29af33ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096039"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-portal"></a>Blockieren von Netzwerkverkehr mit Azure Virtual Network Manager (Vorschau) - Azure-Portal

Dieser Artikel zeigt Ihnen, wie Sie eine Sicherheitsverwaltungsregel erstellen, um den eingehenden Netzwerkverkehr an RDP-Port 3389 zu blockieren, die Sie zu einer Regelsammlung hinzufügen können. Weitere Informationen finden Sie unter [Sicherheitsverwaltungsregeln](concept-security-admins.md).

> [!IMPORTANT]
> Azure Virtual Network Manager befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der Konfiguration der Sicherheitsverwaltungsregeln beginnen, vergewissern Sie sich, dass Sie die folgenden Schritte ausgeführt haben:

* Sie verstehen jedes Element in einer [Sicherheitsverwaltungsregel](concept-security-admins.md).
* Sie haben eine [Azure Virtual Network Manager-Instanz](create-virtual-network-manager-portal.md) erstellt.

## <a name="create-a-securityadmin-configuration"></a>Erstellen einer SecurityAdmin-Konfiguration

1. Wählen Sie **Konfigurationen** unter *Einstellungen* und wählen Sie dann **+ Konfiguration hinzufügen**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-admin.png" alt-text="Screenshot der Sicherheitsadministrationskonfiguration hinzufügen ":::

1. Wählen Sie **SecurityAdmin** aus dem Dropdown-Menü.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-admin-drop-down.png" alt-text="Screenshot des Dropdown-Menüs zum Hinzufügen einer Konfiguration. ":::

1. Geben Sie einen *Namen* ein, um diese Sicherheitskonfiguration zu identifizieren.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-configuration-name.png" alt-text="Screenshot des Namensfeldes der Sicherheitskonfiguration. ":::

## <a name="add-a-rule-collection"></a>Hinzufügen einer Regelsammlung

1. Wählen Sie **+ Regelsammlung hinzufügen**, um Ihr Regelwerk zu erstellen.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-collection-button.png" alt-text="Screenshot der Schaltfläche zum Hinzufügen einer Regelsammlung.":::

1. Geben Sie einen *Namen* ein, um diese Regelsammlung zu identifizieren, und wählen Sie dann die *Netzwerkgruppen*, auf die Sie die Regelsammlung anwenden möchten.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/rule-collection-target.png" alt-text="Screenshot des Namens der Regelsammlung und der Zielnetzgruppen. ":::

## <a name="add-a-security-rule"></a>Hinzufügen einer Sicherheitsregel

1. Wählen Sie **+ Regel hinzufügen** auf der Seite *Regelsammlung hinzufügen*.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-button.png" alt-text="Screenshot der Schaltfläche &quot;Regel hinzufügen&quot;.":::

1. Geben Sie die folgenden Informationen ein oder wählen Sie aus, und wählen Sie dann **Hinzufügen**, um die Regel zur Regelsammlung hinzuzufügen.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule.png" alt-text="Screenshot der Seite zum Hinzufügen einer Regel.":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie den Namen **Deny_RDP** für den Namen der Regel ein. |
    | BESCHREIBUNG | Geben Sie eine Beschreibung der Regel ein. |
    | Vorrang* | Geben Sie einen Wert zwischen 0 und 99 ein, um die Priorität der Regel festzulegen. Je niedriger der Wert, desto höher die Priorität. Geben Sie für dieses Beispiel **1** ein|
    | Aktion* | Wählen Sie **Abweisen**, um den Datenverkehr zu blockieren. Für weitere Informationen siehe [Aktion](concept-security-admins.md#action)
    | Richtung* | Wählen Sie **Eingehend**, da Sie mit dieser Regel eingehenden Verkehr ablehnen möchten. |
    | Protokoll* | Wählen Sie das Protokoll **TCP**. HTTP und HTTPS sind TCP-Ports. |
    | Quellentyp | Wählen Sie als Quellentyp entweder **IP-Adresse** oder **Service-Tags**. |
    | Quell-IP-Adressen | Dieses Feld wird angezeigt, wenn Sie den Quellentyp *IP-Adresse* wählen. Geben Sie eine IPv4- oder IPv6-Adresse oder einen Bereich in CIDR-Notation ein. Wenn Sie mehr als eine Adresse oder Adressblöcke definieren, trennen Sie diese durch ein Komma. Für dieses Beispiel leer lassen.|
    | Quelldiensttag | Dieses Feld wird eingeblendet, wenn Sie den Quellentyp *Service-Tag* wählen. Wählen Sie Service-Tag(s) für Services, die Sie als Quelle angeben möchten. Siehe [Verfügbare Service-Tags](../virtual-network/service-tags-overview.md#available-service-tags) für die Liste der unterstützten Tags. |
    | Quellport | Geben Sie eine einzelne Anschlussnummer oder einen Anschlussbereich ein, z. B. (1024-65535). Wenn Sie mehr als einen Anschluss oder Anschlussbereich definieren, trennen Sie diese durch ein Komma. Um einen beliebigen Anschluss anzugeben, geben Sie * ein. Für dieses Beispiel leer lassen.|
    | Zieltyp | Wählen Sie als Zieltyp entweder **IP-Adresse** oder **Service-Tags**. |
    | Ziel-IP-Adressen | Dieses Feld wird angezeigt, wenn Sie den Zieltyp *IP-Adresse* wählen. Geben Sie eine IPv4- oder IPv6-Adresse oder einen Bereich in CIDR-Notation ein. Wenn Sie mehr als eine Adresse oder Adressblöcke definieren, trennen Sie diese durch ein Komma. |
    | Zieldiensttag | Dieses Feld erscheint, wenn Sie den Zieltyp *Service-Tag* wählen. Wählen Sie Dienstkennzeichen für Dienste, die Sie als Ziel angeben möchten. Siehe [Verfügbare Service-Tags](../virtual-network/service-tags-overview.md#available-service-tags) für die Liste der unterstützten Tags. |
    | Zielport | Geben Sie eine einzelne Anschlussnummer oder einen Anschlussbereich ein, z. B. (1024-65535). Wenn Sie mehr als einen Anschluss oder Anschlussbereich definieren, trennen Sie diese durch ein Komma. Um einen beliebigen Anschluss anzugeben, geben Sie * ein. Geben Sie für dieses Beispiel **3389** ein. |

1. Wiederholen Sie die Schritte 1-3, wenn Sie der Regelsammlung weitere Regeln hinzufügen möchten.

1. Wenn Sie mit allen Regeln, die Sie erstellen möchten, zufrieden sind, wählen Sie **Speichern**, um die Regelsammlung zur Sicherheitsverwaltungskonfiguration hinzuzufügen.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/save-rule-collection.png" alt-text="Screenshot einer Regel in einer Regelsammlung.":::

1. Wählen Sie dann **Hinzufügen**, um die Konfiguration zu erstellen.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-configuration.png" alt-text="Screenshot der Schaltfläche &quot;Hinzufügen&quot; zur Erstellung einer Sicherheitskonfiguration. ":::

## <a name="deploy-the-security-admin-configuration"></a>Bereitstellen der Sicherheitsverwaltungskonfiguration

Wenn Sie gerade eine neue Sicherheitsadministrationskonfiguration erstellt haben, stellen Sie sicher, dass diese Konfiguration für die virtuellen Netzwerke in der Netzwerkgruppe gilt.

1. Wählen Sie **Einrichtungen** unter *Einstellungen*, dann **Konfiguration einrichten**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-configuration.png" alt-text="Screenshot der Schaltfläche &quot;Konfiguration einrichten&quot;. ":::

1. Wählen Sie den Konfigurationstyp **SecurityAdmin** und die Konfiguration, die Sie im letzten Abschnitt erstellt haben. Wählen Sie dann die Region(en), für die Sie diese Konfiguration bereitstellen möchten, und wählen Sie **Bereitstellen**.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-security-configuration.png" alt-text="Screenshot der Seite zur Bereitstellung einer Sicherheitskonfiguration. ":::

1. Wählen Sie **OK**, um zu bestätigen, dass Sie eine vorhandene Konfiguration überschreiben und die Sicherheitsverwaltungskonfiguration bereitstellen möchten.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="Screenshot der Bestätigungsmeldung für die Bereitstellung einer Sicherheitskonfiguration.":::

## <a name="update-existing-security-admin-configuration"></a>Aktualisierung der bestehenden Sicherheitsverwaltungskonfiguration

* Wenn die Sicherheitsverwaltungskonfiguration, die Sie aktualisieren, auf eine Netzwerkgruppe mit statischen Mitgliedern angewendet wird, müssen Sie die Konfiguration erneut bereitstellen, damit sie wirksam wird.
* Sicherheitsverwaltungskonfigurationen werden automatisch auf dynamische Mitglieder in einer Netzwerkgruppe angewendet.

## <a name="verify-security-admin-rules"></a>Überprüfen der Sicherheitsverwaltungsregeln

Gehen Sie zu den Einstellungen **Netzwerke** für eine virtuelle Maschine in einem der virtuellen Netzwerke, auf die Sie die Sicherheitsverwaltungsregeln angewendet haben. Wenn Sie keins haben, stellen Sie eine virtuelle Testmaschine in einem der virtuellen Netzwerke bereit. Unterhalb der Netzwerksicherheitsgruppenregeln sehen Sie nun einen neuen Abschnitt mit den Sicherheitsregeln, die von Azure Virtual Network Manager angewendet werden.

:::image type="content" source="./media/how-to-block-network-traffic-portal/vm-security-rules.png" alt-text="Screenshot der Sicherheitsverwaltungsregeln unter den Netzwerkeinstellungen der virtuellen Maschine." lightbox="./media/how-to-block-network-traffic-portal/vm-security-rules-expanded.png":::

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Sicherheitsverwaltungsregeln](concept-security-admins.md).
