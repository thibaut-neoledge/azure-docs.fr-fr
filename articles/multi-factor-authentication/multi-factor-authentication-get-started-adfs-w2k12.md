<properties 
	pageTitle="Sécuriser les ressources de cloud et locales à l'aide du serveur Azure MFA avec Windows Server 2012 R2 AD FS" 
	description="Voici la page d'authentification multifacteur Azure qui explique la prise en main de Azure MFA et d’AD FS sur Windows Server 2012 R2." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/04/2016" 
	ms.author="billmath"/>


# Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec Windows Server 2012 R2 AD FS

Si votre organisation est fédérée avec Azure AD et que vous souhaitez protéger certaines de vos ressources stockées en local ou sur le cloud, vous pouvez utiliser le serveur Azure Multi-Factor Authentication et le configurer pour qu'il fonctionne avec AD FS, de façon à ce que l'authentification multifacteur soit déclenchée pour les points de terminaison de valeur élevée.

Cette documentation traite de l'utilisation du serveur Azure Multi-Factor Authentication avec AD FS dans Windows Server 2012 R2. Pour plus d'informations sur l'utilisation du serveur Azure Multi-Factor Authentication avec AD FS 2.0, voir [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Sécurisation de Windows Server 2012 R2 AD FS avec le serveur Azure Multi-Factor Authentication

Lorsque vous installez le serveur Azure Multi-Factor Authentication, les deux options suivantes s’offrent à vous :

- Installer le serveur Azure Multi-Factor Authentication localement sur le même serveur qu’AD FS 
- Installez l'adaptateur Azure Multi-Factor Authentication localement sur le serveur AD FS et installez le serveur MFA sur un autre ordinateur

Avant de commencer, tenez compte des informations suivantes :

- Le serveur Azure Multi-Factor Authentication ne doit pas nécessairement être installé sur votre serveur de fédération AD FS, toutefois, l'adaptateur d'authentification multifacteur pour AD FS doit être installé sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu'il s'agit d'une version prise en charge et installer l'adaptateur AD FS séparément sur votre serveur de fédération AD FS. Consultez la procédure ci-dessous pour obtenir des instructions sur l'installation séparée de l’adaptateur.
- Lors de la conception de l’adaptateur AD FS du serveur Multi-Factor Authentication, il était prévu qu’AD FS soit capable de transférer le nom de la partie de confiance à l’adaptateur pour pouvoir l’utiliser en tant que nom d’application. Mais cela n’a finalement pas été le cas. Si vous utilisez la messagerie texte ou les méthodes d’authentification d’application mobile, les chaînes définies dans les paramètres de la société contiennent un espace réservé « <$ application\_name$ > ». Cet espace réservé n’est pas remplacé lors de l’utilisation de l’adaptateur AD FS. Pour cette raison, il est recommandé de supprimer l’espace réservé des chaînes concernées lors de la sécurisation d’AD FS.

- Le compte connecté doit disposer des droits pour créer des groupes de sécurité dans Active Directory.

- L'assistant d’installation de l'adaptateur d’authentification multifacteur AD FS crée un groupe de sécurité appelé PhoneFactor Admins dans Active Directory, puis ajoute le compte de service AD FS de votre service de fédération à ce groupe. Il est recommandé de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins a bien créé et que le compte de service AD FS est membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.
- Pour plus d'informations sur l'installation du Kit de développement logiciel (SDK) Service Web avec le portail de l'utilisateur, consultez la rubrique [Déploiement du portail de l'utilisateur pour le serveur Azure Multi-Factor Authentication.](multi-factor-authentication-get-started-portal.md)
  

### Pour installer le serveur Azure Multi-Factor Authentication localement sur le même serveur qu’AD FS

