---
title: Ajouter des fonctionnalités à votre première application web
description: Ajouter des fonctionnalités intéressantes à votre application web en quelques minutes.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/12/2016
ms.author: cephalin

---
# Ajouter des fonctionnalités à votre première application web
Dans [Prise en main des applications web dans Azure App Service](app-service-web-get-started.md), vous avez déployé un exemple d’application web vers [Azure App Service](../app-service/app-service-value-prop-what-is.md). Cet article vous montrera comment ajouter des fonctionnalités exceptionnelles à votre application web déployée. En quelques minutes, vous allez :

* appliquer l’authentification aux utilisateurs ;
* mettre automatiquement à l’échelle votre application ;
* recevoir des alertes sur les performances de votre application.

Vous pouvez suivre les étapes du tutoriel qui suivent, quel que soit l’exemple d’application que vous avez déployé dans l’article précédent.

Les trois activités dans ce didacticiel ne sont que quelques exemples des nombreuses fonctionnalités utiles dont vous tirez profit lorsque vous déployez votre application web vers App Service. La plupart des fonctionnalités sont disponibles dans le niveau **Gratuit** (dans lequel s’exécute votre première application). Vous pouvez utiliser vos crédits d’évaluation gratuite afin d’essayer les fonctionnalités nécessitant des niveaux tarifaires plus élevés. Soyez assuré que votre application web restera dans le niveau **Gratuit** tant que vous ne changez pas explicitement son niveau tarifaire.

