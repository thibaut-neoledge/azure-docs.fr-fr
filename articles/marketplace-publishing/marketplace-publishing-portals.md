<properties
   pageTitle="Vue d'ensemble des divers portails nécessaires pour créer une offre sur Marketplace | Microsoft Azure"
   description="Vue d'ensemble des divers portails nécessaires pour créer une offre sur Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2015"
   ms.author="hascipio" />


# Portails dont vous aurez besoin
Avant le lancement du processus, nous allons vous présenter différents portails dont vous aurez besoin. Vous trouverez ci-dessous un bref résumé des portails ( **tableau de bord, portail de publication** et **portail Azure en version préliminaire**) avec lesquels vous pourrez interagir.
## Tableau de bord du vendeur
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### Description
La création de votre compte Tableau de bord du vendeur est une tâche ponctuelle. Le partenaire doit s'assurer que l'entreprise ne possède pas déjà de compte Tableau de bord du vendeur avant de tenter d'en créer un. Dans le cadre de ce processus, nous recueillons vos informations bancaires et fiscales, ainsi que l'adresse de votre entreprise.

> [AZURE.NOTE]Si vous publiez des offres gratuites uniquement (ou BYOL (apportez votre propre licence)), nous n'avons pas besoin de vos informations bancaires et fiscales..

### Identité/compte utilisé
Dans l'idéal, une liste de distribution ou un groupe de sécurité (par exemple, azurepublishing@partnercompany.com)). Cette liste de distribution ou ce groupe de sécurité **DOÎT** être enregistré(e) en tant que compte Microsoft.

> [AZURE.TIP]Nous recommandons l'utilisation d'une liste de distribution ou d'un groupe de sécurité, car elle permet de supprimer la dépendance vis-à-vis de toute personne (même si un compte individuel peut également être utilisé).

## Portail de publication
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Description
Portail permettant au partenaire de développer son offre et de la publier (marketing, tarification, publication, certification, le cas échéant, etc.).

### Identité/compte utilisé
La **liste de distribution** ou le **groupe de sécurité** cité ci-dessus doit être utilisé pour la première connexion au portail de publication de portail. D'autres utilisateurs pourront être ajoutés ultérieurement en tant que coadministrateur. Voici comment il est mappé sur les données d'inscription du Tableau de bord du vendeur.

## Version préliminaire du portail Azure
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### Description
Il s'agit du portail dans lequel les partenaires peuvent afficher leurs offres intermédiaires et publiées sur le Marketplace (applicable aux machines virtuelles, aux modèles de solution et aux services de développement basés sur ARM)
### Identité/compte utilisé
Un ID d'abonnement figurant dans la liste blanche est nécessaire lors de la création d'une offre intermédiaire depuis le portail de publication. Le même abonnement [avec un nom d'utilisateur et un mot de passe associé] doit être utilisé lors de la connexion à ce portail pour tester l'offre intermédiaire.

## Voir aussi
- [Mise en route : publication d'une offre pour Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->