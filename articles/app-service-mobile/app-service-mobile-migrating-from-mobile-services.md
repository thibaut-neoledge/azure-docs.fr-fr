<properties
	pageTitle="Migration à partir de Mobile Services vers une application App Service Mobile App"
	description="Découvrez comment migrer facilement votre application Mobile Services vers une application App Service Mobile App."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="adrianhall"/>

# <a name="article-top"></a>Migration de votre service mobile Azure existant vers Azure App Service

Avec la [mise à la disposition générale d’Azure App Service], les sites Azure Mobile Services peuvent être facilement migrés sur place pour tirer parti de toutes les fonctionnalités d’Azure App Service. Ce document explique ce qui se passe lors de la migration de votre site à partir d’Azure Mobile Services vers Azure App Service.

## <a name="what-does-migration-do"></a>Effet de la migration sur votre site

La migration de votre service mobile Azure transforme votre service mobile en application [Azure App Service] sans affecter le code. Elle ne modifie en rien vos Notification Hubs, connexion de données SQL, paramètres d’authentification, travaux planifiés et nom de domaine. Les clients mobiles utilisant votre service mobile Azure peuvent continuer à opérer normalement. La migration redémarre votre service après le transfert de celui-ci vers Azure App Service.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Pourquoi migrer votre site

Microsoft vous recommande de migrer votre service mobile Azure pour tirer parti des fonctionnalités d’Azure App Service, notamment :

  *  nouvelles fonctionnalités d’hôte, dont [WebJobs] et [noms de domaine personnalisés] ;
  *  connectivité à vos ressources locales à l’aide de [VNet] en plus des [connexions hybrides] ;
  *  surveillance et dépannage à l’aide de New Relic ou d’[Application Insights] ;
  *  outils DevOps intégrés, dont [emplacements intermédiaires], restauration et tests en production ;
  *  [mise à l’échelle automatique], équilibrage de charge et [analyse des performances].

Pour plus d’informations sur les avantages d’Azure App Service, voir [Services mobiles et App Service].

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer tout travail majeure sur votre site, vous devez [sauvegarder les scripts] et la base de données SQL de service mobile.

Si vous voulez tester le processus de migration avant de migrer votre site de production, dupliquez votre service mobile Azure de production dans une nouvelle [région Azure] \(complet, avec une copie de la source de données), puis testez la migration par rapport à la nouvelle URL. Vous avez également besoin d’une implémentation de client test qui pointe vers le site de test pour tester correctement le site migré.

## <a name="migrating-site"></a>Migration de vos sites

Le processus de migration migre tous les sites au sein d’une seule région Azure.

Pour migrer votre site :

  1.  Connectez-vous au [portail Azure Classic].
  2.  Sélectionnez un service mobile dans la région que vous souhaitez migrer.
  3.  Cliquez sur le bouton **Migrer vers App Service**.

    ![Bouton Migrer][0]

  4.  Lisez le contenu de la boîte de dialogue Migrer vers App Service.
  5.  Entrez le nom de votre service mobile dans le champ approprié. Par exemple, si votre nom de domaine est contoso.azure-mobile.NET, entrez _contoso_ dans le champ approprié.
  6.  Cliquez sur le bouton de graduation.

Vous pouvez surveiller l’état de la migration dans le moniteur d’activité, et votre site est répertorié comme *en migration* dans le portail Azure Classic.

  ![Moniteur d’activité de migration][1]

Chaque migration peut prendre de 3 à 15 minutes par un service mobile. Votre site reste disponible pendant la migration, mais est redémarré à la fin du processus. Le site n’est pas disponible pendant le processus de redémarrage qui peut durer quelques secondes.

## <a name="finalizing-migration"></a>Finalisation de la migration

Vous devez planifier le test de votre site à partir d’un client mobile à l’issue du processus de migration. Assurez-vous que vous pouvez effectuer toutes les actions de client courantes sans modification du client mobile. Veillez également à ce que les modifications apportées pour effectuer la migration (telles que la modification du niveau tarifaire) soient réversibles si nécessaire.

### <a name="update-app-service-tier"></a>Sélectionner un niveau tarifaire App Service approprié

