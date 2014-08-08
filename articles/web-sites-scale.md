<properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="How to scale" pageTitle="How to scale web sites - Microsoft Azure service management" metaKeywords="Azure scaling web sites" description="Learn how to scale web sites in Azure to use Free, Shared, Basic and Standard web hosting plans." metaCanonical="" services="web-sites" documentationCenter="" title="How to Scale Web Sites" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

Mise à l'échelle de sites Web
=============================

Pour améliorer la performance et le débit de vos sites Web sur Microsoft Azure, vous pouvez utiliser le portail de gestion Azure pour mettre à l'échelle le mode de votre plan d'hébergement en le passant de Gratuit à Partagé, De base ou Standard.

La mise à l'échelle dans Sites Web Azure implique deux actions connexes : d'une part, la modification du mode de votre plan d'hébergement Web vers un niveau de service plus élevé, d'autre part, la configuration de certains paramètres une fois que vous avez changé de mode. Cet article décrit ces deux étapes. Un niveau de service plus élevé, tel que le mode Standard, offre davantage de robustesse et de souplesse dans le choix d'utilisation des ressources sur Azure.

Vous pouvez facilement modifier les modes et les configurer sous l'onglet Scale du portail de gestion. Vous pouvez augmenter ou diminuer la taille des instances au besoin. L'application de ces modifications ne prend que quelques secondes et s'applique à tous les sites Web de votre plan d'hébergement Web. Vous ne devez pas modifier votre code ni redéployer vos applications.

