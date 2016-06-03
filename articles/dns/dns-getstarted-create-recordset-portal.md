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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Créer des jeux d’enregistrements DNS en utilisant le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)


Cet article vous guide dans la création d’enregistrements et de jeux d’enregistrements à l’aide du portail Azure. Après avoir créé votre zone DNS, vous devez ajouter les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre la notion d’enregistrements et de jeux d’enregistrements DNS.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Création d’un jeu d’enregistrements et d’un enregistrement

L’exemple suivant vous guidera tout au long de la création d’un jeu d’enregistrements et d’enregistrements à l’aide du portail Azure. Nous utiliserons le type d’enregistrement DNS « A ».

1. Connectez-vous au portail Azure.

2. Accédez au panneau de la **zone DNS** où vous voulez créer un jeu d’enregistrements.

3. En haut du panneau de votre zone DNS, cliquez sur **Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.
 
	![nouveau jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Dans le panneau **Ajouter un jeu d’enregistrements**, nommez votre jeu d’enregistrements. Par exemple, vous pouvez nommer votre jeu d’enregistrements « **www** ».
  
	![ajouter un jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Sélectionnez le type d’enregistrement que vous souhaitez créer. Par exemple, **A**.

6. Définissez la **durée de vie (TTL)**. La valeur par défaut dans le portail est 1 heure.

7. Ajoutez les adresses IP, une adresse IP par ligne. Avec le nom du jeu d’enregistrements et du type d’enregistrement suggérés ci-dessus, les adresses IP IPv4 sont ajoutées à l’enregistrement A pour le jeu d’enregistrements « www ».

8. Quand vous avez terminé d’ajouter des adresses IP, cliquez sur **OK** en bas du panneau. Le jeu d’enregistrements DNS est créé.


## Étapes suivantes

Pour gérer votre jeu d’enregistrements et vos enregistrements, consultez [Gérer les enregistrements et les jeux d’enregistrements DNS en utilisant le portail Azure](dns-operations-recordsets-portal.md).

Pour plus d’informations sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

<!----HONumber=AcomDC_0518_2016-->