<properties 
	pageTitle="Conversion d'un site WordPress en multisite" 
	description="Découvrez comment prendre un site Web WordPress existant créé via la galerie dans Azure et la convertir en multisite WordPress" 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/28/2014" 
	ms.author="tomfitz"/>





# Conversion d'un site WordPress en multisite

* Par [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

Ce didacticiel vous explique comment prendre un site Web WordPress existant créé via la galerie dans Azure et le convertir en une installation multisite WordPress. En outre, vous allez apprendre à attribuer un domaine personnalisé à chacun des sous-sites de votre installation.

Ce didacticiel part du principe que vous disposez d'une installation existante de WordPress. Si ce n'est pas le cas, suivez les instructions fournies dans la page [Création d'un site Web WordPress à partir de la galerie dans Azure][website-from-gallery].

La conversion d'une installation existante d'un site unique WordPress en multisite est généralement assez simple et un grand nombre d'étapes initiales sont tirées de la page [Création d'un réseau][wordpress-codex-create-a-network] sur le [Codex WordPress](http://codex.wordpress.org).

Commençons.

## Autorisation du multisite

Vous devez d'abord activer le multisite via le fichier `wp-config.php` avec la constante **WP\_ALLOW\_MULTISITE**. Il existe deux méthodes permettant de modifier les fichiers de votre site Web : via FTP et via Git. Si vous ne savez pas comment configurer l'une de ces méthodes, consultez les didacticiels suivants :

* [Site Web PHP avec MySQL et FTP][website-w-mysql-and-ftp-ftp-setup]

* [Site Web PHP avec MySQL et FTP][website-w-mysql-and-git-git-setup]

Ouvrez le fichier `wp-config.php` avec l'éditeur de votre choix et ajoutez ce qui suit au-dessus de la ligne `/* That's all, stop editing! Happy blogging. */`.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

Assurez-vous d'enregistrer le fichier et de le renvoyer au serveur !

## Configuration réseau

Connectez-vous à la zone *wp-admin* de votre site Web. Vous devez voir un nouvel élément nommé **Réseau** sous le menu **Outils**. Cliquez sur **Réseau** et entrez les informations sur votre réseau.

![Network Setup Screen][wordpress-network-setup]

Ce didacticiel utilise le schéma de site *Sub-directories* parce qu'il doit toujours fonctionner. Nous définirons des domaines personnalisés pour chaque sous-site ultérieurement dans le didacticiel. Toutefois, il doit être possible de configurer une installation de sous-domaine si vous mappez un domaine via le portail et que vous configurez correctement le DNS générique.

Pour plus d'informations sur les configurations de sous-domaine et de sous-dossier, consultez l'article [Types de réseau multisite][wordpress-codex-types-of-networks] sur le Codex WordPress.

## Activation du réseau

Le réseau est maintenant configuré dans la base de données, mais il reste une étape pour activer cette fonctionnalité. Finalisez les paramètres `wp-config.php` et vérifiez que `web.config` route correctement chaque site.


Une fois que vous avez cliqué sur le bouton **Installer** de la page *Network Setup*, WordPress essaie de mettre à jour les fichiers `wp-config.php` et `web.config`. Toutefois, vous devez toujours vérifier les fichiers pour vous assurer que les mises à jour ont été correctement effectuées. Si ce n'est pas le cas, cet écran vous indique les mises à jour nécessaires. Modifiez et enregistrez les fichiers.


Après avoir effectué ces mises à jour, vous devez vous déconnecter et vous reconnecter au tableau de bord wp-admin.

Un menu supplémentaire nommé **Mes sites** doit désormais être présent sur la page d'administration. Celui-ci vous permet de contrôler votre nouveau réseau via le tableau de bord **Admin du réseau**.

# Ajout de domaines personnalisés

Le plug-in [WordPress MU Domain Mapping][Wordpress-plugin-wordpress-mu-domain-mapping] facilite grandement l'ajout de domaines personnalisés à un site de votre réseau. Pour que ce plug-in fonctionne correctement, vous devez effectuer quelques configurations supplémentaires sur le portail et auprès de votre bureau d'enregistrement de domaines.

## Activation du mappage de domaine sur le site Web

Le mode de site Web gratuit par défaut ne prend pas en charge l'ajout de domaines personnalisés dans Sites Web Azure. Vous devez basculer sur le mode Partagé ou Standard. Pour ce faire :

* Connectez-vous au portail Azure et recherchez votre site Web. 
* Cliquez sur l'onglet **Mettre à l'échelle** dans la zone de contenu principale.
* Sous **Général**, sélectionnez *SHARED* ou *STANDARD*.
* Cliquez sur **Enregistrer**.

Il se peut que vous receviez un message vous demandant de vérifier la modification et vous informant que votre site Web peut désormais générer des frais en fonction de votre utilisation et des autres configurations définies.

Le traitement des nouveaux paramètres prend quelques secondes ; il est maintenant temps de commencer à configurer votre domaine.

## Vérification de votre domaine

Avant que Sites Web Azure vous autorise à mapper un domaine au site, vous devez vérifier que vous êtes autorisé à mapper le domaine. Pour ce faire, vous devez ajouter un nouvel enregistrement CNAME à votre entrée DNS.

* Connectez-vous au gestionnaire DNS de votre domaine.
* Créer un enregistrement CNAME *awverify*.
* Pointez *awverify* vers *awverify.YOUR_DOMAIN.azurewebsites.net*.

Les modifications apportées au DNS peuvent mettre du temps à être effectives. Par conséquent, si les étapes suivantes ne fonctionnent pas immédiatement, faites une pause, puis revenez et réessayez.

## Ajout du domaine au site Web

Revenez sur votre site Web via le portail Azure, puis cliquez sur l'onglet **CONFIGURE**. Le bouton **MANAGE DOMAINS** doit être disponible. Cliquez dessus.

La boîte de dialogue *Manage custom domains* s'affiche. C'est là que vous allez entrer tous les domaines que vous souhaitez attribuer à votre site Web. Si un domaine n'est pas répertorié ici, cela signifie qu'il n'est pas disponible pour le mappage à l'intérieur de WordPress, indépendamment de la méthode de configuration du DNS du domaine.

![Boîte de dialogue Manage custom domains][wordpress-manage-domains]

Après avoir tapé votre domaine dans la zone de texte, Azure vérifie l'enregistrement CNAME *awverify* que vous avez créé précédemment. Si la propagation du DNS n'est pas complète, un indicateur rouge s'affiche. Si elle est terminée, vous voyez une coche verte. 

Notez l'adresse IP affichée en bas de la boîte de dialogue. Elle est nécessaire pour configurer l'enregistrement A pour votre domaine.

## Configuration de l'enregistrement A du domaine

Si les autres étapes sont effectuées, vous pouvez désormais attribuer le domaine à votre site Web Azure via un enregistrement DNS A. 

Il est important de remarquer que les sites Web Azure acceptent les enregistrements CNAME et A. Vous *devez* toutefois utiliser un enregistrement A pour activer correctement le mappage de domaine. Un enregistrement CNAME ne peut pas être transféré vers un autre CNAME, qui correspond à ce qu'Azure a créé pour vous avec VOTRE_DOMAINE.azurewebsites.net.

À l'aide de l'adresse IP de l'étape précédente, revenez sur votre gestionnaire DNS et configurez l'enregistrement A de manière ce qu'il pointe sur cette IP.


## Installation et configuration du plug-in

Actuellement, la fonctionnalité multisite de WordPress ne dispose pas d'une méthode intégrée pour mapper les domaines personnalisés. Toutefois, il existe un plug-in nommé [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] qui ajoute cette fonctionnalité pour vous. Connectez-vous à la partie Admin du réseau de votre site et installez le plug-in **WordPress MU Domain Mapping**.

Après avoir installé et activé le plug-in, accédez à **Paramètres** > **Mappage de domaine** pour configurer le plug-in. Dans la première zone de texte, *Server IP Address*, entrez l'adresse IP utilisée pour configurer l'enregistrement A du domaine. Définissez les Options du domaine de votre choix (les valeurs par défaut font souvent l'affaire), puis cliquez sur **Enregistrer**.

## Mappage du domaine

Accédez au **Tableau de bord** du site vers lequel vous souhaitez mapper le domaine. Cliquez sur **Outils** > **Mappage de domaine**, tapez le nouveau domaine dans la zone de texte, puis cliquez sur **Ajouter**.

Par défaut, le nouveau domaine est réécrit sur le domaine de site généré automatiquement. Si vous souhaitez que l'ensemble du trafic soit envoyé au nouveau domaine, activez *Domaine principal pour ce blog* avant de procéder à l'enregistrement. Vous pouvez ajouter un nombre de domaines illimité à un site, mais un seul peut être défini comme domaine principal.

## Répétition de l'opération

Sites Web Azure vous permet d'ajouter un nombre de domaines illimité à un site Web. Pour ajouter un autre domaine, vous devez exécuter les sections **Vérification de votre domaine** et **Configuration de l'enregistrement A du domaine** pour chaque domaine.	

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/fr-fr/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png



<!--HONumber=42-->
