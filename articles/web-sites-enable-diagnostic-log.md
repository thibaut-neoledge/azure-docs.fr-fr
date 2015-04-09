<properties 
	pageTitle="Activer la journalisation des diagnostics pour les applications web dans Azure App Service" 
	description="Découvrez comment activer la journalisation de diagnostic et ajouter la fonctionnalité d'instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Activer la journalisation des diagnostics pour les applications web dans Azure App Service

## Vue d'ensemble

Azure fournit des diagnostics intégrés pour aider au débogage d'une application web hébergée dans un [App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous explique comment activer la journalisation de diagnostic et ajouter la fonctionnalité d'instrumentation à votre application, et comment accéder aux informations enregistrées par Azure.

> [AZURE.NOTE] Cet article utilise le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715), Azure PowerShell et l'interface de ligne de commande multiplateforme Azure pour l'exploitation des journaux de diagnostic. Pour plus d'informations sur l'utilisation de journaux de diagnostic avec Visual Studio, voir [Résolution des problèmes Azure dans Visual Studio](troubleshoot-web-sites-in-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostics de serveur web et diagnostics d'application

[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) fournit des fonctionnalités de diagnostic pour les informations de journalisation provenant du serveur web et de l'application web. Ces informations sont réparties, en toute logique, en **diagnostics de serveur web** et en **diagnostics d'application**.

### Diagnostics de serveur web

Vous pouvez activer ou désactiver les types de journaux suivants :

- **Messages d'erreur détaillés** : informations d'erreur détaillées pour les codes d'état HTTP qui indiquent un échec (code d'état 400 ou supérieur). Il peut s'agir d'informations qui vous aident à déterminer la raison pour laquelle le serveur a renvoyé le code d'erreur.
- **Suivi des demandes ayant échoué** : informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Cela peut se révéler utile si vous essayez d'améliorer les performances du site ou d'isoler la cause d'une erreur HTTP spécifique.
- **Journalisation du serveur Web** : informations sur les transactions HTTP à l'aide du [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ce rapport se révèle utile pour déterminer les métriques globales d'un site, comme le nombre de demandes traitées ou le nombre de demandes émanant d'une adresse IP spécifique.

### Diagnostic d'application

Les diagnostics d'application vous permettent de capturer des informations générées par une application web. Les applications ASP.NET peuvent utiliser la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) pour enregistrer des informations dans le journal des diagnostics d'application. Par exemple :

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Le diagnostic d'application vous permet de résoudre les problèmes affectant l'application en cours d'exécution en émettant des informations lorsque certains fragments de code sont utilisés. Cela est très utile lorsque vous essayez de déterminer pourquoi le code emprunte un chemin spécifique, le plus souvent lorsque le chemin entraîne une erreur ou provoque un comportement indésirable.

Pour plus d'informations sur l'utilisation des diagnostics d'application avec Visual Studio, voir [Résolution des problèmes des applications web Azure dans Visual Studio](troubleshoot-web-sites-in-visual-studio.md).

> [AZURE.NOTE] Contrairement à la modification du fichier web.config, le fait d'activer le diagnostic d'application ou de modifier les niveaux de journalisation de diagnostic ne recycle pas le domaine dans lequel l'application s'exécute.

Les applications web Azure journalisent également les informations de déploiement lorsque vous publiez du contenu dans une application web. Cela est effectué automatiquement et il n'existe aucun paramètre de configuration pour la journalisation du déploiement. Cette dernière vous permet de déterminer le motif d'échec d'un déploiement. Si vous utilisez, par exemple, un script de déploiement personnalisé, vous pouvez recourir à la journalisation de déploiement pour déterminer la cause de l'échec du script.

## <a name="enablediag"></a>Activation des diagnostics

