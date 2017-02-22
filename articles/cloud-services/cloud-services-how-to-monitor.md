---
title: "Surveillance d’un service cloud | Microsoft Docs"
description: "Découvrez comment surveiller des services cloud dans le portail Azure Classic."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: ab97962175f4498200db428736a1cbd124fac285
ms.openlocfilehash: aab8f3233191c9b3f839e3358902f72499d93ee5

---
# <a name="how-to-monitor-cloud-services"></a>Surveillance des services cloud
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Vous pouvez surveiller les métriques de performance `key` de vos services cloud dans le portail Azure Classic. Vous pouvez régler un niveau de surveillance minimal ou détaillé pour chaque rôle de service, et vous pouvez personnaliser les affichages de la surveillance. Les données de la surveillance détaillée sont stockées dans un compte de stockage, auquel vous pouvez accéder en dehors du portail. 

Les affichages de surveillance du portail Azure Classic sont configurables à loisir. Vous pouvez choisir les mesures à surveiller dans la liste des mesures de la page **Surveiller**, et choisir les mesures à afficher dans les graphiques sur la page **Surveiller** et dans le tableau de bord. 

## <a name="concepts"></a>Concepts
Par défaut, une surveillance minimale est fournie pour le nouveau service cloud à l'aide de compteurs de performances récupérés sur le système d'exploitation hôte pour les instances de rôle (machines virtuelles). Les mesures minimales sont limitées au pourcentage processeur, aux données entrantes, aux données sortantes, au débit d'écriture sur le disque et au débit de lecture sur le disque. En configurant la surveillance détaillée, vous obtenez des mesures supplémentaires basées sur les données de performances dans les machines virtuelles (instances de rôle). Les mesures détaillées offrent une analyse plus fine des problèmes qui surviennent au cours du fonctionnement de l'application.

Par défaut, les données des compteurs de performances des instances de rôle sont échantillonnées et transférées à partir de l'instance de rôle toutes les 3 minutes. Lorsque vous activez la surveillance détaillée, les données brutes des compteurs de performances sont consolidées pour chaque instance de rôle et pour toutes les instances de rôle de chaque rôle toutes les 5 minutes, toutes les heures et toutes les 12 heures. Les données consolidées sont purgées tous les 10 jours.

Une fois la surveillance détaillée activée, les données de surveillance consolidées sont stockées dans des tables dans votre compte de stockage. Avant d'activer la surveillance détaillée d'un rôle, vous devez configurer une chaîne de connexion de diagnostic qui assure la liaison avec le compte de stockage. Vous pouvez utiliser différents comptes de stockage pour différents rôles.

L’activation de la surveillance détaillée augmente les coûts de stockage liés au stockage des données, au transfert des données et aux transactions de stockage. La surveillance minimale ne nécessite pas de compte de stockage. Les données des mesures exposées au niveau minimal ne sont pas stockées dans votre compte de stockage, même si vous sélectionnez le niveau de surveillance détaillé.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Procédure de configuration de la surveillance des services cloud
Les procédures suivantes permettent de configurer la surveillance minimale ou détaillée dans le portail Azure Classic. 

### <a name="before-you-begin"></a>Avant de commencer
* Créez un compte de stockage *classique* pour stocker les données de surveillance. Vous pouvez utiliser différents comptes de stockage pour différents rôles. Pour plus d’informations, consultez [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account).
* Activez le diagnostic Azure pour vos rôles de service cloud. Consultez [Configuration des diagnostics pour les services cloud](cloud-services-dotnet-diagnostics.md).

Assurez-vous que la chaîne de connexion de diagnostic est présente dans la configuration du rôle. Vous ne pouvez pas activer la surveillance détaillée avant d'activer Azure Diagnostics d’inclure une chaîne de connexion de diagnostic dans la configuration du rôle.   

> [!NOTE]
> Les projets ciblant le Kit de développement logiciel (SDK) Azure 2.5 n’incluent pas automatiquement la chaîne de connexion de diagnostic dans le modèle de projet. Pour ces projets, vous devez ajouter manuellement la chaîne de connexion de diagnostic à la configuration du rôle.
> 
> 

**Pour ajouter manuellement la chaîne de connexion de diagnostic à la configuration de rôle**