1. Téléchargez et installez le serveur Azure Multi-Factor Authentication sur votre serveur de fédération AD FS. Pour plus d'informations sur l'installation du serveur Azure Multi-Factor Authentication, consultez [Mise en route avec le serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md)
2. Dans l'interface utilisateur du serveur Azure Multi-Factor Authentication, cliquez sur l'icône AD FS et sélectionnez l’une des options suivantes : Autoriser l’inscription des utilisateurs ou Autoriser les utilisateurs à sélectionner une méthode .
3. Sélectionnez toute option supplémentaire requise.
4. Cliquez sur Installer adaptateur AD FS.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si l'ordinateur est joint à un domaine et que la configuration d'Active Directory pour sécuriser la communication entre l’adaptateur AD FS et le service Azure Multi-Factor Authentication est incomplète, l'étape Active Directory s'affichera. Cliquez sur le bouton Suivant pour terminer automatiquement cette configuration ou cochez la case Passer la configuration automatique d'Active Directory et configurer les paramètres manuellement, puis cliquez sur Suivant.
6. Si l'ordinateur n’est pas joint à un domaine et que la configuration du groupe local pour sécuriser la communication entre l’adaptateur AD FS et le service Azure Multi-Factor Authentication est incomplète, l'étape Groupe local s'affichera. Cliquez sur le bouton Suivant pour terminer cette configuration automatique ou cochez la case Passer la configuration automatique du groupe local et configurer les paramètres manuellement, puis cliquez sur Suivant.
7. L'assistant d’installation s’ouvre. Cliquez sur Suivant pour autoriser le serveur Azure Multi-Factor Authentication à créer le groupe PhoneFactor Admins et ajouter le compte de service AD FS au groupe PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. À l'étape de lancement du programme d'installation, cliquez sur Suivant.
9. Dans le programme d'installation de l'adaptateur de l'authentification multifacteur AD FS, cliquez sur Suivant.
10. Une fois l'installation terminée, cliquez sur Fermer.
11. Une fois l'adaptateur installé, il doit être enregistré auprès d’AD FS. Ouvrez Windows PowerShell et exécutez la commande suivante : C:\\Program Files\\Multi-Factor Authentication Server\\Register-multifactorauthenticationadfsadapter.ps1
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Vous devez modifier la stratégie d’authentification globale d’AD FS pour utiliser votre nouvel adaptateur enregistré. Dans la Console de gestion AD FS, accédez au nœud Stratégies d'authentification et, dans la section Authentification multifacteur, cliquez sur le lien Modifier en regard de la sous-section Paramètres globaux. Dans la fenêtre Modifier la stratégie d'authentification globale, sélectionnez l'authentification multifacteur comme méthode d'authentification supplémentaire, puis cliquez sur OK. L'adaptateur est enregistré en tant que WindowsAzureMultiFactorAuthentication. Vous devez redémarrer le service AD FS pour que l’enregistrement soit pris en compte.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

À ce stade, le serveur d'authentification multifacteur est configuré comme fournisseur d'authentification supplémentaire à utiliser avec AD FS.

## Pour installer la version autonome de l'adaptateur AD FS à l'aide du SDK du service Web
1. Installez le SDK du service Web sur le serveur exécutant le serveur Multi-Factor Authentication.
2. Copiez les fichiers MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-multifactorauthenticationadfsadapter.ps1, Unregister-multifactorauthenticationadfsadapter.ps1 et MultiFactorAuthenticationAdfsAdapter.config depuis le répertoire \\Program Files\\Multi-Factor du serveur d’authentification vers le serveur sur lequel vous prévoyez d'installer l'adaptateur AD FS.
3. Exécutez le fichier MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Dans le programme d'installation de l'adaptateur AD FS de l'authentification multifacteur, cliquez sur Suivant pour lancer l’installation.
5. Une fois l'installation terminée, cliquez sur Fermer.
6. Modifiez le fichier MultiFactorAuthenticationAdfsAdapter.config en procédant comme suit :

