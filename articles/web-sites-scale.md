<properties title="How to Scale Websites" pageTitle="How to Scale Websites" description="required" metaKeywords="scaling Azure websites" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Mise à l'échelle de sites web

Pour améliorer la performance et le débit de vos sites web sur Microsoft Azure, vous pouvez utiliser le portail de gestion Azure pour mettre à l'échelle le mode de votre plan d'hébergement en le passant de Gratuit à Partagé, De base ou Standard.

La mise à l'échelle dans Sites Web Azure implique deux actions connexes : d'une part, la modification du mode de votre plan d'hébergement web vers un niveau de service plus élevé, d'autre part, la configuration de certains paramètres une fois que vous avez changé de mode. Cet article décrit ces deux étapes. Un niveau de service plus élevé, tel que le mode Standard, offre davantage de robustesse et de souplesse dans le choix d'utilisation des ressources sur Azure.

Vous pouvez facilement modifier les modes et les configurer sous l'onglet Scale du portail de gestion. Vous pouvez augmenter ou diminuer la taille des instances au besoin. L'application de ces modifications ne prend que quelques secondes et s'applique à tous les sites web de votre plan d'hébergement web. Vous ne devez pas modifier votre code ni redéployer vos applications.

Pour plus d'informations sur les plans d'hébergement web, consultez [Présentation des plans d'hébergement web][Présentation des plans d'hébergement web] et [Vue d'ensemble approfondie des plans d'hébergement Sites Web Azure][Vue d'ensemble approfondie des plans d'hébergement Sites Web Azure]. Pour plus d'informations sur les tarifs et les fonctionnalités de chaque plan d'hébergement web, consultez la page [Tarification – Sites web][Tarification – Sites web].

> [WACOM.NOTE] Avant de passer le plan d'hébergement d'un site web du mode **Gratuit** au mode **De base** ou **Standard**, vous devez supprimer les limites de dépense mises en place pour l'abonnement de votre site web. Pour voir ou modifier les options de votre abonnement Sites Web Microsoft Azure, consultez la page [Abonnements Microsoft Azure][Abonnements Microsoft Azure].

Dans cet article :

-   [Mise à l'échelle vers le mode de plan Partagé ou De base][Mise à l'échelle vers le mode de plan Partagé ou De base]
-   [Mise à l'échelle vers le mode de plan Standard][Mise à l'échelle vers le mode de plan Standard]
-   [Mise à l'échelle d'une base de données SQL Server connectée à votre site][Mise à l'échelle d'une base de données SQL Server connectée à votre site]
-   [Fonctionnalités pour développeur][Fonctionnalités pour développeur]
-   [Autres fonctionnalités][Autres fonctionnalités]

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->

## Mise à l'échelle vers le mode de plan Partagé ou De base

<!-- ===================================== -->

1.  Dans votre navigateur, ouvrez le [portail de gestion][portail de gestion].

2.  Sous l'onglet **Sites Web**, sélectionnez votre site web.

    ![Sélection d'un site web][Sélection d'un site web]

3.  Cliquez sur l'onglet **Scale**.

    ![Onglet Scale][Onglet Scale]

4.  Dans la section **Web Hosting Plan Mode**, choisissez **PARTAGÉ** ou **DE BASE**. L'exemple sur l'image illustre le choix De base.

    ![Choisir le plan d'hébergement Web][Choisir le plan d'hébergement Web]

    La section **Sites de plan d'hébergement web** affiche une courte liste de sites du plan actuel. Le mode du plan d'hébergement Web que vous sélectionnez s'applique à tous les sites du plan actuel.

5.  Dans la section **Capacité**, choisissez **Instance Size**. Les options disponibles sont **Petite**, **Moyenne** ou **Grande**. L'option de la taille des instances n'est pas disponible en mode Partagé. Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure][Tailles de machines virtuelles et services de cloud computing pour Azure].

    ![Taille des instances en mode De base][Taille des instances en mode De base]

6.  Utilisez le curseur pour choisir le **nombre d'instances** souhaité.

    ![Nombre d'instances en mode De base][Nombre d'instances en mode De base]

