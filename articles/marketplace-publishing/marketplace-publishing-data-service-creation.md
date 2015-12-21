<properties
   pageTitle="Guide de création d’un service de données pour le Marketplace | Microsoft Azure"
   description="Instructions détaillées pour créer, certifier et déployer un service de données que d’autres peuvent acheter sur Azure Marketplace."
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
      ms.date="12/03/2015"
      ms.author="hascipio; avikova" />

# Guide de publication d’un service de données pour Azure Marketplace
Après avoir effectué l’étape 1, [Création et inscription d’un compte][link-acct-creation], nous vous avons guidé dans les [exigences générales non techniques](marketplace-publishing-prerequisites.md) et les [exigences techniques](marketplace-publishing-data-service-creation-prerequisites.md) d’une offre de service de données sur Azure Marketplace. À présent, nous allons vous guider dans la procédure de création d’une offre de service de données sur le [portail de publication][link-pubportal] pour Azure Marketplace.

## 1\. Connectez-vous au portail de publication.

Accédez à [https://publish.windowsazure.com](https://publish.windowsazure.com.).

**Pour votre première connexion au portail de publication, utilisez le même compte que celui avec lequel le profil de vendeur de votre entreprise a été inscrit dans le Centre de développement.** (Plus tard, vous pourrez ajouter des employés de votre entreprise comme coadministrateurs dans le portail de publication.)

Cliquez sur la mosaïque **Publier un service de données** s’il s’agit de votre première connexion au portail de publication.

## 2\. Choisissez **Data Services** dans le menu de navigation situé à gauche.

  ![dessin](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## 3\. Créez un service de données.

Remplissez le titre de votre nouvelle offre de service de données, puis cliquez sur le signe « + » affiché à droite.

  ![dessin](media/marketplace-publishing-data-service-creation/step-3.png)

## 4\. Consultez le sous-menu sous le service de données nouvellement créé dans le menu de navigation.

Cliquez sur l’onglet **Procédure pas à pas** et passez en revue toutes les étapes nécessaires pour publier correctement le service de données sur Azure Marketplace.

> [AZURE.TIP]Vous pouvez toujours cliquer sur les liens affichés dans la page « Procédure pas à pas » ou utiliser les onglets du sous-menu de l’offre de service de données situé à gauche.

## 5\. Créez un plan.

### Offres, plans, transactions.

Chaque offre peut avoir plusieurs plans, mais doit en avoir au moins un (1). Lorsque les utilisateurs finaux s’abonnent à votre offre, ils s’abonnent à l’un des plans de l’offre. Chaque plan définit la façon dont les utilisateurs finaux pourront utiliser votre service.

Actuellement, Azure Marketplace prend uniquement en charge le modèle d’abonnement mensuel basé les transactions pour Data Services. Autrement dit, les utilisateurs finaux payeront des frais mensuels en fonction du prix du plan spécifique auquel ils sont abonnés et pourront consommer chaque mois le nombre de transactions défini par le plan.

Chaque transaction est généralement définie sous la forme d’un nombre d’enregistrements que votre service de données renverra en fonction de la requête envoyée au service. La valeur par défaut est 100. Le nombre de transactions renvoyées à chaque requête correspond au nombre d’enregistrements divisé par 100 et arrondi à l’entier le plus proche.

Il incombe à la couche de service d’Azure Marketplace pour surveiller (compter) le nombre de transactions utilisées par chaque requête.

> [AZURE.IMPORTANT]Les utilisateurs finaux ayant atteint la limite de transactions au cours du mois ne peuvent pas continuer à utiliser le service jusqu’à la fin de leur cycle d’abonnement mensuel.

> Le plan ou l’un des plans peut (mais ne doit pas nécessairement) inclure un nombre illimité de transactions.

### Créez un plan.
1. Cliquez sur le signe **« + »** en regard de « Ajouter un nouveau plan ».

2. Choisissez l’une des options : utilisation **Illimitée** ou **Limitée** pour ce plan. Si vous choisissez Limitée, indiquez le nombre de transactions que le plan autorise à utiliser en un mois.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.1.png)

    Le portail de publication suggère également un « identificateur de plan ». Cet identificateur sera utilisé pour communiquer aux utilisateurs finaux le nom du plan dans l’interface utilisateur. Il sera également utilisé par le service Marketplace pour identifier le plan. Vous pouvez modifier l’« identificateur de plan », si vous le souhaitez.

    > [AZURE.NOTE]L’« identificateur de plan » doit être unique dans l’étendue de chaque offre. Comme de nombreux autres identificateurs utilisés dans l’identificateur de plan du portail de publication seront verrouillés après la première publication en production, vous ne pourrez pas modifier cet identificateur.

3. Cliquez pour valider votre choix.

4. Vous êtes ensuite invité à répondre à quelques questions supplémentaires concernant votre plan nouvellement créé.

    ![dessin](media/marketplace-publishing-data-service-creation/step-5.2.png)


|Question|Signification|
|----|----|
|**Ce plan est gratuit et disponible dans le monde entier**|Vous pouvez créer un plan entièrement gratuit. Si c’est le seul plan de cette offre, cela signifie que vous publiez une « Offre gratuite » dans Marketplace. Si c’est applicable à un seul plan (parmi plusieurs), cela vous permet de proposer aux utilisateurs finaux d’en savoir plus sur votre service avec un nombre assez restreint de transactions par mois. Si la réponse est « Oui », vous n’aurez à répondre à aucune autre question.|

> [AZURE.NOTE]Les utilisateurs finaux peuvent toujours effectuer une mise à niveau vers les plans payants.

|Question|Signification|
|----|----|
|**Existe-t-il un essai gratuit ?**|Vous pouvez choisir entre absolument « Aucune version d’évaluation » ou proposer une option pour utiliser votre plan pendant « Un mois ». Les éditeurs aiment utiliser cette option pour permettre aux utilisateurs finaux de comprendre gratuitement les avantages de l’offre gratuite pendant un mois.|

> [AZURE.IMPORTANT]Les utilisateurs finaux ne pourront acheter un essai gratuit que s’ils disposent d’un moyen de paiement reconnus, tel qu’une carte de crédit ou un contrat d’entreprise.

> Après un mois d’essai gratuit, Azure Marketplace commence à faire payer le prix aux clients à compter de la date de l’abonnement, sauf si le client a entamé l’annulation de l’abonnement. Aucune notification spéciale n’est fournie aux utilisateurs finaux.

|Question|Signification|
|----|----|
|**L’achat de ce plan nécessite un code de promotion**| Les éditeurs ont la possibilité de limiter l’accès à leurs plans de service en fournissant un code spécial, appelé « Code promo » pour des clients spécifiques. Seuls les utilisateurs finaux disposant de ce Code promo peuvent s’abonner au plan. Si vous choisissez « Non », vous reconnaissez que toutes les personnes de la région où l’offre est disponible (pour plus de détails, consultez le [Guide du contenu marketing d’Azure Marketplace](marketplace-publishing-push-to-staging.md)) pourront s’abonner à ce plan. Vous n’aurez à répondre à aucune autre question.|
|**Également masquer ce plan pour toute personne ne disposant pas d’un code promotionnel valide ?**|Si la réponse à la question précédente est « Oui », l’éditeur a la possibilité de supprimer complètement l’affichage de ce plan dans l’interface utilisateur de Marketplace. Cela signifie que les clients ne verront pas ce plan dans la page des détails de l’offre. Les utilisateurs finaux qui recevront un code promotionnel pour l’achat pourront l’utiliser pour s’y abonner.|

## 6\. Créez votre contenu marketing pour Marketplace.
Pour savoir comment fournir les informations nécessaires les onglets **Marketing, Tarification, Support et Catégories**, consultez le [Guide du contenu marketing d’Azure Marketplace](marketplace-publishing-push-to-staging.md) qui est commun à tous les artefacts publiées dans Azure Marketplace.

## 7\. Connectez votre offre à votre service (basé sur SQL Azure ou sur un service web).

Cliquez sur le sous-menu **Data Services**.

Dans la partie supérieure de la page, vous devez fournir l’**espace de noms** de l’offre.

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.png)

