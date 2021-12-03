---
title: Erstellen eines Privaten Links für einen Streaming-Endpunkt
description: In diesem Abschnitt erfahren Sie, wie Sie einen privaten Link mit einem Streaming-Endpunkt nutzen. Sie erstellen eine private Endpunktressource, die eine Verbindung zwischen einem virtuellen Netzwerk und einem Streaming-Endpunkt darstellt. Diese Bereitstellung erstellt eine IP-Adresse der Netzwerkschnittstelle innerhalb des virtuellen Netzwerks. Mit dem privaten Link können Sie die Netzwerkschnittstelle im privaten Netzwerk mit dem Streamingendpunkt im Media-Services-Konto verbinden. Außerdem erstellen Sie DNS-Zonen, die die privaten IP-Adressen weiterleiten.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 3175925fe8d5273ec5f9bb0220b439a01df5ef11
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095605"
---
# <a name="create-a-private-link-for-a-streaming-endpoint"></a>Erstellen eines Privaten Links für einen Streaming-Endpunkt

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Abschnitt erfahren Sie, wie Sie einen privaten Link mit einem Streaming-Endpunkt nutzen. Es wird davon ausgegangen, dass Sie bereits wissen, wie Sie eine [Azure-Ressourcengruppe](/azure-resource-manager/management/manage-resource-groups-portal), ein [Media-Services-Konto](account-create-how-to.md)und ein [virtuelles Azure-Netzwerk erstellen.](/virtual-network/quick-create-portal)

Sie erstellen eine private Endpunktressource, die eine Verbindung zwischen einem virtuellen Netzwerk und einem Streaming-Endpunkt darstellt. Diese Bereitstellung erstellt eine IP-Adresse der Netzwerkschnittstelle innerhalb des virtuellen Netzwerks. Mit dem privaten Link können Sie die Netzwerkschnittstelle im privaten Netzwerk mit dem Streamingendpunkt im Media-Services-Konto verbinden. Außerdem erstellen Sie DNS-Zonen, die die privaten IP-Adressen weiterleiten.

Das virtuelle Netzwerk, das für diese exemplarische Vorgehensweise (Walkthrough) erstellt wurde, dient nur zur Unterstützung dieses Beispiels.  Es wird davon ausgegangen, dass Sie über ein vorhandenes virtuelles Netzwerk verfügen, das Sie für die Produktion verwenden.

> [!NOTE]
> Wenn Sie die Schritte ausführen, benennen Sie Ihre Ressourcen auf ähnliche Weise, damit sie leicht als ein ähnlicher Zweck verstanden werden können.  Beispiel: *privatelink1stor* für Ihr Speicherkonto und *privatelink1mi* für Ihre verwaltete Identität.

## <a name="create-a-resource-group-and-a-media-services-account"></a>Erstellen einer Ressourcengruppe und eines Media-Services-Kontos

1. Erstellen einer Azure-Ressourcengruppe.
1. Erstellen Sie ein Media Services-Konto.  Beim Erstellen des Kontos wird ein Standard-Streaming-Endpunkt erstellt. Das Erstellen einer verwalteten Identität ist während des Setup-Vorgangs erforderlich.
1. Erstellen Sie ein virtuelles Azure-Netzwerk mit den Standardeinstellungen.

An diesem Punkt ist in Ihrem virtuellen Netzwerk nichts enthalten und Ihr Media-Services-Konto verfügt über einen mit dem Internet verbundenen Endpunkt, der über einen mit dem Internet verbundenen Streaming-Endpunkt, Schlüsselbereitstellung und Live-Ereignisse (Live Events) verfügt.  Im nächsten Schritt wird der Streaming-Endpunkt privat.

## <a name="start-the-streaming-endpoint"></a>Starten des Streamingendpunkts

1. Navigieren Sie zu dem Media-Services-Konto, das Sie erstellt haben.  
1. Wählen Sie aus dem Menü-Bereich **Streaming-Endpunkte** aus. Der Bildschirm Streaming-Endpunkte wird angezeigt.
1. Wählen Sie den standardmäßigen Streaming-Endpunkt aus, den Sie beim Einrichten des Media-Services-Konto erstellt haben.  Der Standardbildschirm für den Streaming-Endpunkt wird angezeigt.
1. Wählen Sie **Starten** aus. Startoptionen werden angezeigt.
1. Wählen Sie **Keine** aus der Dropdown-Liste CDN Tarif aus.
1. Wählen Sie **Starten** aus.  Dadurch wird der Streaming-Endpunkt gestartet. Der Endpunkt ist weiterhin mit dem Internet verbunden.