> [!NOTE]
> L’application web que vous avez créée avec l’interface CLI Azure s’exécute dans le niveau **Gratuit**, ce qui permet uniquement l’exécution d’une seule machine virtuelle partagée avec des quotas de ressources. Pour plus d’informations sur ce que vous pouvez obtenir avec le niveau **Gratuit**, consultez la section [Limites App Service](../azure-subscription-service-limits.md#app-service-limits).
> 
> 

## Authentifiez vos utilisateurs
À présent, découvrez la facilité avec laquelle vous pouvez ajouter des authentifications à votre application. Pour plus d’informations, consultez [Expanding App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) (Étendre l’authentification/autorisation App Service).

1. Dans le panneau du portail de l’application que vous venez d’ouvrir, cliquez sur **Paramètres** > **Authentification/Autorisation**. ![Authentification - panneau Paramètres](./media/app-service-web-get-started/aad-login-settings.png)
2. Cliquez sur **Activé** pour activer l’authentification.
3. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**. ![Authentification - sélectionner Azure AD](./media/app-service-web-get-started/aad-login-config.png)
4. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**, puis sur **OK**. Les paramètres par défaut créent une nouvelle application Azure AD dans votre répertoire par défaut. ![Authentification - configuration express](./media/app-service-web-get-started/aad-login-express.png)
5. Cliquez sur **Save**. ![Authentification - enregistrer la configuration](./media/app-service-web-get-started/aad-login-save.png)
   
    Une fois que la modification a réussi, la cloche de notification devient verte, et un message positif s’affiche.
6. Revenez au panneau du portail de votre application, puis cliquez sur le lien **URL** (ou sur **Parcourir** dans la barre de menus). Le lien est une adresse HTTP. ![Authentification - accéder à l’URL](./media/app-service-web-get-started/aad-login-browse-click.png) Lorsque celle-ci ouvre l’application dans un nouvel onglet, la zone URL effectue plusieurs redirections avant d’ouvrir votre application avec une adresse HTTPS. Vous pouvez voir que vous êtes déjà connecté à votre abonnement Azure, et automatiquement authentifié dans l’application. ![Authentification - connecté](./media/app-service-web-get-started/aad-login-browse-http-postclick.png) Par conséquent, si vous ouvrez une session non authentifiée dans un autre navigateur, un écran de connexion s’affiche lorsque vous accédez à la même URL.
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Si vous n’avez jamais utilisé Azure Active Directory, le répertoire par défaut peut ne pas contenir d’utilisateurs Azure AD. Dans ce cas, le seul compte ici est probablement le compte Microsoft avec votre abonnement Azure. C’est pour cette raison que vous avez été automatiquement connecté à l’application dans le même navigateur plus tôt. Vous pouvez utiliser ce même compte Microsoft pour vous connecter sur cette page de connexion.

Félicitations, vous authentifiez désormais tout le trafic vers votre application web.

Vous avez peut-être remarqué que vous pouvez en faire beaucoup plus dans le panneau **Authentification / Autorisation**, c’est-à-dire :

* activer la connexion à partir de réseaux sociaux ;
* activer plusieurs options de connexion ;
* modifier le comportement par défaut lorsque les utilisateurs accèdent à votre application pour la première fois.

App Service propose une solution clé en main pour une partie des besoins d’authentification les plus courants. Vous n’avez donc pas besoin de fournir la logique d’authentification vous-même. Pour plus d’informations, consultez [Expanding App Service Authentication/Authorization](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) (Étendre l’authentification/autorisation App Service).

## Mettez automatiquement à l’échelle votre application en fonction de la demande
Ensuite, nous allons effectuer la mise à l’échelle automatique de votre application pour que celle-ci ajuste automatiquement sa capacité pour répondre à la demande. Pour plus d’informations, voir [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md) et [Mise à l’échelle manuelle ou automatique du nombre d’instances](../azure-portal/insights-how-to-scale.md).

En bref, vous pouvez adapter la taille de votre application web de deux manières :

* [Montée en puissance](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : vous offre plus de capacité d’UC, de mémoire et d’espace disque. Obtenez également davantage de fonctionnalités, telles que des machines virtuelles dédiées, des domaines et des certificats personnalisés, des emplacements intermédiaires, la mise à l’échelle automatique et bien plus encore. Pour augmenter la taille des instances, changez le niveau tarifaire du plan App Service auquel appartient votre application.
* [Augmentation de la taille des instances](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) : augmente le nombre d’instances de machine virtuelle exécutant votre application. Ce nombre peut atteindre 50 instances, en fonction de votre niveau tarifaire.

Sans plus attendre, préparons à présent la mise à l’échelle automatique.

1. Nous allons tout d’abord procéder à la montée en puissance pour activer la mise à l’échelle automatique. Dans le panneau de portail de votre application, cliquez sur **Paramètres** > ** Mettre à l’échelle, (Plan App Service)**. ![Monter en puissance - panneau Paramètres](./media/app-service-web-get-started/scale-up-settings.png)
2. Faites défiler la page, puis sélectionnez le niveau **S1 Standard** (le plus bas niveau prenant en charge la mise à l’échelle automatique, encerclé dans la capture d’écran), puis cliquez sur **Sélectionner**. ![Monter en puissance - choisir un niveau](./media/app-service-web-get-started/scale-up-select.png)
   
    Vous avez terminé la montée en puissance.
   
   > [!IMPORTANT]
   > Ce niveau épuise votre crédit d’essai gratuit. Si vous avez un compte de paiement à l’utilisation, il occasionne des frais pour votre compte.
   > 
   > 
3. Ensuite, nous allons configurer la mise à l’échelle automatique. Dans le panneau de portail de votre application, cliquez sur **Paramètres** > ** Mettre à l’échelle (Plan App Service)**. ![Augmenter la taille des instances - panneau Paramètres](./media/app-service-web-get-started/scale-out-settings.png)
4. Changez **Mettre à l’échelle selon** sur **Pourcentage UC**. Les curseurs sous la liste déroulante se mettent à jour en conséquence. Ensuite, définissez une plage d’**Instances** comprise entre **1** et **2** et une **Plage cible** entre **40** et **80**. Pour ce faire, effectuez une saisie dans les zones ou déplacez les curseurs. ![Augmenter la taille des instances - configurer la mise à l’échelle automatique](./media/app-service-web-get-started/scale-out-configure.png)
   
    Selon cette configuration, votre application se met à l’échelle automatiquement lorsque l’utilisation de l’UC est supérieure à 80 % (augmentation de la taille des instances) et inférieure à 40 % (diminution de la taille des instances).
5. Cliquez sur **Enregistrer** dans la barre des tâches.

Félicitations, votre application se met à l’échelle automatiquement.

Vous avez peut-être remarqué que vous pouvez en faire beaucoup plus dans le panneau **Paramètres de mise à l’échelle**, et notamment :

* l’augmentation manuelle de la taille des instances à un certain nombre d’instances ;
* la mise à l’échelle selon d’autres mesures de performance, telles que le pourcentage de mémoire ou la longueur de la file d’attente de disque ;
* la personnalisation du comportement de mise à l’échelle lorsqu’une règle de performance est déclenchée ;
* la mise à l’échelle automatique selon un calendrier prévu ;
* le réglage du comportement de mise à l’échelle automatique pour un événement ultérieur.

Pour plus d’informations sur la mise à l’échelle de votre application, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md). Pour plus d’informations sur l’augmentation de la taille des instances, consultez [Mise à l’échelle manuelle ou automatique du nombre d’instances](../azure-portal/insights-how-to-scale.md).

## Recevoir des alertes pour votre application
Maintenant que votre application est mise à l’échelle, que se passe-t-il lorsqu’il atteint le nombre maximal d’instances (2) et lorsque l’utilisation du processeur est supérieure à l’utilisation souhaitée (80 %) ? Vous pouvez configurer une alerte pour vous informer de cette situation afin de faire davantage monter en puissance/augmenter la taille des instances de votre application, par exemple. Pour plus d’informations, voir [Réception de notifications d’alerte](../azure-portal/insights-receive-alert-notifications.md). Nous allons rapidement configurer une alerte pour ce scénario.

1. Dans le panneau du portail de votre application, cliquez sur **Outils** > **Alertes**. ![Alertes - panneau Paramètres](./media/app-service-web-get-started/alert-settings.png)
2. Cliquez sur **Ajouter une alerte**. Ensuite, dans la zone **Ressources**, sélectionnez la ressource qui se termine par **(serverfarms)**. C’est votre plan App Service. ![Alertes - ajouter une alerte pour un plan App Service](./media/app-service-web-get-started/alert-add.png)
3. Définissez **Nom** sur `CPU Maxed`, **Métrique** sur **Pourcentage UC**, et **Seuil** sur `90`, puis sélectionnez **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** et cliquez sur **OK**. ![Alertes - configurer une alerte](./media/app-service-web-get-started/alert-configure.png)
   
    Après la création de l’alerte par Azure, vous la verrez dans le panneau **Alertes**. ![Alertes - vue finale](./media/app-service-web-get-started/alert-done.png)

Félicitations, vous recevez désormais des alertes.

Ce paramètre d’alerte vérifie l’utilisation de l’UC toutes les cinq minutes. Si celle-ci dépasse 90 %, vous (ainsi que toute personne autorisée) recevrez un message d’alerte. Pour afficher tous les utilisateurs autorisés à recevoir les alertes, revenez au panneau du portail de votre application et cliquez sur le bouton **Accès**. ![Afficher qui reçoit des alertes](./media/app-service-web-get-started/alert-rbac.png)

Vous verrez que les **Administrateurs des abonnements** sont déjà les **Propriétaires** de l’application. Ce groupe vous inclut si vous êtes l’administrateur du compte de votre abonnement Azure (par exemple, votre abonnement d’évaluation). Pour plus d’informations sur le contrôle d’accès en fonction du rôle Azure, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

> [!NOTE]
> Règles d’alerte est une fonctionnalité Azure. Pour plus d’informations, consultez la page [Réception de notifications d’alerte](../azure-portal/insights-receive-alert-notifications.md).
> 
> 

## Étapes suivantes
Pendant la configuration de l’alerte, vous avez peut-être remarqué un important ensemble d’outils dans le panneau **Outils**. Ici, vous pouvez résoudre les problèmes, analyser des performances, détecter les failles, gérer les ressources, interagir avec la console de machine virtuelle et ajouter des extensions utiles. Nous vous invitons à cliquer sur chacun d’entre eux pour découvrir les outils simples mais puissants à votre disposition.

En outre, découvrez comment en faire plus avec l’application déployée. Voici une liste partielle :

* [Acheter et configurer un nom de domaine personnalisé](custom-dns-web-site-buydomains-web-app.md) : achetez un domaine attrayant pour votre application web à la place du domaine *.azurewebsites.net. Ou utilisez un domaine que vous possédez déjà.
* [Configurer des environnements intermédiaires](web-sites-staged-publishing.md) : déployez votre application vers une URL intermédiaire avant de la mettre en production. Mettez à jour votre application web en ligne en toute confiance. Configurez une solution DevOps élaborée avec plusieurs emplacements de déploiement.
* [Configurer le déploiement continu](app-service-continuous-deployment.md) : intégrez le déploiement d’applications à votre système de contrôle de code source. Effectuez le déploiement vers Azure avec chaque validation.
* [Accéder à des ressources locales](web-sites-hybrid-connection-get-started.md) : accédez à une base de données locale ou un système CRM existants.
* [Sauvegarder votre application](web-sites-backup.md) : définissez la sauvegarde et la restauration de votre application web. Anticipez les défaillances inattendues et récupérez vos données après leur survenue.
* [Activer les journalisation des données](web-sites-enable-diagnostic-log.md) : consultez les journaux IIS dans Azure ou dans les traces de votre application. Lisez-les dans un flux, téléchargez-les ou déplacez-les dans [Application Insights](../application-insights/app-insights-overview.md) pour une analyse clé en main.
* [Analyser votre application à la recherche de vulnérabilités](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) : analysez votre application pour la protéger contre les menaces modernes à l’aide du service fourni par [Tinfoil Security](https://www.tinfoilsecurity.com/).
* [Exécuter des travaux en arrière-plan](../azure-functions/functions-overview.md) : exécutez des tâches pour le traitement de données, la création de rapports, etc.
* [Découvrir le fonctionnement d’App Service](../app-service/app-service-how-works-readme.md)

<!---HONumber=AcomDC_0803_2016-->