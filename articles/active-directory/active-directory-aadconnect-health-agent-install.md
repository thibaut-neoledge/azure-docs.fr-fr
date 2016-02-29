<properties
	pageTitle="Installation de l'agent Azure AD Connect Health | Microsoft Azure"
	description="Il s'agit de la page Azure AD Connect Health qui décrit l'agent l'installation pour AD FS et la synchronisation."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="billmath"/>






# Installation de l'agent Azure AD Connect Health

Ce document vous guidera dans l'installation et la configuration de l'agent Azure AD Connect Health pour AD FS et la synchronisation.

>[AZURE.NOTE]Avant de pouvoir consulter des données AD FS quelconques dans votre instance d'Azure AD Connect Health, il vous faudra installer l'agent Azure AD Connect Health sur vos serveurs cibles. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health.md#requirements) avant d’installer l’agent. Vous pouvez télécharger l’agent [ici](http://go.microsoft.com/fwlink/?LinkID=518973).


## Installation de l'agent Azure AD Connect Health pour AD FS
Pour démarrer l’installation de l’agent, double-cliquez sur le fichier .exe que vous avez téléchargé. Sur le premier écran, cliquez sur Installer.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Une fois l’installation terminée, cliquez sur Configurer maintenant.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Cette action lance une invite de commandes suivie par certains éléments PowerShell qui exécuteront Register-AzureADConnectHealthADFSAgent. Vous serez invité à vous connecter à Azure. Connectez-vous.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Une fois que vous êtes connecté, PowerShell continue. À l’issue du processus, vous pouvez fermer PowerShell ; la configuration est terminée.

