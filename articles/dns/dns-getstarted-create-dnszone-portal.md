---
title: "Création et gestion d’une zone DNS dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer des zones DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone DNS à l’aide du Portail Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Créer une zone DNS dans le portail Azure

> [!div class="op_single_selector"]
> * [portail Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide du Portail Azure. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou de l’interface de ligne de commande (CLI).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
2. Dans le menu Hub, cliquez sur **Nouveau > Mise en réseau >**, puis cliquez sur **Zone DNS** pour ouvrir le panneau Créer une zone DNS.

    ![Zone DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. Dans le panneau **Créer une zone DNS** , nommez votre zone DNS. Par exemple, *contoso.com*.
 
    ![Créer la zone](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. Ensuite, spécifiez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà. Si vous choisissez de créer un nouveau groupe de ressources, utilisez la liste déroulante **Emplacement** pour spécifier l’emplacement du groupe de ressources. Notez que ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

6. Vous pouvez laisser la case **Épingler au tableau de bord** cochée si vous voulez localiser facilement votre nouvelle zone sur votre tableau de bord. Cliquez ensuite sur **Créer**.

    ![Épingler au tableau de bord](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. Lorsque vous cliquez sur Créer, votre nouvelle zone configurée s’affiche sur le tableau de bord.

    ![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. Lorsque votre nouvelle zone a été créée, le panneau de votre nouvelle zone s’ouvre dans le tableau de bord.

## <a name="view-records"></a>Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements suivants :

* L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
* Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](dns-domain-delegation.md).

Les jeux d’enregistrements de la zone DNS s’affichent dans la partie inférieure du panneau Zone DNS.

![zone](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>Tester les serveurs de noms

Vous pouvez vérifier la présence de votre zone DNS sur les serveurs de noms Azure DNS à l’aide d’outils DNS comme nslookup, dig ou [l’applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone sont indiqués dans le Portail Azure :
    
![zone](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

Veillez à indiquer le bon serveur de noms pour votre zone dans la commande ci-dessous.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, [créez des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-portal.md) afin de créer des enregistrements DNS pour votre domaine Internet.


