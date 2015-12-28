<properties
   pageTitle="Azure AD Connect : Conditions préalables et matériel | Microsoft Azure"
   description="Description de l'article qui sera affichée sur les pages de destination et dans la plupart des résultats de recherche"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/16/2015"
   ms.author="andkjell;billmath"/>

# Conditions préalables pour Azure AD Connect
Cette rubrique décrit les conditions préalables et la configuration matérielle requise pour Azure AD Connect.

## Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici ce dont vous aurez besoin.

**Azure AD**

- Un abonnement Azure ou un [abonnement d’évaluation Azure](http://azure.microsoft.com/pricing/free-trial/). Cette condition est nécessaire uniquement pour accéder au portail Azure mais pas pour l'utilisation d’Azure AD Connect. Si vous utilisez PowerShell ou Office 365, vous n’avez pas besoin d’un abonnement Azure pour utiliser Azure AD Connect. Si vous possédez une licence Office 365, vous pouvez également utiliser le portail Office 365. Avec une licence Office 365 payante, vous pouvez également vous connecter au portail Azure à partir du portail Office 365.
- [Ajoutez et vérifiez le domaine](active-directory-add-domain.md) que vous prévoyez d’utiliser dans Azure AD. Par exemple, si vous envisagez d’utiliser contoso.com pour vos utilisateurs, vérifiez que ce domaine a été vérifié et que vous n’utilisez pas uniquement le domaine par défaut contoso.onmicrosoft.com.
- Un annuaire Azure AD autorise par défaut 50 000 objets. Quand vous vérifiez le domaine, la limite est augmentée à 300 000 objets. Si vous avez besoin de davantage d’objets dans Azure AD, vous devez ouvrir une demande de support pour que la limite soit relevée en conséquence. Si vous avez besoin de plus de 500 000 objets, vous avez besoin d’une licence Office 365, Azure AD De base, Azure AD Premium ou Enterprise Mobility Suite.

**Environnement et serveurs locaux**

- La version de schéma Active Directory et le niveau fonctionnel de forêt doivent être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version aussi longtemps que les exigences relatives au schéma et le niveau de forêt sont remplies.
- Si vous prévoyez d'utiliser la fonctionnalité **Écriture différée du mot de passe**, les contrôleurs de domaine doivent être exécutés sous Windows Server 2008 (avec le dernier SP) ou une version ultérieure.
- Impossible d’installer Azure AD Connect sur Small Business Server ou Windows Server Essentials. Le serveur doit utiliser Windows Server Standard ou une version supérieure.
- Azure Active Directory Connect doit être installé sur Windows Server 2008 ou version ultérieure. Ce serveur peut être un contrôleur de domaine ou un serveur membre si vous utilisez la configuration rapide. Si vous utilisez des paramètres personnalisés, le serveur peut également être autonome et n’a pas besoin d’être joint à un domaine.
- Si vous installez Azure AD Connect sous Windows Server 2008, veillez à appliquer les derniers correctifs à partir de Windows Update. L'installation ne pourra pas démarrer sur un serveur non corrigé.
- Si vous prévoyez d’utiliser la fonctionnalité **Synchronisation de mot de passe**, le serveur Azure AD Connect doit être exécuté sous Windows Server 2008 R2 SP1 ou une version ultérieure.
- [.Net 4.5.1](#component-prerequisites) (ou une version ultérieure) et [PowerShell 3.0](#component-prerequisites) (ou une version ultérieure) doivent être installés sur le serveur Azure AD Connect.
- Si les services de fédération Active Directory sont déployés, les serveurs sur lesquels ces services ou le proxy d’application web doivent être installés doivent être Windows Server 2012 R2 ou version ultérieure. La [gestion à distance de Windows](#windows-remote-management) doit être activée sur ces serveurs pour l’installation à distance.
- Si Active Directory Federation Services est déployé, vous avez besoin de [certificats SSL](#ssl-certificate-requirements).
- Azure AD Connect nécessite une base de données SQL Server pour stocker les données d’identité. Une base de données SQL Server 2012 Express LocalDB (version légère de SQL Server Express) est installée par défaut et le compte du service est créé sur l'ordinateur local. SQL Server Express a une limite de 10 Go qui vous permet de gérer environ 100 000 objets. Si vous avez besoin de gérer un volume plus important d’objets d’annuaire, vous devez pointer le processus d’installation vers une autre version de SQL Server. Azure AD Connect prend en charge toutes les versions de Microsoft SQL Server à partir de SQL Server 2008 (SP4) et jusqu’à SQL Server 2014.

**Comptes**

- Un compte d’administrateur général Azure AD pour l’annuaire Azure AD auquel vous souhaitez effectuer l’intégration
- Un compte d’administrateur d’entreprise pour votre annuaire Active Directory local si vous utilisez la configuration rapide ou effectuez une mise à niveau depuis DirSync.
- [Comptes Active Directory](active-directory-aadconnect-accounts-permissions.md) si vous utilisez le chemin d’installation des paramètres personnalisés.

**Connectivité**

- Si vous utilisez un proxy sortant pour la connexion à Internet, le paramètre suivant dans le fichier **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** doit être ajouté pour que l’assistant d’installation et le dispositif de synchronisation d’Azure AD puissent se connecter à Internet et à Azure AD. Ce texte doit être entré en bas du fichier. Dans ce code, &lt;PROXYADRESS&gt; représente l'adresse IP réelle du proxy ou le nom d’hôte.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Si votre serveur proxy nécessite une authentification, la section devrait se présenter ainsi.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Avec cette modification dans le fichier machine.config, l'Assistant d’installation et le moteur de synchronisation répondront aux demandes d'authentification du serveur proxy. Dans toutes les pages de l'Assistant d’installation, à l'exclusion de la page de **configuration**, les informations d'identification de l’utilisateur sont utilisées. Sur la page de **configuration** à la fin de l'Assistant d'installation, le contexte bascule vers le [compte de service](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) qui a été créé.

Pour plus d’informations sur l’[élément proxy par défaut](https://msdn.microsoft.com/library/kd3cf2ex.aspx), consultez MSDN.

- Si votre proxy limite les URL accessibles, les URL documentées dans [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) doivent être ouvertes dans le proxy.

**Autres**

- Facultatif : un compte d’utilisateur test pour vérifier la synchronisation.

## Configuration requise pour les composants

Azure AD Connect repose sur PowerShell et .Net 4.5.1. Selon votre version de Windows Server, procédez comme suit :

- Windows Server 2012 R2
  - PowerShell est installé par défaut ; aucune action n’est nécessaire.
  - Les versions .NET 4.5.1 et ultérieures sont offertes par le biais de Windows Update. Assurez-vous que vous avez installé les dernières mises à jour de Windows Server dans le Panneau de configuration.
- Windows Server 2008 R2 et Windows Server 2012
  - La dernière version de PowerShell est disponible dans **Windows Management Framework 4.0**, disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
  - Les versions .Net 4.5.1 et ultérieures sont disponibles dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La dernière version prise en charge de PowerShell est disponible dans **Windows Management Framework 3.0**, disponible dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).
 - Les versions .Net 4.5.1 et ultérieures sont disponibles dans le [Centre de téléchargement Microsoft](http://www.microsoft.com/downloads).

## Gestion à distance de Windows

 Lorsque vous utilisez Azure AD Connect pour déployer Active Directory Federation Services ou le proxy d'application web, vérifiez ci-dessous la configuration requise pour garantir la réussite de la connectivité et de la configuration :

 - Si le serveur cible est joint à un domaine, assurez-vous que la gestion à distance Windows est activée.
    - Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande `Enable-PSRemoting –force`
 - Si le serveur cible n’est pas un ordinateur WAP joint à un domaine, il existe quelques conditions requises supplémentaires.
 	- Sur l'ordinateur cible (ordinateur WAP) :
         - Vérifiez que le service winrm (Windows Remote Management/WS-Management) s'exécute via le composant logiciel enfichable Services.
         - Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande `Enable-PSRemoting –force`
    - Sur l'ordinateur sur lequel s'exécute l'Assistant (si l'ordinateur cible n’est pas joint à un domaine ou s’il est joint à un domaine non fiable) :
        - Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
 	    - Dans le Gestionnaire de serveur :
 		     - Ajoutez un hôte WAP DMZ au pool d’ordinateurs (Gestionnaire de serveur -> Gérer -> Ajouter des serveurs... Onglet Utiliser DNS)
 		     - Onglet Gestionnaire de serveur, onglet Tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et sélectionnez Gérer en tant que..., entrez des informations d'identification locales (et pas un domaine) pour l'ordinateur WAP.
 		     - Pour valider la connectivité à distance PSH, dans le Gestionnaire de serveur, onglet Tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et choisissez Windows PowerShell. Une session à distance PSH doit s’ouvrir pour garantir le fait que des sessions PowerShell distantes peuvent être établies.

## Configuration requise des certificats SSL

**Important :** il est fortement recommandé d’utiliser le même certificat SSL sur tous les nœuds de votre batterie de serveurs AD FS, ainsi que sur tous les serveurs proxy d’application web.

- Il doit s’agir d’un certificat X509.
- Vous pouvez utiliser un certificat auto-signé sur les serveurs de fédération dans un environnement de laboratoire de test. Toutefois, pour un environnement de production, nous vous recommandons d'obtenir le certificat auprès d'une autorité de certification publique.
    - Si vous utilisez un certificat qui n'est pas approuvé publiquement, assurez-vous que le certificat installé sur chaque serveur proxy d’application web est approuvé sur le serveur local et sur tous les serveurs de fédération.
- L'identité du certificat doit correspondre au nom du service de fédération (par exemple, fs.contoso.com).
    - L'identité est soit une extension autre nom de l’objet (SAN) de type dNSName, soit à défaut d'entrée SAN, le nom d’objet spécifié comme nom commun.  
    - Le certificat peut contenir plusieurs entrées SAN, pour autant que l'une d'elles corresponde au nom de service de fédération.
    - Si vous envisagez d'utiliser une jonction d'espace de travail, un autre nom d’objet supplémentaire est nécessaire avec la valeur **enterpriseregistration.**, suivie du suffixe de nom d'utilisateur principal de votre organisation, par exemple, **enterpriseregistration.contoso.com**.
- Les certificats basés sur les clés CNG (CryptoAPI Next Generation) et les fournisseurs de stockage de clés ne sont pas pris en charge. Cela signifie que vous devez utiliser un certificat basé sur un fournisseur de services de chiffrement et non sur un fournisseur de stockage de clés.
- Les certificats utilisant des caractères génériques sont pris en charge.

## Composants de prise en charge d’Azure AD Connect

Voici une liste de composants qu’Azure AD Connect installe sur le serveur sur lequel il est installé. Cette liste est destinée à une installation Express de base. Si vous choisissez d’utiliser un autre serveur SQL Server dans la page d’installation des services de synchronisation, SQL Express LocalDB n’est pas installé localement.

- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Client natif Microsoft SQL Server 2012
- Base de données locale Microsoft SQL Server 2012 Express
- Module Azure Active Directory pour Windows PowerShell
- Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique
- Package de redistribution Microsoft Visual C++ 2013


## Configuration matérielle requise pour Azure AD Connect
Le tableau ci-dessous présente la configuration minimale requise pour l’ordinateur de synchronisation Azure AD Connect.

| Nombre d’objets dans Active Directory | UC | Mémoire | Taille du disque dur |
| ------------------------------------- | --- | ------ | --------------- |
| Moins de 10 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 10 000 et 50 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 50 000 et 100 000 | 1,6 GHz | 16 Go | 100 Go |
| Dans les systèmes comportant 100 000 objets ou plus, la version complète de SQL Server est requise.| | | |
| Entre 100 000 et 300 000 | 1,6 GHz | 32 Go | 300 Go |
| Entre 300 000 et 600 000 | 1,6 GHz | 32 Go | 450 Go |
| Plus de 600 000 | 1,6 GHz | 32 Go | 500 Go |

La configuration minimale requise pour les ordinateurs exécutant les services de fédération Active Directory ou les serveurs d’applications web est la suivante :

- Processeur : double cœur 1,6 GHz ou supérieur
- Mémoire : 2 Go ou plus
- Machine virtuelle Azure : configuration A2 ou supérieure


## Étapes suivantes
En savoir plus sur l'[Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1217_2015-->