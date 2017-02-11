---
title: "Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide du portail Azure | Microsoft Docs"
description: "Création d’enregistrements hôtes pour Azure DNS et création d’enregistrements et de jeux d’enregistrements à l’aide du portail Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: b3951106fe2e8607e65bd0ae47fa2ea3346b8ca5

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Création de jeux d’enregistrements et d’enregistrements DNS à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)

Cet article vous guide dans le processus de création de jeux d’enregistrements et d’enregistrements à l’aide du portail Azure. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Les exemples fournis sur cette page utilisent tous le type d’enregistrement DNS « A ». Le processus est similaire pour d’autres types d’enregistrements.

Si votre nouvel enregistrement porte le même nom et est du même type qu’un enregistrement existant, vous devez l’ajouter au jeu d’enregistrements existant&mdash;. Consultez [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du Portail Azure](dns-operations-recordsets-portal.md). Si le nom et le type de votre nouvel enregistrement diffèrent de ceux de tous les enregistrements existants, vous devez créer un autre jeu d’enregistrements comme suit.

## <a name="create-records-in-a-new-record-set"></a>Créer des enregistrements dans un nouveau jeu d’enregistrements

L’exemple suivant vous guide tout au long du processus de création d’un jeu d’enregistrements et d’enregistrements à l’aide du portail Azure.

1. Connectez-vous au portail.
2. Accédez au panneau **Zone DNS** où vous voulez créer un jeu d’enregistrements.
3. En haut du panneau **Zone DNS**, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.

    ![Nouveau jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Dans le panneau **Ajouter un jeu d’enregistrements** , donnez un nom à votre jeu d’enregistrements. Par exemple, vous pouvez nommer votre jeu d’enregistrements «**www**».

    ![Ajouter un jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Sélectionnez le type d’enregistrement que vous souhaitez créer. Par exemple, sélectionnez **A**.
6. Définissez la **durée de vie (TTL)**. La durée de vie par défaut dans le portail est d’une heure.
7. Ajoutez les détails de chaque enregistrement dans le jeu d’enregistrements. Dans ce cas, étant donné que le type d’enregistrement est « A », vous devez ajouter les adresses IP d’enregistrements A, une adresse IP par ligne.
8. Après avoir ajouté les adresses IP, cliquez sur **OK** en bas du panneau. Le jeu d’enregistrements DNS est créé.

### <a name="verify-name-resolution"></a>Vérifier la résolution de noms

Vous pouvez vérifier la présence de vos enregistrements DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone dans Azure DNS, vous devez [diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone](dns-getstarted-create-dnszone.md#test-name-servers). Veillez à indiquer les valeurs correctes pour votre zone d’enregistrements dans la commande ci-dessous.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déléguer votre nom de domaine aux serveurs de noms Azure DNS](dns-domain-delegation.md).

Pour gérer votre jeu d’enregistrements et vos enregistrements, consultez [Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md).



<!--HONumber=Dec16_HO2-->


