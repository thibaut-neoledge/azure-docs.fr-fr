---
title: "Proxy d’application Azure AD - bien démarrer avec l’installation du connecteur | Microsoft Docs"
description: "Activez le proxy d’application dans le portail Azure et installez les connecteurs pour le proxy inverse."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8bfdc2f4b86e38d707c026365e3ffcce471d5367
ms.lasthandoff: 04/07/2017


---

# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Bien démarrer avec le proxy d’application et l’installation du connecteur
Cet article vous guide tout au long des étapes d’activation du proxy d’application Microsoft Azure AD pour votre répertoire de cloud dans Azure AD.

Si vous n’êtes pas familiarisé avec les fonctions du service Proxy d’application, vous pouvez en savoir plus en consultant l’article [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Conditions préalables pour le proxy d’application
Pour pouvoir activer et utiliser les services Proxy d’application, vous devez disposer des éléments suivants :

* Un [abonnement Microsoft Azure AD de base ou Premium](active-directory-editions.md) et un annuaire Azure AD sur lequel vous êtes administrateur général.
* Un serveur exécutant Windows Server 2012 R2 ou Windows 8.1 ou version ultérieure sur lequel vous pouvez installer le connecteur de proxy d’application. Le serveur envoie des requêtes aux services Proxy d’application dans le cloud, et doit disposer d’une connexion HTTP ou HTTPS aux applications que vous publiez.
  * Pour permettre l’authentification unique à vos applications publiées, cette machine doit être jointe au même domaine AD que les applications que vous publiez. Pour plus d’informations, consultez [Authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md).

Si votre organisation utilise des serveurs proxy pour se connecter à Internet, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md) pour découvrir comment les configurer.

## <a name="open-your-ports"></a>Ouvrir vos ports

Si un pare-feu existe, assurez-vous qu’il autorise les requêtes HTTPS (TCP) du connecteur au proxy d’application. Le connecteur utilise ces ports avec des sous-domaines qui font partie des domaines de haut niveau msappproxy.net et servicebus.windows.net. Veillez à ouvrir les ports ci-après pour le trafic **sortant** :

| Numéro de port | Description |
| --- | --- |
| 80 |Active le trafic HTTP sortant pour valider la sécurité. |
| 443 |Active l’authentification utilisateur auprès d’Azure AD (obligatoire uniquement pour le processus d’inscription du connecteur).<br>Active la séquence de démarrage du connecteur et les mise à jour automatiques.<br>Active la communication entre le connecteur et le service Azure pour les requêtes entrantes.  |
| 10100 à 10120 |Active les réponses HTTP LOB renvoyées au proxy. |
| 9350 |Facultatif ; permet de meilleures performances pour les requêtes entrantes. |
| 8080 |Active la séquence de démarrage du connecteur et la mise à jour automatique du connecteur |
| 9090 |Active l’inscription du connecteur (obligatoire uniquement pour le processus d’inscription du connecteur) |
| 9091 |Active le renouvellement automatique de certificat de confiance du connecteur |

> [!IMPORTANT]
> Le tableau reflète la configuration des ports nécessaire pour la version la plus récente du connecteur. Si vous avez un connecteur antérieur à la version 1.5, vous devez également activer les ports 9350, 9352 et 5671. Ces ports activent la communication entre les anciens connecteurs et le service Azure pour les requêtes entrantes. 

Si votre pare-feu régule le trafic en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau. Assurez-vous également d’activer le port 8080 pour NT Authority\System.

Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue. 


## <a name="install-and-register-a-connector"></a>Installer et inscrire un connecteur
1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur.
2. Votre répertoire actuel apparaît sous votre nom d’utilisateur dans le coin supérieur droit. Si vous avez besoin de changer de répertoire, sélectionnez cette icône.
3. Accédez à **Azure Active Directory** > **Proxy d’application**.

   ![Accédez à Proxy d’application](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Sélectionnez **Télécharger le connecteur**.

   ![Télécharger le connecteur](./media/active-directory-application-proxy-enable/download_connector.png)

4. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé conformément aux conditions préalables.
5. Suivez les instructions de l’Assistant pour effectuer l’installation.
6. Au cours de l’installation, vous êtes invité à inscrire le connecteur auprès du proxy d’application de votre locataire Azure AD.
   
   * Fournissez vos informations d’identification d’administrateur général d’Azure AD. Votre client d’administrateur global peut être différent de vos informations d’identification Microsoft Azure.
   * Assurez-vous que l’administrateur qui inscrit le connecteur figure dans l’annuaire où vous avez activé le service Proxy d’application. Par exemple, si le domaine du client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
   * Si l’option **Configuration de sécurité renforcée d’Internet Explorer** présente la valeur **Activé** sur le serveur sur lequel vous installez le connecteur, l’écran d’inscription risque d’être bloqué. Suivez les instructions du message d’erreur pour autoriser l’accès. Vérifiez que la configuration de sécurité renforcée d’Internet Explore est désactivée.
   * Si l’inscription du connecteur n’aboutit pas, voir [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md).  
7. À l’issue de l’installation, deux nouveaux services sont ajoutés à votre serveur :
   
   * **connecteur de proxy d’application Microsoft AAD** active la connectivité.
     
   * **service de mise à jour du connecteur de proxy d’application Microsoft AAD** est un service de mise à jour automatisé, qui recherche périodiquement l’existence de nouvelles versions du connecteur et procède éventuellement à la mise à jour de ce dernier.
     
   ![Services de connecteur de proxy d’application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Pour plus d’informations sur les connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md). 

Pour bénéficier d’une haute disponibilité, vous devez déployer au moins deux connecteurs. Pour déployer d’autres connecteurs, répétez les étapes 2 et 3. Chaque connecteur doit être inscrit séparément.

Si vous souhaitez désinstaller le connecteur, désinstallez le service du connecteur et le service de mise à jour. Redémarrez votre ordinateur pour supprimer complètement le service.

## <a name="next-steps"></a>Étapes suivantes
Vous voici prêt à [publier des applications avec le proxy d’application](application-proxy-publish-azure-portal.md).

Si vous disposez d’applications figurant sur des réseaux distincts ou à différents emplacements, vous pouvez utiliser des groupes de connecteurs pour organiser les différents connecteurs en unités logiques. Apprenez-en davantage sur [l’utilisation de connecteurs de proxy d’application](active-directory-application-proxy-connectors-azure-portal.md).