7.  Dans la barre de commandes, choisissez **Enregistrer**.

    ![Bouton enregistrer][Bouton enregistrer]

    > [WACOM.NOTE] Vous pouvez configurer et enregistrer séparément les paramètres **Web Hosting Plan**, **Instance Size** et **Nombre d'instances** si vous le souhaitez.

8.  Un message de confirmation vous rappelle que le nouveau mode est également appliqué aux sites figurant dans le même plan d'hébergement web que le site web actuel. Choisissez **Oui** pour finaliser la modification.

    L'exemple illustre la modification du mode de plan **De base** :

    ![Modification du plan terminée][Modification du plan terminée]

<a name="scalingstandard"></a>
<!-- ================================= -->

## Mise à l'échelle vers le mode de plan Standard

<!-- ================================= -->

> [WACOM.NOTE] Avant de faire basculer un plan d'hébergement web au mode Standard, vous devez supprimer les limites de dépense mises en place pour l'abonnement Sites Web Microsoft Azure. Dans le cas contraire, votre site risque de devenir inaccessible si vous atteignez la limite avant la fin de la période de facturation. Pour voir ou modifier les options de votre abonnement Sites Web Microsoft Azure, consultez la page [Abonnements Microsoft Azure][Abonnements Microsoft Azure].

1.  Pour effectuer une mise à l'échelle vers le mode Standard, suivez les mêmes étapes initiales que vous avez suivies pour définir le mode Partagé ou De base, puis choisissez **Standard** pour **Web Hosting Plan Mode**.

    ![Choisir le plan Standard][Choisir le plan Standard]

    Comme précédemment, la section **Web Hosting Plan Sites** affiche une courte liste des sites du plan actuel. Dans ce cas, le mode Standard du plan s'applique à tous les sites du plan actuel.

2.  Lorsque vous sélectionnez **Standard**, la section **Capacité** s'affiche, ainsi que les options **Instance Size** et **Nombre d'instances**, qui sont également disponibles en mode De base. Les options **Edit Scale Settings for Schedule** et **Scale by Metric** sont uniquement disponibles en mode Standard.

    ![Section Capacité en mode Standard][Section Capacité en mode Standard]

3.  Configurez **Instance Size**. Les options disponibles sont **Petite**, **Moyenne** ou **Grande**.

    ![Choisir la taille des instances][Choisir la taille des instances]

    Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure][Tailles de machines virtuelles et services de cloud computing pour Azure].

4.  Si vous souhaitez mettre automatiquement à l'échelle les ressources en fonction des heures de jour par opposition aux heures de nuit, des jours de semaine par opposition aux jours de fin de semaine, et/ou des dates et des heures spécifiques, choisissez **Set up schedule times** sous l'option **Edit Scale Settings for Schedule**.

    ![Configurer des heures de planification][Configurer des heures de planification]

5.  La boîte de dialogue **Set up schedule times** offre un nombre de choix de configuration utiles.

    ![SetUpScheduleTimesDialog][SetUpScheduleTimesDialog]

6.  Sous **Recurring Schedules**, sélectionnez **Differing scale between Day and Night** et/ou **Differing Scale between Weekday and Weekend** pour mettre à l'échelle les ressources en fonction de planifications distinctes selon les heures de jour et de nuit et/ou les jours de semaine et de fin de semaine.

    > [WACOM.NOTE] Pour les besoins de cette fonctionnalité, le week-end démarre le vendredi soir (20h00 par défaut) et se termine le lundi matin (8h00 par défaut). Le profil du week-end utilise le jour de début et de fin que vous définissez dans le paramètre **Time**.

7.  Sous **Time**, choisissez une heure de début et de fin pour la journée, par tranche de demi-heure, et un fuseau horaire. Par défaut, la journée commence à 8h00 et se termine à 20h00. Le passage de l'heure d'été à l'heure d'hiver est conforme au fuseau horaire sélectionné.

8.  Sous **Specific Dates**, vous pouvez créer une ou plusieurs plages horaires nommées pour lesquelles vous souhaitez mettre à l'échelle les ressources. Par exemple, vous souhaitez éventuellement fournir des ressources supplémentaires pour un événement commercial ou de lancement au cours duquel des pics importants de trafic Web peuvent se produire.

