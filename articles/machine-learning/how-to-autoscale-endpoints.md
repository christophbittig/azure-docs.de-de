---
title: Autoskalierung für verwaltete Onlineendpunkte
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie verwaltete Endpunkte hochskalieren. Profitieren Sie von mehr CPU, Speicher, Speicherplatz und zusätzlichen Features.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: f979651909a2484f6bcdf7b0953c91874bea7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520653"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>Autoskalierung für einen verwalteten Onlineendpunkt (Vorschau)

Die Autoskalierung führt automatisch die richtige Menge an Ressourcen aus, um die Last Ihrer Anwendung zu bewältigen. [Verwaltete Endpunkte](concept-endpoints.md) unterstützen die automatische Skalierung durch die Integration mit dem Feature zur Azure Monitor-Autoskalierung.

Die automatische Azure Monitor-Skalierung unterstützt eine Vielzahl von Regeln. Sie können metrikbasierte Skalierung (z. B. CPU-Auslastung >70 %), zeitplanbasierte Skalierung (z. B. Skalierungsregeln für Hauptgeschäftszeiten) oder eine Kombination davon konfigurieren. Weitere Informationen finden Sie unter [Überblick über Autoskalierung in Microsoft Azure](/azure-monitor/autoscale/autoscale-overview.md).

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="Diagramm für die Autoskalierung beim bedarfsgesteuerten Hinzufügen/Entfernen einer Instanz":::

Derzeit können Sie die automatische Skalierung entweder mithilfe der Azure-CLI oder über REST, ARM oder das browserbasierte Azure-Portal verwalten. Andere Azure ML SDKs, z. B. das Python SDK, fügen im Laufe der Zeit die entsprechende Unterstützung hinzu.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen 

* Ein bereitgestellter Endpunkt. [Bereitstellen und Bewerten eines Machine Learning-Modells mithilfe eines verwalteten Onlineendpunkts (Vorschau)](how-to-deploy-managed-online-endpoints.md). 

## <a name="define-an-autoscale-profile"></a>Definieren eines Profils für die automatische Skalierung

Zum Aktivieren der Autoskalierung für einen Endpunkt legen Sie zuerst ein Profil für die automatische Skalierung fest. Dieses Profil definiert die standardmäßige, minimale und maximale Kapazität der Skalierungsgruppe. Im folgenden Beispiel wird die Standard- und Mindestkapazität auf zwei VM-Instanzen und die Höchstkapazität auf fünf VM-Instanzen festgelegt:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Der folgende Codeausschnitt legt die Endpunkt- und Bereitstellungsnamen fest:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

Als nächstes erhalten Sie die Azure Resource Manager-ID der Bereitstellung und des Endpunkts:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

