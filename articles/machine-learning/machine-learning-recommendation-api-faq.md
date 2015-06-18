<properties 
	pageTitle="Forum aux questions relatif à la configuration et à l'utilisation de l'API de Machine Learning Recommendations | Azure" 
	description="API Microsoft RECOMMANDATIONS créée avec le FAQ sur Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2014" 
	ms.author="jaymathe"/>  

# Forum aux questions relatif à la configuration et à l'utilisation de l'API de Machine Learning Recommendations
Version 1.0

Sommaire

* Qu'est-ce que RECOMMENDATIONS ?	
* Que puis-je faire avec RECOMMENDATIONS ?	
* Existe-t-il des restrictions pendant la période de lancement ?	
* Achat et facturation	
	* Combien coûte RECOMMENDATIONS pendant la période de lancement ?	
	* Existe-t-il des coûts liés au suivi et/ou au stockage de l'activité des utilisateurs pour moi par RECOMMENDATIONS ?	
	* Existe-t-il une version d'évaluation gratuite de RECOMMENDATIONS ?	
	* Quand la période de lancement prendra-t-elle fin ?	
	* Quel sera son coût lorsqu'il deviendra disponible sur le marché (GA) ?	
	* Quand serai-je facturé pour RECOMMENDATIONS ?	
	* Comment mettre à jour les services ?	
	* Quand mon abonnement à RECOMMENDATIONS prendra-t-il fin ?	
	* Comment annuler mon abonnement à RECOMMENDATIONS ?	
* Mise en route de RECOMMENDATIONS	
	* RECOMMENDATIONS est-il adapté à ma situation ?	
	* Quelles sont les conditions requises pour configurer RECOMMENDATIONS ?	
	* Comment configurer RECOMMENDATIONS pour la première fois ?	
	* Où puis-je trouver la documentation de l'API ?	
	* Quelles sont les options pour télécharger le catalogue et les données d'utilisation de RECOMMENDATIONS ?	
* Maintenance et assistance	
	* Quelle est la taille maximale de mon jeu de données ?	
	* Où puis-je obtenir une aide technique pour RECOMMENDATIONS ?	
	* Où puis-je trouver les conditions d'utilisation ?	
* Informations juridiques	


## Qu'est-ce que RECOMMENDATIONS ?
Conçu pour les organisations et les entreprises qui se basent sur les recommandations pour leurs ventes croisées et incitatives, le programme RECOMMENDATIONS de Microsoft Azure Machine Learning est un moteur de recommandations en libre-service sur Azure. C'est une implémentation du filtrage collaboratif utilisant la factorisation de matrice comme algorithme de base. Il est accessible aux développeurs d'applications sous la forme d'une API via une interface RESTful HTTP. RECOMMENDATIONS est actuellement disponible en version d'évaluation, mais est sur le point de devenir un service disponible sur le marché.

## Que puis-je faire avec RECOMMENDATIONS ?
RECOMMENDATIONS utilise comme données d'entrée un élément ou un ensemble d'éléments et renvoie une liste de recommandations pertinentes. Par exemple : un client d'un détaillant en ligne clique sur un produit. Le détaillant en ligne envoie ce produit comme une entrée à RECOMMENDATIONS, obtient une liste de produits et décide quels produits parmi ceux de la liste seront affichés au client. Vous pouvez également utiliser RECOMMENDATIONS pour optimiser votre boutique en ligne ou même pour informer votre service commercial interne ou votre centre d'appel.


## Existe-t-il des restrictions pendant la période de lancement ?

