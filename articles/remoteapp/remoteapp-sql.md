<properties
   pageTitle="Azure SQL avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment utiliser SQL Azure avec Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# SQL Azure avec Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Souvent, lorsque les clients choisissent d’héberger leurs applications Windows dans le cloud avec Azure RemoteApp, ils souhaitent également migrer leurs données telles que les serveurs SQL dans le cloud pour un déploiement intégral sur le cloud. Cela permet à la solution hébergée en totalité sur le cloud d’être accessible à tout moment par le biais de n’importe quel appareil, n’importe où à l’aide d’Azure RemoteApp. Vous trouverez ci-dessous des liens et des références, ainsi que des conseils pour vous aider dans ce processus.

## Migrer vos données SQL

Commencez par la [Migration d’une base de données SQL Server vers une base de données SQL Azure](../sql-database/sql-database-cloud-migrate.md).

## Configuration d’Azure RemoteApp
Hébergez votre application Windows dans Azure RemoteApp. Vous trouverez ci-dessous une procédure étape par étape de très haut niveau :

1.     Créez la [machine virtuelle du modèle Azure RemoteApp](remoteapp-imageoptions.md).
2.     Installez l’application requise sur la machine virtuelle.
3.     Configurez l’application afin qu’elle se connecte à la base de données SQL et vérifiez qu’elle fonctionne.
4.     Sysprep et arrêt de la machine virtuelle. Enregistrez cet élément sous forme d’image pour l’utiliser avec Azure. **Remarque :** vous devrez vous assurer que l’application est en mesure de conserver les informations de connectivité de base de données via le processus sysprep. Si l’application ne peut pas conserver les informations de connexion de base de données, il se peut que vous deviez engager le fournisseur de l’application pour étudier la façon dont vous spécifiez la chaîne de connexion.
5.     Importez l’image personnalisée dans votre bibliothèque Azure RemoteApp en sélectionnant l’emplacement géographique de résidence du déploiement SQL Azure.
6.     Déployez une collection RemoteApp dans le même centre de données que votre déploiement de SQL Azure en utilisant le modèle ci-dessus, puis publiez l’application. Le déploiement d’Azure RemoteApp dans le même centre de données que votre déploiement SQL Azure garantit des vitesses de connexion plus rapides et réduit les temps de latence.

## Considérations relatives à la configuration d’application et de SQL :
Quelques points doivent être pris en compte lors de l’utilisation de SQL Azure avec RemoteApp :

Apprenez [Comment configurer votre pare-feu Microsoft Azure SQL Database](../sql-database/sql-database-firewall-configure.md). Un extrait de l’article mentionne « Initialement, tout accès à votre serveur de base de données SQL Azure est bloqué par le pare-feu. Pour commencer à utiliser votre serveur Azure SQL Database, vous devez accéder au portail Azure Classic et spécifier une ou plusieurs règles de pare-feu au niveau du serveur qui permettent l’accès à votre serveur Azure SQL Database. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP Internet qui sont autorisées, et si les applications Azure peuvent essayer de se connecter à votre serveur Azure SQL Database. »

De plus, quand un ordinateur tente de se connecter à votre serveur de base de données à partir d’Internet, le pare-feu vérifie l’adresse IP d’origine de la demande par rapport à la totalité des règles de pare-feu au niveau du serveur et, si nécessaire, au niveau de la base de données : « Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée à votre serveur Azure SQL Database. » Par conséquent, nous pouvons utiliser des plages d’adresses IP, et pas uniquement des adresses IP source individuelles.

Suivez les instructions étape par étape de la section [Procédure : Configuration des paramètres du pare-feu sur une base de données SQL à l’aide du portail Azure](../sql-database/sql-database-configure-firewall-settings.md) pour spécifier la plage IP. Lorsque vous configurez les règles de pare-feu de SQL, indiquez la plage IP du sous-réseau spécifiée pour la collection Azure RemoteApp. Cela devrait permettre aux serveurs ARA de se connecter à la base de données SQL, même si des adresses IP sont affectées de manière dynamique.

## Résolution de problèmes
Si l’expérience d’utilisation d’une application cliente hébergée dans Azure RemoteApp qui se connecte à SQL database hébergée sur Azure ou en local est lente, cela peut être dû à plusieurs raisons.

- La latence du réseau à partir de votre appareil dans Azure est élevée. Passez sur la meilleure connexion, la plus rapide pour obtenir les meilleures performances. Utilisez [azurespeed.com](http://azurespeed.com/) comme outil général pour tester la latence de vos périphériques vers le centre de données Azure.
- L’application cliente hébergée dans Azure RemoteApp est surchargée. En sélectionnant un autre plan de facturation, par exemple, la facturation Premium, vous pouvez améliorer les performances. Une autre astuce consiste à analyser les ressources que votre application utilise : pendant une session active, appuyez sur les touches CTRL, ALT et Fin pour lancer l’écran SAP, sélectionnez Gestionnaire des tâches et observez le taux d’utilisation des ressources correspondant à votre application.
- SQL Server est surchargé ou n’est pas optimisé. Suivez les instructions SQL pour le dépannage.

<!---HONumber=AcomDC_0817_2016-->