<properties 
	pageTitle="Activation de la journalisation des diagnostics - Sites Web Azure" 
	description="Découvrez comment activer la journalisation de diagnostic et ajouter la fonctionnalité d'instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





#Activation de la journalisation des diagnostics pour Sites Web Azure

Azure fournit un outil de diagnostic intégré qui vous aide à déboguer une application hébergée dans Sites Web Azure. Cet article vous explique comment activer la journalisation de diagnostic et ajouter la fonctionnalité d'instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure.

> [AZURE.NOTE] Cet article décrit l'utilisation du portail de gestion Azure, d'Azure PowerShell et de l'interface de ligne de commande interplateforme Azure en vue d'une utilisation avec les journaux de diagnostic. Pour plus d'informations sur l'utilisation de journaux de diagnostic avec Visual Studio, consultez la page [Résolution des problèmes liés à Sites Web Azure dans Visual Studio](/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

##Sommaire##

- [Définition des diagnostics de site Web](#whatisdiag)
- [Procédure : activation des diagnostics](#enablediag)
- [Procédure : téléchargement des journaux](#download)
- [Procédure : diffusion en continu des journaux](#streamlogs)
- [Procédure : présentation des journaux de diagnostic](#understandlogs)
- [Étapes suivantes](#nextsteps)

<a name="whatisdiag"></a><h2>Définition des diagnostics de site Web</h2>

L'offre Sites Web Azure fournit des fonctionnalités de diagnostic pour les informations de journalisation provenant aussi bien du serveur Web que de l'application Web. Ces informations sont réparties, en toute logique, en **diagnostic de site** et **diagnostic d'application**.

###Diagnostic de site

Les diagnostics de site vous permettent d'activer ou de désactiver les éléments suivants :

- **Messages d'erreur détaillés** : cette option enregistre des informations d'erreur détaillées pour les codes d'erreur HTTP qui indiquent un échec (code d'état 400 ou supérieur). Il peut s'agir d'informations qui vous aident à déterminer la raison pour laquelle le serveur a renvoyé le code d'erreur.
- **Suivi des demandes ayant échoué** : cette option enregistre des informations sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Cela peut se révéler utile si vous essayez d'améliorer les performances du site ou d'isoler la cause d'une erreur HTTP spécifique.
- **Journalisation du serveur Web**: cette option enregistre toutes les transactions HTTP effectuées sur un site Web à l'aide du [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ce rapport s'avère utile pour déterminer les mesures globales d'un site, telles que le nombre de demandes traitées ou le nombre de demandes émanant d'une adresse IP spécifique.

###Diagnostic d'application

Le diagnostic d'application vous permet de capturer des informations générées par une application Web. Les applications ASP.NET peuvent utiliser la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/fr-fr/library/36hhw2t6.aspx) pour enregistrer des informations dans le journal de diagnostic d'application. Par exemple :

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened") ;

Le diagnostic d'application vous permet de résoudre les problèmes affectant l'application en cours d'exécution en émettant des informations lorsque certains fragments de code sont utilisés. Cela est très utile lorsque vous essayez de déterminer pourquoi le code emprunte un chemin spécifique, le plus souvent lorsque le chemin entraîne une erreur ou provoque un comportement indésirable.

Pour plus d'informations sur l'utilisation du diagnostic d'application avec Visual Studio, consultez la page [Résolution des problèmes liés à Sites Web Azure dans Visual Studio](http://www.windowsazure.com/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

> [AZURE.NOTE] Contrairement à la modification du fichier web.config, le fait d'activer le diagnostic d'application ou de modifier les niveaux de journalisation de diagnostic ne recycle pas le domaine dans lequel l'application s'exécute.

L'offre Sites Web Azure journalise également les informations de déploiement quand vous publiez une application sur un site Web. Cela est effectué automatiquement et il n'existe aucun paramètre de configuration pour la journalisation du déploiement. Cette dernière vous permet de déterminer le motif d'échec d'un déploiement. Si vous utilisez, par exemple, un script de déploiement personnalisé, vous pouvez recourir à la journalisation de déploiement pour déterminer la cause de l'échec du script.

<a name="enablediag"></a><h2>Procédure : activation des diagnostics</h2>

Vous pouvez activer les diagnostics en accédant à la page **Configurer** de votre site Web Azure sur le [portail de gestion Azure](https://manage.microsoft.com). Sur la page **Configurer**, utilisez les sections **Diagnostic d'application** et **Diagnostic de site** pour activer la journalisation.

Lorsque vous activez le **Diagnostic d'application**, vous devez également sélectionner le **niveau de journalisation** et indiquer si la journalisation doit être activée dans le **système de fichiers**, le **stockage de tables** ou le **stockage d'objets blob**. Bien que ces trois emplacements de stockage fournissent les mêmes informations de base pour les événements consignés, le **stockage de tables** et le **stockage d'objets blob** consignent davantage d'informations, telles que l'ID d'instance, l'ID de thread et un horodatage plus précis, que lorsque vous optez pour la journalisation dans le **système de fichiers**.

Lorsque vous activez le **Diagnostic de site**, vous devez sélectionner le **stockage** ou le **système de fichiers** pour la **journalisation du serveur Web**. Si vous sélectionnez le **stockage**, vous avez également la possibilité de sélectionner un compte de stockage, puis un conteneur d'objets blob dans lequel les journaux seront écrits. Tous les autres journaux relatifs au **diagnostic de site** sont écrits uniquement dans le système de fichiers.

> [AZURE.NOTE] Les informations stockées dans le **stockage de tables** ou le **stockage d'objets blob** ne sont accessibles qu'à l'aide d'un client de stockage ou d'une application capable d'utiliser directement ces systèmes de stockage. Par exemple, Visual Studio 2013 contient un Explorateur de stockage qui peut être utilisé pour explorer un système de stockage de tables ou d'objets blob, tandis que HDInsight peut accéder aux données stockées dans un stockage d'objets blob. Vous pouvez également écrire une application qui accède à Azure Storage en utilisant l'un des [Kits de développement logiciel (SDK) Azure](http://www.windowsazure.com/fr-fr/downloads/#).

Les paramètres suivants sont disponibles lors de l'activation du **diagnostic d'application** :

* **Niveau de journalisation** : permet de filtrer les données capturées selon le critère **Information**, **Avertissement** ou **Erreur**. Vous pouvez également sélectionner le niveau **Détaillé** pour que toutes les informations générées par l'application soient consignées. **Le niveau de journalisation** peut être défini différemment pour **Système de fichiers**, **Stockage de tables** et **Stockage d'objets blob**.
* **Système de fichiers** : stocke les informations de diagnostic d'application dans le système de fichiers du site Web. Vous pouvez accéder à ces fichiers par FTP ou les télécharger sous la forme d'une archive ZIP en utilisant Azure PowerShell ou les outils en ligne de commande Azure.
* **Stockage de tables** : stocke les informations de diagnostic d'application dans la table et le compte Azure Storage spécifiés.
* **Stockage d'objets blob** : stocke les informations de diagnostic d'application dans le conteneur d'objets blob et le compte Azure Storage spécifiés.
* **Période de rétention** : par défaut, les journaux ne sont pas automatiquement supprimés du **stockage d'objets blob**. Sélectionnez **Set retention** et entrez la période de conservation des journaux (en jours) si vous souhaitez les supprimer automatiquement.

> [AZURE.NOTE] Vous pouvez activer simultanément toute combinaison de système de fichiers, stockage de tables ou stockage d'objets blob. Des configurations de niveau de journalisation individuelles sont également possibles. Vous pouvez, par exemple, consigner les erreurs et les avertissements dans le stockage d'objets blob dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau de journalisation détaillé du système de fichiers.

> [AZURE.NOTE] Les diagnostics peuvent également être activés à partir du module Azure PowerShell via la cmdlet **Set-AzureWebsite**. Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/).

<a name="download"></a><h2>Procédure : Téléchargement des journaux</h2>

Les informations de diagnostic stockées dans le système de fichiers du site Web sont accessibles directement via FTP. Vous pouvez également les télécharger sous la forme d'une archive ZIP en utilisant Azure PowerShell ou les outils en ligne de commande Azure.

La structure de répertoires dans laquelle les journaux sont stockés est la suivante :

* **Journaux d'application** - /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.

* **Échec de la demande de Traces** - /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.

* **Journaux d'erreurs détaillés** - /LogFiles/DetailedErrors/. Ce dossier contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite. 

* **Journaux de serveur Web** - /LogFiles/http/RawLogs. Ce dossier contient un ou plusieurs fichiers texte au format [de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 

* **Journaux de déploiement** - /LogFiles/Git. Ce dossier contient les journaux générés par les processus de déploiement internes utilisés par Sites Web Azure, ainsi que les journaux des déploiements Git.

###FTP

Pour accéder à des informations de diagnostic via FTP, accédez au **Tableau de bord** de votre site Web dans le portail de gestion Azure. Dans la section **Quick Glance**, cliquez sur le lien **FTP Diagnostic Logs** pour accéder aux fichiers journaux via FTP. L'entrée **Deployment/FTP User** indique le nom d'utilisateur à utiliser pour accéder au site FTP.

> [AZURE.NOTE] Si l'entrée **Deployment/FTP User** n'est pas définie ou que vous avez oublié le mot de passe de cet utilisateur, vous pouvez créer un utilisateur et un mot de passe en utilisant le lien **Reset deployment credentials** dans la section **Quick Glance** du **Tableau de bord**.

###Téléchargement avec Azure PowerShell

Pour télécharger les fichiers journaux, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Save-AzureWebSiteLog -Name websitename

Cette commande enregistre les journaux du site Web spécifié par le paramètre **-Name** dans un fichier nommé **logs.zip** dans le répertoire actif.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Téléchargement avec les outils en ligne de commande Azure

Pour télécharger les fichiers journaux à l'aide des outils en ligne de commande Azure, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log download websitename

Cette commande enregistre les journaux du site Web nommé  'websitename' dans un fichier **diagnostics.zip** dans le répertoire actif.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré les outils en ligne de commande Azure de manière à utiliser votre abonnement Azure, consultez la page [Utilisation des outils en ligne de commande Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/command-line-tools/).

<a name="streamlogs"></a><h2>Procédure : Diffusion en continu des journaux</h2>

Lors du développement d'une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Pour ce faire, vous pouvez diffuser ces informations vers votre environnement de développement en utilisant soit Azure PowerShell, soit les outils en ligne de commande Azure.

> [AZURE.NOTE] Certains types de mémoire tampon de journalisation sont écrits dans le fichier journal. Dès lors, il se peut que les événements apparaissent dans le désordre dans le flux. Ainsi, il est possible qu'une entrée du journal d'application qui se produit lorsqu'un utilisateur visite une page soit affichée dans le flux avant l'entrée de journal HTTP correspondante pour la demande de page.

> [AZURE.NOTE] Lors de la diffusion de journaux en continu, les informations écrites dans tout fichier texte stocké dans le dossier **D:\\home\\LogFiles\\** sont également diffusées.

###Diffusion d'informations en continu avec Azure PowerShell

Pour diffuser des informations de journalisation en continu, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

	Get-AzureWebSiteLog -Name websitename -Tail

Une connexion est alors établie avec le site Web spécifié par le paramètre **-Name**, et les informations sont diffusées vers la fenêtre PowerShell quand des événements de journalisation se produisent sur le site Web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **-Message**. Par exemple :

	Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **-Path**. Par exemple :

	Get-AzureWebSiteLog -Name websitename -Tail -Path http

Pour afficher la liste des chemins disponibles, utilisez le paramètre -ListPath.

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d'Azure PowerShell](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Diffusion d'informations en continu avec les outils en ligne de commande Azure

Pour diffuser des informations de journalisation, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

	azure site log tail websitename

Une connexion est alors établie avec le site Web  'websitename', et les informations sont diffusées vers la fenêtre quand des événements de journalisation se produisent sur le site Web. Toute information enregistrée dans un fichier ayant l'extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **--Filter**. Par exemple :

	azure site log tail websitename --filter Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **--Path**. Par exemple :

	azure site log tail websitename --path http

> [AZURE.NOTE] Si vous n'avez pas installé ou configuré les outils en ligne de commande Azure de manière à utiliser votre abonnement Azure, consultez la page [Utilisation des outils en ligne de commande Azure](http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/command-line-tools/).

<a name="understandlogs"></a><h2>Procédure : Présentation des journaux de diagnostic</h2>

###Journaux de diagnostic d'application

Le diagnostic d'application stocke les informations dans un format spécifique pour les applications .NET selon que vous stockez les journaux dans le système de fichiers, le stockage de tables ou le stockage d'objets blob. L'ensemble de base des données stockées est le même dans les trois types de stockage, à savoir : date et heure auxquelles l'événement s'est produit, ID de processus qui a généré l'événement, type d'événement (informations, avertissement, erreur) et message d'événement.

__File system__

Chaque ligne consignée dans le système de fichiers ou reçue par le biais d'une diffusion en continu se présente au format suivant :

	{Date}  PID[{process id}] {event type/level} {message}

Par exemple, un événement d'erreur se présente comme suit :

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Parmi les trois méthodes disponibles, la journalisation d'informations dans le système de fichiers est celle qui fournit les informations les plus élémentaires. L'heure, l'ID de processus, le niveau d'événement et le message sont, en effet, les seules informations fournies.

__Table storage__

Lorsque vous consignez des informations dans le stockage de tables, des propriétés supplémentaires sont utilisées pour faciliter la recherche des données stockées dans la table, ainsi que des informations plus précises sur l'événement. Les propriétés suivantes (colonnes) sont utilisées pour chaque entité (ligne) stockée dans la table.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Date/heure de l'événement au format aaaaMMjjHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A GUID value that uniquely identifies this entity</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">Date et heure auxquelles l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Nom du site Web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">ID d'événement de l'événement<br>Valeur par défaut 0 si aucune n'est spécifiée</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID du processus</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Message détaillé sur l'événement</td>
</tr>
</table>

__Blob storage__

Lorsque vous consignez des données dans un stockage d'objets blob, elles sont stockées au format CSV (valeurs séparées par des virgules). Comme c'est le cas avec le stockage de tables, des champs supplémentaires sont consignés afin de fournir des informations plus précises sur l'événement. Les propriétés suivantes sont utilisées pour chaque ligne du fichier CSV :

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">Date et heure auxquelles l'événement s'est produit</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Nom du site Web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">ID d'événement de l'événement<br>Valeur par défaut 0 si aucune n'est spécifiée</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID du processus</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Message détaillé sur l'événement</td>
</tr>
</table>

Les données stockées dans un objet blob se présentent comme suit :

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La première ligne du journal contient les en-têtes de colonne, tels qu'ils sont représentés dans cet exemple.

###Suivi des demandes ayant échoué

Le suivi des demandes ayant échoué est stocké dans des fichiers XML nommés __fr######.xml__. Pour faciliter la consultation des informations consignées, une feuille de style XSL nommée __freb.xsl__ est fournie dans le même répertoire que les fichiers XML. Lorsque vous ouvrez l'un des fichiers XML dans Internet Explorer, la feuille de style XSL est utilisée afin de fournir un affichage formaté des informations de suivi. Les informations se présentent alors comme suit :

![failed request viewed in the browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

###Journaux d'erreurs détaillés

Les journaux d'erreurs détaillés sont des documents HTML qui fournissent des informations plus détaillées sur les erreurs HTTP qui se sont produites. Puisqu'il s'agit simplement de documents HTML, ils peuvent être consultés à l'aide d'un navigateur Web.

###Journaux de serveur Web

Les journaux de serveur Web utilisent le [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent être lues à l'aide d'un éditeur de texte ou analysées à l'aide d'utilitaires tels que [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Les journaux générés par les sites Web Azure ne gèrent pas les champs __s-computername__, __s-ip__, ou __cs-version__.

<a name="nextsteps"></a><h2>Étapes suivantes</h2>

- [Surveillance de sites Web](/fr-fr/manage/services/web-sites/how-to-monitor-websites/)
- [Didacticiel - Résolution des problèmes des sites Web](/fr-fr/develop/net/best-practices/troubleshooting-web-sites/)
- [Résolution des problèmes liés à Sites Web Azure dans Visual Studio](/fr-fr/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Analyse des journaux de site Web dans HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)



<!--HONumber=42-->
