<properties
   pageTitle="Création et gestion d’une zone DNS dans le portail Azure | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone DNS et l’hébergement de votre domaine DNS à l’aide du portail Azure."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Création et gestion d’une zone DNS dans le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)



Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide du portail Azure. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou de l’interface de ligne de commande (CLI).

Le domaine « contoso.com » peut contenir un certain nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Avant d’héberger votre domaine, vous devez créer une zone DNS. Les enregistrements DNS créés pour un domaine particulier sont situés dans une zone DNS pour le domaine.

### <a name="names"></a>À propos des noms de zones DNS
 
- Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Sinon, l’opération échoue.

- Le même nom de zone peut être réutilisé dans un autre groupe de ressources ou abonnement Azure. Lorsque plusieurs zones partagent le même nom, chaque instance se voit affecter différentes adresses de serveur de noms, et une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez la page [Délégation d’un domaine Azure DNS](#delegate).

### À propos des balises pour Azure DNS


Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les balises, consultez l’article [Utilisation de balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Vous pouvez ajouter des balises dans le portail Azure à l’aide du panneau **Paramètres** pour votre zone DNS.


## Création d’une zone DNS

1. Connectez-vous au portail Azure.

2. Dans le menu Hub, cliquez sur **Nouveau > Mise en réseau >** puis cliquez sur **Zone DNS** pour ouvrir le panneau de la zone DNS.
 
	![Zone DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. Dans le panneau **Zone DNS**, cliquez sur **Créer** en bas de la page. Cette action ouvre le panneau **Créer une zone DNS**.

	![Créer la zone](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. Dans le panneau **Créer une zone DNS**, nommez votre zone DNS. Par exemple, *contoso.com*. Consultez [À propos des noms de zones DNS](#names) dans la section précédente.

5. Ensuite, spécifiez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà.

6. Dans le menu déroulant **Emplacement**, spécifiez l’emplacement du groupe de ressources. Notez que ce paramètre fait référence à l’emplacement du groupe de ressources et non à l’emplacement de la zone DNS. La ressource DNS est automatiquement « globale ». Vous ne pouvez (devez) pas spécifier ceci dans le portail.

7. Vous pouvez laisser la case **Épingler au tableau de bord** cochée si vous souhaitez localiser facilement votre nouvelle zone sur votre tableau de bord. Cliquez ensuite sur **Créer**.

	![Épingler au tableau de bord](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. Lorsque vous cliquez sur Créer, votre nouvelle zone configurée s’affiche sur le tableau de bord.

	![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. Lorsque votre nouvelle zone a été créée, le panneau de votre nouvelle zone s’ouvre dans le tableau de bord.


## Affichage des enregistrements de zone DNS

La création d’une zone DNS crée également les enregistrements suivants :

- L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
- Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine Azure DNS](dns-domain-delegation.md).

Vous pouvez afficher les enregistrements à partir du portail Azure

1. Dans le panneau de votre **zone DNS**, cliquez sur **Tous les paramètres** pour ouvrir le **panneau Paramètres** pour la zone DNS. 

	![zone](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. Les jeux d’enregistrements pour la zone DNS s’affichent dans la partie inférieure du volet Essentials.


	![zone](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)



## Suppression d’une zone DNS

Vous pouvez supprimer la zone DNS à partir du portail. Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

1. Recherchez le panneau **Zone DNS** pour la zone que vous souhaitez supprimer, puis cliquez sur **Supprimer** en haut du panneau.
 
2. Un message s’affiche vous informant que vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA qui ont été créés automatiquement. Si vous avez supprimé vos jeux d’enregistrements, cliquez sur **Oui**. Notez que lorsque vous supprimez une zone DNS à partir du portail, le groupe de ressources auquel la zone DNS est associée n’est pas supprimé.


## Étapes suivantes

Après avoir créé votre zone DNS, consultez [Prise en main de la création de jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-portal.md), [Gestion de zones DNS](dns-operations-dnszones.md) et [Gestion des enregistrements DNS](dns-operations-recordsets-portal.md).

<!---HONumber=AcomDC_0406_2016-->