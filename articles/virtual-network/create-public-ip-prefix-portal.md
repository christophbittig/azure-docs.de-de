---
title: Erstellen eines Präfix für öffentliche IP-Adressen – Azure-Portal
titlesuffix: Azure Virtual Network
description: Hier erfahren Sie, wie Sie mithilfe des Azure-Portals ein Präfix für eine öffentliche IP-Adresse erstellen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/06/2021
ms.author: allensu
ms.openlocfilehash: 3a1dfefb034816038af4401a22ee87f7f1914c54
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598445"
---
# <a name="create-a-public-ip-address-prefix-using-the-azure-portal"></a>Erstellen eines Präfix für eine öffentliche IP-Adresse mithilfe des Azure-Portals

Sie erhalten Informationen über Präfixe öffentlicher IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Das Präfix einer öffentlichen IP-Adresse ist ein zusammenhängender Bereich öffentlicher IP-Adressen mit Standard-SKU. 

Wenn Sie eine öffentliche IP-Adressressource erstellen, können Sie eine statische öffentliche IP-Adresse aus dem Präfix zuweisen und diese Adresse VMs, Lastenausgleichsmodulen oder anderen Ressourcen zuordnen. Weitere Informationen finden Sie in der [Übersicht über Präfixe für öffentliche IP-Adressen](public-ip-address-prefix.md).

## <a name="prerequisites"></a>Voraussetzungen

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.

## <a name="create-a-public-ip-address-prefix"></a>Erstellen des Präfix einer öffentlichen IP-Adresse

In diesem Abschnitt erstellen Sie das Präfix für öffentliche IP-Adressen im Azure-Portal.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

3. Wählen Sie in den Suchergebnissen **Öffentliche IP-Präfixe** aus.

4. Wählen Sie **+ Erstellen** aus.

5. Geben Sie auf der Registerkarte **Grundlagen** unter **Präfix für öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** |   |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroup** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |   |
    | Name | Geben Sie **myPublicIPprefix** ein. |
    | Region | Wählen Sie **USA, Westen 2** aus. |
    | IP-Version | Übernehmen Sie den Standardwert **IPv4**. |
    | Präfixgröße | Wählen Sie Ihre Präfixgröße aus. |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="Erstellen eines Präfix für öffentliche IP-Adressen im Azure-Portal" border="true":::
    
    > [!NOTE]
    >Wählen Sie **IPv6** als **IP-Version** aus, um ein IPv6-Präfix zu erstellen.

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="Erstellen eines Präfix für öffentliche IPv6-Adressen im Azure-Portal" border="true":::

6. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder unten auf der Seite die blaue Schaltfläche **Überprüfen + erstellen** aus.

7. Klicken Sie auf **Erstellen**.

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Erstellen einer statischen öffentlichen IP-Adresse aus einem Präfix
Sobald Sie ein Präfix erstellt haben, müssen Sie statische IP-Adressen aus dem Präfix erstellen. In diesem Abschnitt erstellen Sie eine statische IP-Adresse aus dem Präfix, das Sie zuvor erstellt haben.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Präfixe** aus.

3. Wählen Sie unter **Öffentliche IP-Präfixe** **myPublicIPPrefix** aus.

4. Wählen Sie in **Übersicht** unter **myPublicIPPrefix** **+ IP-Adresse hinzufügen** aus.

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="Erstellen einer öffentlichen IP-Adresse aus einem Präfix im Azure-Portal" border="true":::

5. Geben Sie **myPublicIP** unter **Name** ein. 

6. Übernehmen Sie die übrigen Standardeinstellungen.

7. Wählen Sie **Hinzufügen**.

    >[!NOTE]
    >Nur statische öffentliche IP-Adressen, die mit der Standard-SKU erstellt wurden, können aus dem Bereich des Präfix zugewiesen werden. Weitere Informationen zu SKUs für öffentliche IP-Adressen finden Sie unter [Öffentliche IP-Adressen](./public-ip-addresses.md#public-ip-addresses).

8. Wählen Sie unter **Einstellungen** die Option **Öffentliche IP-Adressen** aus, um die erstellten IP-Adressen anzuzeigen.
## <a name="delete-a-prefix"></a>Löschen eines Präfix

In diesem Abschnitt erfahren Sie, wie Sie ein Präfix aufrufen oder löschen können.

1. Geben Sie am oberen Rand des Portals den Suchbegriff **Öffentliche IP-Adresse** in das Suchfeld ein.

2. Wählen Sie in den Suchergebnissen **Öffentliche IP-Präfixe** aus.

3. Wählen Sie unter **Öffentliche IP-Präfixe** **myPublicIPPrefix** aus.

4. Klicken Sie im Abschnitt **Übersicht** auf **Löschen**.

    >[!NOTE]
    >Wenn Adressen innerhalb des Präfix mit öffentlichen IP-Adressressourcen verknüpft sind, müssen Sie zuerst die öffentlichen IP-Adressressourcen löschen. Informationen dazu finden Sie unter [Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben Sie ein Präfix für öffentliche IP-Adressen und eine öffentliche IP-Adresse aus diesem Präfix erstellt. 

Wenn Sie die Bearbeitung des Präfix für öffentliche IP-Adressen abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen:

1. Suchen Sie nach der Ressourcengruppe **myResourceGroup**, und wählen Sie sie aus.

2. Wählen Sie die Option **Ressourcengruppe löschen**.

3. Geben Sie **myResourceGroup** für **RESSOURCENGRUPPENNAMEN EINGEBEN** ein, und wählen Sie **Löschen** aus.
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über Szenarien und Vorteile der Verwendung des [Präfix einer öffentlichen IP](public-ip-address-prefix.md).