9.  Une fois vos choix effectués, cliquez sur **OK** pour fermer la boîte de dialogue **Schedule Times**.

10. La boîte de dialogue **Edit Scale Settings for Schedule** affiche désormais des planifications ou des événements configurables, en fonction des modifications que vous avez effectuées. Sélectionnez l'une des planifications récurrentes ou dates spécifiques à configurer.

    ![Modifier les paramètres de mise à l'échelle pour la planification][Modifier les paramètres de mise à l'échelle pour la planification]

    Vous pouvez désormais utiliser les fonctions **Mettre à l'échelle par métrique** et **Nombre d'instances** pour affiner la mise à l'échelle de chaque planification choisie.

11. Pour régler de manière dynamique le nombre d'instances que votre site web utilise si sa charge est modifiée, activez l'option **Mettre à l'échelle par métrique** en choisissant **Unité centrale**.

    ![Mettre à l'échelle par métrique][Mettre à l'échelle par métrique]

    Le graphique affiche le nombre d'instances utilisées la semaine dernière. Vous pouvez utiliser le graphique pour surveiller l'activité de mise à l'échelle.

12. **Scale by Metric** modifie la fonction **Nombre d'instances** afin de pouvoir définir le nombre minimal et maximal de machines virtuelles à utiliser pour la mise à l'échelle automatique. Azure n'ira jamais au-dessus ou au-dessous de ces limites.

    ![Nombre d'instances][Nombre d'instances]

13. **Scale by Metric** active également l'option **Target CPU** pour vous permettre de spécifier une plage cible pour l'utilisation de l'unité centrale. Cette plage représente une utilisation moyenne de l'unité centrale pour votre site web. Azure ajoute ou supprime des instances Standard pour que votre site web reste dans les limites de cette plage.

    ![UC cible][UC cible]

    **Remarque** : quand l'option **Mettre à l'échelle par métrique** est activée, Microsoft Azure vérifie l'unité centrale de votre site web toutes les cinq minutes et ajoute ponctuellement des instances, si nécessaire. Si l'utilisation de l'unité centrale est faible, Microsoft Azure supprime des instances toutes les deux heures afin de garantir la performance de votre site Web. En général, il convient de définir la valeur 1 comme nombre minimal d'instances. Toutefois, en cas de pics d'utilisation soudains sur votre site web, assurez-vous de disposer d'un nombre minimal d'instances suffisant pour gérer la charge. Par exemple, il se peut que votre site manque de réactivité en cas de pic soudain de trafic, dans l'intervalle des cinq minutes précédant l'instant où Microsoft Azure vérifie l'utilisation de votre unité centrale. Si vous vous attendez à de grandes quantités de trafic, définissez un nombre d'instances minimal suffisamment élevé pour prendre en charge ces poussées d'activité.

14. Une fois que vous avez apporté des modifications aux éléments de la liste **Edit Scale Settings for Schedule**, cliquez sur l'icône **Enregistrer** dans la barre de commandes au bas de la page pour enregistrer tous les paramètres de planification en même temps (vous n'avez pas à enregistrer chaque paramètre de manière individuelle).

> [WACOM.NOTE] Dans le [Portail Azure Preview][Portail Azure Preview], vous pouvez mettre à l'échelle non seulement en fonction du pourcentage d'utilisation de l'UC, mais également en fonction de métriques supplémentaires telles que le pourcentage de mémoire, la longueur de file d'attente de disque, la longueur de file d'attente HTTP, les données entrantes et les données sortantes. Vous pouvez aussi créer une ou plusieurs règles de mise à l'échelle descendante ou montante qui vous permettent de contrôler encore mieux la mise à l'échelle. Pour plus d'informations, consultez la page [Mise à l'échelle d’un site web][Mise à l'échelle d’un site web] dans la documentation du portail Azure Preview.

<a name="ScalingSQLServer"></a>

## Mise à l'échelle d'une base de données SQL Server connectée à votre site