À ce stade, les services doivent être démarrés automatiquement. L’agent surveille et collecte désormais les données. Gardez à l’esprit que vous verrez des avertissements s’afficher dans la fenêtre PowerShell si vous n’avez pas satisfait la configuration requise décrite dans les sections précédentes. Assurez-vous de disposer de la configuration requise [ici](active-directory-aadconnect-health.md#requirements) avant d’installer l’agent. La capture d’écran suivante est un exemple de ces erreurs.

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes. Si vous avez terminé la configuration, ces services doivent s’exécuter. Dans le cas contraire, vous devez impérativement terminer la configuration pour exécuter les services.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service

![Vérifier Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Installation de l’agent sur les serveurs Windows Server 2008 R2

Pour les serveurs Windows Server 2008 R2, procédez comme suit :

1. Assurez-vous que le serveur est exécuté au Service Pack 1 ou une version supérieure.
1. Désactivez la Configuration de sécurité renforcée pour l’installation de l’agent :
1. Installez Windows PowerShell 4.0 sur chacun des serveurs avant d’installer l’agent AD Health. Pour installer Windows PowerShell 4.0 :
 - Installez [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) en cliquant sur le lien suivant pour télécharger le programme d’installation hors connexion.
 - Installer PowerShell ISE (à partir des fonctionnalités de Windows)
 - Installez [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Installez Internet Explorer version 10 ou supérieure sur le serveur. Cette installation est requise par le service Health, afin de vous authentifier avec vos informations d’identification d’administrateur Azure.
1. Pour plus d’informations sur l’installation de Windows PowerShell 4.0 sur Windows Server 2008 R2, consultez l’article wiki [ici](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Activer l’audit pour AD FS

Pour que la fonctionnalité d’analyse de l’utilisation puisse collecter et analyser les données, l’agent Azure AD Connect Health doit avoir les informations à disposition dans les journaux d’audit AD FS. Ces journaux ne sont pas activés par défaut. Cette règle s’applique uniquement aux serveurs de fédération AD FS. Il est inutile d’activer l’audit sur les serveurs proxy AD FS ou sur les serveurs proxy d’application web. Utilisez les procédures suivantes pour activer l’audit AD FS et localiser les journaux d’audit AD FS.

#### Pour activer l’audit pour AD FS 2.0

1. Cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur **Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Management**, puis double-cliquez sur Générer des audits de sécurité.
3. Sur l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS 2.0 est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Fermez Stratégie de sécurité locale, puis ouvrez le composant logiciel enfichable Gestion. Pour ouvrir le composant logiciel enfichable Gestion, cliquez sur **Démarrer**, pointez sur **Programmes**, pointez sur **Outils d’administration**, puis cliquez sur Gestion AD FS 2.0.
6. Dans le volet Actions, cliquez sur Modifier les propriétés du service FS (Federation Service).
7. Dans la boîte de dialogue **Propriétés du service FS**, cliquez sur l’onglet **Événements**.
8. Cochez les cases **Audits des succès** et **Audits des échecs**.
9. Cliquez sur **OK**.

#### Pour activer l’audit pour AD FS sur Windows Server 2012 R2

1. Ouvrez **Stratégie de sécurité locale** en ouvrant **Gestionnaire de serveur** sur l’écran d’accueil, ou Gestionnaire de serveur dans la barre des tâches sur le bureau, puis cliquez sur **Outils/Stratégie de sécurité locale**.
2. Accédez au dossier **Security Settings\\Local Policies\\User Rights Assignment**, puis double-cliquez sur **Générer des audits de sécurité**.
3. Sous l’onglet **Paramètre de sécurité locale**, vérifiez que le compte de service AD FS est répertorié. S’il n’est pas présent, cliquez sur **Ajouter un utilisateur ou un groupe** et ajoutez-le à la liste, puis cliquez sur **OK**.
4. Ouvrez une invite de commandes avec des privilèges élevés et exécutez la commande suivante pour activer l’audit : <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Fermez **Stratégie de sécurité locale**, puis ouvrez le composant logiciel enfichable **Gestion AD FS** (dans Gestionnaire de serveur, cliquez sur Outils, puis sélectionnez Gestion AD FS).
6. Dans le volet Actions, cliquez sur **Modifier les propriétés du service de fédération**.
7. Dans la boîte de dialogue Propriétés du service de fédération, cliquez sur l’onglet **Événements**.
8. Sélectionnez les cases **Audits des succès et Audits des échecs**, puis cliquez sur **OK**.






#### Pour localiser les journaux d’audit AD FS


1. Ouvrez l’**Observateur d’événements**.
2. Accédez à Journaux Windows et sélectionnez **Sécurité**.
3. Sur la droite, cliquez sur **Filtrer les journaux actuels**.
4. Dans Source de l’événement, sélectionnez **Audit AD FS**.

![Journaux d’audit AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Si vous disposez d’une stratégie de groupe qui désactive l’audit AD FS, l’agent Azure AD Connect Health ne pourra pas collecter les informations. Assurez-vous de ne pas disposer de stratégie de groupe susceptible de désactiver l’audit.

[//]: # "Début de la section de configuration de l'agent proxy"

## Installation de l'agent Azure AD Connect Health pour la synchronisation
L'agent Azure AD Connect Health pour la synchronisation est installé automatiquement dans la dernière version d'Azure AD Connect. Pour utiliser Azure AD Connect pour la synchronisation, vous devez télécharger la dernière version d'Azure AD Connect et l'installer. Vous pouvez télécharger la dernière version [ici](http://www.microsoft.com/download/details.aspx?id=47594).

Pour vérifier que l’agent a été installé, ouvrez les services et procédez aux vérifications suivantes. Si vous avez terminé la configuration, ces services doivent s’exécuter. Dans le cas contraire, vous devez impérativement terminer la configuration pour exécuter les services.

- Azure AD Connect Health AadSync Insights Service
- Azure AD Connect Health AadSync Monitoring Service

![Vérifier Azure AD Connect Health pour la synchronisation](./media/active-directory-aadconnect-health-sync/services.png)

>[Azure.NOTE] N'oubliez pas que l'utilisation d'Azure AD Connect Health requiert Azure AD Premium. Si vous ne disposez pas d'Azure AD Premium, vous ne serez pas en mesure d'effectuer la configuration dans le portail Azure. Vous trouverez plus d'informations sur la configuration requise [ici](active-directory-aadconnect-health.md#requirements).




## Configuration des agents Azure AD Connect Health pour utiliser le proxy HTTP
Vous pouvez configurer des agents Azure AD Connect Health pour utiliser un proxy HTTP.

>[AZURE.NOTE]
- « Netsh WinHttp set ProxyServerAddress » ne fonctionnera pas car l'agent utilise System.Net pour effectuer des demandes web au lieu des services HTTP Microsoft Windows. - L'adresse du proxy HTTP configuré servira à transmettre des messages HTTPS chiffrés. - Les proxys authentifiés (à l'aide de HTTPBasic) ne sont pas pris en charge.

### Modification de la configuration du proxy d’un agent Health
Vous disposez des options suivantes afin de configurer un agent Azure AD Connect Health pour utiliser un proxy HTTP.

>[AZURE.NOTE] Vous devez redémarrer tous les services de l’agent Azure AD Connect Health pour mettre à jour les paramètres du proxy. Exécutez la commande suivante :<br> Restart-Service AdHealth*

#### Importation des paramètres de proxy existants

##### Importation à partir d'Internet Explorer
Vous pouvez importer vos paramètres de proxy HTTP Internet Explorer et les utiliser pour les agents Azure AD Connect Health en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importation à partir de WinHTTP
Vous pouvez importer vos paramètres de proxy WinHTTP en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Spécification manuelle des adresses du proxy
Vous pouvez spécifier manuellement un serveur proxy en exécutant la commande PowerShell suivante sur chaque serveur qui exécute l'agent Health.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemple : *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- « address » peut être un nom de serveur DNS pouvant être résolu ou une adresse IPv4
- "port" peut être omis. Dans ce cas, 443 est choisi comme port par défaut.

#### Effacement de la configuration de proxy existante
Vous pouvez effacer la configuration du proxy en exécutant la commande suivante.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Lecture des paramètres de proxy actuels
Vous pouvez utiliser la commande suivante pour lire les paramètres de proxy actuellement configurés.

	Get-AzureAdConnectHealthProxySettings


## Tester la connectivité au service Azure AD Connect Health
Des problèmes provoquant la perte de connectivité entre l’agent Azure AD Connect Health et le service Azure AD Connect Health peuvent survenir. Cela inclut, entre autres, des problèmes réseau et des problèmes d’autorisation.

Si l’agent ne parvient pas à envoyer des données au service Azure AD Connect Health pendant plus de 2 heures, vous verrez un message d’alerte indiquant « Les données du service de contrôle d’intégrité ne sont pas à jour. » Dans ce cas, vous pouvez tester si les agents Azure AD Connect Health sont en mesure de télécharger des données vers le service Azure AD Connect Health en exécutant la commande PowerShell suivante à partir de l’ordinateur dont l’agent rencontre ce problème.

    Test-AzureADConnectHealthConnectivity -Role Adfs

Le paramètre de rôle peut avoir les valeurs suivantes :
	
- Adfs
- Synchronisation

Vous pouvez utiliser l’indicateur - ShowResults dans la commande pour afficher des journaux détaillés. Consultez l’exemple qui suit :

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResults

>[AZURE.NOTE]Pour utiliser l’outil de connectivité, vous devez d’abord effectuer l’inscription de l’agent. Si vous n’êtes pas en mesure de terminer l’inscription de l’agent, assurez-vous d’avoir rempli toutes les [conditions](active-directory-aadconnect-health.md#requirements) pour Azure AD Connect Health. Ce test de connectivité est effectué par défaut lors de l’inscription de l’agent.


## Liens connexes

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Opérations Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilisation d’Azure AD Connect Health avec AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilisation d'Azure AD Connect Health pour la synchronisation (en Anglais)](active-directory-aadconnect-health-sync.md)
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=AcomDC_0218_2016-->