## <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

1. Navigieren Sie zurück zu Ihrem Media-Services-Konto.
1. Wählen Sie aus dem Menü die Option **Netzwerken** aus.
1. Wählen Sie die Registerkarte **Private Endpunktverbindungen** aus. Der Bildschirm für private Endpunktverbindungen wird angezeigt.
1. Wählen Sie **Privaten Endpunkt hinzufügen** aus. Der Bildschirm Privaten Endpunkt erstellen wird angezeigt.
1. Geben Sie dem privaten Endpunkt im Feld **Name** einen Namen, wie z. B. *privatelinkpe*.
1. Wählen Sie aus der Dropdownliste **Region** eine Region aus, z. B. *USA, Westen 2.*
1. Klicken Sie auf **Weiter: Ressource** aus. Der Bildschirm Ressource wird angezeigt.

## <a name="assign-the-private-endpoint-to-a-resource"></a>Zuweisen des privaten Endpunkts zu einer Ressource

1. Wählen Sie im Optionsfeld **Verbindungsmethoden** das Optionsfeld *Mit einer Azure-Ressource in meinem Verzeichnis verbinden (Connect to an Azure resource in my directory)* aus.
1. Wählen Sie aus der Dropdown-Liste **Ressourcentyp** *Microsoft.Media/mediaservices*.
1. Wählen Sie aus der Dropdown-Liste **Ressource** das Media-Services-Konto aus, das Sie erstellt haben.
1. Wählen Sie aus der Dropdownliste **Zielunterressource** den von Ihnen erstellten Streaming-Endpunkt aus.

## <a name="deploy-the-private-endpoint-to-the-virtual-network"></a>Bereitstellen des privaten Endpunkts im virtuellen Netzwerk

1. Aus der Dropdown-Liste **Virtuelles Netzwerk** wählen Sie das von Ihnen erstellte virtuelle Netzwerk aus.
1. Wählen Sie aus der Dropdown-Liste **Subnetz** das Subnetz aus, mit dem Sie sich verbinden möchten.
1. Bleiben Sie auf diesem Bildschirm.

## <a name="create-dns-zones-to-use-with-the-private-endpoint"></a>Erstellen von DNS-Zonen für die Verwendung mit dem privaten Endpunkt

Um den Streaming-Endpunkt in Ihrem virtuellen Netzwerk zu nutzen, erstellen Sie private DNS-Zonen. Sie können den gleichen DNS-Namen verwenden und die private IP-Adresse des Streaming-Endpunkts erhalten.

1. Wählen Sie auf demselben Bildschirm für die Konfiguration des **Media-Azure-Netzes (media-azure-net)** aus der Dropdown-Liste **Ressourcengruppe** die Ressourcengruppe aus, die Sie erstellt haben.
1. Für die Konfiguration **Privatelink-Media-Azure-Netz** wählen Sie aus der Dropdown-Liste **Ressourcengruppe** die Ressourcengruppe aus, die Sie erstellt haben.
1. Klicken Sie auf **Weiter: Tags**. Wenn Sie Ihren Ressourcen Tags hinzufügen möchten, können Sie das hier tun.
1. Klicken Sie auf **Weiter: Überprüfen + erstellen**. Der Bildschirm Überprüfen + erstellen wird angezeigt.
1. Überprüfen Sie Ihre Einstellungen und gehen Sie sicher, dass sie richtig sind.
1. Klicken Sie auf **Erstellen**. Der Bildschirm Bereitstellung des privaten Endpunkts wird angezeigt.

Während der Bereitstellung wird auch eine [Azure-Resource-Manager-(ARM-)Vorlage](/azure-resource-manager/templates/overview) erstellt. Sie können ARM-Vorlagen verwenden, um die Bereitstellung zu automatisieren. Um die Vorlage einzusehen, wählen Sie im Menü **Vorlage** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht planen, die in dieser Übung erstellten Ressourcen zu verwenden, löschen Sie einfach die Ressourcengruppe. Wenn Sie die Ressourcen nicht löschen, werden Ihnen diese in Rechnung gestellt.