Étape MultiFactorAuthenticationAdfsAdapter.config| Sous-étape
:------------- | :------------- |
Définissez le nœud UseWebServiceSdk sur la valeur true.||
Définissez WebServiceSdkUrl sur l'URL du SDK du service Web d'authentification multifacteur.||
Option 1 : configurer le SDK du service Web avec un nom d'utilisateur et un mot de passe.|<ol><li>Définissez WebServiceSdkUsername sur un compte membre du groupe de sécurité PhoneFactor Admins. Utilisez le <domain>format < nom\_utilisateur >.<li>Définissez WebServiceSdkPassword sur le mot de passe du compte approprié.</li></ol>
Option 2 : configurer le SDK du service Web avec un certificat client.|<ol><li>Obtenir un certificat client auprès d'une autorité de certification pour le serveur exécutant le Kit de développement logiciel (SDK) du service Web. Pour plus d'informations sur l'obtention d'un certificat, consultez [Obtenir un certificat client] (https://technet.microsoft.com/library/cc770328(v=ws.10).aspx).</li><li>Importez le certificat client dans le magasin de certificats personnel de l'ordinateur local sur le serveur exécutant le SDK du service Web.) Remarque : assurez-vous que le certificat public de l’autorité de certification compte parmi les certificats racines de confiance. </li><li>Exportez les clés publiques et privées du certificat client vers un fichier .pfx. </li><li>Exportez la clé publique au format Base64 vers un fichier .cer. </li><li>Dans le Gestionnaire de serveur, vérifiez que la fonctionnalité (IIS)\\Web Server\\Security\\Client Certificate Mapping Authentication du serveur web est installée. </li><li>Dans le cas contraire, cliquez sur Ajouter des rôles et fonctionnalités pour ajouter cette fonctionnalité. </li><li>Dans le Gestionnaire IIS, double-cliquez sur l’éditeur de configuration du site web qui contient le répertoire virtuel du Kit de développement logiciel (SDK) du service web. Remarque : il est très important d'effectuer cette étape au niveau du site Web et non au niveau du répertoire virtuel.</li><li>Accédez à la section system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Définissez Activé sur la valeur true.</li><li>Définissez oneToOneCertificateMappingsEnabled sur la valeur true.</li><li>Cliquez sur le bouton ... en regard de oneToOneMappings.</li><li>Cliquez sur le lien Ajouter.</li><li>Ouvrez le fichier .cer au format Base64 exporté précédemment. Supprimez -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- et tous les sauts de ligne. Copiez la chaîne résultante.</li><li>Définissez le certificat sur la chaîne copiée à l'étape précédente.</li><li>Définissez Activé sur la valeur true.</li><li>Définissez userName sur un compte membre du groupe de sécurité PhoneFactor Admins. Utilisez le <domain>format < nom\_utilisateur >.</li><li>Définissez le mot de passe sur le mot de passe du compte approprié.</li><li>Fermez l'éditeur de collections.</li><li>Cliquez sur le lien Appliquer.</li><li>Accédez au répertoire virtuel du SDK du service Web.</li><li>Double-cliquez sur Authentification.</li><li>Vérifiez que l'emprunt d'identité ASP.NET et l'authentification de base sont activés et que tous les autres éléments sont désactivés.</li><li>Accédez de nouveau au répertoire virtuel du SDK du service Web.</li><li>Double-cliquez sur Paramètres SSL.</li><li>Définissez les certificats clients sur Accepter et cliquez sur Appliquer.</li><li>Copiez le fichier .pfx exporté précédemment sur le serveur exécutant l'adaptateur AD FS.</li><li>Importez le fichier .pfx dans le magasin de certificats personnels de l'ordinateur local.</li><li>Cliquez sur Gérer les clés privées dans le menu contextuel et accordez l'accès en lecture au compte sous lequel le service Active Directory Federation Services est connecté.</li><li>Ouvrez le certificat client et copiez l'empreinte numérique à partir de l'onglet Détails.</li><li>Dans le fichier MultiFactorAuthenticationAdfsAdapter.config, définissez WebServiceSdkCertificateThumbprint sur la chaîne copiée à l'étape précédente.</li></ol>
Modifiez le script Register-MultiFactorAuthenticationAdfsAdapter.ps1 en ajoutant -ConfigurationFilePath <path> à la fin de la commande Register-AdfsAuthenticationProvider, où <path> correspond au chemin d'accès complet au fichier MultiFactorAuthenticationAdfsAdapter.config.|


À présent, exécutez le script \\Program Files\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 dans PowerShell pour enregistrer l'adaptateur. L'adaptateur est enregistré en tant que WindowsAzureMultiFactorAuthentication. Vous devez redémarrer le service AD FS pour que l’enregistrement soit pris en compte.




























 

 


 

 


 





 


 

























































































 


 

 






 

<!---HONumber=AcomDC_0413_2016-->