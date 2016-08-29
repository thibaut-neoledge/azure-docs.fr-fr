<properties 
   pageTitle="Débogage d’un service cloud publié avec IntelliTrace et Visual Studio | Microsoft Azure"
   description="Débogage d’un service cloud publié avec IntelliTrace et Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Débogage d’un service cloud publié avec IntelliTrace et Visual Studio

##Vue d'ensemble

Avec IntelliTrace, vous pouvez enregistrer des informations de débogage détaillées pour une instance de rôle exécutée dans Azure. Si vous devez identifier la cause d’un problème, utilisez les journaux IntelliTrace pour exécuter pas à pas votre code à partir de Visual Studio comme s’il était exécuté dans Azure. En effet, IntelliTrace enregistre les principales données sur l’exécution du code et sur l’environnement quand votre application Azure est exécutée en tant que service cloud dans Azure. IntelliTrace vous permet d’examiner les données enregistrées à partir de Visual Studio. Vous pouvez également utiliser le débogage distant pour l’attachement direct à un service cloud exécuté dans Azure. Consultez [Débogage de Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace est conçu uniquement pour des scénarios de débogage et ne doit pas être utilisé dans le cadre d’un déploiement de production.

>[AZURE.NOTE] Vous pouvez utiliser IntelliTrace si Visual Studio Enterprise est installé et que votre application Azure cible .NET Framework 4 ou une version ultérieure. IntelliTrace collecte des informations pour vos rôles Azure. Les machines virtuelles pour ces rôles exécutent toujours des systèmes d’exploitation 64 bits.

## Pour configurer une application Azure pour IntelliTrace

Pour activer IntelliTrace pour une application Azure, vous devez créer et publier l’application à partir d’un projet Azure Visual Studio. Configurez IntelliTrace pour votre application Azure avant de la publier dans Azure. Si vous publiez votre application sans configurer IntelliTrace, mais que vous décidez ensuite de le faire, vous devrez republier l’application à partir de Visual Studio. Pour plus d’informations, consultez [Publication d’un service cloud à l’aide d’Azure Tools](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quand vous êtes prêt à déployer votre application Azure, vérifiez que les cibles de publication de votre projet sont définies sur **Debug**.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le projet Azure, puis choisissez **Publier**.
 
    L’Assistant Publication d’application Azure s’affiche.

1. Pour collecter des journaux IntelliTrace pour votre application publiée dans le cloud, cochez la case **Activer IntelliTrace**.

    >[AZURE.NOTE] Vous pouvez activer IntelliTrace ou le profilage quand vous publiez votre application Azure, mais vous ne pouvez pas activer les deux à la fois.

1. Pour personnaliser la configuration IntelliTrace initiale, choisissez le lien hypertexte **Paramètres**.

    La boîte de dialogue Paramètres IntelliTrace s’affiche (voir l’illustration suivante). Vous pouvez spécifier les événements à enregistrer dans le journal, si les informations d’appel doivent être collectées ou non, pour quels modules et processus collecter les journaux, et la quantité d’espace à allouer à l’enregistrement. Pour plus d’informations sur IntelliTrace, consultez [Débogage avec IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST\_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Le journal IntelliTrace est un fichier journal circulaire dont la taille maximale est spécifiée dans les paramètres IntelliTrace (la taille par défaut est de 250 Mo). Les journaux IntelliTrace sont regroupés dans un même fichier, situé dans le système de fichiers de la machine virtuelle. Quand vous demandez les journaux, un instantané est pris à ce moment précis, puis téléchargé vers votre ordinateur local.

Après avoir publié l’application Azure dans Azure, vous pouvez déterminer si IntelliTrace a été activé depuis le nœud de calcul Azure dans l’Explorateur de serveurs, comme indiqué dans l’illustration suivante :

![VST\_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## Téléchargement des journaux IntelliTrace pour une instance de rôle

Vous pouvez télécharger les journaux IntelliTrace pour une instance de rôle à partir du nœud **Cloud Services** dans l’**Explorateur de serveurs**. Développez le nœud **Cloud Services** jusqu’à l’instance qui vous intéresse, ouvrez le menu contextuel de cette instance et choisissez **Afficher les fichiers journaux IntelliTrace**. Les journaux IntelliTrace sont téléchargés et regroupés dans un fichier, situé dans un répertoire de votre ordinateur local. Chaque fois que vous demandez les journaux IntelliTrace, un nouvel instantané est créé.

Visual Studio affiche la progression du téléchargement des journaux dans la fenêtre Journal des activités Azure. Comme le montre l’illustration ci-dessous, il est possible de développer la ligne correspondant à l’opération pour afficher plus de détails.

![VST\_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Vous pouvez continuer à utiliser Visual Studio pendant le téléchargement des journaux IntelliTrace. Quand le téléchargement d’un journal est terminé, le journal s’ouvre automatiquement dans Visual Studio.

>[AZURE.NOTE] Les journaux IntelliTrace peuvent contenir des exceptions que l’infrastructure génère et gère par la suite. Le code de l’infrastructure interne génère ces exceptions dans le cadre normal du démarrage d’un rôle. Vous pouvez donc les ignorer en toute sécurité.

## Voir aussi

[Débogage de Cloud Services](https://msdn.microsoft.com/library/ee405479.aspx)

<!---HONumber=AcomDC_0817_2016-->