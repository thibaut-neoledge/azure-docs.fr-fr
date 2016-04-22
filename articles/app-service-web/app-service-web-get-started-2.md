<properties 
	pageTitle="Prise en main des applications web dans Azure App Service, 2e partie." 
	description="Ajoutez des capacités opérationnelles essentielles à votre application web en quelques clics dans App Service." 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="03/17/2016" 
	ms.author="cephalin"
/>

# Prise en main d’Azure App Services, 2e partie

Dans [Prise en main d’Azure App Service](app-service-web-get-started.md), vous avez déployé une application web vers Azure App Service et pouvez à présent installer des mises à jour rapidement et facilement. Cet article vous montrera comment ajouter des fonctionnalités essentielles au bon fonctionnement de votre application déployée. Vous découvrirez ainsi que le service App Service est bien plus qu’un simple hébergeur de sites web. Il apporte des fonctionnalités de niveau professionnel à votre application ; cela vous permet de consacrer votre énergie créative à l’amélioration de l’expérience de vos utilisateurs, au lieu d’avoir à affronter des exigences en matière de sécurité, d’évolutivité, de performances et de gestion, etc.

En quelques clics, vous apprendrez à :

- appliquer l’authentification aux utilisateurs ;
- appliquer la mise à l’échelle automatique de votre application ;
- recevoir des alertes sur les performances de votre application.

Vous pouvez suivre les étapes du tutoriel qui suivent, quel que soit l’exemple d’application que vous avez déployé dans l’article précédent.

## Authentifiez vos utilisateurs

À présent, découvrez la facilité avec laquelle vous pouvez ajouter des authentifications à votre application.