Pour plus d'informations sur les plans d'hébergement Web, consultez la page [Plans d'hébergement Sites Web Azure](http://go.microsoft.com/fwlink/?LinkId=9845584). Pour plus d'informations sur les tarifs et les fonctionnalités des plans d'hébergement Web individuels, consultez la page [Tarification – Sites Web](http://www.windowsazure.com/en-us/pricing/details/web-sites/).

> [WACOM.NOTE] Avant de passer le plan d'hébergement d'un site Web du mode **Gratuit** au mode **De base** ou **Standard**, vous devez supprimer les limites de dépense mises en place pour l'abonnement de votre site Web. Pour voir ou modifier les options de votre abonnement Sites Web Microsoft Azure, consultez la page [Abonnements Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=235288).

Dans cet article :

-   [Mise à l'échelle vers le mode de plan Partagé ou De base](#scalingsharedorbasic)
-   [Mise à l'échelle vers le mode de plan Standard](#scalingstandard)
-   [Mise à l'échelle d'une base de données SQL Server connectée à votre site](#ScalingSQLServer)
-   [Fonctionnalités pour développeur](#devfeatures)
-   [Autres fonctionnalités](#OtherFeatures)

<a name="scalingsharedorbasic"></a>
## Mise à l'échelle vers le mode de plan Partagé ou De base

1.  Dans votre navigateur, ouvrez le [portail de gestion](https://manage.windowsazure.com/).

2.  Sous l'onglet **Web Sites**, sélectionnez votre site Web.

    ![Sélectionner un site Web](./media/web-sites-scale/01SelectWebSite.png)

3.  Cliquez sur l'onglet **Scale**.

    ![Onglet Scale](./media/web-sites-scale/02SelectScaleTab.png)

4.  Dans la section **Web Hosting Plan Mode**, choisissez **PARTAGÉ** ou **DE BASE**. L'exemple sur l'image illustre le choix De base.

    ![Choisir le plan d'hébergement Web](./media/web-sites-scale/03aChooseWHP.png)

    La section **Web Hosting Plan Sites** affiche une courte liste de sites du plan actuel. Le mode du plan d'hébergement Web que vous sélectionnez s'applique à tous les sites du plan actuel.

5.  Dans la section **Capacité**, choisissez **Instance Size**. Les options disponibles sont **Petite**, **Moyenne** ou **Grande**. L'option de la taille des instances n'est pas disponible en mode Partagé. Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

    ![Taille des instances en mode De base](./media/web-sites-scale/03bChooseBasicInstanceSize.png)

6.  Utilisez le curseur pour choisir le **nombre d'instances** souhaité.

    ![Nombre d'instances en mode De base](./media/web-sites-scale/04ChooseBasicInstanceCount.png)

7.  Dans la barre de commandes, choisissez **Enregistrer**.

    ![Bouton enregistrer](./media/web-sites-scale/05SaveButton.png)

    > [WACOM.NOTE] Vous pouvez configurer et enregistrer séparément les paramètres **Web Hosting Plan**, **Instance Size** et **Nombre d'instances** si vous le souhaitez.

8.  Un message de confirmation vous rappelle que le nouveau mode est également appliqué aux sites figurant dans le même plan d'hébergement Web que le site Web actuel. Choisissez **Oui** pour finaliser la modification.

    L'exemple illustre la modification du mode de plan **De base** :

    ![Modification du plan terminée](./media/web-sites-scale/06BasicComplete.png)
<a name="scalingstandard"></a>
## Mise à l'échelle vers le mode de plan Standard

> [WACOM.NOTE] Avant de passer un plan d'hébergement Web au mode Standard, vous devez supprimer les limites de dépense mises en place pour l'abonnement Sites Web Microsoft Azure. Dans le cas contraire, votre site risque de devenir inaccessible si vous atteignez la limite avant la fin de la période de facturation. Pour voir ou modifier les options de votre abonnement Sites Web Microsoft Azure, consultez la page [Abonnements Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=235288).

1.  Pour effectuer une mise à l'échelle vers le mode Standard, suivez les mêmes étapes initiales que vous avez suivies pour définir le mode Partagé ou De base, puis choisissez **Standard** pour **Web Hosting Plan Mode**.

    ![Choisir le plan Standard](./media/web-sites-scale/07ChooseStandard.png)

    Comme précédemment, la section **Web Hosting Plan Sites** affiche une courte liste des sites du plan actuel. Dans ce cas, le mode Standard du plan s'applique à tous les sites du plan actuel.

2.  Lorsque vous sélectionnez **Standard**, la section **Capacité** s'affiche, ainsi que les options **Instance Size** et **Nombre d'instances**, qui sont également disponibles en mode De base. Les options **Edit Scale Settings for Schedule** et **Scale by Metric** sont uniquement disponibles en mode Standard.

    ![Section Capacité en mode Standard](./media/web-sites-scale/08CapacitySectionStandard.png)

3.  Configurez **Instance Size**. Les options disponibles sont **Petite**, **Moyenne** ou **Grande**.

    ![Choisir la taille des instances](./media/web-sites-scale/09ChooseInstanceSize.png)

    Pour plus d'informations sur la taille des instances, consultez la page [Tailles de machines virtuelles et services de cloud computing pour Azure](http://go.microsoft.com/fwlink/?LinkId=309169).

4.  Si vous souhaitez mettre automatiquement à l'échelle les ressources en fonction des heures de jour par opposition aux heures de nuit, des jours de semaine par opposition aux jours de fin de semaine, et/ou des dates et des heures spécifiques, choisissez **Set up schedule times** sous l'option **Edit Scale Settings for Schedule**.

    ![Configurer des heures de planification](./media/web-sites-scale/10SetUpScheduleTimesButton.png)

5.  La boîte de dialogue **Set up schedule times** offre un nombre de choix de configuration utiles.

    ![SetUpScheduleTimesDialog](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

6.  Sous **Recurring Schedules**, sélectionnez **Differing scale between Day and Night** et/ou **Differing Scale between Weekday and Weekend** pour mettre à l'échelle les ressources en fonction de planifications distinctes selon les heures de jour et de nuit et/ou les jours de semaine et de fin de semaine.

    > [WACOM.NOTE] Pour les besoins de cette fonctionnalité, le week-end démarre le vendredi soir (20h00 par défaut) et se termine le lundi matin (8h00 par défaut). Le profil du week-end utilise le jour de début et de fin que vous définissez dans le paramètre **Time**.

7.  Sous **Time**, choisissez une heure de début et de fin pour la journée, par tranche de demi-heure, et un fuseau horaire. Par défaut, la journée commence à 8h00 et se termine à 20h00. Le passage de l'heure d'été à l'heure d'hiver est conforme au fuseau horaire sélectionné.

8.  Sous **Specific Dates**, vous pouvez créer une ou plusieurs plages horaires nommées pour lesquelles vous souhaitez mettre à l'échelle les ressources. Par exemple, vous souhaitez éventuellement fournir des ressources supplémentaires pour un événement commercial ou de lancement au cours duquel des pics importants de trafic Web peuvent se produire.

9.  Une fois vos choix effectués, cliquez sur **OK** pour fermer la boîte de dialogue **Schedule Times**.

10.   La boîte de dialogue **Edit Scale Settings for Schedule** affiche désormais des planifications ou des événements configurables, en fonction des modifications que vous avez effectuées. Sélectionnez l'une des planifications récurrentes ou dates spécifiques à configurer.
    
    ![SetUpScheduleTimesDialog](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

    Vous pouvez désormais utiliser les fonctions **Scale by Metric** et **Nombre d'instances** pour affiner la mise à l'échelle de chaque planification choisie. 

1.  Pour régler de manière dynamique le nombre d'instances que votre site Web utilise si sa charge est modifiée, activez l'option **Scale by Metric** en choisissant **CPU**.
	
	![Scale By Metric](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)
	
    Le graphique affiche le nombre d'instances utilisées la semaine dernière. Vous pouvez utiliser le graphique pour surveiller l'activité de mise à l'échelle.

1.  **Scale by Metric** modifie la fonction **Nombre d'instances** afin de pouvoir définir le nombre minimal et maximal de machines virtuelles à utiliser pour la mise à l'échelle automatique. Azure n'ira jamais au-dessus ou au-dessous de ces limites.

    ![Nombre d'instances](./media/web-sites-scale/14InstanceCount.png)

2.  **Scale by Metric** active également l'option **Target CPU** pour vous permettre de spécifier une plage cible pour l'utilisation de l'unité centrale. Cette plage représente une utilisation moyenne de l'unité centrale pour votre site Web. Azure ajoute ou supprime des instances Standard afin que votre site Web reste dans les limites de cette plage.

    ![UC cible](./media/web-sites-scale/15TargetCPU.png)

    **Remarque** : lorsque l'option **Scale by Metric** est activée, Microsoft Azure vérifie l'unité centrale de votre site Web toutes les cinq minutes et ajoute ponctuellement des instances, si nécessaire. Si l'utilisation de l'unité centrale est faible, Microsoft Azure supprime des instances toutes les deux heures afin de garantir la performance de votre site Web. En général, il convient de définir la valeur 1 comme nombre minimal d'instances. Toutefois, en cas de pics d'utilisation soudains sur votre site Web, assurez-vous de disposer d'un nombre minimal d'instances suffisant pour gérer la charge. Par exemple, il se peut que votre site manque de réactivité en cas de pic soudain de trafic, dans l'intervalle des cinq minutes précédant l'instant où Microsoft Azure vérifie l'utilisation de votre unité centrale. Si vous vous attendez à de grandes quantités de trafic, définissez un nombre d'instances minimal suffisamment élevé pour prendre en charge ces poussées d'activité.

3.  Une fois que vous avez apporté des modifications aux éléments de la liste **Edit Scale Settings for Schedule**, cliquez sur l'icône **Enregistrer** dans la barre de commandes au bas de la page pour enregistrer tous les paramètres de planification en même temps (vous n'avez pas à enregistrer chaque paramètre de manière individuelle).
<a name="ScalingSQLServer"></a>
## Mise à l'échelle d'une base de données SQL Server connectée à votre site 
Si une ou plusieurs bases de données SQL Server sont liées à votre site Web (indépendamment du mode du plan d'hébergement Web), elles sont répertoriées dans la section **Linked Resources** au bas de la page Scaling.

1.  Pour mettre à l'échelle une ou plusieurs bases de données, dans la section **Linked Resources**, cliquez sur le lien **Manage scale for this database** en regard du nom de la base de données.

    ![Base de données liée](./media/web-sites-scale/16LinkedResources.png)

2.  Le lien vous permet d'accéder à l'onglet SQL Server du portail de gestion Azure, où vous pouvez configurer les options **Edition** et **Maximum Size** de la base de données :

    ![Mettre à l'échelle une base de données SQL Server](./media/web-sites-scale/17ScaleDatabase.png)

    Concernant l'option **Edition**, choisissez **Web** ou **Business** selon la capacité de stockage souhaitée. L'édition **Web** offre un éventail de petites capacités, tandis que l'édition **Business** offre un éventail de capacités plus importantes.

    La valeur que vous choisissez pour **Max Size** spécifie une limite supérieure pour la base de données. Les charges de la base de données s'appuient sur la quantité de données que vous stockez réellement. Ainsi, la modification de la propriété **Max Size** n'a aucune incidence sur les charges de la base de données. Pour plus d'informations, consultez la page [Comptes et facturation dans la base de données SQL Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=234930).
<a name="devfeatures"></a>
## Fonctionnalités pour développeur 
Selon le mode du plan d'hébergement Web, les fonctionnalités orientées développeur sont les suivantes :

**Nombre de bits**

-   Les modes de plan De base et Standard prennent en charge des applications 32 bits et 64 bits.
-   Les modes de plan Gratuit et Partagé prennent uniquement en charge des applications 32 bits.

**Prise en charge du débogueur**

-   La prise en charge du débogueur est disponible pour les modes Gratuit, Partagé et De base du plan d'hébergement Web et permet une connexion simultanée par application.
-   La prise en charge du débogueur est disponible pour les modes Standard du plan d'hébergement Web et permet cinq connexions simultanées par application.
-   
<a name="OtherFeatures"></a>
## Autres fonctionnalités

**Surveillance de point de terminaison Web**

-   La surveillance de point de terminaison Web est disponible dans les modes De base et Standard du plan d'hébergement Web. Pour plus d'informations sur la surveillance des points de terminaison Web, consultez la page [Surveillance des sites Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/).

-   Pour des informations détaillées sur toutes les autres fonctionnalités des plans d'hébergement Web, notamment la tarification et les fonctionnalités présentant de l'intérêt pour tous les utilisateurs (notamment les développeurs), consultez la page [Tarification – Sites Web](http://www.windowsazure.com/en-us/pricing/details/web-sites/).

<a name="Next Steps"></a>
## Étapes suivantes
- Pour plus d'informations sur les meilleures pratiques liées à Sites Web Azure, notamment la création d'une architecture évolutive et résiliente, consultez la page [Meilleures pratiques : Sites Web Azure](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

-   Pour plus d'informations sur la tarification, le support et les contrats SLA, accédez aux liens suivants.

    [Détails de la tarification – Transferts de données](http://www.windowsazure.com/en-us/pricing/details/data-transfers/)

    [Plans de support Microsoft Azure](http://www.windowsazure.com/en-us/support/plans/)

    [Contrats de niveau de service](http://www.windowsazure.com/en-us/support/legal/sla/)

    [Tarification – Base de données SQL](http://www.windowsazure.com/en-us/pricing/details/sql-database/)

    [Tailles de machines virtuelles et services cloud pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=309169)

    [Tarification – Sites Web](http://www.windowsazure.com/en-us/pricing/details/web-sites/)

    [Tarification – Sites Web – Connexions SSL](http://www.windowsazure.com/en-us/pricing/details/web-sites/#ssl-connections)

-   Les liens suivants permettent d'accéder à des vidéos sur la mise à l'échelle de Sites Web Azure :

    [Quand mettre à l'échelle Sites Web Azure - avec Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-free-vs-standard-scaling/)

    [Mise à l'échelle automatique de Sites Web Azure, unité centrale ou planification - avec Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/auto-scaling-azure-web-sites/)

    [Mise à l'échelle de Sites Web Azure - avec Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://manage.windowsazure.com/

<!-- IMAGES -->
[SelectWebsite]: ./media/web-sites-scale/01SelectWebSite.png
[SelectScaleTab]: ./media/web-sites-scale/02SelectScaleTab.png

[ChooseWHP]: ./media/web-sites-scale/03aChooseWHP.png
[ChooseBasicInstanceSize]: ./media/web-sites-scale/03bChooseBasicInstanceSize.png
[ChooseBasicInstanceCount]: ./media/web-sites-scale/04ChooseBasicInstanceCount.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ChooseStandard]: ./media/web-sites-scale/07ChooseStandard.png
[CapacitySectionStandard]: ./media/web-sites-scale/08CapacitySectionStandard.png
[ChooseInstanceSize]: ./media/web-sites-scale/09ChooseInstanceSize.png
[SetUpScheduleTimesButton]: ./media/web-sites-scale/10SetUpScheduleTimesButton.png
[SetUpScheduleTimesDialog]: ./media/web-sites-scale/11SetUpScheduleTimesDialog.png
[EditScaleSettingsForSchedule]: ./media/web-sites-scale/12EditScaleSettingsForSchedule.png
[ScaleByMetric]: ./media/web-sites-scale/13ScaleByMetric.png
[InstanceCount]: ./media/web-sites-scale/14InstanceCount.png
[TargetCPU]: ./media/web-sites-scale/15TargetCPU.png
[LinkedResources]: ./media/web-sites-scale/16LinkedResources.png
[ScaleDatabase]: ./media/web-sites-scale/17ScaleDatabase.png
