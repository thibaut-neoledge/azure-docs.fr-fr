<properties 
	pageTitle="Conversion de WordPress en WordPress multisite dans Azure App Service" 
	description="Découvrez comment convertir une application web WordPress créée par le biais de la galerie dans Azure, en WordPress multisite" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>



# Conversion de WordPress en WordPress multisite dans Azure App Service

## Vue d'ensemble

*Par [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Ce didacticiel vous explique comment prendre une application Web WordPress existante créée par le biais de la galerie dans Azure et la convertir en une installation WordPress multisite. En outre, vous allez apprendre à attribuer un domaine personnalisé à chacun des sous-sites de votre installation.

Ce didacticiel part du principe que vous disposez d'une installation existante de WordPress. Si ce n'est pas le cas, suivez les instructions fournies dans la page [Création d'un site Web WordPress à partir de la galerie dans Azure][website-from-gallery].

La conversion d'une installation existante d'un site unique WordPress en multisite est généralement assez simple et un grand nombre d'étapes initiales sont tirées de la page [Création d'un réseau][wordpress-codex-create-a-network] sur le [Codex WordPress](http://codex.wordpress.org).

Commençons.

## Autorisation du multisite

Vous devez d’abord activer le multisite via le fichier `wp-config.php` avec la constante **WP\_ALLOW\_MULTISITE**. Vous disposez de deux méthodes pour modifier vos fichiers d’application web : via FTP et via Git. Si vous ne savez pas comment configurer l'une de ces méthodes, consultez les didacticiels suivants :

* [Site web PHP avec MySQL et FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site web PHP avec MySQL et Git][website-w-mysql-and-git-git-setup]

Ouvrez le fichier `wp-config.php` avec l’éditeur de votre choix et ajoutez ce qui suit au-dessus de la ligne `/* That's all, stop editing! Happy blogging. */`.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

Assurez-vous d'enregistrer le fichier et de le renvoyer au serveur !

## Configuration réseau

Connectez-vous à la zone *wp-admin* de votre application web. Vous devez voir un nouvel élément nommé **Réseau** sous le menu **Outils**. Cliquez sur **Réseau** et entrez les informations sur votre réseau.

![Écran Configuration réseau][wordpress-network-setup]

Ce didacticiel utilise le schéma de site *Sous-dossiers* parce qu'il doit toujours fonctionner. Nous définirons des domaines personnalisés pour chaque sous-site ultérieurement dans le didacticiel. Toutefois, il doit être possible de configurer l’installation du sous-domaine, si vous mappez un domaine via le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) et que vous configurez correctement le DNS générique.

Pour plus d'informations sur les configurations de sous-domaine et de sous-dossier, consultez l'article [Types de réseau multisite][wordpress-codex-types-of-networks] sur le Codex WordPress.

## Activation du réseau

Le réseau est maintenant configuré dans la base de données, mais il reste une étape pour activer cette fonctionnalité. Finalisez les paramètres `wp-config.php` et vérifiez que `web.config` achemine correctement chaque site.


Une fois que vous avez cliqué sur le bouton **Installer** de la page *Réseau*, WordPress essaie de mettre à jour les fichiers `wp-config.php` et `web.config`. Toutefois, vous devez toujours vérifier les fichiers pour vous assurer que les mises à jour ont été correctement effectuées. Si ce n'est pas le cas, cet écran vous indique les mises à jour nécessaires. Modifiez et enregistrez les fichiers.


Après avoir effectué ces mises à jour, vous devez vous déconnecter et vous reconnecter au tableau de bord wp-admin.

Un menu supplémentaire nommé **Mes sites** doit désormais être présent sur la page d'administration. Celui-ci vous permet de contrôler votre nouveau réseau via le tableau de bord **Admin du réseau**.

## Ajout de domaines personnalisés

Le plug-in [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] facilite grandement l'ajout de domaines personnalisés à un site de votre réseau. Pour que ce plug-in fonctionne correctement, vous devez effectuer quelques configurations supplémentaires sur le portail et auprès de votre bureau d'enregistrement de domaines.

## Activer le mappage de domaine sur l’application web

Le mode **Gratuit** du plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) ne prend pas en charge l’ajout de domaines personnalisés à Web Apps. Vous devez basculer vers le mode **Partagé** ou **Standard**. Pour ce faire :

* Connectez-vous au portail Azure et recherchez votre application web. 
* Cliquez sur l'onglet **Scale** dans la zone de contenu principale.
* Sous **Général**, sélectionnez *PARTAGÉ* ou *STANDARD*.
* Cliquez sur **Enregistrer**.

Il se peut que vous receviez un message vous demandant de vérifier la modification et vous informant que votre application web peut désormais générer des frais en fonction de votre utilisation et des autres configurations définies.

Le traitement des nouveaux paramètres prend quelques secondes ; il est maintenant temps de commencer à configurer votre domaine.

## Vérification de votre domaine

Avant que la fonctionnalité Azure Web Apps vous permette de mapper un domaine sur le site, vous devez commencer par vérifier que vous êtes autorisé à effectuer cette opération. Pour ce faire, vous devez ajouter un nouvel enregistrement CNAME à votre entrée DNS.

* Connectez-vous au gestionnaire DNS de votre domaine.
* Créez un enregistrement CNAME *awverify*.
* Faites pointer *awverify* sur *awverify.VOTRE\_DOMAINE.azurewebsites.net*.

Les modifications apportées au DNS peuvent mettre du temps à être effectives. Par conséquent, si les étapes suivantes ne fonctionnent pas immédiatement, faites une pause, puis revenez et réessayez.

## Ajouter le domaine à l’application web

Revenez à votre application web dans le portail Azure, cliquez sur **Paramètres** puis sur **Domaines personnalisés et SSL**.

Lorsque les *Paramètres SSL* s’affichent, vous voyez apparaître les champs dans lesquels spécifier tous les domaines que vous souhaitez attribuer à votre application web. Si un domaine n'est pas répertorié ici, cela signifie qu'il n'est pas disponible pour le mappage à l'intérieur de WordPress, indépendamment de la méthode de configuration du DNS du domaine.

![Boîte de dialogue Manage custom domains][wordpress-manage-domains]

Une fois que vous avez tapé votre domaine dans la zone de texte, Azure vérifie l’enregistrement CNAME que vous avez créé précédemment. Si la propagation du DNS n'est pas complète, un indicateur rouge s'affiche. Si elle est terminée, vous voyez une coche verte.

Notez l'adresse IP affichée en bas de la boîte de dialogue. Elle est nécessaire pour configurer l'enregistrement A pour votre domaine.

## Configuration de l'enregistrement A du domaine

Si les autres étapes se sont correctement déroulées, vous pouvez désormais attribuer le domaine à votre application web Azure via un enregistrement DNS A.

Il est important de remarquer que les applications web Azure acceptent les enregistrements CNAME et A. Toutefois, vous *devez* utiliser un enregistrement A pour activer le mappage de domaine approprié. Un enregistrement CNAME ne peut pas être transféré vers un autre CNAME, qui correspond à ce qu'Azure a créé pour vous avec VOTRE\_DOMAINE.azurewebsites.net.

À l'aide de l'adresse IP de l'étape précédente, revenez sur votre gestionnaire DNS et configurez l'enregistrement A de manière ce qu'il pointe sur cette IP.


## Installation et configuration du plug-in

Actuellement, la fonctionnalité multisite de WordPress ne dispose pas d'une méthode intégrée pour mapper les domaines personnalisés. Toutefois, il existe un plug-in nommé [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] qui ajoute cette fonctionnalité pour vous. Connectez-vous à la partie Admin du réseau de votre site et installez le plug-in **WordPress MU Domain Mapping**.

Après avoir installé et activé le plug-in, accédez à **Paramètres** > **Mappage de domaine** pour configurer le plug-in. Dans la première zone de texte, *Adresse IP du serveur*, entrez l'adresse IP utilisée pour configurer l'enregistrement A du domaine. Définissez les *Options du domaine* de votre choix (les valeurs par défaut font souvent l'affaire), puis cliquez sur **Enregistrer**.

## Mappage du domaine

Accédez au **Tableau de bord** du site vers lequel vous souhaitez mapper le domaine. Cliquez sur **Outils** > **Mappage de domaine**, tapez le nouveau domaine dans la zone de texte, puis cliquez sur **Ajouter**.

Par défaut, le nouveau domaine est réécrit sur le domaine de site généré automatiquement. Si vous souhaitez que l'ensemble du trafic soit envoyé au nouveau domaine, activez *Domaine principal pour ce blog* avant de procéder à l'enregistrement. Vous pouvez ajouter un nombre de domaines illimité à un site, mais un seul peut être défini comme domaine principal.

## Répétition de l'opération

La fonctionnalité Azure Web Apps vous permet d’ajouter un nombre de domaines illimité à une application web. Pour ajouter un autre domaine, vous devez exécuter les sections **Vérification de votre domaine** et **Configuration de l'enregistrement A du domaine** pour chaque domaine.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 

<!---HONumber=Oct15_HO2-->