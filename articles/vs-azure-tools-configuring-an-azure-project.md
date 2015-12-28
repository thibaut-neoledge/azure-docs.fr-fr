<properties
   pageTitle="Configurer un projet de service cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure dans Visual Studio selon vos spécifications pour ce projet."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/29/2015"
   ms.author="tarcher" />

# Configurer un projet de service cloud Azure avec Visual Studio

Vous pouvez configurer un projet de service cloud Azure selon vos spécifications pour ce projet. Vous pouvez définir des propriétés pour le projet pour les catégories suivantes :

- **Publier un service cloud sur Azure**

  Vous pouvez définir une propriété pour vous assurer qu’un service cloud existant déployé sur Azure ne puisse pas être effacé accidentellement.

- **Exécuter ou déboguer un service cloud sur l’ordinateur local**

  Vous pouvez sélectionner une configuration de service à utiliser et indiquer si vous voulez démarrer l’émulateur de stockage Azure.

- **Valider un package de services cloud une fois qu’il est créé**

  Vous pouvez décider de traiter tous les avertissements comme des erreurs, afin de vous assurer que le package de service cloud se déploie sans problèmes. Cela réduit votre temps d’attente quand vous déployez puis que vous découvrez qu’un échec s’est produit.

L’illustration suivante montre comment sélectionner une configuration à utiliser quand vous exécutez ou que vous déboguez votre service cloud localement. Depuis cette fenêtre, vous pouvez définir n’importe laquelle des propriétés du projet dont vous avez besoin, comme indiqué dans l’illustration.

![Configurer un projet Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Pour configurer un projet de service cloud Azure

1. Pour configurer un projet de service cloud depuis l’**Explorateur de solutions**, ouvrez le menu contextuel pour le projet de service cloud, puis choisissez **Propriétés**.

  Une page avec le nom du projet de service cloud est affichée dans l’éditeur Visual Studio.

1. Choisissez l’onglet **Développement**.

1. Pour vous assurer que vous ne supprimez pas accidentellement un déploiement existant dans Azure, dans la liste Demandez confirmation de la suppression d’un déploiement existant, choisissez **Vrai**.

1. Pour sélectionner la configuration de service que vous voulez utiliser quand vous exécutez ou que vous déboguez votre service cloud localement, dans la liste **Configuration du service**, choisissez la configuration de service.

  >[AZURE.NOTE]Si vous voulez créer une configuration de service à utiliser, consultez Procédure : gestion des configurations de service et des profils. Si vous voulez modifier une configuration de service pour un rôle, consultez [Comment configurer les rôles pour un service cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Pour démarrer l’émulateur de stockage Azure quand vous exécutez ou que vous déboguez localement votre service cloud, dans **Démarrer l’émulateur de stockage Microsoft Azure**, choisissez **Vrai**.

1. Pour vous assurer que vous ne pouvez pas publier s’il y a des erreurs de validation du package, dans **Considérer les avertissements comme des erreurs**, choisissez **Vrai**.

1. Pour vous assurer que votre rôle web utilise le même port à chaque démarrage local dans IIS Express, dans **Utiliser les ports du projet web**, choisissez **Vrai**. Pour utiliser un port spécifique pour un projet web donné, ouvrez le menu contextuel du projet web, choisissez l’onglet **Propriétés**, puis l’onglet **Web**, et changez le numéro de port dans le paramètre **Url du projet** de la section **IIS Express**. Par exemple, entrez `http://localhost:14020` comme URL du projet.

1. Pour enregistrer toutes les modifications que vous avez apportées aux propriétés du projet de service cloud, choisissez le bouton **Enregistrer** dans la barre d’outils.

## Étapes suivantes

Pour en savoir plus sur la façon de configurer des projets de service cloud Azure dans Visual Studio, consultez [Configuration de votre projet Azure à l’aide de plusieurs configurations de service](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=AcomDC_1217_2015-->