Après une migration vers Azure App Service, vous bénéficiez de plus de souplesse en matière de tarification .

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3.  Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4.  Dans le menu Paramètres, cliquez sur **Plan App Service**.
  5.  Cliquez sur la vignette **Niveau tarifaire**.
  6.  Cliquez sur la vignette adaptée à vos besoins, puis sur **Sélectionner**. Il se peut que vous deviez cliquer sur **Afficher tout** pour voir les niveaux tarifaires disponibles.

Pour commencer, nous recommandons ce qui suit :

| Niveau tarifaire du service mobile | Niveau tarifaire d’App Service |
| :-------------------------- | :----------------------- |
| Gratuit | F1 Gratuit |
| De base | B1 De base |
| Standard | S1 Standard |

Notez la flexibilité considérable du choix du niveau tarifaire approprié pour votre application. Pour plus d’informations sur la tarification de votre nouvel App Service, voir [Tarification d’App Service].

> [AZURE.TIP] Le niveau App Service standard englobe l’accès à de nombreuses fonctionnalités que vous pouvez utiliser, dont les [emplacements intermédiaires], les sauvegardes automatiques et la mise à l’échelle automatique. Découvrez les nouvelles fonctionnalités tant que vous êtes là.

### <a name="review-migration-scheduler-jobs"></a>Passer en revue les tâches migrées du planificateur

Les tâches du planificateur ne sont visibles pendant environ 30 minutes après une migration. Toutes les tâches planifiées continuent à s’exécuter en arrière-plan. Pour afficher vos tâches planifiées :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Parcourir>**, entrez **Planification** dans la zone _Filtre_, puis sélectionnez **Collections Scheduler**.

Il existe un nombre limité de tâches de planificateur gratuites disponibles après la migration. Vous devez examiner votre utilisation et les [Plans d’Azure Scheduler].

### <a name="configure-cors"></a>Configurer CORS si nécessaire

Le partage des ressources cross-origin est une technique permettant d’autoriser un site web à accéder à une API web sur un autre domaine. Si vous utilisiez Azure Mobile Services avec un site web associé, vous devez configurer CORS dans le cadre de la migration. Si vous accédez à Azure Mobile Services exclusivement à partir d’appareils mobiles, CORS ne nécessite de configuration que dans de rares cas.

Vos paramètres CORS migrés sont disponibles en tant que paramètre d’application **MS\_CrossDomainWhitelist**. Pour migrer votre site vers l’installation CORS d’Application Service :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3.  Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4.  Cliquez sur **CORS** dans le menu de l’API.
  5.  Entrez les origines autorisées dans le champ approprié, en appuyant chaque fois sur Entrée pour les valider.
  6.  Une fois votre liste d’origines autorisées complète, cliquez sur le bouton Enregistrer.

Cette tâche est facultative, mais contribue à vous offrir une meilleure expérience de gestion.

