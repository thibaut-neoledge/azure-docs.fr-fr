<properties
   pageTitle="Création d’un jeu d’enregistrements et d’enregistrements pour une zone DNS à l’aide de l’interface de ligne de commande | Microsoft Azure"
   description="Création d’enregistrements hôtes pour Azure DNS. Configuration d’enregistrements et de jeux d’enregistrements à l’aide de l’interface de ligne de commande"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Création de jeux d’enregistrements et d’enregistrements DNS à l’aide de l’interface de ligne de commande

> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Interface de ligne de commande Azure](dns-getstarted-create-recordset-cli.md)


Cet article vous guide dans le processus de création de jeux d’enregistrements et d’enregistrements à l’aide de la CLI. Après avoir créé votre zone DNS, vous devez ajouter les enregistrements DNS de votre domaine. Pour ce faire, vous devez d’abord comprendre les enregistrements DNS et les jeux d’enregistrements.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Création d’un jeu d’enregistrements et d’un enregistrement

Dans cette section, nous allons vous montrer comment créer un jeu d’enregistrements et des enregistrements. Dans cet exemple, vous allez créer un jeu d’enregistrements avec le nom relatif « www » dans la zone DNS « contoso.com ». Le nom complet des enregistrements est « www.contoso.com ». Le type d’enregistrement est « A » et la durée de vie est de 60 secondes. À la fin de cette étape, vous aurez créé un jeu d’enregistrements vide.

Pour créer un jeu d’enregistrements à l’apex de la zone (dans cet exemple, « contoso.com »), utilisez le nom d’enregistrement "@" (guillemets compris). Il s'agit d'une convention DNS courante.

### 1\. Création d’un jeu d'enregistrements

Pour créer un jeu d’enregistrements, utilisez `azure network dns record-set create`. Spécifiez le groupe de la ressource, le nom de la zone, le nom relatif du jeu d’enregistrements, le type d’enregistrement et la durée de vie. Si le paramètre `--ttl` n’est pas défini, la valeur par défaut est quatre (en secondes). À la fin de cette étape, vous obtiendrez un jeu d’enregistrements « www » vide.

*Utilisation : network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. Ajout d’enregistrements

Pour utiliser le jeu d’enregistrements « www » que vous venez de créer, vous devez y ajouter des enregistrements. Pour ajouter des enregistrements aux jeux d’enregistrements, utilisez `azure network dns record-set add-record`.

Les paramètres pour ajouter des enregistrements à un jeu d'enregistrements varient selon le type de jeu d'enregistrements. Par exemple, lors de l’utilisation d’un jeu d’enregistrements de type « A », vous ne pourrez spécifier que les enregistrements avec le paramètre `-a <IPv4 address>`.

Ajoutez les enregistrements IPv4 *A* au jeu d’enregistrements « www » à l’aide de la commande suivante :

*Utilisation : network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Autres exemples de types d’enregistrements

Les exemples suivants montrent comment créer un jeu d’enregistrements de chaque type d’enregistrement. Chaque jeu d’enregistrements contient un seul enregistrement.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## Étapes suivantes

Pour gérer votre jeu d’enregistrements et vos enregistrements, consultez [Gestion des enregistrements DNS à l’aide de l’interface de ligne de commande](dns-operations-recordsets-portal.md).

Pour plus d’informations sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

<!---HONumber=AcomDC_0608_2016-->