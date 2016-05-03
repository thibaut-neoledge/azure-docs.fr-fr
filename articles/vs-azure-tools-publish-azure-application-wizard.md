<properties 
   pageTitle="Assistant de publication d’application Azure | Microsoft Azure"
   description="Assistant Publication d’application Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/19/2016"
   ms.author="tarcher" />

# Assistant Publication d’application Azure

## Vue d’ensemble

Une fois que vous avez développé une application web dans Visual Studio, vous pouvez publier cette application plus facilement dans un service cloud Azure à l'aide de l’Assistant **Publication d’application Azure**. La première section décrit les étapes que vous devez effectuer avant d’utiliser l'Assistant, et les sections suivantes décrivent les fonctionnalités de l'Assistant.

>[AZURE.NOTE] Cette rubrique concerne le déploiement sur des services cloud, pas sur des sites web. Pour plus d'informations sur le déploiement sur des sites web, consultez [Déploiement d'un site web Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## Configuration requise

Avant de pouvoir publier votre application web sur Azure, vous devez disposer d'un compte Microsoft et d’un abonnement Azure, et vous devez associer votre application web à un service cloud Azure. Si vous avez déjà effectué ces tâches, vous pouvez passer à la section suivante.

1. Obtenez un compte Microsoft et un abonnement Azure. Vous pouvez faire un essai gratuit de l’abonnement Azure pendant un mois [ici](https://azure.microsoft.com/pricing/free-trial/)

1. Créez un service cloud et un compte de stockage dans Azure. Vous pouvez le faire à partir de l'Explorateur de serveurs dans Visual Studio ou à l'aide du [portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Activez votre application web pour Azure. Pour que votre application web puisse être publiée sur Azure à partir de Visual Studio, vous devez l’associer à un projet de service cloud Azure dans Visual Studio. Pour créer le projet de service cloud associé, ouvrez le menu contextuel du projet de votre application web, puis sélectionnez Convertir, **Convertir en projet de service cloud Azure**.

1. Une fois le projet de service cloud ajouté à votre solution, ouvrez de nouveau le même menu contextuel, puis sélectionnez **Publier**. Pour plus d'informations sur l'activation des applications pour Azure, consultez [Migration et publication d’une application web sur un service cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Veillez à démarrer Visual Studio en tant qu'administrateur (Exécuter en tant qu'administrateur).

1. Lorsque vous êtes prêt à publier votre application, ouvrez le menu contextuel du projet de service cloud Azure, puis sélectionnez **Publier**. Les étapes suivantes montrent l'Assistant Publication d’application Azure.

## Choisir votre abonnement

### Pour choisir un abonnement

1. Avant d'utiliser l'Assistant pour la première fois, vous devez vous connecter. Sélectionnez le lien **Se connecter**. Connectez-vous au portail Azure lorsque vous y êtes invité et fournissez votre nom d'utilisateur et votre mot de passe Azure. 

    ![Il s’agit d’un des écrans de l'Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    La liste des abonnements se remplit avec les abonnements associés à votre compte. Vous pouvez également voir des abonnements provenant de tous les fichiers d'abonnement que vous avez importés précédemment.

1. Dans la liste **Choisissez votre abonnement**, sélectionnez l'abonnement à utiliser pour ce déploiement.

   Si vous sélectionnez **<Gérer...>**, la boîte de dialogue **Gérer les abonnements** s'affiche et vous pouvez choisir l'abonnement et le compte utilisateur à utiliser. L’onglet **Comptes** montre tous vos comptes et l’onglet **Abonnements** affiche tous les abonnements associés aux comptes. Vous pouvez également sélectionner une région à partir de laquelle utiliser les ressources Azure, ainsi que créer ou importer des certificats pour votre abonnement à partir du portail Azure. Si vous avez importé des abonnements à partir d'un fichier d'abonnement, les certificats associés s’affichent dans l’onglet **Certificats**. Lorsque vous avez terminé, sélectionnez le bouton **Fermer**.

    ![Manage subscriptions](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] A subscription file can contain more than one subscription.

1. Sélectionnez le bouton **Suivant** pour continuer. 

    S’il n’y a pas de services cloud dans votre abonnement, vous devez créer un service cloud dans Azure pour héberger votre projet. La boîte de dialogue **Créer un service cloud et un compte de stockage** s'affiche.

    Spécifiez un nouveau nom pour le service cloud. Le nom doit être unique dans Azure. Spécifiez ensuite une région ou un groupe d'affinités pour un centre de données proche de chez vous ou de la plupart de vos clients. Ce nom est également utilisé pour un nouveau compte de stockage qu’Azure crée pour votre service cloud.

1. Modifiez les paramètres que vous souhaitez pour ce déploiement, puis publiez-le en sélectionnant le bouton **Publier** (La section suivante fournit plus de détails sur les différents paramètres). Pour vérifier les paramètres avant la publication, sélectionnez le bouton **Suivant**.

    >[AZURE.NOTE] Si vous avez sélectionné Publier à cette étape, vous pouvez surveiller l'état de ce déploiement dans Visual Studio.

Vous pouvez modifier les paramètres courants et avancés du déploiement à l'aide de l’Assistant **Publication d’application Azure**. Par exemple, vous pouvez choisir un paramètre permettant de déployer votre application dans un environnement de test avant de la diffuser. L'illustration suivante montre l’onglet **Paramètres courants** pour un déploiement Azure.

![Paramètres courants](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## Configuration de vos paramètres de publication

### Pour configurer les paramètres de publication

1. Dans la liste **Service cloud**, suivez l'une des procédures suivantes :

   1. Dans la liste déroulante, sélectionnez un service cloud existant. L'emplacement du centre de données du service s'affiche. Notez cet emplacement et assurez-vous que l’emplacement de votre compte de stockage soit dans le même centre de données.

    1. Sélectionnez **Créer** pour créer un service cloud hébergé par Azure. Dans la boîte de dialogue **Créer un service cloud**, donnez un nom au service et spécifiez une région ou un groupe d'affinités pour spécifier l'emplacement du centre de données dans lequel vous souhaitez héberger ce service cloud. Le nom doit être unique dans Azure.

1. Dans la liste **Environnement**, sélectionnez **Production** ou **Intermédiaire**. Choisissez l'environnement intermédiaire si vous souhaitez déployer votre application dans un environnement de test. Vous pourrez déplacer ultérieurement votre application dans l'environnement de production.

1. Dans la liste **Configuration de build**, sélectionnez **Débogage** ou **Version finale**.

1. Dans la liste **Configuration de service**, sélectionnez **Cloud** ou **Local**.

    Cochez la case **Activer le Bureau à distance pour tous les rôles** si vous souhaitez pouvoir vous connecter à distance au service. Cette option est principalement utilisée pour le dépannage. Une fois cette case cochée, la boîte de dialogue **Configuration du Bureau à distance** s’affiche. Cliquez sur le lien Paramètres pour modifier la configuration.

    Cochez la case **Activer Web Deploy pour tous les rôles web** pour activer le déploiement web pour le service. Vous devez activer le Bureau à distance pour pouvoir utiliser cette fonctionnalité. Pour plus d’informations, consultez [[Publication d’un service cloud à l’aide des outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Pour plus d’informations sur Web Deploy, consultez [[Publication d’un service cloud à l’aide des outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Sélectionnez l’onglet **Paramètres avancés**. Dans le champ **Étiquette de déploiement**, acceptez le nom par défaut ou entrez un nom de votre choix. Pour ajouter la date à l'étiquette de déploiement, laissez la case cochée.

    ![Troisième écran de l'Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Dans la liste **Compte de stockage** sélectionnez le compte de stockage à utiliser pour ce déploiement. Comparez les emplacements des centres de données pour votre service cloud et votre compte de stockage. Dans l'idéal, ces emplacements doivent être identiques.

    >[AZURE.NOTE] Le compte de stockage Azure stocke le package pour le déploiement de l'application. Une fois l'application déployée, le package est supprimé du compte de stockage.

1. Cochez la case **Mise à jour du déploiement** si vous souhaitez déployer uniquement les composants mis à jour. Ce type de déploiement peut être plus rapide qu'un déploiement complet. Sélectionnez le lien **Paramètres** pour ouvrir la boîte de dialogue **Paramètres de mise à jour du déploiement** présentée dans l'illustration suivante.

    ![Paramètres de déploiement](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Vous pouvez choisir une des deux options de déploiement d’une mise à jour : incrémentiel ou simultané. Un déploiement incrémentiel met à jour une instance déployée à la fois, afin que votre application reste en ligne et accessible aux utilisateurs. Un déploiement simultané met à jour toutes les instances déployées en même temps. La mise à jour simultanée est plus rapide que la mise à jour incrémentielle mais, si vous choisissez cette option, votre application peut ne pas être disponible pendant le processus de mise à jour.

    Cochez la case Si le déploiement ne peut pas être mis à jour, effectuer un déploiement complet si vous souhaitez qu’un déploiement complet soit automatiquement lancé en cas d'échec du déploiement de la mise à jour. Un déploiement complet réinitialise l'adresse IP virtuelle (VIP) du service cloud. Pour plus d'informations, consultez [Conservation d’une adresse IP virtuelle constante pour un service cloud](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Pour déboguer votre service, cochez la case **Activer IntelliTrace** ou, si vous déployez une configuration **Débogage** et que vous souhaitez déboguer votre service cloud dans Azure, cochez la case **Activer le débogueur distant pour tous les rôles** pour déployer les services de débogage à distance.

2. Pour profiler l'application, cochez la case **Activer le profilage**, puis sélectionnez le lien **Paramètres** pour afficher les options de profilage.


    >[AZURE.NOTE] Vous devez utiliser Visual Studio Ultimate pour activer IntelliTrace ou le profilage d'interaction de couche (TIP), et vous ne pouvez pas activer les deux en même temps.

    Pour plus d’informations, consultez [Débogage d’un service cloud publié avec IntelliTrace et Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) et [Test des performances d'un service cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Sélectionnez **Suivant** pour afficher la page de résumé de l'application.

## Publication de votre application

1. Vous pouvez choisir de créer un profil de publication à partir des paramètres que vous avez choisis. Par exemple, vous pouvez créer un profil pour un environnement de test et un autre pour la production. Pour enregistrer ce profil, sélectionnez l’icône **Enregistrer**. L'Assistant crée le profil et l'enregistre dans le projet Visual Studio. Pour modifier le nom du profil, ouvrez la liste **Profil cible**, puis sélectionnez **<Gérer...>**.

    ![Écran de résumé de l'Assistant Publication](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] Le profil de publication s'affiche dans l'Explorateur de solutions dans Visual Studio, et les paramètres du profil sont écrits dans un fichier portant l'extension .azurePubxml. Les paramètres sont enregistrés en tant qu'attributs de balises XML.

1. Sélectionnez **Publier** pour publier votre application. Vous pouvez surveiller l'état du processus dans la fenêtre **Sortie** dans Visual Studio.

## Voir aussi

[Migration et publication d’une application web sur un service cloud Azure à partir de Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publication d’un service cloud à l’aide des outils Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Débogage d’un service cloud publié avec IntelliTrace et Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Test des performances d'un service cloud](https://msdn.microsoft.com/library/azure/hh369930.aspx)

<!---HONumber=AcomDC_0420_2016-->