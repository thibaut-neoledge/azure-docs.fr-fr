<properties 
	pageTitle="Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0" 
	description="Voici la page d'authentification multifacteur Azure qui explique la prise en main de l'authentification multifacteur Azure et d’AD FS 2.0." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>
# Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0

Si votre organisation est fédérée avec Azure Active Directory et que vous souhaitez protéger certaines de vos ressources stockées en local ou sur le cloud, vous pouvez utiliser le serveur Azure Multi-Factor Authentication et le configurer pour qu'il fonctionne avec AD FS, de façon à ce que l'authentification multifacteur soit déclenchée pour les points de terminaison de valeur élevée.

Cette documentation traite de l'utilisation du serveur Azure Multi-Factor Authentication avec AD FS 2.0. Pour plus d'informations sur l'utilisation du serveur Azure Multi-Factor Authentication avec Windows Server 2012 R2 AD FE, voir [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy AD FS 2.0
Pour sécuriser AD FS 2.0 avec un proxy, installez le serveur Azure Multi-Factor Authentication sur le serveur de proxy ADFS et configurez le serveur selon la procédure suivante.

### Pour sécuriser AD FS 2.0 avec un proxy
<ol>
<li>Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification IIS dans le menu de gauche.</li>
<li>Cliquez sur l'onglet d’authentification basée sur formulaire.</li>
<li>Cliquez sur le bouton Ajouter...</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>Pour détecter automatiquement les variables de nom d'utilisateur, de mot de passe et de domaine, entrez l'URL de connexion (par exemple, https://sso.contoso.com/adfs/ls) dans la boîte de dialogue Auto-configurer un site Web basé sur formulaire et cliquez sur OK.</li>
<li>Cochez la case de correspondance d'utilisateur Authentification multifacteur Azure requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.</li>
<li>Si les variables de page ne peuvent pas être détectées automatiquement, cliquez sur le bouton Spécifier manuellement... dans la boîte de dialogue Auto-configurer un site Web basé sur formulaire.</li>
<li>Dans la boîte de dialogue Ajouter un site Web basé sur formulaire, entrez l'URL de la page de connexion ADFS dans le champ URL de soumission (par exemple, https://sso.contoso.com/adfs/ls) et entrez un nom d'application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile. Voir le fichier d'aide pour plus d'informations sur l'URL de soumission.</li>
<li>Définissez le format de demande sur «&#160;POST ou GET&#160;».</li>
<li>Entrez les variables de nom d'utilisateur (ctl00$ ContentPlaceHolder1$ UsernameTextBox) et de mot de passe (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Si votre page de connexion basée sur formulaire affiche une zone de texte de domaine, entrez la variable de domaine également. Vous devrez peut-être accéder à la page de connexion dans un navigateur Web. Pour ce faire, cliquez avec le bouton droit de la souris sur la page et sélectionnez « Afficher la Source » pour rechercher les noms des zones de saisie dans la page de connexion.</li>
<li>Cochez la case de correspondance d'utilisateur Authentification multifacteur Azure requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>Cliquez sur le bouton Avancé... pour passer en revue les paramètres avancés, notamment la possibilité de sélectionner un fichier de page de refus personnalisé, de mettre en cache les authentifications réussies sur le site Web pendant une période définie à l'aide de cookies, et de sélectionner comment authentifier les informations d'identification principales.</li>
<li>Le serveur proxy ADFS n'étant pas susceptible d'être joint au domaine, vous utiliserez probablement LDAP pour vous connecter à votre contrôleur de domaine pour l'importation et la pré-authentification des utilisateurs. Dans la boîte de dialogue Site Web avancé basé sur formulaire, cliquez sur l'onglet Authentification principale et sélectionnez « Liaison LDAP » pour le type de pré-authentification.</li>
<li>Lorsque vous avez terminé, cliquez sur le bouton OK pour revenir à la boîte de dialogue Ajouter un site Web basé sur formulaire. Voir le fichier d'aide pour plus d'informations sur les paramètres avancés.</li>
<li>Cliquez sur le bouton OK pour fermer la boîte de dialogue.</li>
<li>Une fois les variables de page et d’URL détectées ou entrées, les données de site Web s’affichent dans le panneau de configuration basé sur formulaire.</li>
<li>Cliquez sur l'onglet Module natif et sélectionnez le serveur, le site Web qui exécute le proxy AD FS (par exemple, « Site Web par défaut ») ou l'application de proxy ADFS (par exemple, « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.</li>
<li>Cochez la case Activer l’authentification IIS en haut de l'écran.</li>
<li>L'authentification IIS est maintenant activée. Toutefois, pour effectuer la pré-authentification de votre Active Directory (AD) via LDAP, vous devez configurer la connexion LDAP au niveau du contrôleur de domaine. Pour ce faire, cliquez sur l'icône Intégration d'annuaire.</li>
<li>Sous l'onglet Paramètres, sélectionnez le bouton radio Utiliser une configuration LDAP spécifique.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>Cliquez sur le bouton Edition...</li>
<li>Dans la boîte de dialogue Modifier la configuration LDAP, remplissez les champs avec les informations requises pour se connecter au contrôleur de domaine Active Directory. Les champs sont décrits dans le tableau ci-dessous. Remarque : cette information est également incluse dans le fichier d'aide du serveur Azure Multi-Factor Authentication.</li>

Champ| Description
:------------- | :-------------
Serveur|Entrez le nom d'hôte ou l’adresse IP du serveur exécutant l'annuaire LDAP. Un serveur de sauvegarde peut également être spécifié, en le séparant par un point-virgule.<br> **Remarque :** lorsque le type de liaison est SSL, un nom d'hôte complet est généralement nécessaire et il doit correspondre au nom associé au certificat SSL installé sur le serveur d'annuaire LDAP. 
Nom unique de base|Entrez le nom unique de l'objet de répertoire de base à partir duquel toutes les requêtes d'annuaire seront lancées. Par exemple, dc=contoso,dc=com.
Type de liaison|Sélectionnez le type de liaison approprié à utiliser lors de la liaison pour rechercher l'annuaire LDAP. Il est utilisé pour les importations, la synchronisation et la résolution de nom d'utilisateur.<ul><li>**Anonyme** : une liaison anonyme sera effectuée. Nom unique de liaison et Mot de passe de liaison ne seront pas utilisés. Cela fonctionne uniquement si l'annuaire LDAP autorise une liaison anonyme et si les autorisations permettent l'interrogation des enregistrements appropriés et des attributs.</li><li>**Simple** : le nom unique de liaison et le mot de passe de liaison sont transmis en texte brut pour la liaison à l'annuaire LDAP. Cela doit uniquement être utilisé à des fins de test pour vérifier que le serveur peut être atteint et que le compte de liaison dispose de l'accès approprié. Il est recommandé d'utiliser SSL une fois le certificat approprié installé.</li><li>**SSL** : le nom unique et le mot de passe de liaison seront chiffrés à l'aide de SSL pour la liaison à l'annuaire LDAP. Cela nécessite l'installation d'un certificat sur le serveur d'annuaire LDAP auquel le serveur Azure Multi-Factor Authentication fait confiance.</li><li>**Windows** : le nom d'utilisateur et le mot de passe de liaison seront utilisés pour se connecter en toute sécurité à un contrôleur de domaine Active Directory ou à un annuaire ADAM. Si le champ Nom d’utilisateur de liaison n’est pas renseigné, le compte de l'utilisateur connecté sera utilisé pour la liaison.</li></ul> 
Nom d’utilisateur de liaison|Entrez le nom unique de l'enregistrement d'utilisateur pour le compte à utiliser lors de la liaison à l'annuaire LDAP. Le nom unique de liaison est utilisé uniquement lorsque le Type de liaison est Simple ou SSL.    
Mot de passe de liaison|Entrez le mot de passe de liaison pour le nom unique de liaison ou le nom d'utilisateur utilisé pour la liaison à l'annuaire LDAP. Pour configurer le mot de passe du service AdSync du serveur d’authentification multifacteur, la synchronisation doit être activée et le service doit être exécuté sur l'ordinateur local. Le mot de passe sera enregistré dans les noms d'utilisateur et mots de passe stockés de Windows et associé au compte sous lequel le service AdSync du serveur Azure Multi-Factor Authentication est exécuté. Le mot de passe sera également associé au compte sous lequel l'interface utilisateur du serveur d’authentification multifacteur est exécutée ainsi qu’au compte sous lequel le service du serveur Azure Multi-Factor Authentication est exécuté. Remarque : étant donné que le mot de passe est uniquement enregistré dans les noms d’utilisateur et mots de passe stockés de Windows sur le serveur local, cette étape doit être effectuée sur chaque serveur d'authentification multifacteur ayant besoin d'accéder au mot de passe. 
Limite de taille de requête|Spécifiez la taille limite pour le nombre maximal d'utilisateurs qu’une recherche dans l'annuaire peut renvoyer. Cette limite doit correspondre à la configuration de l'annuaire LDAP. Pour les recherches volumineuses où la pagination n'est pas prise en charge, les tâches d’importation et de synchronisation tenteront de récupérer les utilisateurs par lots. Si la taille limite spécifiée ici est supérieure à la limite configurée sur l'annuaire LDAP, certains utilisateurs risquent d'être omis. 



<li>Testez la connexion LDAP en cliquant sur le bouton Test.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>Si le test de connexion LDAP a réussi, cliquez sur le bouton OK.</li>
<li>Ensuite, cliquez sur l'icône Paramètres de la société et sélectionnez l'onglet Résolution du nom d'utilisateur.</li>
<li>Sélectionnez le bouton radio Utiliser l'attribut d'identificateur unique LDAP pour établir les correspondances entre les noms d'utilisateur.</li>
<li>Si les utilisateurs entreront leur nom d'utilisateur dans le formulaire de connexion du proxy ADFS au format « domaine\nom_utilisateur », le serveur doit être en mesure de supprimer le nom d'utilisateur du domaine lorsqu'il crée la requête LDAP. Ceci peut être effectué via un paramètre du registre.</li>
<li>Ouvrez l’éditeur du registre et accédez à HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor sur un serveur 64&#160;bits. Si vous utilisez un serveur 32&#160;bits, retirez la partie «&#160;Wow6432Node&#160;» du chemin d'accès. Créez une nouvelle clé de registre DWORD appelée «&#160;UsernameCxz_stripPrefixDomain&#160;» et définissez la valeur sur 1. Le proxy ADFS est désormais protégé par le serveur Azure Multi-Factor Authentication. Assurez-vous que les utilisateurs ont été importés sur le serveur à partir d'Active Directory. Consultez la section Adresses IP de confiance ci-dessous si vous souhaitez ajouter des adresses IP internes à la liste blanche de sorte que l'authentification à deux facteurs ne soit pas requise lors de la connexion au site Web à partir de ces emplacements.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct sans proxy

Pour sécuriser AD FS lorsqu’aucun proxy AD FS n’est utilisé, installez le serveur Azure Multi-Factor Authentication sur le serveur AD FS et configurez le serveur selon la procédure suivante.

### Pour sécuriser AD FS 2.0 sans proxy
<ol>
<li>Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification IIS dans le menu de gauche.</li>
<li>Cliquez sur l'onglet HTTP.</li>
<li>Cliquez sur le bouton Ajouter...</li>
<li>Dans la boîte de dialogue Ajouter une URL de base, entrez l'URL du site Web AD FS où l'authentification HTTP est effectuée (par exemple, https://sso.domain.com/adfs/ls/auth/integrated) dans le champ URL de base et entrez un nom d'application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile.</li>
<li>Si vous le souhaitez, vous pouvez régler le délai de déconnexion en cas d'inactivité et la durée maximale des sessions.</li>
<li>Cochez la case de correspondance d'utilisateur Authentification multifacteur Azure requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.</li>
<li>Cochez la case de cache de cookie si vous le souhaitez.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>Cliquez sur le bouton OK.</li>
<li>Cliquez sur l'onglet Module natif et sélectionnez le serveur, le site Web qui exécute le proxy ADFS (par exemple, « Site Web par défaut ») ou l'application ADFS (par exemple, « ls » sous « adfs ») pour activer le plug-in IIS au niveau souhaité.</li>
<li>Cochez la case Activer l’authentification IIS en haut de l'écran. Le proxy ADFS est désormais protégé par le serveur Azure Multi-Factor Authentication. Assurez-vous que les utilisateurs ont été importés sur le serveur à partir d'Active Directory. Consultez la section Adresses IP de confiance ci-dessous si vous souhaitez ajouter des adresses IP internes à la liste blanche, de sorte que l'authentification à deux facteurs ne soit pas requise lors de la connexion au site Web à partir de ces emplacements.</li>

## Adresses IP de confiance
Les adresses IP approuvées permettent aux utilisateurs de contourner l'authentification multifacteur Azure pour les requêtes de sites Web provenant d'adresses IP ou de sous-réseaux spécifiques. Il se peut par exemple que vous souhaitiez exempter certains utilisateurs de l'authentification multifacteur Azure lorsqu’ils se connectent du bureau. Pour ce faire, vous devez définir le sous-réseau du bureau comme adresse IP de confiance.

### Pour configurer des adresses IP de confiance

<ol>
<li>Dans la section Authentification IIS, cliquez sur l'onglet Adresses IP de confiance.</li>
<li>Cliquez sur le bouton Ajouter...</li>
<li>Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s'affiche, sélectionnez le bouton Adresse IP unique, Plage d'adresses IP ou Sous-réseau.</li>
<li>Entrez l'adresse IP, la plage d'adresses IP ou le sous-réseau à ajouter à la liste blanche. Si vous entrez un sous-réseau, sélectionnez le masque réseau approprié et cliquez sur OK. La liste blanche est alors mise à jour.</li>


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->