La question ci-dessous définit la façon dont l’éditeur va exposer l’offre nouvellement créée dans Azure Marketplace. (Pour plus d’informations, consultez le [guide des conditions techniques préalables de Data Services](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Publication du service basée sur une base de données**

Cliquez sur **Base de données**. La page suivante s’affiche :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.3.png)

Pour créer un mappage CSDL pour le jeu de données basé sur la base de données SQL Azure :

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.4.png)

Puis pour chaque table

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.6.png)

Dans le cas d’un service web

  ![dessin](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [AZURE.IMPORTANT]Pour obtenir des instructions détaillées et des exemples sur la création d’un service web CSDL, lisez [Mappage d’un service web existant à OData via le langage CSDL](marketplace-publishing-data-service-creation-odata-mapping.md).

## Étapes suivantes
Maintenant que vous avez créé votre offre de service de données, assurez-vous de suivre les instructions du [Guide du contenu marketing d’Azure Marketplace](marketplace-publishing-push-to-staging.md) avant de passer au [test de votre service de données dans un environnement intermédiaire](marketplace-publishing-data-service-test-in-staging.md).

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)
- Si vous souhaitez comprendre le processus de mappage OData global et son rôle, lisez l’article [Mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping.md) pour passer en revue des définitions, des structures et des instructions.
- Si vous souhaitez découvrir et comprendre les nœuds spécifiques et leurs paramètres, lisez l’article [Nœuds de mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) pour obtenir des définitions et des explications, des exemples, ainsi qu’un contexte de cas d’utilisation.
- Si vous souhaitez passer en revue des exemples, lisez l’article [Exemples de mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour consulter des exemples de code, ainsi que pour comprendre la syntaxe et le contexte du code.


[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=AcomDC_1210_2015-->