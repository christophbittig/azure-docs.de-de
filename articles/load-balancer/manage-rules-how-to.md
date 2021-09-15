---
title: Verwalten von Regeln für Azure Load Balancer – Azure-Portal
description: In diesem Artikel erfahren Sie, wie Sie Regeln für Azure Load Balancer mithilfe von Azure-Portal verwalten
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: e94978fa61c144ced0b405717eefc8ffecc47873
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122832297"
---
# <a name="manage-rules-for-azure-load-balancer-using-the-azure-portal"></a>Verwalten von Regeln für Azure Load Balancer mithilfe von Azure-Portal

Azure Load Balancer unterstützt Regeln zum Konfigurieren des Datenverkehrs an den Back-End-Pool.  In diesem Artikel erfahren Sie, wie Sie die Regeln für ein Azure Load Balancer verwalten.

Es gibt vier Arten von Regeln:

* Mithilfe einer **Lastenausgleichsregel** wird definiert, wie eingehender Datenverkehr auf **alle** Instanzen innerhalb des Back-End-Pools verteilt werden soll. Eine Lastenausgleichsregel ordnet mehreren Back-End-IP-Adressen und Ports eine bestimmte Front-End-IP-Konfiguration und einen Port zu. Ein Beispiel wäre eine Regel, die an Port 80 erstellt wird, um einen Lastenausgleich für Webdatenverkehr zu erstellen.

* **Hochverfügbarkeitsports**: Eine Lastenausgleichsregel, die mit **Protokoll – alle** und **Port - 0** konfiguriert ist. Diese Regel ermöglicht es, dass eine einzige Regel den Lastenausgleich aller TCP- und UDP-Datenflüsse vornimmt, die an allen Ports einer internen Lastenausgleich-Standardinstanz eingehen. Die Lastenausgleichsregeln für Hochverfügbarkeitsports unterstützen Sie bei Szenarien wie z. B. Hochverfügbarkeit und Skalierung für virtuelles Gerät in virtuellen Netzwerken. Das Feature kann hilfreich sein, wenn für eine große Anzahl von Ports ein Lastenausgleich vorgenommen werden muss.

* **NAT-Regel für eingehenden Datenverkehr**: Eine NAT-Regel für eingehenden Datenverkehr leitet eingehenden Datenverkehr weiter, der an eine Kombination aus Front-End-IP-Adresse und Port gesendet wird. Der Datenverkehr wird an einen **bestimmten** virtuellen Computer oder eine Instanz im Back-End-Pool gesendet. Der Portweiterleitung liegt die gleiche hashbasierte Verteilung zugrunde wie dem Lastenausgleich.

* **Ausgangsregel**: Eine Ausgangsregel konfiguriert die Netzwerkadressenübersetzung (Network Address Translation, NAT) für ausgehenden Datenverkehr für **alle** virtuellen Computer oder Instanzen, die vom Back-End-Pool identifiziert wurden. Durch diese Regel können Instanzen im Back-End (ausgehend) mit dem Internet oder anderen Endpunkten kommunizieren.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ein standardmäßiger öffentlicher Lastenausgleich in Ihrem Abonnement. Weitere Informationen zur Erstellung eines Azure Load Balancer finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer unter Verwendung des Azure-Portals](quickstart-load-balancer-standard-public-portal.md). Der Name des Lastenausgleichs für die Beispiele in diesem Artikel ist **myLoadBalancer**.

- Ein standardmäßiger interner Load Balancer in Ihrem Abonnement. Weitere Informationen zur Erstellung eines Azure Load Balancer finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer unter Verwendung des Azure-Portals](quickstart-load-balancer-standard-internal-portal.md). Der Name des Lastenausgleichs für die Beispiele in diesem Artikel ist **myLoadBalancer**.

## <a name="load-balancing-rules"></a>Lastenausgleichsregeln

In diesem Abschnitt erfahren Sie, wie Sie eine Lastenausgleichsregel hinzufügen und entfernen. In den Beispielen wird ein öffentlicher Lastenausgleich verwendet.

### <a name="add-a-load-balancing-rule"></a>Hinzufügen einer Lastenausgleichsregel

In diesem Beispiel erstellen Sie eine Regel für den Lastenausgleich von Port 80.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite **Lastenausgleichsregeln** in **Einstellungen** aus.

5. Wählen Sie **+ hinzufügen** in **Lastenausgleichsregeln**, um eine Regel hinzuzufügen.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Screenshot der Seite &quot;Lastenausgleichsregeln&quot; in einem standardmäßigen Load Balancer." border="true":::

6. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | **myHTTPRule** |
    | IP-Version | Wählen Sie **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse vom Lastenausgleich aus. <br> In diesem Beispiel ist sie **myFrontendIP**. |
    | Protocol | Übernehmen Sie den Standard von **TCP**. |
    | Port | Geben Sie **80** ein. |
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie den Back-End-Pool des Lastenausgleichs aus. </br> In diesem Beispiel wird **myBackendPool** verwendet. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** **myHealthProbe** ein. </br> Wählen Sie für **HTTP** in **Protokoll** aus. </br> Belassen Sie den Rest bei den Standardeinstellungen oder passen Sie ihn an Ihre Bedürfnisse an. </br> Klicken Sie auf **OK**. |
    | Sitzungspersistenz | Wählen Sie **Keine** oder die erforderliche Persistenz. </br> Weitere Informationen zu Verteilungsmodi finden Sie unter [Azure Load Balancer-Verteilungsmodi](load-balancer-distribution-mode.md). | 
    | Leerlaufzeitüberschreitung (Minuten) | Belassen Sie den Standardwert von **4** oder stellen Sie den Schieberegler auf die für Sie erforderliche Leerlauftimeout ein. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. </br> Weitere Informationen finden Sie unter [Load Balancer TCP-Zurücksetzung und Leerlauftimeout für Load Balancer](load-balancer-tcp-reset.md). |
    | Unverankerte IP | Belassen Sie die Standardeinstellung **Disabled** oder aktivieren Sie die Bereitstellung von Floating IP. </br> Weitere Informationen finden Sie unter [Konfiguration der Floating IP](load-balancer-floating-ip.md). |
    | Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Übernehmen Sie die Standardeinstellung **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern Zugriff auf das Internet zu gewähren**. </br> Weitere Informationen zu Ausgangsregeln und (SNAT) finden Sie unter [Ausgehende Regeln Azure Load Balancer](outbound-rules.md) und [Verwendung von Source Network Address Translation (SNAT) für ausgehende Verbindungen](load-balancer-outbound-connections.md).|

7. Wählen Sie **Hinzufügen**.

    :::image type="content" source="./media/manage-rules-how-to/add-load-balancing-rule.png" alt-text="Screenshot der Seite „Lastenausgleichsregel hinzufügen“." border="true":::

### <a name="remove-a-load-balancing-rule"></a>Entfernen einer Lastenausgleichsregel

In diesem Beispiel entfernen Sie eine Lastenausgleichsregel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite **Lastenausgleichsregeln** in **Einstellungen** aus.

5. Wählen Sie die drei Punkte neben der Regel aus, die Sie entfernen möchten.

6. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage-rules-how-to/remove-load-balancing-rule.png" alt-text="Screenshot vom Entfernen einer Lastenausgleichsregel." border="true":::

## <a name="high-availability-ports"></a>Hochverfügbarkeitsports

In diesem Abschnitt lernen Sie, wie Sie eine Regel für Hochverfügbarkeitsports hinzufügen und entfernen. In diesem Beispiel verwenden Sie einen internen Lastenausgleich. 

Regeln für Hochverfügbarkeitsports werden für einen standardmäßigen internen Load Balancer unterstützt.

### <a name="add-high-availability-ports-rule"></a>Hinzufügen einer Regel für Hochverfügbarkeitsports

In diesem Beispiel erstellen Sie eine Regel für Hochverfügbarkeitsports.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite **Lastenausgleichsregeln** in **Einstellungen** aus.

5. Wählen Sie **+ hinzufügen** in **Lastenausgleichsregeln**, um eine Regel hinzuzufügen.

    :::image type="content" source="./media/manage-rules-how-to/load-balancing-rules.png" alt-text="Screenshot der Seite &quot;Lastenausgleichsregeln&quot; in einem standardmäßigen Load Balancer." border="true":::

6. Geben Sie unter **Lastenausgleichsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHARule** ein. |
    | IP-Version | Wählen Sie **IPv4** oder **IPv6** aus. |
    | Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse vom Lastenausgleich aus. <br> In diesem Beispiel ist sie **myFrontendIP**. </br> Aktivieren Sie das Kästchen neben **Hochverfügbarkeitsports**. |
    | Back-End-Pool | Wählen Sie den Back-End-Pool des Lastenausgleichs aus. </br> In diesem Beispiel wird **myBackendPool** verwendet. |
    | Integritätstest | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** **myHealthProbe** ein. </br> Wählen Sie **TCP** in **Protokoll** aus. </br> Geben Sie ein TCP-Port in **Port**. In diesem Beispiel wird **80** verwendet. Geben Sie einen Port ein, der Ihren Anforderungen entspricht. </br> Belassen Sie den Rest bei den Standardeinstellungen oder passen Sie ihn an Ihre Bedürfnisse an. </br> Klicken Sie auf **OK**. |
    | Sitzungspersistenz | Wählen Sie **Keine** oder die erforderliche Persistenz. </br> Weitere Informationen zu Verteilungsmodi finden Sie unter [Azure Load Balancer-Verteilungsmodi](load-balancer-distribution-mode.md). | 
    | Leerlaufzeitüberschreitung (Minuten) | Belassen Sie den Standardwert von **4** oder stellen Sie den Schieberegler auf die für Sie erforderliche Leerlauftimeout ein. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. </br> Weitere Informationen finden Sie unter [Load Balancer TCP-Zurücksetzung und Leerlauftimeout für Load Balancer](load-balancer-tcp-reset.md). |
    | Unverankerte IP | Belassen Sie die Standardeinstellung **Disabled** oder aktivieren Sie die Bereitstellung von Floating IP. </br> Weitere Informationen finden Sie unter [Konfiguration der Floating IP](load-balancer-floating-ip.md). |

    Weitere Informationen über Hochverfügbarkeitsports finden Sie unter **[Übersicht über Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md)** .

