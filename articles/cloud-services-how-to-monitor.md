<properties linkid="manage-services-how-to-monitor-a-cloud-service" urlDisplayName="How to monitor" pageTitle="How to monitor a cloud service - Azure" metaKeywords="Azure monitoring cloud services, Azure Management Portal cloud services" description="Learn how to monitor cloud services by using the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Monitor Cloud Services" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Surveillance des services cloud

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Vous pouvez surveiller les indicateurs de performances clés de vos services cloud dans le portail de gestion Azure. Vous pouvez régler un niveau de surveillance minimal ou détaillé pour chaque rôle de service, et vous pouvez personnaliser les affichages de la surveillance. Les données de la surveillance détaillée sont stockées dans un compte de stockage, auquel vous pouvez accéder en dehors du portail.

Les affichages de surveillance dans le portail de gestion sont configurables à loisir. Vous pouvez choisir les mesures à surveiller dans la liste des mesures de la page **Monitor**, et choisir les mesures à afficher dans les graphiques sur la page **Monitor** et dans le tableau de bord.

## Sommaire

-   [Concepts][Concepts]
-   [Configuration de la surveillance pour les services cloud][Configuration de la surveillance pour les services cloud]
-   [Réception d'alertes pour les mesures de services cloud][Réception d'alertes pour les mesures de services cloud]
-   [Ajout de mesures à la table des mesures][Ajout de mesures à la table des mesures]
-   [Personnalisation des graphiques de mesure][Personnalisation des graphiques de mesure]
-   [Accès aux données de la surveillance détaillée en dehors du portail de gestion][Accès aux données de la surveillance détaillée en dehors du portail de gestion]

## <span id="concepts"></span></a>Concepts

Par défaut, une surveillance minimale est fournie pour le nouveau service cloud à l'aide de compteurs de performances récupérés sur le système d'exploitation hôte pour les instances de rôle (machines virtuelles). Les mesures minimales sont limitées au pourcentage processeur, aux données entrantes, aux données sortantes, au débit d'écriture sur le disque et au débit de lecture sur le disque. En configurant la surveillance détaillée, vous obtenez des mesures supplémentaires basées sur les données de performances dans les machines virtuelles (instances de rôle). Les mesures détaillées offrent une analyse plus fine des problèmes qui surviennent au cours du fonctionnement de l'application.

