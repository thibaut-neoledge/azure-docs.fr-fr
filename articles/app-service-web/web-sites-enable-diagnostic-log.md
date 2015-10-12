<properties
	pageTitle="Activer la journalisation des diagnostics pour les applications web dans Azure App Service"
	description="Découvrez comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="cephalin"/>

# Activer la journalisation des diagnostics pour les applications web dans Azure App Service

## Vue d'ensemble

Azure fournit des diagnostics intégrés pour aider au débogage d'une [application Web App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous explique comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, et comment accéder aux informations enregistrées par Azure.

Cet article utilise le [portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=529715), Azure PowerShell et l’interface de ligne de commande Azure (CLI Azure) pour l’exploitation des journaux de diagnostic. Pour plus d’informations sur l’utilisation de journaux de diagnostic avec Visual Studio, consultez [Résolution des problèmes Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostics de serveur Web et diagnostics d’application

Les applications web App Service fournissent des fonctionnalités de diagnostic pour les informations de journalisation provenant du serveur Web et de l'application web. Ces informations sont réparties, en toute logique, en **diagnostics de serveur Web** et en **diagnostics d’application**.

### Diagnostics de serveur web

Vous pouvez activer ou désactiver les types de journaux suivants :

- **Messages d’erreur détaillés** : informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur). Il peut s'agir d'informations qui vous aident à déterminer la raison pour laquelle le serveur a renvoyé le code d'erreur.
- **Suivi des demandes ayant échoué** : informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Cela peut se révéler utile si vous essayez d'améliorer les performances du site ou d'isoler la cause d'une erreur HTTP spécifique.
- **Journalisation du serveur Web** : informations sur les transactions HTTP à l’aide du [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ce rapport se révèle utile pour déterminer les métriques globales d’un site, comme le nombre de demandes traitées ou le nombre de demandes émanant d’une adresse IP spécifique.

### Diagnostic d'application

Le diagnostic d'application vous permet de capturer des informations générées par une application Web. Les applications ASP.NET peuvent utiliser la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) pour enregistrer des informations dans le journal de diagnostic d'application. Par exemple :

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Lors de l'exécution, vous pouvez récupérer ces journaux pour vous aider lors du dépannage. Pour plus d’informations, consultez la page [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Les applications web App Service journalisent également les informations de déploiement lorsque vous publiez du contenu dans une application web. Cela est effectué automatiquement et il n'existe aucun paramètre de configuration pour la journalisation du déploiement. Cette dernière vous permet de déterminer le motif d'échec d'un déploiement. Si vous utilisez, par exemple, un script de déploiement personnalisé, vous pouvez recourir à la journalisation de déploiement pour déterminer la cause de l'échec du script.

## <a name="enablediag"></a>Activation des diagnostics

Pour activer les diagnostics sur le [portail Azure en version préliminaire](https://portal.azure.com), accédez au panneau de votre application web, puis cliquez sur **Paramètres > Journaux de diagnostic**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Partie des journaux](./media/web-sites-enable-diagnostic-log/logspart.png)

Quand vous activez le **diagnostic d’application**, vous choisissez aussi le **niveau**. Ce paramètre vous permet de filtrer les données capturées selon le critère **Information**, **Avertissement** ou **Erreur**. Vous pouvez également sélectionner le niveau **Détaillé** pour que toutes les informations générées par l'application soient consignées.

> [AZURE.NOTE]Contrairement à la modification du fichier web.config, le fait d'activer le diagnostic d'application ou de modifier les niveaux de journalisation de diagnostic ne recycle pas le domaine dans lequel l'application s'exécute.

Dans le [portail Azure](https://manage.windowsazure.com), sous l’onglet **Configurer** de l’application web, vous pouvez sélectionner **stockage** ou **système de fichiers** pour la **journalisation du serveur web**. Si vous sélectionnez le **stockage**, vous avez également la possibilité de sélectionner un compte de stockage, puis un conteneur d'objets blob dans lequel les journaux seront écrits. Tous les autres journaux relatifs au **diagnostic de site** sont écrits uniquement dans le système de fichiers.

Dans le [portail Azure](https://manage.windowsazure.com), l’onglet **Configurer** de l’application web comprend aussi des paramètres supplémentaires pour le diagnostic d’application :

* **Système de fichiers** : stocke les informations de diagnostics d’application dans le système de fichiers d’application web. Vous pouvez accéder à ces fichiers par FTP ou les télécharger sous la forme d’une archive ZIP en utilisant Azure PowerShell ou l’interface de ligne de commande Azure (CLI Azure).
* **Stockage de tables** : stocke les informations de diagnostic d’application dans la table et le compte Azure Storage spécifiés.
* **Stockage d'objets blob** : stocke les informations de diagnostic d'application dans le conteneur d'objets blob et le compte Azure Storage spécifiés.
* **Période de rétention** : par défaut, les journaux ne sont pas automatiquement supprimés du **stockage d'objets blob**. Sélectionnez **Set retention** et entrez la période de conservation des journaux (en jours) si vous souhaitez les supprimer automatiquement.

Vous pouvez activer simultanément toute combinaison de système de fichiers, stockage de tables ou stockage d’objets blob. Des configurations de niveau de journalisation individuelles sont également possibles. Vous pouvez, par exemple, consigner les erreurs et les avertissements dans le stockage d'objets blob dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau de journalisation détaillé du système de fichiers.

Bien que ces trois emplacements de stockage fournissent les mêmes informations de base pour les événements consignés, le **stockage de tables** et le **stockage d'objets blob** consignent davantage d'informations, telles que l'ID d'instance, l'ID de thread et un horodatage plus précis, que lorsque vous optez pour la journalisation dans le **système de fichiers**.

> [AZURE.NOTE]Les informations stockées dans le **stockage de tables** ou le **stockage d’objets blob** ne sont accessibles qu’à l’aide d’un client de stockage ou d’une application capable d’utiliser directement ces systèmes de stockage. Par exemple, Visual Studio 2013 contient un Explorateur de stockage qui peut être utilisé pour explorer un système de stockage de tables ou d'objets blob, tandis que HDInsight peut accéder aux données stockées dans un stockage d'objets blob. Vous pouvez également écrire une application qui accède à Azure Storage en utilisant l'un des [Kits de développement logiciel (SDK) Azure](/downloads/#).

> [AZURE.NOTE]Les diagnostics peuvent également être activés à partir du module Azure PowerShell via la cmdlet **Set-AzureWebsite**. Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a> Téléchargement de journaux

Les informations de diagnostic stockées dans le système de fichiers d’application web sont directement accessibles via FTP. Vous pouvez également les télécharger sous la forme d’une archive ZIP en utilisant Azure PowerShell ou l’interface de ligne de commande Azure.

La structure de répertoires dans laquelle les journaux sont stockés est la suivante :

* **Journaux d'application** : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.

* **Suivi des demandes ayant échoué** : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.

* **Journaux d'erreurs détaillés** : /LogFiles/DetailedErrors/. Ce dossier contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite.

* **Journaux des serveurs Web** : /LogFiles/http/RawLogs. Ce dossier contient un ou plusieurs fichiers texte au format [de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Journaux de déploiement** : /LogFiles/Git. Ce dossier contient les journaux générés par les processus de déploiement internes utilisés par les applications web Azure, ainsi que les journaux des déploiements Git.

### FTP

Pour accéder à des informations de diagnostic par FTP, consultez le **Tableau de bord** de votre application web sur le [portail Azure](https://manage.windowsazure.com). Dans la section **Quick Glance**, cliquez sur le lien **FTP Diagnostic Logs** pour accéder aux fichiers journaux via FTP. L'entrée **Deployment/FTP User** indique le nom d'utilisateur à utiliser pour accéder au site FTP.

> [AZURE.NOTE]Si l’entrée **Utilisateur du déploiement/FTP** n’est pas définie ou si vous avez oublié le mot de passe de cet utilisateur, vous pouvez créer un utilisateur et un mot de passe en utilisant le lien **Réinitialiser les informations d’identification du déploiement** dans la section **Aperçu rapide** du **Tableau de bord**.

### Téléchargement avec Azure PowerShell

Pour télécharger les fichiers journaux, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Save-AzureWebSiteLog -Name webappname

Cette commande enregistre les journaux de l’application Web spécifiée par le paramètre **-Name** dans un fichier nommé **logs.zip** du répertoire en cours.

> [AZURE.NOTE]Si vous n’avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d’Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Téléchargement avec l’interface de ligne de commande Azure

Pour télécharger les fichiers journaux à l’aide de l’interface de ligne de commande Azure, ouvrez une nouvelle session d’invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log download webappname

Cette commande enregistre les journaux de l’application web nommée « webappname » dans un fichier **diagnostics.zip** du répertoire en cours.

> [AZURE.NOTE]Si vous n’avez pas installé ou configuré l’interface de ligne de commande Azure (CLI Azure) de manière à utiliser votre abonnement Azure, consultez la page[ Utilisation de l’interface de ligne de commande Azure](../xplat-cli-install.md).

## Affichage des journaux dans Application Insights

Visual Studio Application Insights fournit des outils de filtrage et de recherche dans les journaux, mais aussi de mise en corrélation des journaux avec les requêtes et d’autres événements.

1. Ajoutez le Kit de développement logiciel Application Insights à votre projet dans Visual Studio.
 * Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez Ajouter Application Insights. Vous serez guidé tout au long de la création de la ressource Application Insights. [En savoir plus](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. Ajoutez le package de l’écouteur de suivi à votre projet.
 * Cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet. Sélectionnez `Microsoft.ApplicationInsights.TraceListener` [En savoir plus](../application-insights/app-insights-asp-net-trace-logs.md)
3. Téléchargez votre projet et exécutez-le pour générer des données de journal.
4. Sur le [portail Azure en version préliminaire](http://portal.azure.com/), accédez à votre nouvelle ressource Application Insights, puis ouvrez la fonction de **recherche**. Vous pouvez voir vos données de journal, ainsi que la requête, l’utilisation et les autres mesures de télémétrie. Vous devrez parfois patienter quelques minutes pour accéder à certaines mesures de télémétrie : dans ce cas, cliquez sur Actualiser. [En savoir plus](../application-insights/app-insights-diagnostic-search.md)

[En savoir plus sur le suivi des performances avec Application Insights](../insights-perf-analytics.md)

##<a name="streamlogs"></a> Diffusion en continu des journaux

Lors du développement d’une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Pour ce faire, vous pouvez diffuser ces informations vers votre environnement de développement en utilisant soit Azure PowerShell, soit l’interface de ligne de commande Azure.

> [AZURE.NOTE]Certains types de mémoire tampon de journalisation sont écrits dans le fichier journal. Dès lors, il se peut que les événements apparaissent de manière désordonnée dans le flux. Ainsi, il est possible qu'une entrée du journal d'application qui se produit lorsqu'un utilisateur visite une page soit affichée dans le flux avant l'entrée de journal HTTP correspondante pour la demande de page.

> [AZURE.NOTE]Lors de la diffusion de journaux en continu, les informations écrites dans tout fichier texte stocké dans le dossier **D:\\home\\LogFiles\** sont également diffusées.

### Diffusion d'informations en continu avec Azure PowerShell

Pour diffuser des informations de journalisation en continu, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Get-AzureWebSiteLog -Name webappname -Tail

Une connexion est alors établie avec l’application Web spécifiée par le paramètre **-Name**, et les informations sont diffusées vers la fenêtre PowerShell à mesure que des événements de journalisation se produisent sur l’application Web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **-Message**. Par exemple :

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **-Path**. Par exemple :

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Pour afficher la liste des chemins disponibles, utilisez le paramètre -ListPath.

> [AZURE.NOTE]Si vous n’avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d’Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Diffusion d’informations en continu avec l’interface de ligne de commande Azure

Pour diffuser des informations de journalisation, ouvrez une nouvelle session d’invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log tail webappname

Une connexion est alors établie avec l’application Web nommée « webappname », et les informations sont diffusées vers la fenêtre à mesure que des événements de journalisation se produisent sur l’application Web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **--Filter**. Par exemple :

	azure site log tail webappname --filter Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **--Path**. Par exemple :

	azure site log tail webappname --path http

> [AZURE.NOTE]Si vous n’avez pas installé ou configuré l’interface de ligne de commande Azure de manière à utiliser votre abonnement Azure, consultez la page [Utilisation de l’interface de ligne de commande Azure](../xplat-cli-install.md).

##<a name="understandlogs"></a> Présentation des journaux de diagnostic

### Journaux de diagnostic d'application

Le diagnostic d'application stocke les informations dans un format spécifique pour les applications .NET selon que vous stockez les journaux dans le système de fichiers, le stockage de tables ou le stockage d'objets blob. L'ensemble de base des données stockées est le même dans les trois types de stockage, à savoir : date et heure auxquelles l'événement s'est produit, ID de processus qui a généré l'événement, type d'événement (informations, avertissement, erreur) et message d'événement.

__Système de fichiers__

Chaque ligne consignée dans le système de fichiers ou reçue par le biais d'une diffusion en continu se présente au format suivant :

	{Date}  PID[{process id}] {event type/level} {message}

Par exemple, un événement d'erreur se présente comme suit :

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Parmi les trois méthodes disponibles, la journalisation d'informations dans le système de fichiers est celle qui fournit les informations les plus élémentaires. L'heure, l'ID de processus, le niveau d'événement et le message sont, en effet, les seules informations fournies.

__Stockage de tables__

Lorsque vous consignez des informations dans le stockage de tables, des propriétés supplémentaires sont utilisées pour faciliter la recherche des données stockées dans la table, ainsi que des informations plus précises sur l'événement. Les propriétés suivantes (colonnes) sont utilisées pour chaque entité (ligne) stockée dans la table.

Nom de la propriété|Valeur/format
---|---
PartitionKey|Date/heure de l'événement au format aaaaMMjjHH
RowKey|Valeur GUID qui identifie cette entité de façon unique
Timestamp|Date et heure auxquelles l'événement s'est produit
EventTickCount|Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue)
ApplicationName|Nom de l’application web
Niveau|Niveau d'événement (erreur, avertissement ou information, par exemple)
EventId|ID de cet événement<p><p>Il est, par défaut, défini sur 0
InstanceId|Instance de l’application web sur laquelle l’événement s’est produit
Pid|ID du processus
Tid|ID de thread qui a généré l'événement
Message|Message détaillé sur l'événement

__Stockage d’objets blob__

Lorsque vous consignez des données dans un stockage d'objets blob, elles sont stockées au format CSV (valeurs séparées par des virgules). Comme c'est le cas avec le stockage de tables, des champs supplémentaires sont consignés afin de fournir des informations plus précises sur l'événement. Les propriétés suivantes sont utilisées pour chaque ligne du fichier CSV :

Nom de la propriété|Valeur/format
---|---
Date|Date et heure auxquelles l'événement s'est produit
Level|Niveau d'événement (erreur, avertissement ou information, par exemple)
ApplicationName|Nom de l’application web
InstanceId|Instance de l’application web sur laquelle l’événement s’est produit
EventTickCount|Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue)
EventId|ID de cet événement<p><p>Il est, par défaut, défini sur 0
Pid|ID du processus
Tid|ID de thread qui a généré l'événement
Message|Message détaillé sur l'événement

Les données stockées dans un objet blob se présentent comme suit :

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE]La première ligne du journal contient les en-têtes de colonne, tels qu'ils sont représentés dans cet exemple.

### Suivi des demandes ayant échoué

Le suivi des demandes ayant échoué est stocké dans des fichiers XML nommés __fr######.xml__. Pour faciliter la consultation des informations consignées, une feuille de style XSL nommée __freb.xsl__ est fournie dans le même répertoire que les fichiers XML. Lorsque vous ouvrez l'un des fichiers XML dans Internet Explorer, la feuille de style XSL est utilisée afin de fournir un affichage formaté des informations de suivi. Les informations se présentent alors comme suit :

![affichage d'une demande ayant échoué dans le navigateur](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Journaux d'erreurs détaillés

Les journaux d'erreurs détaillés sont des documents HTML qui fournissent des informations plus détaillées sur les erreurs HTTP qui se sont produites. Puisqu'il s'agit simplement de documents HTML, ils peuvent être consultés à l'aide d'un navigateur Web.

### Journaux de serveur Web

Les journaux de serveur Web utilisent le [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent être lues à l'aide d'un éditeur de texte ou analysées à l'aide d'utilitaires tels que [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE]Les journaux générés par les applications Web Azure ne prennent pas en charge les champs __s-computername__, __s-ip__ ou __cs-version__.

##<a name="nextsteps"></a>Étapes suivantes

- [Surveillance d’applications Web](/fr-FR/manage/services/web-sites/how-to-monitor-websites/)
- [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Analyse des journaux d’application Web dans HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) (en anglais)

> [AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l'ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=Oct15_HO1-->