Mit dem folgenden Codeausschnitt wird das Profil für die Autoskalierung erstellt:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> Weitere Informationen finden Sie auf der [Referenzseite für die Autoskalierung](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie in [Azure Machine Learning Studio](https://ml.azure.com) Ihren Arbeitsbereich dann links auf der Seite die Option __Endpunkte__ aus. Nachdem die Endpunkte aufgelistet sind, wählen Sie den Endpunkt aus, den Sie konfigurieren möchten.

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="Screenshot: Endpunktbereitstellungseintrag im Portal":::

Wählen Sie auf der Registerkarte __Details__ des Endpunkts die Option __Configure auto scaling__ (Automatische Skalierung konfigurieren) aus.

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="Screenshot: Link zum Konfigurieren der automatischen Skalierung in den Endpunktdetails":::

Wählen Sie unter __Choose how to scale your resources__ (Skalierung Ihrer Ressourcen auswählen) die Option __Custom autoscale__ (Benutzerdefinierte automatische Skalierung) aus, um die Konfiguration zu beginnen. Verwenden Sie für die Standardskalierungsbedingung die folgenden Werte:

* Legen Sie den __Skalierungsmodus__ auf __Basierend auf einer Metrik skalieren__ fest.
* Legen Sie __Minimum__ auf __2__ fest.
* Legen Sie __Maximum__ auf __5__ fest.
* Legen Sie __Standard__ auf __2__ fest.

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="Screenshot: Auswahl der benutzerdefinierten Autoskalierung":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>Erstellen einer Regel zum Aufskalieren mithilfe von Metriken

Eine gängige Regel für das Aufskalieren besteht darin, die Anzahl der VM-Instanzen zu erhöhen, wenn die durchschnittliche CPU-Auslastung hoch ist. Im folgenden Beispiel werden zwei weitere Knoten (bis zum Maximalwert) zugeordnet, wenn die CPU-Auslastung fünf Minuten lang durchschnittlich über 70 % liegt:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

Die Regel ist Teil des `my-scale-settings`-Profils (`autoscale-name` entspricht dem `name` des Profils). Der Wert des Arguments `condition` besagt, dass die Regel ausgelöst werden soll, wenn „die durchschnittliche CPU-Auslastung der VM-Instanzen fünf Minuten lang 70 % überschreitet“. Wenn diese Bedingung erfüllt ist, werden zwei weitere VM-Instanzen zugeordnet. 

> [!NOTE]
> Weitere Informationen zur CLI-Syntax finden Sie unter [`az monitor autoscale`](/cli/azure/monitor/autoscale).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Abschnitt __Regeln__ die Option __Regel hinzufügen__ aus. Die Seite __Skalierungsregel__ wird angezeigt. Verwenden Sie die folgenden Informationen zum Füllen der Felder auf dieser Seite:

* Legen Sie __Metrikname__ auf __Prozentsatz der CPU-Auslastung__ fest.
* Legen Sie __Operator__ auf __Größer als__ und __Metrikschwellenwert__ auf __70__ fest.
* Legen Sie __Dauer (Minuten)__ auf 5 fest. Belassen Sie für die Option __Statistik zum Aggregationsintervall__ den Wert __Durchschnitt__.
* Legen Sie __Vorgang__ auf __Anzahl erhöhen um__ und __Instanzenanzahl__ auf __2__ fest.

Wählen Sie schließlich die Schaltfläche __Hinzufügen__ aus, um die Regel zu erstellen.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="Screenshot: Regel zum Aufskalieren „>70 % CPU-Auslastung für 5 Minuten“":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>Erstellen einer Regel zum Abskalieren mithilfe von Metriken

Bei geringer Auslastung kann eine Skalierungsregel die Anzahl der VM-Instanzen reduzieren. Das folgende Beispiel gibt einen einzelnen Knoten frei (bis auf ein Minimum von zwei Knoten), wenn die CPU-Auslastung fünf Minuten lang weniger als 30 % beträgt:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie im Abschnitt __Regeln__ die Option __Regel hinzufügen__ aus. Die Seite __Skalierungsregel__ wird angezeigt. Verwenden Sie die folgenden Informationen zum Füllen der Felder auf dieser Seite:

* Legen Sie __Metrikname__ auf __Prozentsatz der CPU-Auslastung__ fest.
* Legen Sie __Operator__ auf __Kleiner als__ und den __Metrikschwellenwert__ auf __30__ fest.
* Legen Sie __Dauer (Minuten)__ auf __5__ fest.
* Legen Sie __Vorgang__ auf __Anzahl verringern um__ und __Instanzenanzahl__ auf __1__ fest.

Wählen Sie schließlich die Schaltfläche __Hinzufügen__ aus, um die Regel zu erstellen.

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="Screenshot: Regel für das Abskalieren":::

Wenn Sie sowohl Regeln für die Auf- als auch für die Abskalierung besitzen, sehen Ihre Regeln ähnlich aus wie im folgenden Screenshot. Sie haben festgelegt, dass, wenn die durchschnittliche CPU-Auslastung fünf Minuten lang 70 % übersteigt, zwei weitere Knoten zugewiesen werden sollen, bis zu einer Höchstgrenze von fünf Knoten. Wenn die CPU-Auslastung fünf Minuten lang weniger als 30 % beträgt, sollte ein einzelner Knoten freigegeben werden, und zwar bis auf den Mindestwert von zwei Knoten. 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="Screenshot: Einstellungen für die Autoskalierung, einschließlich Regeln":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>Erstellen einer Skalierungsregel basierend auf Endpunktmetriken

Die vorherigen Regeln, die auf die Bereitstellung angewendet wurden. Fügen Sie nun eine Regel hinzu, die für den Endpunkt gilt. Wenn die Anforderungslatenz in diesem Beispiel fünf Minuten lang durchschnittlich über 70 Millisekunden liegt, ordnen Sie einen anderen Knoten zu.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie unten auf der Seite __+ Skalierungsbedingung hinzufügen__ aus.

Wählen Sie __Auf Basis von Metrik skalieren__ und dann __Regel hinzufügen__ aus. Die Seite __Skalierungsregel__ wird angezeigt. Verwenden Sie die folgenden Informationen zum Füllen der Felder auf dieser Seite:

* Legen Sie __Metrikquelle__ auf __Andere Ressource__ fest.
* Legen Sie __Ressourcentyp__ auf __Onlineendpunkte für maschinelles Lernen__ fest.
* Legen Sie __Ressource__ auf Ihren Endpunkt fest.
* Legen Sie __Metrikname__ auf __Anforderungslatenz__ fest.
* Legen Sie __Operator__ auf __Größer als__ und __Metrikschwellenwert__ auf __70__ fest.
* Legen Sie __Dauer (Minuten)__ auf __5__ fest.
* Legen Sie __Vorgang__ auf __Anzahl erhöhen um__ und __Instanzenanzahl__ auf „1“ fest.

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="Screenshot: Regeln für Endpunktmetriken":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>Erstellen von Skalierungsregeln basierend auf einem Zeitplan

Sie können auch Regeln erstellen, die nur an bestimmten Tagen oder zu bestimmten Zeiten gelten. In diesem Beispiel wird die Knotenanzahl am Wochenende auf „2“ festgelegt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie unten auf der Seite __+ Skalierungsbedingung hinzufügen__ aus. Verwenden Sie in der neuen Skalierungsbedingung die folgenden Informationen, um die Felder zu füllen:
 
* Wählen Sie __Auf eine bestimmte Anzahl von Instanzen skalieren__ aus.
* Legen Sie __Instanzenanzahl__ auf __2__ fest.
* Legen Sie __Zeitplan__ auf __An bestimmten Tagen wiederholen__ fest.
* Legen Sie für den Zeitplan die Option __Wiederholen jeden__ auf __Samstag__ und __Sonntag__ fest.

:::image type="content" source="media/how-to-autoscale-endpoints/schedule-rules.png" lightbox="media/how-to-autoscale-endpoints/schedule-rules.png" alt-text="Screenshot: Zeitplanbasierte Regeln":::

---

## <a name="delete-resources"></a>Löschen von Ressourcen

Wenn Sie Ihre Bereitstellungen nicht verwenden möchten, löschen Sie sie:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Autoskalierung mit Azure Monitor finden Sie in den folgenden Artikeln:

- [Grundlegendes zu Einstellungen für die automatische Skalierung](/azure-monitor/autoscale/autoscale-understand-settings)
- [Übersicht über allgemeine Muster für die automatische Skalierung](/azure-monitor/autoscale/autoscale-common-scale-patterns)
- [Bewährte Methoden für die automatische Skalierung](/azure-monitor/autoscale/autoscale-best-practices)
- [Problembehandlung für automatische Skalierung in Azure](/azure-monitor/autoscale/autoscale-troubleshoot)