> [AZURE.TIP]  L’un des avantages de l’utilisation d’un Azure App Service est que vous pouvez exécuter votre site web et votre service mobile sur le même site. Pour plus d’informations, voir la section [Étapes suivantes](#next-steps).

### <a name="download-publish-profile"></a>Télécharger un nouveau profil de publication

Le profil de publication de votre site est modifié lors de la migration vers Azure App Service. Si vous avez l’intention de publier votre site depuis Visual Studio, vous avez besoin d’un nouveau profil de publication. Pour télécharger un nouveau profil de publication :

  1.  Connectez-vous au [portail Azure].
  2.  Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3.  Cliquez sur **Obtenir le profil de publication**.

Le fichier PublishSettings sera téléchargé sur votre ordinateur. Normalement, il est appelé _sitename_.PublishSettings. Vous pouvez ensuite importer les paramètres de publication dans votre projet existant :

  1.  Ouvrez Visual Studio et votre projet Service mobile Azure.
  2.  Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions**, puis sélectionnez **Publier**.
  3.  Cliquez sur **Importer**
  4.  Cliquez sur **Parcourir** et sélectionnez le fichier de paramètres de publication que vous avez téléchargé. Cliquez sur **OK**.
  5.  Cliquez sur **Valider la connexion** pour garantir le fonctionnement des paramètres de publication.
  6.  Cliquez sur **Publier** pour publier votre site.


## <a name="working-with-your-site"></a>Utilisation de votre site après migration

Commencez à utiliser votre nouvel App Service dans le [portail Azure] après migration. Voici quelques remarques concernant des opérations spécifiques que vous étiez habitué à effectuer dans le [portail Azure Classic], avec leurs équivalents App Service.

### <a name="publishing-your-site"></a>Téléchargement et publication de votre site migré

Votre site est disponible via git ou ftp, et peut être publié à nouveau à l’aide de différents mécanismes, dont WebDeploy, TFS, Mercurial, GitHub et FTP. Les informations d’identification de déploiement sont migrées avec le reste de votre site. Si vous n’avez pas défini vos informations d’identification de déploiement ou ne vous en souvenez pas, vous pouvez les réinitialiser :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4. Dans le menu PUBLICATION, cliquez sur **Informations d’identification du déploiement**.
  5. Entrez les nouvelles informations d’identification du déploiement dans les champs appropriés, puis cliquez sur le bouton Enregistrer.

Vous pouvez utiliser ces informations d’identification pour cloner le site avec git ou configurer des déploiements automatisés à partir de GitHub, TFS ou Mercurial. Pour plus d’informations, voir [Documentation sur le déploiement d’Azure App Service].

### <a name="appsettings"></a>Paramètres de l’application

La plupart des paramètres pour un service mobile migré sont disponibles via les Paramètres de l’application. Vous pouvez obtenir une liste des paramètres de l’application à partir du [portail Azure]. Pour afficher ou modifier les paramètres de votre application :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4. Cliquez sur **Paramètres de l’application** dans le menu GÉNÉRAL.
  5. Accédez à la section Paramètres de l’application, puis recherchez le paramètre de votre application.
  6. Cliquez sur la valeur du paramètre de l’application pour en modifier la valeur. Cliquez sur **Enregistrer** pour enregistrer la valeur.

Vous pouvez mettre à jour plusieurs paramètres d’application en même temps.

> [AZURE.TIP]  Vous pouvez remarquer que deux Paramètres de l’application ont la même valeur. Par exemple, vous pouvez voir _ApplicationKey_ et _MS\_ApplicationKey_. Vous devez uniquement modifier le paramètre de l’application commençant par **MS\_**. Toutefois, il est judicieux de mettre à jour les deux paramètres de l’application en même temps.

### <a name="authentication"></a>Authentification

Tous les paramètres d’authentification sont disponibles en tant que Paramètres de l’application dans le site migré. Pour mettre à jour vos paramètres d’authentification, vous devez modifier les paramètres de l’application appropriés. Le tableau suivant présente les paramètres de l’application appropriés pour votre fournisseur d’authentification :

| Fournisseur | ID client | Clé secrète client | Autres paramètres |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Compte Microsoft | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

Remarque : **MS\_AadTenants** est stocké sous forme de liste de domaines de client séparés par des virgules (champ « Client autorisés » du portail Mobile Services).

> [AZURE.WARNING] **N’utilisez pas de mécanismes d’authentification dans le menu Paramètres**
>
> Azure App Service fournit un système d’authentification et d’autorisation « sans code » séparé dans le menu de paramètres _Authentification/Autorisation_, ainsi que l’option (déconseillée) _Authentification de mobile_. Ces options sont incompatibles avec un service mobile Azure migré. Vous pouvez [mettre à niveau votre site](app-service-mobile-net-upgrading-from-mobile-services.md) pour tirer parti de l’authentification d’Azure App Service.

### <a name="easytables"></a>Données

L’onglet _Données_ dans Mobile Services a été remplacé par _Easy Tables_ dans le portail Azure. Pour accéder à Easy Tables :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4. Cliquez sur **Easy Tables** dans le menu MOBILE.

Vous pouvez ajouter une nouvelle table en cliquant sur le bouton **Ajouter**, ou accéder à vos tables existantes en cliquant sur un nom de table. À partir de ce panneau, vous pouvez effectuer diverses opérations , notamment :

  * modifier les autorisations de table ;
  * modifier des scripts d’exploitation ;
  * gérer le schéma de table ;
  * supprimer la table ;
  * effacer le contenu de la table ;
  * supprimer des lignes spécifiques de la table.

### <a name="easyapis"></a>API

L’onglet _API_ dans Mobile Services a été remplacé par _Easy APIs_ dans le portail Azure. Pour accéder à Easy APIs :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4. Cliquez sur **Easy APIs** dans le menu MOBILE.

Vos API migrées figurent déjà dans le panneau. Vous pouvez également ajouter une nouvelle API à partir de ce panneau. Pour gérer une API spécifique, cliquez dessus. Dans le nouveau panneau, vous pouvez ajuster les autorisations et modifier les scripts de l’API.

### <a name="on-demand-jobs"></a>Tâches du planificateur

Tous les travaux du planificateur sont disponibles via la section Collections de travaux du planificateur. Pour accéder à vos tâches de planificateur :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir>**, entrez **Planification** dans la zone _Filtre_, puis sélectionnez **Collections Scheduler**.
  3. Sélectionnez la Collection de tâches pour votre site. Elle sera nommée _sitename_-Jobs.
  4. Cliquez sur **Paramètres**.
  5. Cliquez sur **Tâches du planificateur** sous MANAGE.

Les tâches planifiées seront affichées à la fréquence que vous avez spécifiée avant la migration. Les travaux à la demande seront désactivés. Pour exécuter une tâche sur demande :

  1. sélectionnez la tâche que vous souhaitez exécuter.
  2. Le cas échéant, cliquez sur **Activer** pour activer la tâche.
  3. Cliquez sur **Paramètres**, puis sur **Planification**.
  4. Sélectionnez **Une fois** comme Périodicité, puis cliquez sur **Enregistrer**

Vos tâches à la demande se trouvent dans `App_Data/config/scripts/scheduler post-migration`. Nous vous conseillons de convertir toutes les tâches à la demande en [WebJobs]. Vous devez écrire de nouvelles tâches du planificateur en tant que [Tâches web].

### <a name="notification-hubs"></a>Notification Hubs

Mobile Services utilise Notification Hubs pour les notifications push. Les paramètres de l’application utilisés pour lier le concentrateur de notification à votre service mobile après la migration sont les suivants :

| Paramètre de l’application | Description |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | Espace de nom du concentrateur de notification |
| **MS\_NotificationHubName** | Nom du concentrateur de notification |
| **MS\_NotificationHubConnectionString** | Chaîne de connexion du concentrateur de notification |
| **MS\_NamespaceName** | Alias pour MS\_PushEntityNamespace |

Votre concentrateur de notification sera géré via le [portail Azure]. Notez le nom du concentrateur de notification (vous le trouverez à l’aide des Paramètres de l’application) :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Parcourir**>, puis **Notification Hubs**.
  3. Cliquez sur le nom de concentrateur de notification associé au service mobile.

> [AZURE.NOTE] Votre concentrateur de notification n’est pas visible s’il est de type « mixte ». Les concentrateurs de notification de type « Mixte » utilisent Notification Hubs et des fonctionnalités de Service Bus héritées. Vous devez [convertir vos espaces de noms mixte]. Une fois la conversion terminée, votre concentrateur de notification s’affiche dans le [portail Azure].

Pour plus d’informations, voir la documentation de [Notification Hubs].

> [AZURE.TIP] Les fonctions de gestion de Notification Hubs dans le [portail Azure] sont encore en version préliminaire. Le [portail Azure Classic] reste disponible pour la gestion de tous vos Notification Hubs.

### <a name="legacy-push"></a>Paramètres Push hérités

Si vous avez configuré des notifications push sur votre service mobile avant leur introduction dans des Notification Hubs, vous utilisez des paramètres _push hérités_. Si vous utilisez des notifications push et ne voyez aucun Notification Hub répertorié dans votre configuration, il est probable que vous utilisez des paramètres _push hérités_. Cette fonctionnalité est migrée avec toutes les autres et est toujours disponible. Toutefois, nous vous recommandons d’effectuer une mise à niveau vers Notification Hubs peu après la migration.

Dans l’intervalle, tous les paramètres push hérités (à l’exception notable du certificat APNs) sont disponibles dans les paramètres de l’application. Le certificat APNs peut être remplacé par le fichier approprié sur le site. Pour cela, il suffit d’utiliser des options de déploiement quelconques disponibles pour Azure App Service.

### <a name="app-settings"></a>Autres paramètres d’application

Les paramètres d’application supplémentaires suivants sont migrés à partir de votre service mobile, et disponibles sous *Paramètres* > *Paramètres de l’application* :

| Paramètre de l’application | Description |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | Nom de votre application |
| **MS\_MobileServiceDomainSuffix** | Préfixe du domaine, c’est-à-dire azure-mobile.NET |
| **MS\_ApplicationKey** | Clé de votre application. |
| **MS\_MasterKey** | Clé principale de votre application |

La clé et la clé principale de l’application doivent être identiques pour les clés de l’application de votre service mobile d’origine. En particulier, la clé de l’application est envoyée par les clients mobiles pour valider leur utilisation de l’API mobile.

### <a name="cliequivalents"></a>Équivalents de ligne de commande

Vous ne pourrez plus utiliser la commande _Azure mobile_ pour gérer votre site Azure Mobile Services. Au lieu de cela, de nombreuses fonctions ont été remplacées par la commande _azure site_. Pour connaître les équivalents des commandes courantes, voir le tableau ci-dessous :

| Commande _Azure Mobile_ | Commande _Azure Site_ équivalente |
| :----------------------------------------- | :----------------------------------------- |
| mobile locations | site location list |
| mobile list | site list |
| mobile show _nom_ | site show _nom_ |
| mobile restart _nom_ | site restart _nom_ |
| mobile redeploy _nom_ | site deployment redeploy _IdValidation_ _nom_ |
| mobile key set _nom_ _type_ _valeur_ | site appsetting delete _clé_ _nom_ <br/> site appsetting add _clé_=_valeur_ _nom_ |
| mobile config list _nom_ | site appsetting list _nom_ |
| mobile config get _nom_ _clé_ | site appsetting show _clé_ _nom_ |
| mobile config set _nom_ _clé_ | site appsetting delete _clé_ _nom_ <br/> site appsetting add _clé_=_valeur_ _nom_ |
| mobile domain list _nom_ | site domain list _nom_ |
| mobile domain add _nom_ _domaine_ | site domain add _domaine_ _nom_ |
| mobile domain delete _nom_ | site domain delete _domaine_ _nom_ |
| mobile scale show _name_ | site show _nom_ |
| mobile scale change _nom_ | site scale mode _mode_ _nom_ <br /> site scale instances _instances_ _nom_ |
| mobile appsetting list _nom_ | site appsetting list _nom_ |
| mobile appsetting add _nom_ _clé_ _valeur_ | site appsetting add _clé_=_valeur_ _nom_ |
| mobile appsetting delete _nom_ _clé_ | site appsetting delete _clé_ _nom_ |
| mobile appsetting show _nom_ _clé_ | site appsetting delete _clé_ _nom_ |

Mettez à jour les paramètres d’authentification ou de notification push en mettant à jour le paramètre de l’application approprié. Modifiez des fichiers et publiez votre site via ftp ou git.

### <a name="diagnostics"></a>Diagnostics et journalisation

Les journalisation des diagnostics est normalement désactivée dans un Azure App Service. Pour activer la journalisation des diagnostics :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Par défaut, le panneau Paramètres s’ouvre. S’il ne s’ouvre pas, cliquez sur **Paramètres**.
  4. Sélectionnez **journaux de Diagnostic** dans le menu FONCTIONNALITÉS.
  5. Cliquez sur **ON** pour les fichiers journaux suivants : **Journal des applications (Filesystem)**, **Messages d’erreur détaillés** et **Suivi des demandes ayant échoué**.
  6. Cliquez sur **Système de fichiers** pour la journalisation du serveur web.
  7. Cliquez sur **Enregistrer**.

Comment afficher les journaux :

  1. Connectez-vous au [portail Azure].
  2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre service mobile migré.
  3. Cliquez sur le bouton **Outils**.
  4. Dans le menu OBSERVER, sélectionnez **Flux de journaux**.

Les journaux sont diffusés dans la fenêtre fournie à mesure qu’ils sont générés. Vous pouvez également télécharger les journaux pour les analyser ultérieurement à l’aide de vos informations d’identification de déploiement. Pour plus d’informations, voir la documentation [Journalisation].

## <a name="known-issues"></a>Problèmes connus

### La suppression d’un clone d’application mobile migré entraîne un arrêt du site

Si vous clonez votre service mobile migré à l’aide d’Azure PowerShell, puis que vous supprimez le clone, l’entrée DNS du service de production est supprimée. Par conséquent, votre site n’est plus accessible sur Internet.

Résolution : Nous travaillons actuellement à la correction de ce problème. Si vous souhaitez cloner votre site, utilisez le portail.

### La modification du fichier web.config ne fonctionne pas

Si vous avez un site ASP.NET, les changements apportés au fichier `Web.config` ne fonctionneront pas. Azure App Service crée un fichier `Web.config` adapté lors du démarrage pour prendre en charge le runtime Mobile Services. Vous pouvez remplacer certains paramètres (comme les en-têtes personnalisés) à l’aide d’un fichier de transformation XML. Créez un fichier appelé `applicationHost.xdt` : ce fichier doit terminer dans le répertoire `D:\home\site` du Service Azure. Vous pouvez le faire avec un script de déploiement personnalisé ou en utilisant directement Kudu. Voici un exemple de document :

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Pour plus d’informations, consultez la documentation [Exemples de transformation XDT] sur GitHub.

### Vous ne pouvez pas ajouté une version migrée de Mobile Services à Traffic Manager

Lorsque vous créez un profil Traffic Manager, vous ne pouvez pas choisir directement une version migrée de Mobile Services pour le profil. Vous devez utiliser un « système d'extrémité externe ». Le système d'extrémité externe peut uniquement être ajouté via PowerShell. Pour plus d’informations, reportez-vous au [Didacticiel de Traffic Manager](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Étapes suivantes

À présent que votre application a été migrée vers App Service, vous pouvez tirer parti de davantage de fonctionnalités :

  * Le déploiement d’[emplacements intermédiaires] vous permet d’organiser les modifications apportées à votre site par phases et d’effectuer un test A/B.
  * Les [WebJobs] remplacent les tâches planifiées à la demande.
  * Vous pouvez [déployer en continu] votre site en le liant à GitHub, à TFS ou à Mercurial.
  * Vous pouvez utiliser [Application Insights] pour analyser votre site.
  * Servez un site web et une API mobile à partir du même code.

### <a name="upgrading-your-site"></a>Mise à niveau de votre site Mobile Services vers le Kit de développement logiciel (SDK) Azure Mobile Apps

  * Pour les projets de serveur basés sur Node.js, le nouveau [Kit de développement logiciel (SDK) Mobile Apps Node.js] apporte un certain nombre de nouvelles fonctionnalités. Par exemple, vous pouvez maintenant faire du développement et du débogage localement, utiliser n’importe quelle version de Node.js à partir de 0.10 et utiliser n’importe quel middleware Express.js pour personnaliser.

  * Pour les projets de serveur basé sur .NET, les nouveaux [packages NuGet du Kit de développement logiciel (SDK) Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offrent plus de flexibilité sur les dépendances NuGet, prennent en charge les nouvelles fonctionnalités d’authentification App Service et s’associent avec tous les projets ASP.NET, y compris MVC. Pour en savoir plus sur la mise à niveau, voir [Mettre à niveau votre service Mobile Services .NET existant vers App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Tarification d’App Service]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[mise à l’échelle automatique]: ../app-service-web/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Documentation sur le déploiement d’Azure App Service]: ../app-service-web/web-sites-deploy.md
[portail Azure Classic]: https://manage.windowsazure.com
[portail Azure]: https://portal.azure.com
[région Azure]: https://azure.microsoft.com/regions/
[Plans d’Azure Scheduler]: ../scheduler/scheduler-plans-billing.md
[déployer en continu]: ../app-service-web/app-service-continuous-deployment.md
[convertir vos espaces de noms mixte]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[noms de domaine personnalisés]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[mise à la disposition générale d’Azure App Service]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[connexions hybrides]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Journalisation]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Kit de développement logiciel (SDK) Mobile Apps Node.js]: https://github.com/azure/azure-mobile-apps-node
[Services mobiles et App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md
[analyse des performances]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[sauvegarder les scripts]: ../mobile-services/mobile-services-disaster-recovery.md
[emplacements intermédiaires]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Tâches web]: ../app-service-web/websites-webjobs-resources.md
[Exemples de transformation XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples

<!---HONumber=AcomDC_0803_2016-->