---
title: "Prise en main d’Azure DNS à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment créer une zone et un enregistrement DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide du portail Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 3aea60bc21bfb0650a336f6674005bbab47201fe
ms.lasthandoff: 04/20/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Prise en main d’Azure DNS à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [portail Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Cet article vous indique la procédure à suivre pour créer votre première zone et votre premier enregistrement à l’aide du portail Azure. Vous pouvez également effectuer ces étapes à l’aide d’Azure PowerShell ou de l’interface de ligne de commande Azure multiplateforme.

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Enfin, pour publier votre zone DNS sur Internet, vous devez configurer les serveurs de noms du domaine. Chacune de ces étapes est décrite plus bas.

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
2. Dans le menu Hub, cliquez sur **Nouveau > Mise en réseau >**, puis cliquez sur **Zone DNS** pour ouvrir le panneau Créer une zone DNS.

    ![Zone DNS](./media/dns-getstarted-portal/openzone650.png)

4. Dans le panneau **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :


   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|contoso.com|Nom de la zone DNS|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la passerelle de l’application.|
   |**Groupe de ressources**|**Créer :** contosoDNSRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Emplacement**|Ouest des États-Unis||

> [!NOTE]
> Le groupe de ressources fait référence à l’emplacement du groupe de ressources et n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

L’exemple suivant vous guide tout au long du processus de création d’un enregistrement « A ». Pour découvrir d’autres types d’enregistrements et pour modifier les enregistrements existants, consultez [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md). 

1. Une fois la zone DNS créée, allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur la zone DNS **contoso.com** dans le panneau Toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte plusieurs ressources, vous pouvez saisir **contoso.com** dans la case **Filtrer par nom...** pour accéder facilement à la zone DNS.

1. En haut du panneau **Zone DNS**, sélectionnez **+ Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.

1. Dans le panneau **Ajouter un jeu d’enregistrements**, entrez les valeurs suivantes, puis cliquez sur **OK**. Dans cet exemple, vous créez un enregistrement A.

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|www|Nom de l’enregistrement|
   |**Type**|Un | Type d’enregistrement DNS pour créer. Les valeurs acceptables sont A, AAAA, CNAME, MX, NS, SRV, TXT et PTR.  Pour plus d’informations sur les types d’enregistrement, voir [Aperçu des zones DNS et des enregistrements](dns-zones-records.md)|
   |**TTL**|1|Durée de vie de la requête DNS.|
   |**Unité de durée de vie**|Heures|Mesure de temps pour la durée de vie.|
   |**Adresse IP**|{ipAddressValue| Cette valeur est l’adresse IP correspondant à l’enregistrement DNS.|

## <a name="view-records"></a>Affichage des enregistrements

Les enregistrements de la zone DNS s’affichent dans la partie inférieure du panneau Zone DNS. Vous devez voir les enregistrements DNS et SOA par défaut, qui sont créés dans chaque zone, ainsi que les nouveaux enregistrements que vous avez créés.

![zone](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Mettre à jour les serveurs de noms

Une fois que vous jugez que votre zone et vos enregistrements DNS ont été configurés correctement, vous devez configurer votre nom de domaine pour utiliser les serveurs de noms Azure DNS. Cela permet à d’autres utilisateurs sur Internet de rechercher vos enregistrements DNS.

Les serveurs de noms de votre zone sont indiqués dans le Portail Azure :

![zone](./media/dns-getstarted-portal/viewzonens500.png)

Ces serveurs de noms doivent être configurés avec le bureau d’enregistrement de noms de domaine (dans lequel vous avez acheté le nom de domaine). Votre bureau d’enregistrement offre la possibilité de configurer les serveurs de noms pour le domaine. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

1. Allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur le groupe de ressources **MyResourceGroup** dans le panneau Toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte plusieurs ressources, vous pouvez saisir **MyResourceGroup** dans la case **Filtrer par nom...** pour accéder facilement au groupe de ressources.
1. Dans le panneau **MyResourceGroup**, cliquez sur le bouton **Supprimer**.
1. Le portail nécessite que vous saisissiez le nom du groupe de ressources pour confirmer la suppression. Cliquez sur **Supprimer**, tapez *MyResourceGroup* comme nom du groupe de ressources, puis cliquez sur **Supprimer**. La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Veuillez donc toujours vérifier le contenu d’un groupe de ressources avant de le supprimer. Le portail supprime toutes les ressources contenues dans le groupe de ressources, puis supprime le groupe de ressources lui-même. Cette opération prend plusieurs minutes.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gestion d’enregistrements et de jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md).