Si une ou plusieurs bases de données SQL Server sont liées à votre site web (indépendamment du mode du plan d'hébergement web), elles sont répertoriées dans la section **Ressources liées** au bas de la page Mise à l'échelle.

1.  Pour mettre à l'échelle une ou plusieurs bases de données, dans la section **Linked Resources**, cliquez sur le lien **Manage scale for this database** en regard du nom de la base de données.

    ![Base de données liée][Base de données liée]

2.  Le lien vous permet d'accéder à l'onglet SQL Server du portail de gestion Azure, où vous pouvez configurer les options **Edition** et **Maximum Size** de la base de données :

    ![Mettre à l'échelle une base de données SQL Server][Mettre à l'échelle une base de données SQL Server]

    Pour **Édition**, choisissez **Web** ou **Business** selon la capacité de stockage souhaitée. L'édition **Web** offre un éventail de petites capacités, tandis que l'édition **Business** offre un éventail de capacités plus importantes.

    La valeur que vous choisissez pour **Max Size** spécifie une limite supérieure pour la base de données. Les charges de la base de données s'appuient sur la quantité de données que vous stockez réellement. Ainsi, la modification de la propriété **Max Size** n'a aucune incidence sur les charges de la base de données. Pour plus d'informations, consultez la page [Comptes et facturation dans la base de données SQL Microsoft Azure][Comptes et facturation dans la base de données SQL Microsoft Azure].

<a name="devfeatures"></a>

## Fonctionnalités pour développeur

Selon le mode du plan d'hébergement web, les fonctionnalités orientées développeur sont les suivantes :

**Nombre de bits**

-   Les modes de plan De base et Standard prennent en charge des applications 32 bits et 64 bits.
-   Les modes de plan Gratuit et Partagé prennent uniquement en charge des applications 32 bits.

**Prise en charge du débogueur**

-   La prise en charge du débogueur est disponible pour les modes Gratuit, Partagé et De base du plan d'hébergement Web et permet une connexion simultanée par application.
-   La prise en charge du débogueur est disponible pour les modes Standard du plan d'hébergement Web et permet cinq connexions simultanées par application.

<a name="OtherFeatures"></a>

## Autres fonctionnalités

**Surveillance de point de terminaison Web**

-   La surveillance de point de terminaison Web est disponible dans les modes De base et Standard du plan d'hébergement Web. Pour plus d'informations sur la surveillance des points de terminaison web, consultez la page [Surveillance des sites web][Surveillance des sites web].

-   Pour obtenir des informations détaillées sur toutes les autres fonctionnalités des plans d'hébergement web, notamment la tarification et les fonctionnalités présentant de l'intérêt pour tous les utilisateurs (notamment les développeurs), consultez la page [Tarification – Sites web][Tarification – Sites web].

<a name="Next Steps"></a>

## Étapes suivantes

-   Pour la prise en main d'Azure, consultez la page [Version d'évaluation gratuite de Microsoft Azure][Version d'évaluation gratuite de Microsoft Azure].

-   Pour plus d'informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.

    [Détails de la tarification – Transferts de données][Détails de la tarification – Transferts de données]

    [Plans de support Microsoft Azure][Plans de support Microsoft Azure]

    [Contrats de niveau de service][Contrats de niveau de service]

    [Tarification – Base de données SQL][Tarification – Base de données SQL]

    [Tailles de machines virtuelles et services cloud pour Microsoft Azure][Tailles de machines virtuelles et services de cloud computing pour Azure]

    [Tarification – Sites web][Tarification – Sites web]

    [Tarification – Sites web – Connexions SSL][Tarification – Sites web – Connexions SSL]

-   Pour plus d'informations sur les meilleures pratiques liées à Sites Web Azure, notamment la création d'une architecture extensible et résiliente, consultez la page [Meilleures pratiques : Sites Web Azure][Meilleures pratiques : Sites Web Azure].

-   Vidéos sur la mise à l'échelle de Sites Web Azure :

    [Quand mettre à l'échelle Sites Web Azure - avec Stefan Schackow][Quand mettre à l'échelle Sites Web Azure - avec Stefan Schackow]

    [Mise à l'échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow][Mise à l'échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow]

    [Mise à l'échelle de Sites Web Azure - avec Stefan Schackow][Mise à l'échelle de Sites Web Azure - avec Stefan Schackow]


  [Présentation des plans d'hébergement web]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-web-hosting-plan-overview/
  [Vue d'ensemble approfondie des plans d'hébergement Sites Web Azure]: http://www.azure.microsoft.com/fr-fr/Documentation/Articles/azure-web-sites-web-hosting-plans-in-depth-overview/
  [Tarification – Sites web]: http://www.windowsazure.com/fr-fr/pricing/details/web-sites/
  [Abonnements Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkID=235288
  [Mise à l'échelle vers le mode de plan Partagé ou De base]: #scalingsharedorbasic
  [Mise à l'échelle vers le mode de plan Standard]: #scalingstandard
  [Mise à l'échelle d'une base de données SQL Server connectée à votre site]: #ScalingSQLServer
  [Fonctionnalités pour développeur]: #devfeatures
  [Autres fonctionnalités]: #OtherFeatures
  [portail de gestion]: https://manage.windowsazure.com/
  [Sélection d'un site web]: ./media/web-sites-scale/01SelectWebSite.png
  [Onglet Scale]: ./media/web-sites-scale/02SelectScaleTab.png
  [Choisir le plan d'hébergement Web]: ./media/web-sites-scale/03aChooseWHP.png
  [Tailles de machines virtuelles et services de cloud computing pour Azure]: http://go.microsoft.com/fwlink/?LinkId=309169
  [Taille des instances en mode De base]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
  [Nombre d'instances en mode De base]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
  [Bouton enregistrer]: ./media/web-sites-scale/05SaveButton.png
  [Modification du plan terminée]: ./media/web-sites-scale/06BasicComplete.png
  [Choisir le plan Standard]: ./media/web-sites-scale/07ChooseStandard.png
  [Section Capacité en mode Standard]: ./media/web-sites-scale/08CapacitySectionStandard.png
  [Choisir la taille des instances]: ./media/web-sites-scale/09ChooseInstanceSize.png
  [Configurer des heures de planification]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
  [SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
  [Modifier les paramètres de mise à l'échelle pour la planification]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
  [Mettre à l'échelle par métrique]: ./media/web-sites-scale/13ScaleByMetric.png
  [Nombre d'instances]: ./media/web-sites-scale/14InstanceCount.png
  [UC cible]: ./media/web-sites-scale/15TargetCPU.png
  [Portail Azure Preview]: https://portal.azure.com/
  [Mise à l'échelle d’un site web]: http://azure.microsoft.com/fr-fr/documentation/articles/insights-how-to-scale/
  [Base de données liée]: ./media/web-sites-scale/16LinkedResources.png
  [Mettre à l'échelle une base de données SQL Server]: ./media/web-sites-scale/17ScaleDatabase.png
  [Comptes et facturation dans la base de données SQL Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=234930
  [Surveillance des sites web]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-monitor/
  [Version d'évaluation gratuite de Microsoft Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
  [Détails de la tarification – Transferts de données]: http://www.windowsazure.com/fr-fr/pricing/details/data-transfers/
  [Plans de support Microsoft Azure]: http://www.windowsazure.com/fr-fr/support/plans/
  [Contrats de niveau de service]: http://www.windowsazure.com/fr-fr/support/legal/sla/
  [Tarification – Base de données SQL]: http://www.windowsazure.com/fr-fr/pricing/details/sql-database/
  [Tarification – Sites web – Connexions SSL]: http://www.windowsazure.com/fr-fr/pricing/details/web-sites/#ssl-connections
  [Meilleures pratiques : Sites Web Azure]: http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx
  [Quand mettre à l'échelle Sites Web Azure - avec Stefan Schackow]: http://www.windowsazure.com/fr-fr/documentation/videos/azure-web-sites-free-vs-standard-scaling/
  [Mise à l'échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow]: http://www.windowsazure.com/fr-fr/documentation/videos/auto-scaling-azure-web-sites/
  [Mise à l'échelle de Sites Web Azure - avec Stefan Schackow]: http://www.windowsazure.com/fr-fr/documentation/videos/how-azure-web-sites-scale/