* Nombre maximal de modèles par abonnement : 10
* Nombre maximal d'éléments qu'un catalogue peut contenir : 100 000 100,000
* La taille maximale de données pouvant être envoyée dans POST (par exemple, importer des données de catalogue ou des données d'utilisation) est de 200 Mo.
* Le nombre de transactions par seconde pour un modèle de recommandation créé mais non activé est d'environ 2 TPS ; seul un modèle de recommandation créé mais actif peut contenir jusqu'à à 20 TPS.

## Achat et facturation 
Pour en savoir plus à propos des abonnements, lisez les réponses ci-dessous.

###Combien coûte RECOMMENDATIONS pendant la période de lancement ?
RECOMMENDATIONS est un service par abonnement. La facturation est effectuée en fonction du volume de transactions par mois. Pendant la période de lancement, le service est gratuit et limité à 100 000 transactions par mois.

###Existe-t-il des coûts liés au suivi et/ou au stockage de l'activité des utilisateurs pour moi par RECOMMENDATIONS ?
Pas pour le moment.

###Existe-t-il une version d'évaluation gratuite de RECOMMENDATIONS ?
Pendant la période de lancement, le service est gratuit et limité à 100 000 transactions par mois.

###Quand la période de lancement prendra-t-elle fin ?
La période de lancement et son prix sont supposés durer plusieurs mois. Nous informerons nos utilisateurs abonnés avant de modifier le statut et la tarification de ce service.

###Quel sera son coût lorsqu'il deviendra disponible sur le marché (GA) ?
Les tarifs de RECOMMENDATIONS lorsque le service sera disponible sur le marché n'ont pas encore été fixés. En cas de modifications des prix, les abonnés en seront avertis.

###Quand serai-je facturé pour RECOMMENDATIONS ?
Un abonnement payant est un abonnement pour lequel il existe des frais mensuels. Lorsque vous achetez un abonnement payant, le premier mois vous est immédiatement facturé. Le montant qui vous est facturé est celui qui est indiqué en regard de l'offre sur la page d'abonnement (plus les taxes applicables). Ce prix mensuel est facturé chaque mois à la même date jusqu'à l'annulation de votre abonnement. Cliquez ici pour plus d'informations sur les abonnements.

###Comment mettre à jour les services ?
Pendant la période de lancement, le service est limité à 100 000 transactions par mois et il n'est pas possible de le mettre à jour.

Lorsque RECOMMENDATIONS deviendra disponible sur le marché, si vous constatez que votre abonnement ne vous offre pas suffisamment de transactions, vous pourrez le mettre à niveau pour un abonnement avec une limite de transaction supérieure.

Lorsque vous mettez à niveau un abonnement :

* Les transactions qui demeurent sur votre ancien abonnement ne sont pas reportées sur votre nouvel abonnement. 
* Vous payez le plein prix du nouvel abonnement, même s'il reste des transactions inutilisées sur votre ancien abonnement.

Processus de mise à niveau d'un abonnement

* Connectez-vous à Marketplace.
* Cliquez sur l'onglet Mes Données.
* Cliquez sur Utiliser derrière l'abonnement que vous souhaitez mettre à niveau. 
* Dans le volet de droite, tous les abonnements disponibles sont répertoriés et votre abonnement apparaît grisé. Cliquez sur le bouton radio pour l'abonnement que vous souhaitez mettre à niveau.
* Si vous souhaitez mettre à niveau, cliquez sur OK dans la boîte de dialogue. Si vous ne souhaitez pas mettre à niveau, cliquez sur Annuler.

IMPORTANT : lisez attentivement la boîte de dialogue avant de mettre à niveau, car votre action aura des conséquences sur la facturation et l'utilisation du service.

Cliquez [ici](http://msdn.microsoft.com/library/gg312164.aspx) pour plus d'informations sur les abonnements.

###Quand mon abonnement à RECOMMENDATIONS prendra-t-il fin ?
Votre abonnement prendra fin lorsque vous l'annulerez. Si vous souhaitez annuler vos abonnements, consultez les instructions ci-dessous.

###Comment annuler mon abonnement à RECOMMENDATIONS ?
Pour annuler votre abonnement, suivez les étapes ci-dessous. Si votre abonnement actuel est un abonnement payant, il se poursuit jusqu'à la fin de la période de facturation actuelle. Si vous souhaitez que l'annulation soit immédiatement effective, [contactez-nous](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

Aucun remboursement ne sera accordé si vous annulez l'abonnement avant la fin d'une période de facturation ou pour les transactions non utilisées d'une période de facturation.

* Connectez-vous à Marketplace.
* Cliquez sur l'onglet Mes Données.
* Recherchez l'abonnement que vous souhaitez annuler.
* Cliquez sur Annuler à droite du nom et de l'état du jeu de données. Vous pouvez utiliser cet abonnement jusqu'à la fin de la période de facturation actuelle ou jusqu'à ce que votre limite de transaction soit atteinte.

Si vous souhaitez annuler votre abonnement immédiatement afin d'en acheter un nouveau, cliquez [ici](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn) pour compléter une demande auprès de notre service client.


## Mise en route de RECOMMENDATIONS

###RECOMMENDATIONS est-il adapté à ma situation ? 
RECOMMENDATIONS de Microsoft Azure Machine Learning est conçu pour les entreprises qui s'appuient sur les recommandations pour les ventes croisées et incitatives. Si vous disposez d'un site web à destination de vos clients, d'une équipe commerciale, d'une équipe commerciale interne ou d'un centre d'appels et si vous proposez un catalogue contenant quelques dizaines de produits ou services, l'utilisation de RECOMMENDATIONS pourrait être un avantage pour vos résultats financiers. Tester RECOMMENDATIONS est relativement simple. La version actuelle de l'API nécessite des compétences de programmation basiques. Si vous avez besoin d'aide, contactez le fournisseur qui a développé votre site web. Si vous avez un service informatique interne ou un développeur interne, ils doivent être en mesure de faire fonctionner RECOMMENDATIONS. 

###Quelles sont les conditions requises pour configurer RECOMMENDATIONS ?
RECOMMENDATIONS requiert que vous disposiez d'un journal des choix de l'utilisateur, car il est lié à votre catalogue. Si vous ne possédez pas ce type de journal mais que vous disposez d'un site web à destination des clients, RECOMMENDATIONS peut collecter l'activité des utilisateurs pour vous. RECOMMENDATIONS nécessite également un catalogue de vos produits ou services. Si vous n'avez pas le catalogue, RECOMMENDATIONS peut utiliser les données d'utilisation clients réelles et créer un catalogue. Un catalogue " implicite " n'inclut pas les éléments qui n'ont pas été " signalés " comme faisant partie des transactions utilisateur.

###Comment configurer RECOMMENDATIONS pour la première fois ?
Une fois abonné à RECOMMENDATIONS, vous devez utiliser la documentation de l'API   [ici](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ) pour configurer le service.

###Où puis-je trouver la documentation de l'API ? 
La documentation de l'API est [ici](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ).

###Quelles sont les options pour télécharger le catalogue et les données d'utilisation de RECOMMENDATIONS ?
Vous avez deux options pour télécharger vos données de catalogue et d'utilisation : soit vous exportez ces données à partir de votre système CRM ou d'autres journaux et les téléchargez dans RECOMMENDATIONS, soit vous ajoutez des balises à votre site web qui feront le suivi des activités de l'utilisateur. Si vous choisissez cette dernière option, les données sont stockées sur Azure.

##Maintenance et assistance

###Quelle est la taille maximale de mon jeu de données ?
Chaque jeu de données peut contenir jusqu'à 100 000 éléments de catalogue et jusqu'à 2 048 Mo de données d'utilisation.
En outre, un abonnement peut contenir jusqu'à 10 jeux de données (modèles).

###Où puis-je obtenir une aide technique pour RECOMMENDATIONS ?
Une assistance technique est disponible [ici](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning).

###Où puis-je trouver les conditions d'utilisation ?

Les conditions d'utilisation sont disponibles [ici](https://datamarket.azure.com/dataset/amla/recommendations#terms).

#Informations juridiques
Ce document est fourni " en l'état ". Les informations et illustrations présentées dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiées sans notification préalable. 
Certains exemples sont fournis à titre indicatif uniquement et sont fictifs. Aucune association ou connexion réelle n'est voulue et ne devrait être inférée. 
Ce document ne vous accorde aucun droit légal à la propriété intellectuelle pour un produit Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à titre de référence. 
(c) 2014 Microsoft. Tous droits réservés. 



<!--HONumber=46--> 
 