1. Ouvrez le projet Cloud Service dans Visual Studio.
2. Double-cliquez sur le **rôle** pour ouvrir le concepteur de rôle et sélectionnez l’onglet **Paramètres**
3. Recherchez un paramètre appelé **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Si ce paramètre n’est pas présent, cliquez sur le bouton **Ajouter un paramètre** pour l’ajouter à la configuration et modifier le type pour le nouveau paramètre sur **ConnectionString**
5. Définissez la valeur de chaîne de connexion en cliquant sur le bouton **...** . Ceci ouvrira une boîte de dialogue qui vous permet de sélectionner un compte de stockage.
   
    ![Paramètres Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Pour modifier le niveau de surveillance détaillée ou minimale
1. Dans le [portail Azure Classic](https://manage.windowsazure.com/), ouvrez la page **Configurer** du déploiement du service cloud.
2. Dans **Niveau**, cliquez sur **Détaillé** ou **Minimal**. 
3. Cliquez sur **Enregistrer**.

Une fois la surveillance détaillée activée, vous devez commencer à voir les données de surveillance dans le portail Azure Classic dans l’heure qui suit.

Les données des compteurs de performances brutes et les données de surveillance consolidées sont stockées dans le compte de stockage dans les tables définies par l'ID de déploiement pour les rôles. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Procédure de réception d’alertes pour les mesures des services cloud
Vous pouvez recevoir des alertes en fonction des mesures de surveillance de votre service cloud. Dans la page **Services de gestion** du portail Azure Classic, vous pouvez créer une règle pour déclencher une alerte lorsque la mesure de votre choix atteint une valeur définie. Vous pouvez également paramétrer l'envoi d'un courrier électronique lorsque l'alerte est déclenchée. Pour plus d’informations, consultez la page [Réception de notifications d’alerte et gestion des règles d’alerte dans Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Ajout de mesures au tableau des mesures
1. Dans le [portail Azure Classic](http://manage.windowsazure.com/), ouvrez la page **Surveiller** du service cloud.
   
    Par défaut, la table des mesures affiche un sous-ensemble des mesures disponibles. Cette illustration présente les mesures détaillées par défaut d'un service cloud, qui sont limitées au compteur de performances Memory\Available MBytes, avec les données consolidées au niveau du rôle. Utilisez **Ajouter des métriques** pour sélectionner les données consolidées et les données au niveau du rôle à surveiller dans le portail Azure Classic.
   
    ![Affichage détaillé](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. Pour ajouter des mesures à la table des mesures :
   
   1. Cliquez sur **Ajouter des métriques** pour ouvrir **Choisir des métriques**, comme illustré ci-dessous.
      
       La première mesure disponible est développée, afin de présenter les options disponibles. Pour chaque mesure, l'option du haut affiche les données de surveillance consolidées pour tous les rôles. De plus, vous pouvez choisir des rôles supplémentaires pour lesquels afficher des données.
      
       ![Ajouter des métriques](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Pour sélectionner les métriques à afficher
      
      * Cliquez sur la flèche vers le bas à côté de la mesure pour développer les options de surveillance.
      * Activez la case à cocher de chaque option de surveillance à afficher.
        
        Vous pouvez afficher jusqu'à 50 mesures dans la table des mesures.
        
        > [!TIP]
        > Dans le cas de la surveillance détaillée, la liste des mesures peut contenir des dizaines de mesures. Pour afficher une barre de défilement, passez la souris sur la partie droite de la boîte de dialogue. Pour filtrer la liste, cliquez sur l'icône de recherche, puis entrez du texte dans la zone de recherche comme illustré ci-dessous.
        > 
        > 
        
        ![Add metrics search](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Une fois que vous avez terminé de sélectionner les mesures, cliquez sur OK (coche).
   
    Les mesures sélectionnées sont ajoutées à la table des mesures, comme illustré ci-dessous.
   
    ![mesures de surveillance](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. Pour supprimer une mesure de la table, cliquez sur la mesure pour la sélectionner, puis sur **Delete Metric**. ( **Delete Metric** ne s'affiche que si une mesure est sélectionnée.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Pour ajouter des mesures personnalisées au tableau des mesures
Le niveau de surveillance **détaillée** fournit une liste des mesures par défaut que vous pouvez surveiller sur le portail. De plus, vous pouvez surveiller toute mesure personnalisée ou tout compteur de performance définis par votre application via le portail.

Les étapes suivantes supposent que vous avez activé le niveau de surveillance **détaillée** et que vous avez configuré votre application pour collecter et transférer des compteurs de performance personnalisés. 

Pour afficher les compteurs de performance personnalisés dans le portail, vous devez mettre à jour la configuration dans wad-control-container :

1. Ouvrez l'objet blob wad-control-container dans votre compte de stockage de diagnostics. Pour cela, vous pouvez utiliser Visual Studio ou tout autre explorateur de stockage.
   
    ![Explorateur de serveurs Visual Studio.](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Accédez au chemin d'accès de l'objet blob à l'aide du modèle **DeploymentId/RoleName/RoleInstance** pour trouver la configuration de votre instance de rôle. 
   
    ![Explorateur de stockage Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Téléchargez le fichier de configuration pour votre instance de rôle et mettez-le à jour pour inclure des compteurs de performance personnalisés. Par exemple, pour surveiller le *nombre d’octets d’écriture de disque/s* pour le *lecteur C*, ajoutez ce qui suit sous le nœud **PerformanceCounters\Subscriptions**.
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Enregistrez les modifications et téléchargez le fichier de configuration au même emplacement en remplaçant le fichier existant dans l'objet blob.
5. Passez au mode détaillé dans la configuration du portail Azure Classic. Si vous étiez déjà en mode détaillé, vous devez passer au niveau minimum puis de nouveau en mode détaillé.
6. Le compteur de performance personnalisé sera alors disponible dans la boîte de dialogue **Ajouter des mesures** . 

## <a name="how-to-customize-the-metrics-chart"></a>Procédure de personnalisation des graphiques de mesures
1. Dans la table des mesures, sélectionnez jusqu'à 6 mesures à afficher dans le graphique. Pour sélectionner une mesure, cliquez sur la case à cocher à sa gauche. Pour supprimer une mesure du graphique, désactivez sa case à cocher dans la table des mesures.
   
    Au fur et à mesure que vous sélectionnez des mesures dans la table des mesures, elles sont ajoutées au graphique. Sur un écran étroit, une liste déroulante **n more** contient les en-têtes des mesures qui ne peuvent pas être affichées à l'écran.
2. Pour passer de l'affichage des valeurs relatives (valeur finale uniquement pour chaque mesure) à l'affichage des valeurs absolues (Axe Y affiché), sélectionnez Relative ou Absolute en haut du graphique.
   
    ![Relative ou Absolute](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez 1 heure, 24 heures ou 7 jours en haut du graphique.
   
    ![Période d'affichage de la surveillance](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    Dans le graphique des mesures du tableau de bord, la méthode de traçage des mesures est différente. Un jeu standard de mesures est disponible et des mesures peuvent y être ajoutées ou en être supprimées en sélectionnant l'en-tête des mesures.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Pour personnaliser les graphiques de mesure dans le tableau de bord
1. Ouvrez le tableau de bord du service cloud.
2. Ajoutez ou supprimez des mesures du graphique :
   
   * Pour tracer une mesure, activez la case à cocher correspondante dans les en-têtes du graphique. Sur un écran étroit, cliquez sur la flèche vers le bas à côté de ***n*??metrics** pour créer le graphique d’une métrique que la zone d’en-tête du graphique ne peut pas afficher.
   * Pour supprimer une mesure du graphique, désactivez la case à cocher à côté de son en-tête.
   
3. Basculez entre les affichages **Relative** et **Absolute**.
4. Choisissez 1 heure, 24 heures ou 7 jours de données à afficher.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Accès aux données de la surveillance détaillée en dehors du portail Azure Classic
Les données de la surveillance détaillée sont stockées dans des tables dans les comptes de stockage que vous avez spécifiés pour chaque rôle. Pour chaque déploiement de service cloud, six tables sont créées pour le rôle. Deux tables sont créées toutes les (5 minutes, 1 heure et 12 heures). Une de ces tables stocke les consolidations au niveau du rôle, et l'autre table stocke les consolidations pour les instances de rôle. 

Les noms de table suivent ce format :

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

où :

* *deploymentID* est le GUID attribué au déploiement du service cloud
* *aggregation_interval* = 5 m, 1 h ou 12 h
* consolidations au niveau du rôle = R
* consolidations pour les instances de rôle = RI

Par exemple, les tables suivantes stockent les données de la surveillance détaillée consolidées toutes les heures :

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```



<!--HONumber=Dec16_HO2-->


