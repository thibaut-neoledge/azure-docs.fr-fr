<properties 
	pageTitle="Activation de la surveillance et des diagnostics" 
	description="Découvrez comment configurer des diagnostics pour vos ressources dans Azure." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="stepsic"/>

# Activation de la surveillance et des diagnostics

Dans le [portail Azure](http://portal.azure.com), vous pouvez configurer les données de surveillance et de diagnostic, riches et fréquentes, concernant vos ressources. Vous pouvez également utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn931932.aspx) ou le [Kit de développement (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour configurer les diagnostics par programmation.

Les données de diagnostic, de surveillance et de mesure d’Azure sont enregistrées dans le compte de stockage de votre choix. Ceci vous permet d'utiliser les outils avec lesquels vous souhaitez lire les données, de l'explorateur de stockage à Power BI,  en passant par les outils tiers.

## Lorsque vous créez une ressource

La plupart des services vous permettent d'activer les diagnostics lorsque vous les créez dans le [portail Azure](http://portal.azure.com).

1. Accédez à **Nouveau** et choisissez la ressource qui vous intéresse. 

2. Sélectionnez **Configuration facultative**. ![Panneau Diagnostics](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Sélectionnez **Diagnostics**, puis cliquez sur **Activé**. Vous devrez ensuite choisir le compte de stockage dans lequel vous souhaitez que les diagnostics soient enregistrés. Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.

4. Cliquez sur **OK** et créez la ressource.

## Modifiez les paramètres d'une ressource existante

Si vous avez déjà créé une ressource et que vous souhaitez modifier les paramètres de diagnostic (pour modifier le niveau de collecte de données, par exemple), vous pouvez le faire directement dans le portail Azure.

1. Accédez à la ressource et cliquez sur la commande **Paramètres**.

2. Sélectionnez **Diagnostics**.

3. Le panneau **Diagnostics** dispose, pour cette ressource, de tous les diagnostics possibles et d’une surveillance de collecte de données. Pour certaines ressources, vous pouvez également choisir une stratégie de **Rétention** des données, vous permettant de les nettoyer de votre compte de stockage. ![Diagnostics de stockage](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Une fois que vous avez choisi vos paramètres, cliquez sur la commande **Enregistrer**. La surveillance des données peut prendre un certain temps avant d’apparaître, si vous l'activez pour la première fois.

### Catégories de collecte de données pour les machines virtuelles
Pour les machines virtuelles, toutes les mesures et tous les journaux sont enregistrés à des intervalles d'une minute, de façon à vous fournir les informations les plus récentes possible concernant votre machine.

- **Mesures de base** : mesures sur l'état d'intégrité de votre machine virtuelle (par ex., processeur et mémoire) 
- **Mesures réseau et mesures Web** : mesures concernant vos connexions réseau et services Web
- **Mesures .NET** : mesures concernant les applications .NET et ASP.NET exécutées sur votre machine virtuelle
- **Mesures SQL** : si vous exécutez Microsoft SQL Service, ses mesures de performances
- **Journaux d'événements Windows de l'application** : événements Windows envoyés au canal Application
- **Journaux d'événements Windows du système** : événements Windows envoyés au canal Système Cela inclut également tous les événements de [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409). 
- **Journaux d'événements Windows de la sécurité** : événements Windows envoyés au canal Sécurité
- **Journaux d'infrastructure de diagnostics** : journalisation concernant l'infrastructure de collecte de diagnostics
- **Journaux IIS** : journaux concernant votre serveur IIS

Notez qu’à ce stade, certaines distributions de Linux ne sont pas prises en charge, et que l'agent invité doit être installé sur la machine virtuelle.

## Étapes suivantes

* [Réceptions de notifications d'alerte](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Mise à l’échelle automatique du nombre d’instances](insights-how-to-scale.md) pour vous assurer que votre service est mis à l’échelle en fonction de la demande.
* [Surveillance des performances d'une application](insights-perf-analytics.md) si vous voulez comprendre exactement comment votre code s'exécute dans le cloud.
* [Affichage des événements et journaux d'audit](insights-debugging-with-events.md) pour découvrir tout ce qui s'est produit dans votre service.
* [Suivi de l'intégrité du service](insights-service-health.md) pour déterminer à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances. 
 

<!---HONumber=July15_HO4-->