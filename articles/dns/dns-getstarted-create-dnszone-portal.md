<properties
   pageTitle="Création et gestion d’une zone DNS dans le portail Azure | azure.microsoft.com/ Azure"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Créer une zone DNS dans le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-dnszone-cli.md)



Cet article vous indique la procédure à suivre pour créer une zone DNS à l’aide du portail Azure. Vous pouvez également créer une zone DNS à l’aide de PowerShell ou de l’interface de ligne de commande (CLI).

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### À propos des balises pour Azure DNS


Les balises sont une liste de paires nom-valeur. Elles sont utilisées par Azure Resource Manager pour identifier les ressources à des fins de facturation ou de regroupement. Pour plus d’informations sur les étiquettes, consultez l’article [Utilisation d’étiquettes pour organiser vos ressources Azure](../resource-group-using-tags.md).

Vous pouvez ajouter des étiquettes dans le portail Azure en utilisant le panneau **Paramètres** pour votre zone DNS.


## Création d’une zone DNS

1. Connectez-vous au portail Azure.

2. Dans le menu Hub, cliquez sur **Nouveau > Mise en réseau >**, puis cliquez sur **Zone DNS** pour ouvrir le panneau de la zone DNS.
 
	![Zone DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. Dans le panneau **Zone DNS**, cliquez sur **Créer** en bas de la page. Cette action ouvre le panneau **Créer une zone DNS**.

	![Créer la zone](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. Dans le panneau **Créer une zone DNS**, nommez votre zone DNS. Par exemple, *contoso.com*. Consultez [À propos des noms de zones DNS](#names) dans la section ci-dessus.

5. Ensuite, spécifiez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un qui existe déjà.

6. Dans le menu déroulant **Emplacement**, spécifiez l’emplacement du groupe de ressources. Notez que ce paramètre fait référence à l’emplacement du groupe de ressources et non à l’emplacement de la zone DNS. La ressource DNS est automatiquement « globale ». Vous ne pouvez (devez) pas spécifier ceci dans le portail.

7. Vous pouvez laisser la case **Épingler au tableau de bord** cochée si vous voulez localiser facilement votre nouvelle zone sur votre tableau de bord. Cliquez ensuite sur **Créer**.

	![Épingler au tableau de bord](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. Lorsque vous cliquez sur Créer, votre nouvelle zone configurée s’affiche sur le tableau de bord.

	![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. Lorsque votre nouvelle zone a été créée, le panneau de votre nouvelle zone s’ouvre dans le tableau de bord.


## Affichage des enregistrements

La création d’une zone DNS crée également les enregistrements suivants :

- L’enregistrement « SOA » (Start of Authority). Il est présent à la racine de chaque zone DNS.
- Les enregistrements de serveur de noms faisant autorité (NS). Ceux-ci indiquent quels serveurs de noms hébergent la zone. Azure DNS utilise un pool de serveurs de noms. Il se peut donc que différents serveurs de noms soient attribués à différentes zones dans Azure DNS. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](dns-domain-delegation.md).

Vous pouvez afficher les enregistrements à partir du portail Azure

1. Dans le panneau de votre **zone DNS**, cliquez sur **Tous les paramètres** pour ouvrir le **panneau Paramètres** pour la zone DNS.

	![zone](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. Les jeux d’enregistrements pour la zone DNS s’affichent dans la partie inférieure du volet Essentials.


	![zone](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## Test

Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, dig ou l’[applet de commande PowerShell Resolve-DnsName](https://technet.azure.microsoft.com/.com/library/jj590781.aspx).

Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans les enregistrements NS, répertoriés ci-dessus par `Get-AzureRmDnsRecordSet`. Veillez à indiquer les valeurs correctes pour votre zone dans la commande ci-dessous.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.azure.microsoft.com/.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)



## Suppression d’une zone DNS

Vous pouvez supprimer la zone DNS à partir du portail. Avant de supprimer une zone DNS dans Azure DNS, vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement en même temps que cette dernière.

1. Recherchez le panneau **Zone DNS** pour la zone que vous souhaitez supprimer, puis cliquez sur **Supprimer** en haut du panneau.
 
2. Un message s’affiche vous informant que vous devez supprimer tous les jeux d’enregistrements, sauf les enregistrements NS et SOA qui ont été créés automatiquement. Si vous avez supprimé vos jeux d’enregistrements, cliquez sur **Oui**. Notez que lorsque vous supprimez une zone DNS à partir du portail, le groupe de ressources auquel la zone DNS est associée n’est pas supprimé.


## Étapes suivantes

Après avoir créé une zone DNS, créez des [jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-portal.md) pour démarrer la résolution des noms pour votre domaine Internet.

<!---HONumber=AcomDC_0817_2016-->