> [WACOM.NOTE]
> Si vous utilisez la surveillance détaillée, vous pouvez ajouter d'autres compteurs de performances au démarrage de l'instance de rôle, via un fichier de configuration du diagnostic, ou bien à distance à l'aide de l'API Azure Diagnostics. Pour pouvoir surveiller ces mesures dans le portail de gestion, vous devez ajouter les compteurs de performances avant de configurer la surveillance détaillée. Pour plus d'informations, consultez les pages [Recueillir des données de journaux à l'aide des diagnostics Windows Azure][Recueillir des données de journaux à l'aide des diagnostics Windows Azure] et [Créer et utiliser des compteurs de performances dans une application Windows Azure][Créer et utiliser des compteurs de performances dans une application Windows Azure].

Par défaut, les données des compteurs de performances des instances de rôle sont échantillonnées et transférées à partir de l'instance de rôle toutes les 3 minutes. Lorsque vous activez la surveillance détaillée, les données brutes des compteurs de performances sont consolidées pour chaque instance de rôle et pour toutes les instances de rôle de chaque rôle toutes les 5 minutes, toutes les heures et toutes les 12 heures. Les données consolidées sont purgées tous les 10 jours.

Une fois la surveillance détaillée activée, les données de surveillance consolidées sont stockées dans des tables dans votre compte de stockage. Avant d'activer la surveillance détaillée d'un rôle, vous devez configurer une chaîne de connexion de diagnostic qui assure la liaison avec le compte de stockage. Vous pouvez utiliser différents comptes de stockage pour différents rôles.

Notez que l'activation de la surveillance détaillée va augmenter les coûts de stockage liés au stockage des données, au transfert des données et aux transactions de stockage. La surveillance minimale ne nécessite pas de compte de stockage. Les données des mesures exposées au niveau minimal ne sont pas stockées dans votre compte de stockage, même si vous sélectionnez le niveau de surveillance détaillé.

## <span id="verbose"></span></a> Configuration de la surveillance pour les services cloud

Les procédures suivantes permettent de configurer la surveillance minimale ou détaillée dans le portail de gestion. Vous ne pouvez pas activer la surveillance détaillée tant que vous n'avez pas activé Azure Diagnostics et configuré les chaînes de connexion de diagnostic pour permettre à Azure Diagnostics d'accéder aux comptes de stockage où doivent être placées les données de surveillance détaillées.

### Avant de commencer

-   Créez un compte de stockage pour stocker les données de surveillance. Vous pouvez utiliser différents comptes de stockage pour différents rôles. Pour plus d'informations, consultez **Comptes de stockage** ou la page [Création d'un compte de stockage][Création d'un compte de stockage].

-   Activez le diagnostic Azure pour vos rôles de service cloud.
    Vous devez mettre à jour le fichier de définition de service cloud (.csdef) et le fichier de configuration de service cloud (.cscfg). Pour plus d'informations, consultez la page [Configuration des diagnostics Azure][Configuration des diagnostics Azure].

Dans le portail de gestion, vous pouvez ajouter ou modifier les chaînes de connexion de diagnostic utilisées par Azure Diagnostics pour accéder aux comptes de stockage qui contiennent les données de la surveillance détaillée, et vous pouvez choisir le niveau de surveillance minimal ou détaillé. Comme la surveillance détaillée stocke les données dans un compte de stockage, vous devez configurer les chaînes de connexion de diagnostic avant de sélectionner le niveau de surveillance détaillé.

### Configuration de chaînes de connexion de diagnostic pour la surveillance détaillée

1.  Copiez une clé d'accès au stockage correspondant au compte que vous allez utiliser pour stocker les données de la surveillance détaillée. Dans le [portail de gestion Azure][portail de gestion Azure], vous pouvez utiliser **Manage Keys** dans la page **Storage Accounts**. Pour plus d'informations, consultez la page [Gestion des services cloud][Gestion des services cloud] ou l'aide sur les **Comptes de stockage**.

2.  Ouvrez **Cloud Services**. Ensuite, pour ouvrir le tableau de bord, cliquez sur le nom du service cloud que vous voulez configurer.

3.  Cliquez sur **Production** ou sur **Staging** pour afficher le déploiement à configurer.

4.  Cliquez sur **Configurer**.

    Vous allez modifier les paramètres **monitoring** en haut de la page **Configure** présentée ci-dessous. Si vous n'avez pas activé Azure Diagnostics pour le service cloud, l'option **Level** n'est pas disponible. Vous ne pouvez pas modifier la stratégie de rétention des données. Les données de la surveillance détaillée d'un service cloud sont stockées pendant 10 jours.

    ![Options de surveillance][Options de surveillance]

5.  Dans **Diagnostics Connection Strings**, complétez la chaîne de connexion de diagnostic pour chaque rôle pour lequel vous souhaitez une surveillance détaillée.

    Les chaînes de connexion suivent ce format. (Cet exemple est valable pour un service cloud qui utilise les points de terminaison par défaut.) Pour mettre à jour une chaîne de connexion, entrez un nom de compte de stockage valide, ainsi que la clé d'accès du compte de stockage que vous voulez utiliser.

    DefaultEndpointsProtocol=https;AccountName=StorageAccountName;AccountKey=StorageAccountKey

6.  Cliquez sur **Enregistrer**.

Si vous activez la surveillance détaillée, effectuez la procédure suivante une fois que vous avez configuré les chaînes de connexion de diagnostic pour les rôles de service.

### Pour modifier le niveau de surveillance détaillée ou minimale

1.  Dans le [portail de gestion][portail de gestion Azure], ouvrez la page **Configure** du déploiement du service cloud.

2.  Dans **Level**, cliquez sur **Verbose** ou **Minimal**.

3.  Cliquez sur **Enregistrer**.

Une fois la surveillance détaillée activée, vous devriez commencer à voir les données de surveillance dans le portail de gestion dans l'heure qui suit.

Les données des compteurs de performances brutes et les données de surveillance consolidées sont stockées dans le compte de stockage dans les tables définies par l'ID de déploiement pour les rôles.

## <span id="receivealerts"></span></a> Réception d'alertes pour les mesures de services cloud

Vous pouvez recevoir des alertes en fonction des mesures de surveillance de votre service cloud. Dans la page **Management Services** du portail de gestion Azure, vous pouvez créer une règle pour déclencher une alerte lorsque la mesure de votre choix atteint une valeur définie. Vous pouvez également paramétrer l'envoi d'un courrier électronique lorsque l'alerte est déclenchée. Pour plus d'informations, consultez la page [Réception de notifications d'alerte et gestion des règles d'alerte dans Azure][Réception de notifications d'alerte et gestion des règles d'alerte dans Azure].

## <span id="addmetrics"></span></a> Ajout de mesures à la table des mesures

1.  Dans le [portail de gestion][portail de gestion], ouvrez la page **Monitor** du service cloud.

    Par défaut, la table des mesures affiche un sous-ensemble des mesures disponibles. Cette illustration présente les mesures détaillées par défaut d'un service cloud, qui sont limitées au compteur de performances Memory\\Available MBytes, avec les données consolidées au niveau du rôle. Utilisez **Add Metrics** pour sélectionner les données consolidées et les données au niveau du rôle à surveiller dans le portail de gestion.

    ![Affichage détaillé][Affichage détaillé]

2.  Pour ajouter des mesures à la table des mesures :

    a. Cliquez sur **Ajouter des métriques** pour ouvrir **Choisir des métriques** (illustré ci-dessous).
    La première mesure disponible est développée, afin de présenter les options disponibles. Pour chaque mesure, l'option du haut affiche les données de surveillance consolidées pour tous les rôles. De plus, vous pouvez choisir des rôles supplémentaires pour lesquels afficher des données.

    ![Add metrics][Add metrics]

    b. Pour sélectionner les mesures à afficher :

    -   Cliquez sur la flèche vers le bas à côté de la mesure pour développer les options de surveillance.
    -   Activez la case à cocher de chaque option de surveillance à afficher.

    Vous pouvez afficher jusqu'à 50 mesures dans la table des mesures.

    <div class="dev-callout"> 
<b>Conseil</b> 
<p>Dans le cas de la surveillance d&eacute;taill&eacute;e, la liste des mesures peut contenir des dizaines de mesures. Pour afficher une barre de d&eacute;filement, passez la souris sur la partie droite de la bo&icirc;te de dialogue. Pour filtrer la liste, cliquez sur l'ic&ocirc;ne de recherche, puis entrez du texte dans la zone de recherche comme illustr&eacute; ci-dessous.</p> 
</div>

    ![Add metrics search][Add metrics search]

3.  Une fois que vous avez terminé de sélectionner les mesures, cliquez sur OK (coche).

    Les mesures sélectionnées sont ajoutées à la table des mesures, comme illustré ci-dessous.

    ![mesures de surveillance][mesures de surveillance]

4.  Pour supprimer une mesure de la table, cliquez sur la mesure pour la sélectionner, puis sur **Delete Metric**. (**Delete Metric** ne s'affiche que si une mesure est sélectionnée.)

## <span id="customizechart"></span></a> Personnalisation des graphiques de mesure

1.  Dans la table des mesures, sélectionnez jusqu'à 6 mesures à afficher dans le graphique. Pour sélectionner une mesure, cliquez sur la case à cocher à sa gauche. Pour supprimer une mesure du graphique, désactivez sa case à cocher dans la table des mesures.

    Au fur et à mesure que vous sélectionnez des mesures dans la table des mesures, elles sont ajoutées au graphique. Sur un écran étroit, une liste déroulante **n more** contient les en-têtes des mesures qui ne peuvent pas être affichées à l'écran.

2.  Pour passer de l'affichage des valeurs relatives (valeur finale uniquement pour chaque mesure) à l'affichage des valeurs absolues (Axe Y affiché), sélectionnez Relative ou Absolute en haut du graphique.

    ![Relative ou Absolute][Relative ou Absolute]

3.  Pour modifier la plage horaire des mesures affichées dans le graphique, sélectionnez 1 heure, 24 heures ou 7 jours en haut du graphique.

    ![Période d'affichage de la surveillance][Période d'affichage de la surveillance]

    Dans le graphique des mesures du tableau de bord, la méthode de traçage des mesures est différente. Un jeu standard de mesures est disponible et des mesures peuvent y être ajoutées ou en être supprimées en sélectionnant l'en-tête des mesures.

### Pour personnaliser les graphiques de mesure dans le tableau de bord

1.  Ouvrez le tableau de bord du service cloud.

2.  Ajoutez ou supprimez des mesures du graphique :

    -   Pour tracer une mesure, activez la case à cocher correspondante dans les en-têtes du graphique. Sur un écran étroit, cliquez sur la flèche vers le bas à côté de ***n*??metrics** pour créer le graphique d'une mesure que la zone d'en-têtes du graphique ne peut pas afficher.

    -   Pour supprimer une mesure du graphique, désactivez la case à cocher à côté de son en-tête.

3.  Basculez entre les affichages **Relative** et **Absolute**.

4.  Choisissez 1 heure, 24 heures ou 7 jours de données à afficher.

## <span id="accessverbose"></span></a> Accès aux données de la surveillance détaillée en dehors du portail de gestion

Les données de la surveillance détaillée sont stockées dans des tables dans les comptes de stockage que vous avez spécifiés pour chaque rôle. Pour chaque déploiement de service cloud, six tables sont créées pour le rôle. Deux tables sont créées toutes les (5 minutes, 1 heure et 12 heures). Une de ces tables stocke les consolidations au niveau du rôle, et l'autre table stocke les consolidations pour les instances de rôle.

Les noms de table suivent ce format :

    WAD*deploymentID*PT*aggregation_interval*[R|RI]Table

où :

-   *deploymentID* est le GUID attribué au déploiement du service cloud

-   *aggregation\_interval* = 5 m, 1 h ou 12 h

-   consolidations au niveau du rôle = R

-   consolidations pour les instances de rôle = RI

Par exemple, les tables suivantes stockent les données de la surveillance détaillée consolidées toutes les heures :

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

    WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)

  [disclaimer]: ../includes/disclaimer.md
  [Concepts]: #concepts
  [Configuration de la surveillance pour les services cloud]: #verbose
  [Réception d'alertes pour les mesures de services cloud]: #receivealerts
  [Ajout de mesures à la table des mesures]: #addmetrics
  [Personnalisation des graphiques de mesure]: #customizechart
  [Accès aux données de la surveillance détaillée en dehors du portail de gestion]: #accessverbose
  [Recueillir des données de journaux à l'aide des diagnostics Windows Azure]: http://msdn.microsoft.com/fr-fr/library/gg433048.aspx
  [Créer et utiliser des compteurs de performances dans une application Windows Azure]: http://msdn.microsoft.com/fr-fr/library/hh411542.aspx
  [Création d'un compte de stockage]: /fr-fr/manage/services/storage/how-to-create-a-storage-account/
  [Configuration des diagnostics Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn186185.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Gestion des services cloud]: http://www.windowsazure.com/fr-fr/manage/services/cloud-services/how-to-manage-a-cloud-service/
  [Options de surveillance]: ./media/cloud-services-how-to-monitor/CloudServices_MonitoringOptions.png
  [Réception de notifications d'alerte et gestion des règles d'alerte dans Azure]: http://go.microsoft.com/fwlink/?LinkId=309356
  [portail de gestion]: http://manage.windowsazure.com/
  [Affichage détaillé]: ./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png
  [Add metrics]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png
  [Add metrics search]: ./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png
  [mesures de surveillance]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png
  [Relative ou Absolute]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png
  [Période d'affichage de la surveillance]: ./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png
