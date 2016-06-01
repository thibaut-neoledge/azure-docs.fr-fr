<properties 
   pageTitle="Gestion des jeux d’enregistrements DNS et des enregistrements à l’aide du portail Azure | Microsoft Azure" 
   description="Gestion des jeux d'enregistrements DNS et des enregistrements lorsque votre domaine est hébergé dans Azure DNS." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
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

# Gérer les enregistrements et les jeux d’enregistrements DNS à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](dns-operations-recordsets-portal.md)
- [Interface de ligne de commande Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Ce guide explique comment gérer les jeux d’enregistrements et les enregistrements pour votre zone DNS en utilisant le portail Azure.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et les enregistrements DNS individuels. Un jeu d’enregistrements est une collection d’enregistrements dans une zone ayant le même nom et le même type. Pour plus d’informations, consultez [Présentation des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-portal.md).

## Création d’un nouveau jeu d’enregistrements et d’un enregistrement

Pour créer un jeu d’enregistrements dans le portail Azure, consultez [Créer des enregistrements DNS à l’aide du portail Azure](dns-getstarted-create-recordset-portal.md).


## Affichage d’un jeu d’enregistrements

1. Dans le portail Azure, accédez au panneau de votre zone DNS.

2. Vous pouvez rechercher le jeu d’enregistrements et le sélectionner dans la liste. Cliquez sur le jeu d’enregistrements pour le sélectionner. Les propriétés du jeu d’enregistrements s’affichent.

	![rechercher le jeu d’enregistrements](./media/dns-operations-recordsets-portal/searchset500.png)


## Ajouter un nouvel enregistrement à un jeu d’enregistrements

Vous pouvez ajouter jusqu'à 20 enregistrements à n'importe quel jeu d'enregistrements. Un jeu d’enregistrements ne peut pas contenir deux enregistrements identiques. Des jeux d'enregistrements vides (avec zéro enregistrement) peuvent être créés mais ils n'apparaîtront pas sur les serveurs de nom Azure DNS. Les jeux d’enregistrements du type CNAME peuvent contenir, au maximum, un enregistrement.


1. Dans le panneau **Propriétés du jeu d’enregistrements** de votre zone DNS, cliquez sur le jeu d’enregistrements auquel vous souhaitez ajouter un enregistrement.

	![sélectionner le jeu](./media/dns-operations-recordsets-portal/selectset500.png)

2. Spécifiez les paramètres de l’enregistrement en remplissant les champs.

	![ajouter un enregistrement](./media/dns-operations-recordsets-portal/addrecord500.png)

2. Cliquez sur **Enregistrer** en haut du panneau pour enregistrer vos paramètres avant de fermer le panneau.

3. Dans l’angle, vous verrez que l’enregistrement est en cours de sauvegarde.

	![sauvegarder l’enregistrement](./media/dns-operations-recordsets-portal/saving150.png)

4. Une fois que l’enregistrement a été sauvegardé, les valeurs du jeu d’enregistrements dans le panneau DNS reflètent le nouvel enregistrement.


## Mise à jour d’un enregistrement

Lors de la mise à jour d’un enregistrement dans un jeu d’enregistrements existant, les champs disponibles pour la mise à jour varient selon le type d’enregistrement que vous utilisez.

1. Dans le panneau **Propriétés du jeu d’enregistrements** de votre jeu d’enregistrements, recherchez l’enregistrement.

2. Modifiez l’enregistrement. Lorsque vous modifiez un enregistrement, vous pouvez modifier les paramètres disponibles pour l’enregistrement. Dans l’exemple ci-dessous, nous avons cliqué sur le champ d’adresse IP et l’adresse IP est en cours de modification.

	![modifier l’enregistrement](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Cliquez sur **Enregistrer** en haut du panneau pour enregistrer vos paramètres. Une notification s’affiche dans le coin supérieur droit indiquant que l’enregistrement a été sauvegardé.
	
	![sauvegarder l’enregistrement](./media/dns-operations-recordsets-portal/saved150.png)


3. Une fois que l’enregistrement a été sauvegardé, les valeurs du jeu d’enregistrements dans le panneau DNS reflètent l’enregistrement mis à jour.


## Suppression d’un enregistrement d’un jeu d’enregistrements

Vous pouvez utiliser le portail Azure pour supprimer des enregistrements d’un jeu d’enregistrements. Notez que la suppression du dernier enregistrement d’un jeu d’enregistrements ne supprime pas le jeu d’enregistrements.

1. Dans le panneau **Propriétés du jeu d’enregistrements** de votre jeu d’enregistrements, recherchez l’enregistrement.

2. Cliquez sur l’enregistrement que vous souhaitez supprimer. Puis cliquez sur **Supprimer**.

	![supprimer l’enregistrement](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Cliquez sur **Enregistrer** en haut du panneau pour enregistrer vos paramètres.

3. Une fois que l’enregistrement a été supprimé, les valeurs du jeu d’enregistrements dans le panneau DNS reflètent la suppression de l’enregistrement.


## <a name="delete"></a>Suppression d’un jeu d’enregistrements

1. Dans le panneau **Propriétés du jeu d’enregistrements** de votre jeu d’enregistrements, cliquez sur **Supprimer**. 

	![supprimer le jeu d’enregistrements](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Un message s’affiche vous demandant si vous souhaitez supprimer le jeu d’enregistrements « nom\_ du\_jeu\_d’enregistrements ».

3. Vérifiez que le nom correspond au jeu d’enregistrements que vous souhaitez supprimer, puis cliquez sur **Oui**.

4. Dans le panneau de la zone DNS, vous pouvez vérifier que le jeu d’enregistrements n’est plus visible.


## Utilisation d’enregistrements NS et SOA

Les enregistrements NS et SOA qui sont créés automatiquement sont gérés différemment des autres types d’enregistrements.

### Modification des enregistrements SOA

Vous ne pouvez pas ajouter ou supprimer des enregistrements du jeu d'enregistrements SOA créé automatiquement dans l’extrémité de la zone (nom = « @ »), mais vous pouvez modifier les paramètres dans l'enregistrement SOA (excepté « Host ») et la durée de vie du jeu d'enregistrements.

### Modification des enregistrements NS à l’extrémité de la zone

Vous ne pouvez pas ajouter, supprimer ou modifier les enregistrements dans le jeu d'enregistrements NS créé automatiquement à l’extrémité de la zone (nom = « @ »). La seule modification possible est la modification de la durée de vie du jeu d'enregistrements.

### Suppression de jeux d’enregistrements SOA ou NS

Vous ne pouvez pas supprimer les jeux d’enregistrements SOA et NS à l’extrémité de la zone (nom = « @ ») qui sont créés automatiquement lorsque la zone est créée. Ils seront automatiquement supprimés lors de la suppression de la zone.

## Étapes suivantes

Pour plus d’informations sur Azure DNS, consultez la [Vue d’ensemble d’Azure DNS](dns-overview.md). Pour plus d’informations sur l’automatisation de DNS, consultez [Création des zones DNS et de jeux d’enregistrements à l’aide du Kit de développement logiciel (SDK) .NET](dns-sdk.md).


Si vous voulez utiliser des enregistrements DNS inversés, consultez [Comment gérer les enregistrements DNS inversés](dns-reverse-dns-record-operations-ps.md).
 

<!---HONumber=AcomDC_0518_2016-->