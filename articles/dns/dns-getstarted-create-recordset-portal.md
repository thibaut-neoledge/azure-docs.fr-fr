<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide du portail Azure | azure.microsoft.com/ Azure"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>



# Création de jeux d’enregistrements et d’enregistrements DNS à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)


Cet article vous guide dans le processus de création de jeux d’enregistrements et d’enregistrements à l’aide du portail Azure. Après avoir créé votre zone DNS, vous ajoutez les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Création d’un jeu d’enregistrements et d’un enregistrement

L’exemple suivant vous guide tout au long du processus de création d’un jeu d’enregistrements et d’enregistrements à l’aide du portail Azure. Nous utiliserons le type d’enregistrement DNS « A ».

1. Connectez-vous au portail.

2. Accédez au panneau **Zone DNS** où vous voulez créer un jeu d’enregistrements.

3. En haut du panneau **Zone DNS**, sélectionnez **Jeu d’enregistrements** pour ouvrir le panneau **Ajouter un jeu d’enregistrements**.

	![Nouveau jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Dans le panneau **Ajouter un jeu d’enregistrements**, donnez un nom à votre jeu d’enregistrements. Par exemple, vous pouvez nommer votre jeu d’enregistrements « **www** ».

	![Ajouter un jeu d’enregistrements](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Sélectionnez le type d’enregistrement que vous souhaitez créer. Par exemple, sélectionnez **A**.

6. Définissez la **durée de vie (TTL)**. La durée de vie par défaut dans le portail est d’une heure.

7. Ajoutez les adresses IP, une adresse IP par ligne. Lorsque vous utilisez le nom de jeu d’enregistrements et le type d’enregistrement suggérés ci-dessus, vous ajoutez les adresses IP IPv4 sont ajoutées à l’enregistrement **A** pour le jeu d’enregistrements www.

8. Après avoir ajouté les adresses IP, cliquez sur **OK** en bas du panneau. Le jeu d’enregistrements DNS est créé.


## Étapes suivantes

Pour gérer votre jeu d’enregistrements et vos enregistrements, consultez [Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide du portail Azure](dns-operations-recordsets-portal.md).

Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md).

<!---HONumber=AcomDC_0817_2016-->