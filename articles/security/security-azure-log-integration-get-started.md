---
title: "Bien démarrer l’intégration des journaux Azure | Microsoft Docs"
description: "Découvrez comment installer le service d’intégration des journaux Azure et intégrer les journaux d’audit Azure et les alertes de l’Azure Security Center."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Intégration des journaux Azure avec Azure Diagnostics Logging et Windows Event Forwarding
L’intégration des journaux Azure (AzLog) permet d’intégrer des journaux bruts de vos ressources Azure dans vos systèmes SIEM (Security Information and Event Management) locaux. Cette intégration offre un tableau de bord de sécurité unifié pour toutes vos ressources, en local ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité associés à vos applications.
>[!NOTE]
Pour plus d’informations sur l’intégration des journaux Azure, vous pouvez consulter la [Présentation de l’intégration des journaux Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview).

Cet article vous aidera à vous familiariser avec l’intégration des journaux Azure en se concentrant sur l’installation du service Azlog et en intégrant le service avec Diagnostics Azure. Le service d’intégration des journaux Azure sera alors en mesure de collecter les informations du journal des événements Windows provenant du canal des événements de sécurité Windows à partir des machines virtuelles déployées dans Azure IaaS. Cela est très similaire au « Transfert d’événements » que vous avez peut-être utilisé en local.

>[!NOTE]
>La possibilité de définir la sortie de l’intégration des journaux Azure sur le SIEM est fournie par le SIEM lui-même. Veuillez consulter l’article [Intégration de l’intégration des journaux Azure avec votre SIEM local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) pour plus d’informations.

Pour être très clair, le service d’intégration des journaux Azure s’exécute sur un ordinateur physique ou virtuel qui utilise le système d’exploitation Windows Server 2008 R2 ou version ultérieure (Windows Server 2012 R2 ou Windows Server 2016 sont les versions préférées).

L’ordinateur physique peut fonctionner en local (ou sur un site hébergeur). Si vous choisissez d’exécuter le service d’intégration des journaux Azure sur un ordinateur virtuel, cette machine virtuelle peut être située en local ou dans un cloud public tel que Microsoft Azure.

La machine physique ou virtuelle qui exécute le service d’intégration des journaux Azure nécessite une connexion réseau avec le cloud public Azure. La procédure décrite dans cet article fournit des détails sur la configuration.

## <a name="prerequisites"></a>Composants requis
Au minimum, l’installation d’AzLog nécessite les éléments suivants :
* Un **abonnement Azure**. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/free/).
* Un **compte de stockage** qui peut être utilisé pour la journalisation des diagnostics Windows Azure (vous pouvez utiliser un compte de stockage préconfiguré ou en créer un nouveau : nous apprendrons à configurer le compte de stockage plus loin dans cet article)
  >[!NOTE]
  Selon votre scénario, un compte de stockage n’est peut-être pas nécessaire. Pour le scénario des diagnostics Azure abordé dans cet article, un compte de stockage sera nécessaire.
