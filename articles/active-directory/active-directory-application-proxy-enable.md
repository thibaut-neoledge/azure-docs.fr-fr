---
title: Activer le proxy d’application Azure AD | Microsoft Docs
description: Activez le proxy d’application dans le Portail Azure Classic et installez les connecteurs pour le proxy inverse.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: kgremban

---
# Activer le proxy d’application dans le Portail Azure
Cet article vous guide tout au long des étapes d’activation du proxy d’application Microsoft Azure AD pour votre répertoire de cloud dans Azure AD.

Si vous n’êtes pas familiarisé avec les fonctions du service Proxy d’application, vous pouvez en savoir plus en consultant l’article [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

## Conditions préalables pour le proxy d’application
Pour pouvoir activer et utiliser les services Proxy d’application, vous devez disposer des éléments suivants :

* Un [abonnement Microsoft Azure AD de base ou Premium](active-directory-editions.md) et un annuaire Azure AD sur lequel vous êtes administrateur général.
* Un serveur exécutant Windows Server 2012 R2 ou Windows 8.1 ou version ultérieure sur lequel vous pouvez installer le connecteur de proxy d’application. Le serveur envoie des requêtes aux services Proxy d’application dans le cloud, et doit disposer d’une connexion HTTP ou HTTPS aux applications que vous publiez.
  
  * Pour permettre l’authentification unique à vos applications publiées, cette machine doit être jointe au même domaine AD que les applications que vous publiez.
* Si un pare-feu existe, assurez-vous qu’il autorise les requêtes HTTPS (TCP) du connecteur au proxy d’application. Le connecteur utilise ces ports avec des sous-domaines qui font partie des domaines de haut niveau msappproxy.net et servicebus.windows.net. Veillez à ouvrir les ports ci-après pour le trafic **sortant** :
  
  | Numéro de port | Description |
  | --- | --- |
  | 80 |Active le trafic HTTP sortant pour valider la sécurité. |
  | 443 |Active l’authentification utilisateur auprès d’Azure AD (obligatoire uniquement pour le processus d’inscription du connecteur). |
  | 10100 à 10120 |Active les réponses HTTP LOB renvoyées au proxy. |
  | 9352, 5671 |Active la communication depuis le connecteur vers le service Azure pour les requêtes entrantes. |
  | 9350 |Facultatif ; permet de meilleures performances pour les requêtes entrantes. |
  | 8080 |Active la séquence de démarrage du connecteur et la mise à jour automatique du connecteur. |
  | 9090 |Active l’enregistrement du connecteur (obligatoire uniquement pour le processus d’inscription du connecteur). |
  | 9091 |Active le renouvellement automatique de certificat de confiance du connecteur. |
  
    Si votre pare-feu régule le trafic en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau. Assurez-vous également d’activer le port 8080 pour NT Authority\\System.
* Si votre organisation utilise des serveurs proxy pour se connecter à Internet, consultez le billet de blog [Working with existing on-premises proxy servers](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) (Utilisation de serveurs proxy locaux existants) pour découvrir comment les configurer.

## Étape 1 : activer le proxy d’application dans Azure AD
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/) en tant qu’administrateur.
2. Accédez à Active Directory, puis sélectionnez l’annuaire dans lequel vous souhaitez activer le proxy d’application.
   
    ![Active Directory - icône](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Sélectionnez **Configurer** dans la page de l’annuaire, puis faites défiler la page vers le bas jusqu’à **Proxy d’application**.
4. Définissez **Activer les services de proxy d’application pour cet annuaire** sur **Activé**.
   
    ![Activer le proxy d’application](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Sélectionnez **Télécharger maintenant**. Vous accédez à la page de **téléchargement du connecteur de proxy d’application Azure AD**. Lisez et acceptez les termes du contrat de licence, puis cliquez sur **Télécharger** pour enregistrer le fichier Windows Installer (.exe) du connecteur.

## Étape 2 : installer et inscrire le connecteur
1. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé conformément aux conditions préalables.
2. Suivez les instructions de l’Assistant pour effectuer l’installation.
3. Au cours de l’installation, vous serez invité à inscrire le connecteur auprès du service Proxy d’application de votre client Azure AD.
   
   * Fournissez vos informations d’identification d’administrateur général d’Azure AD. Votre client d’administrateur global peut être différent de vos informations d’identification Microsoft Azure.
   * Assurez-vous que l’administrateur qui inscrit le connecteur figure dans l’annuaire où vous avez activé le service Proxy d’application. Par exemple, si le domaine du client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
   * Si l’option **Configuration de sécurité renforcée d’Internet Explorer** présente la valeur **Activé** sur le serveur sur lequel vous installez le connecteur, l’écran d’inscription risque d’être bloqué. Suivez les instructions du message d’erreur pour autoriser l’accès. Vérifiez que la configuration de sécurité renforcée d’Internet Explore est désactivée.
   * Si l’inscription du connecteur n’aboutit pas, voir [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md).
4. À l’issue de l’installation, deux nouveaux services sont ajoutés à votre serveur :
   
   * Le **connecteur de proxy d’application Microsoft AAD** active la connectivité.
     
     * Le **service de mise à jour du connecteur de proxy d’application Microsoft AAD** est un service de mise à jour automatisé, qui recherche périodiquement l’existence de nouvelles versions du connecteur et procède éventuellement à la mise à jour de ce dernier.
     
     ![Services de connecteur de proxy d’application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Cliquez sur **Terminer** dans la fenêtre d’installation.

Pour bénéficier d’une haute disponibilité, vous devez déployer au moins deux connecteurs. Pour déployer d’autres connecteurs, répétez les étapes 2 et 3 décrites plus haut. Chaque connecteur doit être inscrit séparément.

Si vous souhaitez désinstaller le connecteur, désinstallez le service du connecteur et le service de mise à jour. Redémarrez votre ordinateur pour supprimer complètement le service.

## Étapes suivantes
Vous voici prêt à [publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).

Si vous disposez d’applications figurant sur des réseaux distincts ou à différents emplacements, vous pouvez utiliser des groupes de connecteurs pour organiser les différents connecteurs en unités logiques. Apprenez-en davantage sur [l’utilisation de connecteurs de proxy d’application](active-directory-application-proxy-connectors.md).

<!----HONumber=AcomDC_0727_2016-->