Pour activer les diagnostics dans le [Portail de gestion Azure](https://portal.azure.com), accédez au panneau de votre application web, puis cliquez sur **Tous les paramètres > Journaux de diagnostics**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Logs part](./media/web-sites-enable-diagnostic-log/logspart.png)

Lors de l'activation de l'option **Journal des applications**, vous devez également sélectionner le **Niveau de journalisation** et préciser si la journalisation doit être activée pour **Système de fichiers**, **Stockage de tables** ou **Stockage d'objets blob**. Bien que ces trois emplacements de stockage fournissent les mêmes informations de base pour les événements journalisés, les options **Stockage de tables** et **Stockage d'objets blob** enregistrent davantage d'informations, comme l'ID d'instance, l'ID de thread et un horodateur plus précis (format de battement) que lorsque vous optez pour la journalisation dans le **système de fichiers**.

Lorsque vous activez l'option **Diagnostics de site**, vous devez sélectionner **Stockage** ou **Système de fichiers** pour la **Journalisation du serveur Web**. Si vous sélectionnez **Stockage**, vous avez également la possibilité de sélectionner un compte de stockage, puis un conteneur d'objets blob dans lequel les journaux seront écrits. Tous les autres journaux relatifs aux **Diagnostics de site** sont écrits uniquement dans le système de fichiers.

> [AZURE.NOTE] Les informations stockées dans **Stockage de tables** ou dans **Stockage d'objets blob** sont uniquement accessibles à l'aide d'un client de stockage ou d'une application pouvant travailler directement avec ces systèmes de stockage. Par exemple, Visual Studio 2013 contient un Explorateur de stockage qui peut être utilisé pour explorer un système de stockage de tables ou d'objets blob, tandis que HDInsight peut accéder aux données stockées dans un stockage d'objets blob. Vous pouvez également écrire une application qui accède à Azure Storage à l'aide de l'un des [Kits de développement logiciel (SDK) Azure](/downloads/#).

Les paramètres disponibles lors de l'activation des **diagnostics d'application** sont les suivants :

* **Niveau de journalisation** : permet de filtrer les informations capturées en fonction du critère **Information**, **Avertissement** ou **Erreur**. Vous pouvez également sélectionner le niveau **Détaillé** pour journaliser toutes les informations générées par l'application. **Le niveau de journalisation** peut être défini différemment pour **Système de fichiers**, **Stockage de tables** et **Stockage d'objets blob**.
* **Système de fichiers** : stocke les informations de diagnostics d'application dans le système de fichiers d'application web. Vous pouvez accéder à ces fichiers par FTP ou les télécharger sous la forme d'une archive ZIP en utilisant Azure PowerShell ou les outils en ligne de commande Azure.
* **Stockage de tables** : stocke les informations de diagnostics d'application dans la table et le compte Azure Storage spécifiés.
* **Stockage d'objets blob** : stocke les informations de diagnostics d'application dans le conteneur d'objets blob et le compte Azure Storage spécifiés.
* **Période de rétention** : par défaut, les journaux ne sont pas automatiquement supprimés du **Stockage d'objets blob**. Sélectionnez **Définir la rétention** et entrez le nombre de jours de conservation des journaux si vous souhaitez supprimer automatiquement ces derniers.

> [AZURE.NOTE] Vous pouvez activer simultanément toute combinaison de système de fichiers, stockage de tables ou stockage d'objets blob. Des configurations de niveau de journalisation individuelles sont également possibles. Vous pouvez, par exemple, consigner les erreurs et les avertissements dans le stockage d'objets blob dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau de journalisation détaillé du système de fichiers.

> [AZURE.NOTE] Les diagnostics peuvent également être activés à partir du module Azure PowerShell à l'aide de l'applet de commande **Set-AzureWebsite**. Si vous n'avez pas installé ou configuré Azure PowerShell pour l'utilisation de votre abonnement Azure, voir [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a>  Téléchargement des journaux

Les informations de diagnostic stockées dans le système de fichiers d'application web sont directement accessibles via FTP. Vous pouvez également les télécharger sous la forme d'une archive ZIP en utilisant Azure PowerShell ou les outils en ligne de commande Azure.

La structure de répertoires dans laquelle les journaux sont stockés est la suivante :

* **Journaux d'application** : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.

* **Suivi des demandes ayant échoué** : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.

* **Journaux d'erreurs détaillés** : /LogFiles/DetailedErrors/. Ce dossier contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite. 

* **Journaux des serveurs web** : /LogFiles/http/RawLogs. Ce dossier contient un ou plusieurs fichiers texte au [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 

* **Journaux de déploiement** : /LogFiles/Git. Ce dossier contient les journaux générés par les processus de déploiement internes utilisés par les applications web Azure, ainsi que les journaux des déploiements Git.

### FTP

Pour accéder à des informations de diagnostic via FTP, consultez le **Tableau de bord** de votre application web dans le Portail de gestion Azure. Dans la section **aperçu rapide**, cliquez sur le lien **Journaux de diagnostic FTP** pour accéder aux fichiers journaux via FTP. L'entrée **Utilisateur du déploiement/FTP** indique le nom d'utilisateur à utiliser pour accéder au site FTP.

> [AZURE.NOTE] Si l'entrée **Utilisateur du déploiement/FTP** n'est pas définie ou que vous avez oublié le mot de passe de cet utilisateur, vous pouvez créer un utilisateur et un mot de passe en utilisant le lien **Réinitialisez vos informations d'identification de déploiement** dans la section **aperçu rapide** du **Tableau de bord**.

### Téléchargement avec Azure PowerShell

Pour télécharger les fichiers journaux, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Save-AzureWebSiteLog -Name webappname

Cette commande enregistre les journaux de l'application web spécifiée par le paramètre **-Name** dans un fichier nommé **logs.zip** dans le répertoire en cours.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré Azure PowerShell pour l'utilisation de votre abonnement Azure, voir [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Téléchargement avec les outils en ligne de commande Azure

Pour télécharger les fichiers journaux à l'aide des outils en ligne de commande Azure, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log download webappname

Cette commande enregistre les journaux de l'application web nommée 'webappname' dans un fichier **diagnostics.zip** dans le répertoire en cours.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré les outils en ligne de commande Azure pour l'utilisation de votre abonnement Azure, voir [Utilisation des outils en ligne de commande Azure](xplat-cli.md).

##<a name="streamlogs"></a>Procédure : Diffusion en continu des journaux

Lors du développement d'une application, il est utile de visualiser des informations de journalisation en temps quasi-réel. Pour ce faire, vous pouvez diffuser ces informations vers votre environnement de développement en utilisant soit Azure PowerShell, soit les outils en ligne de commande Azure.

> [AZURE.NOTE] Certains types de mémoire tampon de journalisation sont écrits dans le fichier journal. Dès lors, il se peut que les événements apparaissent dans le désordre dans le flux. Ainsi, il est possible qu'une entrée du journal d'application qui se produit lorsqu'un utilisateur visite une page soit affichée dans le flux avant l'entrée de journal HTTP correspondante pour la demande de page.

> [AZURE.NOTE] La diffusion en continu des journaux diffusera également les informations écrites dans n'importe quel fichier texte stocké dans le dossier **D:\\home\\LogFiles\\**.

### Diffusion d'informations en continu avec Azure PowerShell

Pour diffuser des informations de journalisation en continu, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Get-AzureWebSiteLog -Name webappname -Tail

Une connexion est alors établie avec l'application web spécifiée par le paramètre **-Name**, et les informations sont diffusées vers la fenêtre PowerShell à mesure que des événements de journalisation se produisent sur l'application web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **-Message**. Par exemple :

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **-Path**. Par exemple :

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Pour afficher la liste des chemins disponibles, utilisez le paramètre -ListPath.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré Azure PowerShell pour l'utilisation de votre abonnement Azure, voir [Utilisation d'Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Diffusion d'informations en continu avec les outils en ligne de commande Azure

Pour diffuser des informations de journalisation, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log tail webappname

Une connexion est alors établie avec l'application web nommée 'webappname', et les informations sont diffusées vers la fenêtre à mesure que des événements de journalisation se produisent sur l'application web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **--Filter**. Par exemple :

	azure site log tail webappname --filter Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **--Path**. Par exemple :

	azure site log tail webappname --path http

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré les outils en ligne de commande Azure pour l'utilisation de votre abonnement Azure, voir [Utilisation des outils en ligne de commande Azure](xplat-cli.md).

##<a name="understandlogs"></a>Procédure : Présentation des journaux de diagnostic

### Journaux de diagnostic d'application

Le diagnostic d'application stocke les informations dans un format spécifique pour les applications .NET selon que vous stockez les journaux dans le système de fichiers, le stockage de tables ou le stockage d'objets blob. L'ensemble de base des données stockées est le même dans les trois types de stockage, à savoir : date et heure auxquelles l'événement s'est produit, ID de processus qui a généré l'événement, type d'événement (informations, avertissement, erreur) et message d'événement.

__Système de fichiers__

Chaque ligne consignée dans le système de fichiers ou reçue par le biais d'une diffusion en continu se présente au format suivant :

	{Date}  PID[{process id}] {event type/level} {message}

Par exemple, un événement d'erreur se présente comme suit :

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Parmi les trois méthodes disponibles, la journalisation d'informations dans le système de fichiers est celle qui fournit les informations les plus élémentaires. L'heure, l'ID de processus, le niveau d'événement et le message sont, en effet, les seules informations fournies.

__Stockage de tables__

Lorsque vous consignez des informations dans le stockage de tables, des propriétés supplémentaires sont utilisées pour faciliter la recherche des données stockées dans la table, ainsi que des informations plus précises sur l'événement. Les propriétés suivantes (colonnes) sont utilisées pour chaque entité (ligne) stockée dans la table.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Nom de la propriété</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Valeur/format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Date/heure de l'événement au format aaaaMMjjHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Valeur GUID qui identifie cette entité de façon unique</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">Date et heure auxquelles l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Date et heure auxquelles l'événement s'est produit, au format de battement (précision accrue))</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Nom de l'application web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Niveau d'événement (erreur, avertissement ou information, par exemple)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">ID de l'événement<br>Défini par défaut sur 0 si aucun ID n'est spécifié</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance de l'application web sur laquelle l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID du processus</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">ID de thread qui a généré l'événement</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Message détaillé sur l'événement</td>
</tr>
</table>

__Stockage d'objets blob__

Lorsque vous consignez des données dans un stockage d'objets blob, elles sont stockées au format CSV (valeurs séparées par des virgules). Comme c'est le cas avec le stockage de tables, des champs supplémentaires sont consignés afin de fournir des informations plus précises sur l'événement. Les propriétés suivantes sont utilisées pour chaque ligne du fichier CSV :

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Nom de la propriété</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Valeur/format</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">Date et heure auxquelles l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Niveau d'événement (erreur, avertissement ou information, par exemple)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Nom de l'application web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance de l'application web sur laquelle l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">ID de l'événement<br>Défini par défaut sur 0 si aucun ID n'est spécifié</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID du processus</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">ID de thread qui a généré l'événement</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Message détaillé sur l'événement</td>
</tr>
</table>

Les données stockées dans un objet blob se présentent comme suit :

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La première ligne du journal contient les en-têtes de colonne, tels qu'ils sont représentés dans cet exemple.

### Suivi des demandes ayant échoué

Le suivi des demandes ayant échoué est stocké dans des fichiers XML nommés __fr######.xml__. Pour faciliter la consultation des informations journalisées, une feuille de style XSL nommée __freb.xsl__ est fournie dans le même répertoire que les fichiers XML. Lorsque vous ouvrez l'un des fichiers XML dans Internet Explorer, la feuille de style XSL est utilisée afin de fournir un affichage formaté des informations de suivi. Les informations se présentent alors comme suit :

![failed request viewed in the browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Journaux d'erreurs détaillés

Les journaux d'erreurs détaillés sont des documents HTML qui fournissent des informations plus détaillées sur les erreurs HTTP qui se sont produites. Puisqu'il s'agit simplement de documents HTML, ils peuvent être consultés à l'aide d'un navigateur Web.

### Journaux de serveur Web

Les journaux de serveur web utilisent le [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations sont lisibles à l'aide d'un éditeur de texte ou analysables à l'aide d'utilitaires tels que [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Les journaux générés par les applications web Azure ne prennent pas en charge les champs __s-computername__, __s-ip__ ou __cs-version__.

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

##<a name="nextsteps"></a> Étapes suivantes

- [Surveillance d'applications web](/fr-fr/manage/services/web-sites/how-to-monitor-websites/)
- [Didacticiel - Résolution des problèmes des applications web](/fr-fr/develop/net/best-practices/troubleshooting-web-sites/)
- [Résolution des problèmes des applications web Azure dans Visual Studio](/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Analyser les journaux d'application web dans HDInsight (en anglais)](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->