* **Deux systèmes** : une machine qui exécute le service d’intégration de journaux Azure et une machine qui sera surveillée et dont les informations de journalisation seront envoyées à la machine exécutant le service Azlog.
   * Une machine que vous souhaitez surveiller : il s’agit d’une machine virtuelle utilisée comme [Machine virtuelle Azure](../virtual-machines/virtual-machines-windows-overview.md)
   * Une machine qui exécute le service d’intégration des journaux Azure. Cette machine collecte toutes les informations de journalisation qui seront importées ultérieurement dans votre SIEM.
    * Ce système peut être local ou dans Microsoft Azure.  
    * Il doit fonctionner sous une version x64 de Windows Server 2008 R2 SP1 ou version ultérieure et .NET 4.5.1 doit être installé dessus. Vous pouvez déterminer la version de .NET installée en consultant l’article intitulé [Comment : déterminer les versions .NET Framework installées](https://msdn.microsoft.com/library/hh925568)  
    Il doit être connecté à un compte de stockage Azure utilisé pour la journalisation des diagnostics Azure. Nous fournirons des instructions plus loin dans cet article sur la façon dont vous pouvez vérifier cette connectivité

Pour une démonstration rapide du processus de création d’une machine virtuelle à l’aide du portail Azure, visionnez la vidéo ci-dessous.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement
Lors du test d’intégration des journaux Azure, vous pouvez utiliser n’importe quel système qui respecte les exigences de configuration minimale du système d’exploitation. Toutefois, pour un environnement de production, la charge peut vous obliger à planifier une montée en puissance ou une augmentation de la taille des instances.

Vous pouvez exécuter plusieurs instances du service d’intégration des journaux Azure (une seule instance par machine physique ou virtuelle) si le volume d’événements est élevé. En outre, vous pouvez équilibrer la charge des comptes de stockage Diagnostics Windows Azure pour Windows (WAD) et le nombre d’abonnements à fournir aux instances doit être basé sur la capacité.
>[!NOTE]
À ce stade, nous n’avons aucune recommandation spécifique pour le moment auquel monter en puissance des instances des machines d’intégration de journaux Azure (par exemple, les machines qui exécutent le service d’intégration des journaux Azure), ou pour les abonnements ou comptes de stockage. Les décisions de mise à l’échelle doivent être basées sur vos observations de performances dans chacun de ces domaines.

Vous avez également la possibilité de faire monter en puissance le service d’intégration des journaux Azure afin d’améliorer les performances. Les indicateurs de performance suivants peuvent vous aider à mettre à l’échelle les machines sur lesquelles vous choisissez d’exécuter le service d’intégration des journaux Azure :
* Sur un ordinateur 8 processeurs (cœurs), une seule instance de l’intégrateur Azlog peut traiter environ 24 millions d’événements par jour (environ 1 million/heure).

* Sur un ordinateur 4 processeurs (cœurs), une seule instance de l’intégrateur Azlog peut traiter environ 1,5 million d’événements par jour (environ 62 500 millions/heure).

## <a name="install-azure-log-integration"></a>Installer l’intégration des journaux Azure
Pour installer l’intégration des journaux Azure, vous devez télécharger le fichier d’installation [Intégration des journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324). Exécutez la routine de configuration et décidez si vous souhaitez fournir des informations de télémétrie à Microsoft.  

![Écran d’installation avec la case Télémétrie cochée](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Nous vous conseillons d’autoriser Microsoft à collecter les données de télémétrie. Vous pouvez désactiver la collecte des données de télémétrie en décochant cette option.
>


Le service d’intégration des journaux Azure collecte les données de télémétrie à partir de l’ordinateur sur lequel il est installé.  

Les données de télémétrie recueillies sont les suivantes :

* Les exceptions qui se produisent pendant l’exécution de l’intégration des journaux Azure
* Des métriques concernant le nombre de requêtes et d’événements traités
* Des statistiques sur les options de ligne de commande Azlog.exe sont utilisées

Le processus d’installation est traité dans la vidéo ci-dessous.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Étapes postérieures à l’installation et la validation
À l’issue de la routine d’installation de base, vous êtes prêt à effectuer les étapes postérieures à l’installation et la validation :
1. Ouvrez une fenêtre PowerShell avec élévation de privilèges et accédez à **c:\Program Files\Microsoft Azure Log Integration**
2. La première étape à effectuer consiste à importer les applets de commande AzLog. Pour cela, exécutez le script **LoadAzlogModule.ps1** (remarquez le « .\ » dans la commande suivante). Saisissez **.\LoadAzlogModule.ps1** et appuyez sur **Entrée**.  
Vous devriez voir quelque chose comme ce qui apparaît dans la figure ci-dessous. </br></br>
![Écran d’installation avec la case Télémétrie cochée](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Vous devez maintenant configurer AzLog afin d’utiliser un environnement Azure spécifique. Un « environnement Azure » est le « type » de centre de données cloud Azure avec lequel vous souhaitez travailler. Bien qu’il existe plusieurs environnements à ce stade, les options pertinentes pour le moment sont **AzureCloud** ou **AzureUSGovernment**.   Dans votre environnement PowerShell avec élévation de privilèges, assurez-vous de vous trouver dans **c:\program files\Microsoft Azure Log Integration\** </br></br>
    Une fois que vous y êtes, exécutez la commande suivante : </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (pour Azure Commercial)

      >[!NOTE]
      Lorsque la commande aboutit, vous ne recevrez aucun retour.  Si vous souhaitez utiliser le cloud Azure du gouvernement américain, vous devez utiliser **AzureUSGovernment** (pour la variable -Nom) pour le cloud de gouvernement des États-Unis. Les autres clouds Azure ne sont pas pris en charge pour l’instant.  
4. Pour pouvoir surveiller un système, vous aurez besoin du nom du compte de stockage utilisé pour les diagnostics Azure.  Dans le portail Azure, accédez à **Machines virtuelles** et recherchez la machine virtuelle à surveiller. Dans la section **Propriétés**, choisissez **Paramètres de diagnostic**.  Cliquez sur **Agent** et prenez note du nom de compte de stockage indiqué. Vous aurez besoin de ce nom de compte pour une étape ultérieure.
![Paramètres des diagnostics Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Paramètres des diagnostics Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      Si la surveillance n’a pas été activée lors de la création de la machine virtuelle, vous aurez la possibilité de l’activer comme indiqué ci-dessus.
5. Revenons à présent sur la machine exécutant l’intégration des journaux Azure. Nous devons vérifier que vous êtes connecté au compte de stockage à partir du système dans lequel vous avez installé l’intégration des journaux d’Azure. L’ordinateur physique ou la machine virtuelle qui exécute le service d’intégration des journaux Azure a besoin d’accéder au compte de stockage pour récupérer les informations journalisées par les diagnostics Azure, tel que configuré sur chacun des systèmes surveillés.  
  1. Vous pouvez télécharger l’Explorateur de stockage Azure [ici](http://storageexplorer.com/).
  2. Exécuter la routine d’installation
  3. Une fois l’installation terminée, cliquez sur **Suivant** et laissez la case **Lancer l’Explorateur de stockage Microsoft Azure** cochée.  
  4. Connectez-vous à Azure.
  5. Vérifiez que vous pouvez voir le compte de stockage que vous avez configuré pour les diagnostics Azure.  
![Comptes de stockage](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. Notez qu’il existe quelques options sous Comptes de stockage. L’une d’entre elles est **Tables**. Sous **Tables**, vous devriez voir une table nommée **WADWindowsEventLogsTable**. </br></br>
   ![Comptes de stockage](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Intégrer la journalisation des diagnostics Azure
Dans cette étape, vous allez configurer la machine qui exécute le service d’intégration des journaux Azure pour qu’elle se connecte au compte de stockage qui contient les fichiers journaux.
Pour effectuer cette étape, nous aurons besoin de quelques éléments.  
* **FriendlyNameForSource :** il s’agit d’un nom convivial que vous pouvez appliquer au compte de stockage que vous avez configuré sur l’ordinateur virtuel pour stocker les informations de Diagnostics Azure
* **StorageAccountName :** il s’agit du nom du compte de stockage que vous avez spécifié lorsque vous avez configuré les diagnostics Azure.  
* **StorageKey :** il s’agit de la clé de stockage pour le compte de stockage où les informations de Diagnostics Azure sont stockées pour cette machine virtuelle.  

Procédez comme suit pour obtenir la clé de stockage :
 1. Accédez au [portail Azure](http://portal.azure.com).
 2. Dans le volet de navigation de la console Azure, faites défiler vers le bas et cliquez sur **Plus de services**.

 ![Plus de services](./media/security-azure-log-integration-get-started/more-services.png)
 3. Entrez **Stockage** dans la zone de texte **Filtre**. Cliquez sur **Comptes de stockage** (option qui apparaît une fois que vous entrez **Stockage**)

   ![zone filtre](./media/security-azure-log-integration-get-started/filter.png)
 4. Une fois que la liste des comptes de stockage s’affiche, double-cliquez sur le compte que vous avez affecté au stockage des journaux.

   ![liste des comptes de stockage](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Cliquez sur **Clés d’accès** dans la section **Paramètres**.

  ![clés d'accès](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Copiez-collez **key1** dans un emplacement sécurisé auquel vous pouvez accéder à l’étape suivante.

   ![deux clés d’accès](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Sur le serveur sur lequel vous avez installé l’intégration des journaux Azure, ouvrez une invite de commandes avec élévation de privilèges (veuillez noter que nous utilisons ici une fenêtre d’invite de commandes avec élévation de privilèges et non une console PowerShell avec élévation de privilèges).
 8. Accédez à **c:\Program Files\Microsoft Azure Log Integration**
 9. Exécutez ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> ``. </br> Par exemple ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` Si vous souhaitez que l’ID d’abonnement s’affiche dans le XML de l’événement, ajoutez l’ID d’abonnement au nom convivial : ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` ou par exemple, ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
Patientez jusqu'à 60 minutes, puis affichez les événements qui sont extraits du compte de stockage. Pour afficher, ouvrez **Observateur d’événements > Journaux Windows > Événements transférés** dans l’intégrateur Azlog.

Ici, vous pouvez visionner une vidéo répertoriant les étapes abordées ci-dessus.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Que se passe-t-il si les données ne s’affichent pas dans le dossier Événements transférés ?
Si les données ne s’affichent pas dans le dossier **Événements transférés** après une heure, procédez comme suit :

1. Examinez la machine exécutant le service d’intégration des journaux Azure et vérifiez qu’elle peut accéder à Azure. Pour tester la connectivité, essayez d’ouvrir le [portail Azure](http://portal.azure.com) à partir du navigateur.
2. Assurez-vous que le compte d’utilisateur **Azlog** dispose d’un accès en écriture au dossier **utilisateurs\Azlog**.
  <ol type="a">
   <li>Ouvrez l’**Explorateur Windows** </li>
  <li> Accédez à **c:\users** </li>
  <li> Faites un clic droit sur **c:\users\Azlog** </li>
  <li> Cliquez sur **Sécurité**  </li>
  <li> Cliquez sur **Service NT\Azlog** et vérifiez les autorisations pour le compte. Si le compte n’apparaît pas dans cet onglet ou si les autorisations appropriées ne sont pas affichées pour l’instant, vous pouvez accorder les droits de compte dans cet onglet.</li>
  </ol>
3. Assurez-vous que le compte de stockage ajouté à la commande **Azlog source add** est répertorié lorsque vous exécutez la commande **Azlog source list**.
4. Accédez à **Observateur d’événements > Journaux Windows > Application** pour voir si des erreurs sont signalées par l’intégration des journaux Azure.


Si vous rencontrez des problèmes pendant l’installation et la configuration, ouvrez une [demande de support](../azure-supportability/how-to-create-azure-support-request.md) et sélectionnez le service **Intégration des journaux** pour demander un support.

Une autre option de support est le [Forum MSDN relatif à l’intégration des journaux Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Ce forum permet aux membres de la communauté de s’entraider, grâce à des questions, des réponses, des conseils et des astuces, afin de profiter au mieux de l’intégration des journaux Azure. En outre, l’équipe d’intégration des journaux Azure supervise ce forum et apporte son aide lorsque cela est possible.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’intégration des journaux Azure, consultez les documents suivants :

* [Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324) : Centre de téléchargement pour plus d’informations, la configuration système requise et les instructions d’installation sur l’intégration des journaux Azure.
* [Introduction à l’intégration de journaux Azure](security-azure-log-integration-overview.md) : ce document présente l’intégration des journaux Azure, ses principales fonctionnalités et son fonctionnement.
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : ce billet de blog vous montre comment configurer l’intégration des journaux Azure pour travailler avec des solutions de partenaires Splunk, HP ArcSight et IBM QRadar. Voici nos recommandations sur la configuration des composants SIEM. Pour plus d’informations, contactez d’abord votre fournisseur SIEM.
* [FAQ de l’intégration des journaux Azure](security-azure-log-integration-faq.md) : ce forum aux questions répond aux questions sur l’intégration des journaux Azure.
* [Intégration des alertes du Security Center avec les journaux Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) : ce document montre comment synchroniser les alertes du Security Center, ainsi que les événements de sécurité des machines virtuelles collectés par Azure Diagnostics et les journaux d’activité Azure dans leur solution SIEM ou Log Analytics.
* [Nouvelles fonctionnalités des diagnostics Azure et des journaux d’Audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : ce billet de blog présente les journaux d’Audit Azure et autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.
