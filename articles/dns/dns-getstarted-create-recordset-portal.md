<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide du portail Azure | Microsoft Azure"
   description="Création d’enregistrements hôtes pour Azure DNS et création d’enregistrements et de jeux d’enregistrements à l’aide du portail Azure"
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


# Création d’enregistrements DNS et de jeux d’enregistrements à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)


Après avoir créé votre zone DNS, vous devez ajouter les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

## Compréhension des jeux d’enregistrements et des enregistrements.

### À propos des enregistrements

Chaque enregistrement DNS a un nom et un type.

Le nom de domaine complet inclut le nom de la zone, contrairement au nom relatif. Par exemple, le nom d’enregistrement relatif « www » dans la zone « contoso.com » crée le nom d’enregistrement complet « www.contoso.com ».

>[AZURE.NOTE] Dans Azure DNS, les enregistrements sont spécifiés à l’aide de noms relatifs.

Il existe différents types d’enregistrement, selon les données qu’ils contiennent. Le type le plus courant est un enregistrement « **A** » qui associe un nom à une adresse IPv4. Un autre type est un enregistrement « **MX** », qui associe un nom à un serveur de messagerie.

Azure DNS prend en charge tous les types d’enregistrement DNS courants : A, AAAA, CNAME, MX, NS, SOA, SRV et TXT. Notez que les enregistrements SPF doivent être créés à l’aide du type d’enregistrement TXT. Pour plus d’informations, consultez [cette page](http://tools.ietf.org/html/rfc7208#section-3.1).


### À propos des jeux d’enregistrements

Vous devez parfois créer plusieurs enregistrements DNS avec un nom et un type donnés. Par exemple, supposons que le site web www.contoso.com est hébergé sur des adresses IP différentes. Cela requiert deux enregistrements A, un pour chaque adresse IP : Il s’agit d’un exemple de jeu d’enregistrements.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Azure DNS gère les enregistrements DNS à l’aide de jeux d’enregistrements. Un jeu d’enregistrements est une collection d’enregistrements DNS dans une zone ayant le même nom et le même type. La plupart des jeux d’enregistrements contiennent un enregistrement unique, mais les exemples comme celui ci-dessus dans lequel un jeu d’enregistrements contient plusieurs enregistrements sont relativement courants.

Les jeux d’enregistrements de type SOA et CNAME constituent une exception, les normes DNS n’autorisant pas plusieurs enregistrements avec le même nom pour ces types.

La durée de vie (TTL) spécifie la durée pendant laquelle chaque enregistrement est mis en cache par les clients avant d’être réinterrogé. Dans l’exemple ci-dessus, la durée de vie est de 3 600 secondes ou 1 heure. La durée de vie est spécifiée pour le jeu d’enregistrements, pas pour chaque enregistrement. La même valeur est donc utilisée pour tous les enregistrements au sein de ce jeu d’enregistrements.

#### Jeux d’enregistrements génériques

Azure DNS prend en charge les [enregistrements génériques](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Ces derniers sont retournés pour toute requête avec un nom correspondant (à moins qu’une correspondance plus proche provienne d'un jeu d'enregistrements non génériques). Les jeux d'enregistrements génériques sont pris en charge pour tous les types d'enregistrements, hormis NS et SOA.

Pour créer un jeu d'enregistrements génériques, utilisez le nom de jeu d'enregistrements « \* », ou un nom dont la première étiquette est « \* », par exemple, « \*.foo ».

#### Jeux d’enregistrements CNAME

Les jeux d'enregistrements CNAME ne peuvent pas coexister avec d'autres jeux d'enregistrements portant le même nom. Par exemple, vous ne pouvez pas créer un CNAME avec le nom relatif « www » et un enregistrement A avec le nom relatif « www » en même temps. Étant donné que l’extrémité de la zone (nom = « @ ») contient toujours les jeux d’enregistrements NS et SOA créés lors de la création de la zone, cela signifie que vous ne pouvez pas créer un jeu d’enregistrements CNAME au niveau de l’extrémité de la zone. Ces contraintes sont dues aux normes DNS, il ne s’agit pas de limites d’Azure DNS.


## Pour créer un nouveau jeu d’enregistrements et un enregistrement

L’exemple suivant vous guidera tout au long de la création d’un jeu d’enregistrements et d’enregistrements à l’aide du portail Azure. Nous utiliserons le type d’enregistrement DNS « A ».

1. Connectez-vous au portail Azure.

2. Accédez au panneau de la **zone DNS** dans laquelle vous souhaitez créer un jeu d’enregistrements.

3. En haut du panneau de votre zone DNS, cliquez sur **Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.
 
	![nouveau jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Dans le panneau **Ajouter un jeu d’enregistrements**, nommez votre jeu d’enregistrements. Par exemple, vous pouvez nommer votre jeu d’enregistrements « **www** ».
  
	![ajouter un jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Sélectionnez le type d’enregistrement que vous souhaitez créer. Par exemple, **A**.

6. Définissez la **durée de vie (TTL)**. La valeur par défaut dans le portail est 1 heure.

7. Ajoutez les adresses IP, une adresse IP par ligne. Avec le nom du jeu d’enregistrements et du type d’enregistrement suggérés ci-dessus, les adresses IP IPv4 sont ajoutées à l’enregistrement A pour le jeu d’enregistrements « www ».

8. Lorsque vous avez terminé d’ajouter des adresses IP, cliquez sur **OK** en bas du panneau. Le jeu d’enregistrements DNS est créé.

## Test de votre zone DNS à l’aide des outils DNS


Si vous n’avez pas encore délégué votre domaine pour qu’il utilise la nouvelle zone Azure DNS, vous devez diriger la requête DNS directement vers l’un des serveurs de noms pour votre zone. Les serveurs de noms de votre zone figurent dans le panneau Essentials du panneau de votre zone DNS. Pour plus d’informations, consultez l’article [Déléguer votre domaine à Azure DNS](dns-domain-delegation.md).

Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, dig ou l’[applet de commande PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).


## Étapes suivantes

Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).

<!-----HONumber=AcomDC_0406_2016-->