7. Wählen Sie **Hinzufügen**.

    :::image type="content" source="./media/manage-rules-how-to/add-ha-ports-load-balancing-rule.png" alt-text="Screenshot der Seite &quot;Lastenausgleich-Hochverfügbarkeitsportsregel hinzufügen“." border="true":::

### <a name="remove-a-high-availability-ports-rule"></a>Entfernen einer Hochverfügbarkeitsportsregel

In diesem Beispiel entfernen Sie eine Lastenausgleichsregel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite **Lastenausgleichsregeln** in **Einstellungen** aus.

5. Wählen Sie die drei Punkte neben der Regel aus, die Sie entfernen möchten.

6. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage-rules-how-to/remove-ha-ports-load-balancing-rule.png" alt-text="Screenshot vom Entfernen einer Lastenausgleich-Hochverfügbarkeitsportsregel" border="true":::.

## <a name="inbound-nat-rule"></a>Eingehende NAT-Regel

NAT-Regeln für eingehenden Datenverkehr werden verwendet, um Verbindungen zu einem bestimmten virtuellen Computer im Back-End-Pool weiterzuleiten. Weitere Informationen und ein ausführliches Tutorial zum Konfigurieren und Testen von NAT-Regeln für eingehenden Datenverkehr finden Sie unter [Tutorial: Konfigurieren der Portweiterleitung in Azure Load Balancer unter Verwendung des Portals](tutorial-load-balancer-port-forwarding-portal.md).

## <a name="outbound-rule"></a>Ausgehende Regel

In diesem Abschnitt lernen Sie, wie Sie eine Ausgangsregel hinzufügen und entfernen. In diesem Beispiel verwenden Sie einen öffentlichen Lastenausgleich. 

Ausgangsregeln werden für standardmäßige öffentliche Load Balancer Lastenausgleiche unterstützt.

### <a name="add-outbound-rule"></a>Ausgangsregel hinzufügen

In diesem Beispiel erstellen Sie eine Ausgangsregel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite „Lastenausgleich“ **Ausgangsregeln** in **Einstellungen** aus.

5. Wählen Sie **+ hinzufügen** in **Ausgangsregeln**, um eine Regel hinzuzufügen.

    :::image type="content" source="./media/manage-rules-how-to/outbound-rules.png" alt-text="Screenshot der Seite &quot;Ausgangsregeln&quot; in einem standardmäßigen Load Balancer." border="true":::

6. Geben Sie unter **Ausgangsregel hinzufügen** die folgenden Informationen ein, oder wählen Sie sie aus.

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myOutboundRule** ein. |
    | Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse vom Lastenausgleich aus. <br> In diesem Beispiel ist sie **myFrontendIP**. | 
    | Protocol | Übernehmen Sie die Standardeinstellung **Alle**. |
    | Leerlaufzeitüberschreitung (Minuten) | Belassen Sie den Standardwert von **4** oder stellen Sie den Schieberegler auf die für Sie erforderliche Leerlauftimeout ein. |
    | TCP-Zurücksetzung | Übernehmen Sie die Standardeinstellung **Aktiviert**. |
    | Back-End-Pool | Wählen Sie den Back-End-Pool des Lastenausgleichs aus. </br> In diesem Beispiel wird **myBackendPool** verwendet. |
    | **Portzuordnung** |   |
    | Portzuordnung | Wählen Sie **Anzahl ausgehender Ports manuell auswählen** aus. |
    | **Ausgehende Ports** |  |
    | Auswählen nach | Wählen Sie **Ports pro Instanz** aus. |
    | Ports pro Instanz | Geben Sie **10000** ein. |

7. Wählen Sie **Hinzufügen**.

    :::image type="content" source="./media/manage-rules-how-to/add-outbound-rule.png" alt-text="Screenshot der Seite &quot;Ausgangsregel hinzufügen&quot;" border="true":::.

### <a name="remove-an-outbound-rule"></a>Entfernen einer Ausgangsregel

In diesem Beispiel entfernen Sie eine Ausgangsregel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Lastenausgleich** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus.

3. Wählen Sie **myLoadBalancer** oder Ihren Lastenausgleich.

4. Wählen Sie auf der Seite „Lastenausgleich“ **Ausgangsregeln** in **Einstellungen** aus.

5. Wählen Sie die drei Punkte neben der Regel aus, die Sie entfernen möchten.

6. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/manage-rules-how-to/remove-outbound-rule.png" alt-text="Screenshot: Entfernen einer Ausgangsregel" border="true":::.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Lastenausgleichsregeln für ein Azure Load Balancer verwalten.

Weitere Informationen über Azure Load Balancer finden Sie unter:
- [Was ist Azure Load Balancer?](load-balancer-overview.md)
- [Häufig gestellte Fragen: Azure Load Balancer](load-balancer-faqs.yml)