1. Dans le panneau du portail de l’application que vous venez d’ouvrir, cliquez sur **Paramètres** > **Authentification/Autorisation**. ![Authentification - panneau Paramètres](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Cliquez sur **Activé** pour activer l’authentification. ![Authentification - activer](./media/app-service-web-get-started/aad-login-auth-on.png)
    
4. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**. ![Authentification - sélectionner Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**, puis sur **OK**. Les paramètres par défaut créent une nouvelle application Azure AD dans votre répertoire par défaut. ![Authentification - configuration express](./media/app-service-web-get-started/aad-login-express.png)

6. Cliquez sur **Save**. ![Authentification - enregistrer la configuration](./media/app-service-web-get-started/aad-login-save.png)

    Une fois que la modification a réussi, la cloche de notification devient verte, et un message positif s’affiche.

7. Revenez au panneau du portail de votre application, puis cliquez sur le lien **URL** (ou sur **Parcourir** dans la barre de menus). Le lien est une adresse HTTP. ![Authentification - accéder à l’URL](./media/app-service-web-get-started/aad-login-browse-click.png) Lorsque celle-ci ouvre l’application dans un nouvel onglet, la zone URL effectue plusieurs redirections avant d’ouvrir votre application avec une adresse HTTPS. Vous pouvez voir que vous êtes déjà connecté au compte Microsoft associé à votre abonnement Azure, et que celui-ci vous connecte automatiquement à l’application. ![Authentification - connecté](./media/app-service-web-get-started/aad-login-browse-http-postclick.png) Donc, si vous ouvrez une session non authentifiée dans un autre explorateur, une page de connexion s’affiche lorsque vous accédez à l’URL de l’application : ![Authentification - page de connexion](./media/app-service-web-get-started/aad-login-browse.png) Si vous n’avez jamais utilisé Azure Active Directory, le répertoire par défaut ne contient probablement pas d’utilisateur Azure AD. Dans ce cas, le seul compte ici est probablement le compte Microsoft avec votre abonnement Azure. C’est pour cette raison que vous avez été automatiquement connecté à l’application dans le même navigateur plus tôt. Vous pouvez utiliser ce même compte Microsoft pour vous connecter sur cette page de connexion.

Félicitations, vous authentifiez désormais tout le trafic vers votre site.

Vous avez peut-être remarqué que vous pouvez en faire beaucoup plus dans le panneau **Authentification / Autorisation**, c’est-à-dire :

- activer la connexion à partir de réseaux sociaux ;
- activer plusieurs options de connexion ;
- modifier le comportement par défaut lorsque les utilisateurs accèdent à votre application pour la première fois.

App Service propose une solution clé en main pour une partie des besoins d’authentification les plus courants. Vous n’avez donc pas besoin de fournir la logique d’authentification vous-même. Pour plus d’informations, consultez [Authentification/Autorisation Service App](/blog/announcing-app-service-authentication-authorization/).

## Adaptez la taille de votre application

Vous allez à présent apprendre à adapter la taille de votre application. Vous pouvez adapter la taille de votre application App Service de deux manières :

- [Monter en puissance](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : lorsque vous faites monter en puissance une application App Service, vous modifiez le niveau tarifaire du plan App Service auquel appartient l’application. La mise l’échelle vous permet d’améliorer le processeur et d’obtenir une mémoire et un espace disque plus importants ainsi que d’autres fonctionnalités telles que des instances de machine virtuelle dédiées, une mise à l’échelle automatique, un contrat SLA à 99,95 %, des domaines personnalisés, des certificats SSL personnalisés, des emplacements de déploiement, la sauvegarde, la restauration et bien plus encore. Des niveaux supérieurs fournissent davantage de fonctionnalités à votre application App Service.  
- [Augmenter la taille des instances](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : lorsque vous augmentez la taille des instances de l’application App Service, cela modifie le nombre d’instances de machines virtuelles qui exécutent votre application (ou les applications du même plan App Service). Avec le niveau standard ou supérieur, vous pouvez activer la mise à l’échelle automatique des instances de machines virtuelles en fonction des métriques de performances. 

Sans plus attendre, préparons à présent la mise à l’échelle automatique de votre application.

1. Nous allons tout d’abord procéder à la montée en puissance pour activer la mise à l’échelle automatique. Dans le panneau de portail de votre application, cliquez sur **Paramètres** > ** Mettre à l’échelle, (Plan App Service)**. ![Monter en puissance - panneau Paramètres](./media/app-service-web-get-started/scale-up-settings.png)

2. Faites défiler la page, puis sélectionnez le niveau **S1 Standard** (le plus bas niveau prenant en charge la mise à l’échelle automatique, encerclé dans la capture d’écran), puis cliquez sur **Sélectionner**. ![Monter en puissance - choisir un niveau](./media/app-service-web-get-started/scale-up-select.png)

    Vous avez terminé la montée en puissance.
    
    >[AZURE.IMPORTANT] Ce niveau épuisera votre crédit d’évaluation. Si vous avez un compte de paiement à l’utilisation, il occasionne des frais pour votre compte.
    
3. Ensuite, nous allons configurer la mise à l’échelle automatique. Dans le panneau de portail de votre application, cliquez sur **Paramètres** > ** Mettre à l’échelle (Plan App Service)**. ![Augmenter la taille des instances - panneau Paramètres](./media/app-service-web-get-started/scale-out-settings.png)

4. Changez **Mettre à l’échelle selon** sur **Pourcentage UC**. Les curseurs sous la liste déroulante changent en conséquence. Ensuite, définissez une plage d’**Instances** comprise entre **1** et **2** et une **Plage cible** entre **40** et **80**. Pour ce faire, effectuez une saisie dans les zones ou déplacez les curseurs. ![Augmenter la taille des instances - configurer la mise à l’échelle automatique](./media/app-service-web-get-started/scale-out-configure.png)
    
    Selon cette configuration, votre application se met à l’échelle automatiquement lorsque l’utilisation de l’UC est supérieure à 80 % et lorsque l’utilisation de l’UC est inférieure à 40 %.
    
5. Cliquez sur **Enregistrer** dans la barre des tâches.

Félicitations, votre application se met à l’échelle automatiquement.

Vous avez peut-être remarqué que vous pouvez en faire beaucoup plus dans le panneau **Paramètres de mise à l’échelle**, et notamment :

- l’augmentation manuelle de la taille des instances à un certain nombre d’instances ;
- la mise à l’échelle selon d’autres mesures de performance, telles que le pourcentage de mémoire ou la longueur de la file d’attente de disque ;
- la personnalisation du comportement de mise à l’échelle lorsqu’une règle de performance est déclenchée ;
- la mise à l’échelle automatique selon un calendrier prévu ;
- le réglage du comportement de mise à l’échelle automatique pour un événement ultérieur.

Pour plus d’informations sur la mise à l’échelle de votre application, consultez [Changement de niveau de tarification dans Azure App Service](../app-service/app-service-scale.md). Pour plus d’informations sur l’augmentation de la taille des instances, consultez [Mise à l’échelle manuelle ou automatique du nombre d’instances](../azure-portal/insights-how-to-scale.md).

## Recevoir des alertes pour votre application

Maintenant que votre application est mise à l’échelle, que se passe-t-il lorsqu’il atteint le nombre maximal d’instances (2) et lorsque l’utilisation du processeur est supérieure à l’utilisation souhaitée (80 %) ? Vous pouvez configurer une alerte pour vous informer de cette situation afin de faire davantage monter en puissance votre application, par exemple. Nous allons rapidement configurer une alerte pour ce scénario.

1. Dans le panneau du portail de votre application, cliquez sur **Outils** > **Alertes**. ![Alertes - panneau Paramètres](./media/app-service-web-get-started/alert-settings.png)

2. Cliquez sur **Ajouter une alerte**. Ensuite, dans la zone **Ressources**, sélectionnez la ressource qui se termine par **(serverfarms)**. C’est votre plan App Service. ![Alertes - ajouter une alerte pour un plan App Service](./media/app-service-web-get-started/alert-add.png)

3. Définissez **Nom** sur `CPU Maxed`, **Métrique** sur **Pourcentage UC**, et **Seuil** sur `90`, puis sélectionnez **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** et cliquez sur **OK**. ![Alertes - configurer une alerte](./media/app-service-web-get-started/alert-configure.png)
    
    Après la création de l’alerte par Azure, vous la verrez dans le panneau **Alertes**. ![Alertes - vue finale](./media/app-service-web-get-started/alert-done.png)

Félicitations, vous recevez désormais des alertes.

Ce paramètre d’alerte vérifie l’utilisation du processeur toutes les cinq minutes. Si celle-ci dépasse 90 %, vous (ainsi que toute personne autorisée) recevrez un message d’alerte. Pour afficher tous les utilisateurs autorisés à recevoir les alertes, revenez au panneau du portail de votre application et cliquez sur le bouton **Accès**. ![Afficher qui reçoit des alertes](./media/app-service-web-get-started/alert-rbac.png)

Vous verrez que les **Administrateurs des abonnements** sont déjà les **Propriétaires** de l’application. Ce groupe vous inclut si vous êtes l’administrateur du compte de votre abonnement Azure (par exemple, votre abonnement d’évaluation). Pour plus d’informations sur le contrôle d’accès en fonction du rôle Azure, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## Étapes suivantes

Pendant la configuration de l’alerte, vous avez peut-être remarqué un important ensemble d’outils dans le panneau **Outils**. Ici, vous pouvez résoudre les problèmes, analyser des performances, détecter les failles, gérer les ressources, interagir avec la console de machine virtuelle et ajouter des extensions utiles. Nous vous invitons à cliquer sur chacun d’entre eux pour découvrir les outils simples mais puissants à votre disposition.

En outre, découvrez comment en faire plus avec l’application déployée. Voici une liste partielle :

- [Acheter et configurer un nom de domaine personnalisé.](custom-dns-web-site-buydomains-web-app.md)
- [Configurer des environnements intermédiaires](web-sites-staged-publishing.md)
- [Configurer un déploiement continu](web-sites-publish-source-control.md)
- [Sauvegarder votre application](web-sites-backup.md)
- [Activer la journalisation des diagnostics](web-sites-enable-diagnostic-log.md)
- [Accéder à des ressources locales](web-sites-hybrid-connection-get-started.md)
- [Découvrir le fonctionnement d’App Service](../app-service/app-service-how-works-readme.md) 

<!----HONumber=AcomDC_0323_2016-->