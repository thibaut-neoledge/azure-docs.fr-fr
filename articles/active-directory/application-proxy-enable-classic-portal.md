---
title: "Activer le proxy d’application Azure AD dans le portail Azure Classic | Microsoft Docs"
description: "Activez le proxy d’application dans le Portail Azure Classic et installez les connecteurs pour le proxy inverse."
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
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---

# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>Activer le proxy d’application Azure AD dans le portail Azure Classic et télécharger des connecteurs
Cet article vous guide tout au long des étapes d’activation du proxy d’application Microsoft Azure AD pour votre répertoire de cloud dans Azure AD.

Si vous n’êtes pas familiarisé avec les fonctions du service Proxy d’application, vous pouvez en savoir plus en consultant l’article [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Conditions préalables pour le proxy d’application
Pour pouvoir activer et utiliser les services Proxy d’application, vous devez disposer des éléments suivants :

* Un [abonnement Microsoft Azure AD de base ou Premium](active-directory-editions.md) et un annuaire Azure AD sur lequel vous êtes administrateur général.
* Un serveur exécutant Windows Server 2012 R2 ou 2016, sur lequel vous pouvez installer le connecteur de proxy d’application. Le serveur envoie des requêtes aux services Proxy d’application dans le cloud, et doit disposer d’une connexion HTTP ou HTTPS aux applications que vous publiez.
  * Pour permettre l’authentification unique à vos applications publiées, cette machine doit être jointe au même domaine AD que les applications que vous publiez. Pour plus d’informations, consultez [Authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md).
* Si votre organisation utilise des serveurs proxy pour se connecter à Internet, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md) pour découvrir comment les configurer.

## <a name="open-your-ports"></a>Ouvrir vos ports

Pour préparer votre environnement pour le proxy d’application Azure AD, vous devez d’abord activer la communication avec les centres de données Azure. Si un pare-feu existe, assurez-vous qu’il autorise les requêtes HTTPS (TCP) du connecteur au proxy d’application.

1. Ouvrez les ports suivants pour le trafic **sortant** :

   | Numéro de port | Utilisation |
   | --- | --- |
   | 80 | Téléchargement de la liste de révocation de certificats lors de la validation du certificat SSL |
   | 443 | Toutes les communications sortantes avec le service de proxy d’application |

   Si votre pare-feu régule le trafic en fonction des utilisateurs d’origine, ouvrez ces ports au trafic provenant des services Windows exécutés en tant que service réseau.

   > [!IMPORTANT]
   > Le tableau reflète la configuration des ports nécessaire pour les versions du connecteur 1.5.132.0 et ultérieures. Si vous avez encore une version plus ancienne du connecteur, vous devez également activer les ports suivants : 5671, 8080, 9090, 9091, 9350, 9352 et 10100-10120.
   >
   >Pour plus d’informations sur la mise à jour de vos connecteurs vers la version la plus récente, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Si votre pare-feu ou votre proxy autorise la mise en liste verte de DNS, vous pouvez y placer les connexions à msappproxy.net et à servicebus.windows.net. Dans le cas contraire, vous devez autoriser l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine.

3. Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue.

## <a name="enable-application-proxy-in-azure-ad"></a>Activer le proxy d’application dans Azure AD
1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com/)en tant qu’administrateur.
2. Accédez à Active Directory, puis sélectionnez l’annuaire dans lequel vous souhaitez activer le proxy d’application.

    ![Active Directory - icône](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Sélectionnez **Configurer** dans la page de l’annuaire, puis faites défiler la page vers le bas jusqu’à **Proxy d’application**.
4. Définissez **Activer les services de proxy d’application pour cet annuaire** sur **Activé**.

    ![Activer le proxy d’application](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Sélectionnez **Télécharger maintenant**. La page **Télécharger le connecteur de proxy d’application Azure AD** s’ouvre. Lisez et acceptez les termes du contrat de licence, puis cliquez sur **Télécharger** pour enregistrer le fichier Windows Installer (.exe) du connecteur.

## <a name="install-and-register-the-connector"></a>Installer et inscrire le connecteur
1. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé conformément aux conditions préalables.
2. Suivez les instructions de l’Assistant pour effectuer l’installation.
3. Au cours de l’installation, vous êtes invité à inscrire le connecteur auprès du service Proxy d’application de votre locataire Azure AD.

   * Fournissez vos informations d’identification d’administrateur général d’Azure AD. Votre client d’administrateur global peut être différent de vos informations d’identification Microsoft Azure.
   * Assurez-vous que l’administrateur qui inscrit le connecteur figure dans l’annuaire où vous avez activé le service Proxy d’application. Par exemple, si le domaine du client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
   * Si l’option **Configuration de sécurité renforcée d’Internet Explorer** présente la valeur **Activée** sur le serveur, l’écran d’inscription risque d’être bloqué. Suivez les instructions du message d’erreur pour autoriser l’accès. Vérifiez que la configuration de sécurité renforcée d’Internet Explore est désactivée.
   * Si l’inscription du connecteur n’aboutit pas, voir [Résoudre les problèmes du proxy d’application](active-directory-application-proxy-troubleshoot.md).  
4. À l’issue de l’installation, deux nouveaux services sont ajoutés à votre serveur :

   * **connecteur de proxy d’application Microsoft AAD** active la connectivité.

     * **Programme de mise à jour du connecteur de proxy d’application Microsoft AAD** est un service de mise à jour automatique. Régulièrement, ce service recherche de nouvelles versions du connecteur et le met à jour si besoin.

     ![Services de connecteur de proxy d’application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Cliquez sur **Terminer** dans la fenêtre d’installation.

Pour plus d’informations sur les connecteurs, consultez [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md).

Pour bénéficier d’une haute disponibilité, vous devez déployer au moins deux connecteurs. Pour déployer d’autres connecteurs, répétez les étapes 2 et 3. Chaque connecteur doit être inscrit séparément.

Si vous souhaitez désinstaller le connecteur, désinstallez le service du connecteur et le service de mise à jour. Redémarrez votre ordinateur pour supprimer complètement le service.

## <a name="next-steps"></a>Étapes suivantes
Vous voici prêt à [publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).

Si vous disposez d’applications figurant sur des réseaux distincts ou à différents emplacements, vous pouvez utiliser des groupes de connecteurs pour organiser les différents connecteurs en unités logiques. Apprenez-en davantage sur [l’utilisation de connecteurs de proxy d’application](active-directory-application-proxy-connectors.md).

