<properties
   pageTitle="Vue d'ensemble des divers portails nécessaires pour créer une offre sur Marketplace | Microsoft Azure"
   description="Vue d'ensemble des divers portails nécessaires pour créer une offre sur Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/27/2016"
   ms.author="hascipio" />


# Portails dont vous aurez besoin
Avant le lancement du processus de publication d’une offre, nous allons vous présenter les différents portails dont vous aurez besoin. Vous trouverez ci-dessous un résumé des portails (Centre de développement, portail de publication Azure et portail Azure) avec lesquels vous pourrez interagir.
## Centre de développement
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)
### Description
La création d’un compte auprès du Centre de développement Microsoft est une tâche ponctuelle. Assurez-vous que l’entreprise ne possède pas déjà de compte auprès du Centre de développement avant de tenter d’en créer un. Dans le cadre de ce processus, nous recueillons vos informations bancaires et fiscales, ainsi que l'adresse de votre entreprise.

> [AZURE.NOTE] Si vous publiez des offres gratuites uniquement (ou offres BYOL (apportez votre propre licence)), nous n'avons pas besoin de vos informations bancaires et fiscales.

### Identité/compte utilisé
Dans l’idéal, une liste de distribution ou un groupe de sécurité (par exemple, azurepublishing@*entreprisepartenaire*.com). Cette liste de distribution ou ce groupe de sécurité **doit** être enregistré en tant que compte Microsoft.

> [AZURE.TIP] Nous recommandons l'utilisation d'une liste de distribution ou d'un groupe de sécurité, car elle permet de supprimer la dépendance vis-à-vis de toute personne (même si un compte individuel peut également être utilisé).

## Portail de publication
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Description
Ce portail vous permet de développer votre offre et de la publier (marketing, tarification, publication, certification, le cas échéant, etc.).

### Identité/compte utilisé
Le groupe de sécurité ou la liste de distribution ci-dessus est nécessaire pour la première connexion au portail de publication. Par la suite, d’autres utilisateurs pourront être ajoutés en tant que co-administrateurs. Voici comment il est mappé aux données d’inscription du Centre de développement.

## Portail Azure
[https://portal.azure.com](https://portal.azure.com)
### Description
Il s'agit du portail dans lequel vous pouvez afficher vos offres intermédiaires et publiées sur le Marketplace (applicable aux machines virtuelles, aux modèles de solution et aux services de développement basés sur ARM)
### Identité/compte utilisé
Un ID d'abonnement figurant dans la liste blanche est nécessaire lors de la création d'une offre intermédiaire depuis le portail de publication. Le même abonnement (avec un nom d'utilisateur et un mot de passe associé) doit être utilisé lors de la connexion à ce portail pour tester l'offre intermédiaire.

## Voir aussi
- [Mise en route : publication d'une offre pour Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->