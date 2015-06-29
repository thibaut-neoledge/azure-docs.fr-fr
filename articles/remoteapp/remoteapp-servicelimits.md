<properties 
    pageTitle="Limites du service RemoteApp"
    description="En savoir plus sur les limites et les valeurs par défaut du service Azure RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/07/2015" 
    ms.author="elizapo" />


# Limites et valeurs par défaut du service RemoteApp

Cette rubrique présente les limites du service Azure RemoteApp.

  
## Limites de collection
Toutes les collections :

- Collections par utilisateur : 1
- Applications publiées par collection : 100 

Collections d'évaluation :

- Durée d'évaluation : 30 jours
- Collections d'évaluation : 2 par abonnement
- Utilisateurs de la version d'évaluation par collection : 10 
- Images du modèle d'évaluation : 25
 
Collections activées (payantes) :

- Collections payantes : 3 (cette limite peut augmenter sur votre demande)
- Images du modèle payant : 25

 
## Limites du nombre d'utilisateurs

Les limites de collection décrites sont des limites souples (ce qui signifie qu'elles peuvent être modifiées). Le nombre maximal d'utilisateurs par collection est une limite stricte, il n'est pas modifiable. Toutefois, le nombre d'utilisateurs que vous pouvez avoir est déterminé par le niveau de votre collection, comme suit :


- De base - 800 utilisateurs
- Standard - 500 utilisateurs

(Le nombre d'utilisateurs que vous pouvez prendre en charge est déterminé par le nombre de machines virtuelles utilisées pour votre collection. Pour le niveau De base, il existe 16 utilisateurs par machine virtuelle, tandis que le niveau Standard compte 10 utilisateurs par machine virtuelle.)

Pour déterminer le nombre maximal d'utilisateurs affectés que vous pouvez prendre en charge, multipliez le nombre d'utilisateurs payants par collection par le nombre de collections que vous avez.
  
Vous pouvez créer plusieurs collections dans votre compte en respectant ces limites et atteindre jusqu'à 5 000 connexions d'utilisateur simultanées pour l'ensemble des collections. Si vous avez besoin d'un nombre plus élevé, vous pouvez en faire la demande.

## Autres valeurs et limites :

- Stockage des données utilisateur par utilisateur et par collection : 50 Go
- Délai d'inactivité : 4 heures
- Délai de déconnexion : 4 heures

## Comment demander une augmentation de limite
Vous pouvez demander une augmentation des limites de collection et d'utilisateurs simultanés. Pour ce faire, ouvrez un ticket de support en expliquant vos besoins.


Pour ouvrir un ticket de support, procédez comme suit :

1.	Dans le portail de gestion, cliquez sur [Obtenir un support](https://manage.windowsazure.com/?getsupport=true). Si vous n'êtes pas connecté, vous devrez entrer vos informations d'identification.
2.	Sélectionnez votre abonnement.
3.	Sous le type de support, sélectionnez **Technique**.
4.	Cliquez sur **Créer un ticket**. 
5.	Sélectionnez **Azure RemoteApp** dans la liste de produits affichée dans la page suivante.
6.	Sélectionnez un **type de problème** approprié à votre situation.
7.	Cliquez sur **Continuer**.
8.	Suivez les instructions données dans la page suivante, puis entrez les détails de l'augmentation de limite dont vous avez besoin. Par exemple, si vous voulez prolonger votre période d'essai, entrez : « Veuillez prolonger ma période d'essai de X jours. » 
9.	Cliquez sur **Envoyer** pour ouvrir le ticket.

 

<!---HONumber=58_postMigration-->