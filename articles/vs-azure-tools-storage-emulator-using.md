<properties 
   pageTitle="Configuration et utilisation de l’émulateur de stockage avec Visual Studio"
   description="Configuration et utilisation de l’émulateur de stockage avec Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# Configuration et utilisation de l’émulateur de stockage avec Visual Studio

L’environnement de développement du Kit de développement logiciel (SDK) Azure comprend l’émulateur de stockage, un utilitaire qui simule les services de stockage d’objets blob, de files d’attente et de tables d’Azure Storage sur votre ordinateur de développement local. Si vous créez un service cloud qui utilise les services de stockage Azure ou écrivez une application externe qui appelle les services de stockage, vous pouvez tester localement votre code sur l’émulateur de stockage. Azure Tools pour Microsoft Visual Studio intègre la gestion de l’émulateur de stockage dans Visual Studio. Azure Tools initialise la base de données de l’émulateur de stockage à la première utilisation, il démarre le service de l’émulateur de stockage quand vous exécutez ou déboguez votre code dans Visual Studio, et il fournit un accès en lecture seule aux données de l’émulateur de stockage via l’Explorateur de stockage Azure.

Pour plus d’informations sur l’émulateur de stockage, y compris la configuration système requise et les instructions de configuration personnalisée, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](./storage/storage-use-emulator/).

>[AZURE.NOTE]Il existe quelques différences de fonctionnalité entre la simulation de l’émulateur de stockage et les services de stockage Azure. Pour plus d’informations sur ces différences, consultez [Différences entre l’émulateur de stockage et Azure Storage](./storage/storage-use-emulator) dans la documentation du Kit de développement logiciel (SDK) Azure.

## Configuration d’une chaîne de connexion pour l’émulateur de stockage

Pour accéder à l’émulateur de stockage depuis du code au sein d’un rôle, vous devez configurer une chaîne de connexion qui pointe vers l’émulateur de stockage et qui pourra être modifiée ultérieurement pour pointer vers un compte de stockage Azure. Une chaîne de connexion est un paramètre de configuration que votre rôle peut lire pendant l’exécution pour se connecter à un compte de stockage. Pour plus d’informations sur la création de chaînes de connexion, consultez [Configuration d’une application Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE]Vous pouvez retourner une référence au compte de l’émulateur de stockage à partir de votre code à l’aide de la propriété **DevelopmentStorageAccount**. Cette approche fonctionne correctement si vous voulez accéder à l’émulateur de stockage à partir de votre code. Toutefois, si vous projetez de publier votre application dans Azure, vous devrez créer une chaîne de connexion pour accéder à votre compte de stockage et modifier votre code pour utiliser cette chaîne de connexion avant de publier l’application. Si vous passez fréquemment du compte de l’émulateur de stockage à un compte de stockage Azure, l’utilisation d’une chaîne de connexion vous permettra de simplifier ce processus.

## Initialisation et exécution de l’émulateur de stockage

Il est possible de lancer automatiquement l’émulateur de stockage quand vous exécutez ou déboguez votre service dans Visual Studio. Dans l’Explorateur de solutions, ouvrez le menu contextuel de votre projet **Azure**, puis choisissez **Propriétés**. Sous l’onglet **Développement**, dans la liste **Démarrer l’émulateur de stockage Microsoft Azure**, choisissez **Vrai** (si cette valeur n’est pas déjà définie).

La première fois que vous exécutez ou déboguez votre service à partir de Visual Studio, l’émulateur de stockage lance un processus d’initialisation. Ce processus réserve des ports locaux pour l’émulateur de stockage et crée la base de données de l’émulateur de stockage. Une fois terminé, ce processus n’aura pas besoin d’être réexécuté, à moins que la base de données de l’émulateur de stockage ne soit supprimée.

>[AZURE.NOTE]À compter de la version de juin 2012 d’Azure Tools, l’émulateur de stockage est exécuté par défaut dans SQL Express LocalDB. Dans les versions antérieures d’Azure Tools, l’émulateur de stockage est exécuté sur une instance par défaut de SQL Express 2005 ou 2008, que vous devez installer avant de pouvoir installer le Kit de développement logiciel (SDK) Azure. Vous pouvez également exécuter l’émulateur de stockage sur une instance nommée de SQL Express ou sur une instance nommée ou par défaut de Microsoft SQL Server. Si vous avez besoin de configurer l’émulateur de stockage pour qu’il soit exécuté sur une instance autre que celle par défaut, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](./storage/storage-use-emulator/).

L’émulateur de stockage fournit une interface utilisateur qui permet de voir l’état des services de stockage local, ainsi que de les démarrer, arrêter et réinitialiser. Une fois que le service d’émulateur de stockage a été démarré, vous pouvez afficher l’interface utilisateur et démarrer ou arrêter le service en double-cliquant sur l’icône de zone de notification de l’émulateur Microsoft Azure dans la barre des tâches Windows.

## Affichage des données de l’émulateur de stockage dans l’Explorateur de serveurs

Le nœud Azure Storage dans l’Explorateur de serveurs permet d’afficher des données et de modifier les paramètres des données d’objets blob et de tables de vos comptes de stockage, y compris celui de l’émulateur de stockage. Pour plus d’informations, consultez [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](https://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=Oct15_HO3-->