<properties
	pageTitle="Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows"
	description="Si vous ne pouvez pas connecter votre machine virtuelle Azure Windows, utilisez les tests de diagnostic et les étapes qui suivent pour isoler la source du problème."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="dkshir"/>

# Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows

Cet article décrit une approche méthodique pour corriger et identifier la cause première des connexions Bureau à distance dans le cas où vous ne parvenez pas à vous connecter avec les machines virtuelles Azure Windows.

## Étape 1 : Exécution du package de diagnostic Azure IaaS

Pour résoudre de nombreux problèmes courants survenant lors de la création de connexions Bureau à distance, Microsoft propose un [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Cliquez sur le **package de diagnostic Microsoft Azure IaaS (Windows)** sur cette page pour créer une session de diagnostic.
2.	Sur la page **Lequel des problèmes suivants se produit sur votre machine virtuelle Azure ?**, sélectionnez le problème **Connectivité RDP à une machine virtuelle Azure (redémarrage requis)**.

Pour plus d’informations, consultez l’[article de la base de connaissances relatif au package de diagnostic Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Si l’exécution du package de diagnostic Azure IaaS ne corrige pas le problème ou si vous ne parvenez pas à exécuter le package de diagnostic, vous devrez peut-être suivre la procédure de dépannage détaillée ci-après.

> [AZURE.NOTE]Le package de diagnostic Azure IaaS (Windows) doit être exécuté à partir d’un ordinateur Windows 8, Windows 8.1, Windows Server 2012 ou Windows Server 2012 R2.

## Étape 2 : Identification du message d’erreur provenant du client Bureau à distance

Reportez-vous aux paragraphes suivants en fonction du message d’erreur qui s’affiche lorsque vous essayez de vous connecter.

### Fenêtre de message de connexion Bureau à distance : La session distante a été déconnectée, car aucun serveur de licences Bureau à distance n’est disponible pour fournir une licence.

Cause : La période de grâce du Gestionnaire de licences de 120 jours pour le rôle de serveur Bureau à distance a expiré et vous devez installer les licences.

Pour résoudre temporairement ce problème, enregistrez une copie locale du fichier RDP à partir du portail Azure, puis exécutez cette commande au niveau d’une invite de commande Windows PowerShell pour vous connecter.

	mstsc <File name>.RDP /admin

Cette opération désactive la gestion des licences pour cette connexion uniquement.

Si vous n’avez pas besoin de plus de deux connexions Bureau à distance simultanées avec la machine virtuelle, vous pouvez utiliser le Gestionnaire de serveur pour supprimer le rôle de serveur Bureau à distance.

Consultez également le billet de blog [La machine virtuelle Azure échoue et affiche le message « Aucun serveur de licences Bureau à distance n’est disponible »](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

### Fenêtre de message de connexion Bureau à distance : Le Bureau à distance ne trouve pas le «nom» de l’ordinateur.

Cause : Le client Bureau à distance de votre ordinateur ne peut pas résoudre le nom de l’ordinateur dans les paramètres du fichier RDP.

Voici un exemple de fichier RDP généré par le portail Azure :

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

Dans cet exemple, la partie adresse comprend le nom de domaine complet du service cloud qui contient la machine virtuelle (ici, tailspin-azdatatier.cloudapp.net) et le port TCP externe du point de terminaison pour le trafic de Bureau à distance (55919).

Solutions possibles à ce problème :

- Si vous êtes sur un intranet d’entreprise, vérifiez que votre ordinateur a bien accès au serveur proxy et peut lui transmettre le trafic HTTPS.
- Si vous utilisez un fichier RDP stocké localement, essayez d’utiliser celui généré par le portail Azure pour vous assurer de disposer du nom DNS correct de la machine virtuelle ou du service cloud et du port du point de terminaison de la machine virtuelle.

### Fenêtre de message de Connexion Bureau à distance : Une erreur d’authentification s’est produite. L’autorité de sécurité locale ne peut pas être contactée.

Cause : la machine virtuelle à laquelle vous vous connectez ne peut pas localiser l’autorité de sécurité indiquée dans la partie nom d’utilisateur de vos informations d’identification.

Quand votre nom d’utilisateur est au format *SecurityAuthority*\*UserName* (par exemple : CORP\\User1), la partie *SecurityAuthority* est soit le nom d’ordinateur de la machine virtuelle (pour l’autorité de sécurité locale), soit un nom de domaine Active Directory.

Solutions possibles à ce problème :

- Si le compte d’utilisateur est local sur la machine virtuelle, vérifiez l’orthographe du nom de machine virtuelle.
- Si le compte d’utilisateur est un compte de domaine Active Directory, vérifiez l’orthographe du nom de domaine.
- Si le compte d’utilisateur est un compte de domaine Active Directory et que le nom de domaine est orthographié correctement, vérifiez qu’un contrôleur de domaine est disponible pour le domaine Active Directory. Ce problème peut être courant dans un réseau virtuel Azure contenant des contrôleurs de domaine dans lequel un ordinateur contrôleur de domaine n’est pas démarré. Solution temporaire : utiliser un compte d’administrateur local, plutôt qu’un compte de domaine.

### Fenêtre de message de sécurité Windows : Vos informations d’identification n’ont pas fonctionné.

Cause : Le nom de compte et le mot de passe que vous avez indiqués ne peuvent pas être validés par la machine virtuelle à laquelle vous vous connectez.

Un ordinateur Windows peut valider les informations d’identification d’un compte local ou d’un compte de domaine.

- Pour les comptes locaux, utilisez la syntaxe *ComputerName*\*UserName* (par exemple : SQL1\\Admin4798).
- Pour les comptes de domaine, utilisez la syntaxe *DomainName*\*UserName* (par exemple : CONTOSO\\johndoe).

Pour les ordinateurs que vous promouvez vers les contrôleurs de domaine d’une nouvelle forêt Active Directory, le compte administrateur local auquel vous êtes connecté lorsque vous effectuez la promotion se transforme en un compte équivalent avec le même mot de passe dans la nouvelle forêt et le nouveau domaine. Le compte administrateur local précédent est supprimé. Par exemple, si vous vous connectez au compte administrateur local DC1\\DCAdmin et promouvez la machine virtuelle en tant que contrôleur de domaine dans une nouvelle forêt du domaine corp.contoso.com, le compte local DC1\\DCAdmin est supprimé et un compte de domaine (CORP\\DCAdmin) est créé avec le même mot de passe.

Vérifiez bien que le nom du compte peut être considéré comme valide par la machine virtuelle. Vérifiez bien l’exactitude du mot de passe.

Pour modifier le mot de passe du compte administrateur local, consultez [Réinitialisation d’un mot de passe ou du Service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-password.md).

### Fenêtre de message de connexion Bureau à distance : Cet ordinateur ne peut pas se connecter à l’ordinateur distant.

Cause : Le compte que vous utilisez pour vous connecter ne dispose pas des droits de connexion au Bureau à distance.

Chaque ordinateur Windows dispose d’un groupe local d’utilisateurs du Bureau à distance, qui comporte les comptes et les groupes autorisés à utiliser une connexion Bureau à distance. Les membres du groupe Administrateurs local y ont également accès, même si ces comptes ne sont pas répertoriés en tant que membres du groupe local d’utilisateurs du Bureau à distance. Pour les ordinateurs associés à un domaine, le groupe Administrateurs local contient également les administrateurs de domaine du domaine en question.

Assurez-vous que le compte que vous utilisez pour établir la connexion dispose des droits de connexion au Bureau à distance. Utilisez un compte d’administrateur local ou d’administrateur de domaine pour résoudre temporairement le problème en créant une connexion Bureau à distance et en ajoutant le compte de votre choix au groupe local d’utilisateurs du Bureau à distance à l’aide du composant logiciel enfichable Gestion de l’ordinateur (**Outils système > Utilisateurs et groupes locaux > Groupes > Utilisateurs du Bureau à distance**).

### Fenêtre de message de connexion Bureau à distance : Le Bureau à distance ne peut pas se connecter à l’ordinateur distant pour l’une des raisons suivantes…

Cause : Le client Bureau à distance ne peut pas atteindre le service Services Bureau à distance sur la machine virtuelle. Ce problème peut être dû à différentes causes premières.

Voici les composants impliqués.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)

Avant de vous plonger dans un processus de dépannage détaillé, nous vous recommandons de réfléchir à tout ce qui a changé depuis que vous avez créé des connexions Bureau à distance et de vous baser sur ces modifications pour résoudre le problème. Par exemple :

- Si vous avez créé des connexions Bureau à distance et modifié l’adresse IP publique de la machine virtuelle ou du service cloud contenant votre machine virtuelle (également appelée adresse IP virtuelle [VIP]), le cache du client DNS peut contenir une entrée pour le nom DNS et l’*ancienne adresse IP*. Videz le cache client DNS et réessayez. Vous pouvez aussi essayer de vous connecter à l’aide de la nouvelle adresse VIP.
- Si vous avez effectué des modifications à partir du portail Azure ou du portail Azure en version préliminaire afin d’utiliser une application pour gérer vos connexions Bureau à distance, vérifiez que la configuration de l’application inclut bien le port TCP déterminé de façon aléatoire pour le trafic de Bureau à distance.

Les paragraphes suivants traitent de l’identification et de la détermination des causes premières du problème, et fournissent des solutions.

## Étape 3 : Procédure précédant le dépannage détaillé

Procédez comme suit :

- Vérifiez l’état de la machine virtuelle sur le portail Azure ou le portail Azure en version préliminaire
- [Redémarrez la machine virtuelle](https://msdn.microsoft.com/library/azure/dn763934.aspx).
- [Redimensionnez la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

Puis testez de nouveau la connexion Bureau à distance.

## Étape 4 : Résolution détaillée des problèmes

L’incapacité de votre client Bureau à distance à atteindre le service Services Bureau à distance sur la machine virtuelle Azure peut être due aux problèmes ou erreurs de configuration survenant au niveau des éléments suivants :

- ordinateur client de Bureau à distance ;
- périphérique de périmètre intranet de l’entreprise ;
- point de terminaison de service cloud et liste de contrôle d’accès (ACL) ;
- groupes de sécurité réseau ;
- machine virtuelle Azure Windows.

### Source 1 : Ordinateur client de Bureau à distance

Pour vous assurer que votre ordinateur n’est pas la source de votre problème ou de votre erreur de configuration, vérifiez que votre ordinateur peut établir des connexions Bureau à distance avec un autre ordinateur Windows local.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)

Si vous n’y parvenez pas, recherchez sur votre ordinateur :

- un paramètre de pare-feu local qui bloque le trafic de Bureau à distance ;
- un logiciel proxy client installé localement qui empêche les connexions Bureau à distance ;
- un logiciel de surveillance réseau installé localement qui empêche les connexions Bureau à distance ;
- d’autres types de logiciel de sécurité qui analysent le trafic ou autorisent/interdisent des types spécifiques de trafic empêchant les connexions Bureau à distance.

Dans tous les cas, essayez de désactiver temporairement le logiciel concerné et tentez d’établir une connexion Bureau à distance avec un ordinateur local afin de déterminer la cause première. Contactez ensuite votre administrateur réseau pour corriger les paramètres du logiciel afin d’autoriser les connexions Bureau à distance.

### Source 2 : Périphérique de périmètre intranet de l’entreprise

Pour vous assurer que votre périphérique de périmètre intranet d’entreprise n’est pas la source de votre problème ou de votre erreur de configuration, vérifiez qu’un ordinateur directement connecté à Internet peut établir des connexions Bureau à distance avec votre machine virtuelle Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)

Si vous n’avez pas d’ordinateur directement connecté à Internet, vous pouvez facilement créer une machine virtuelle Azure dans son propre groupe de ressources ou service cloud et l’utiliser. Pour plus d’informations, consultez [Création d’une machine virtuelle exécutant Windows dans Azure](virtual-machines-windows-tutorial.md). Une fois le test terminé, supprimez le groupe de ressources ou la machine virtuelle et le service cloud.

Si vous pouvez créer une connexion Bureau à distance avec un ordinateur directement connecté à Internet, recherchez sur votre périphérique de périmètre intranet d’entreprise :

- un pare-feu qui bloque les connexions HTTPS à Internet ;
- un serveur proxy qui empêche les connexions Bureau à distance ;
- un logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur les périphériques de votre réseau de périmètre et qui empêche les connexions Bureau à distance.

Contactez votre administrateur réseau pour corriger les paramètres de votre périphérique de périmètre intranet d’entreprise afin d’autoriser les connexions Bureau à distance basées sur HTTPS à Internet.

### Source 3 : Point de terminaison de service cloud et liste de contrôle d’accès

Pour éviter que le point de terminaison de service cloud et les ACL entraînent des problèmes ou des défauts de configuration sur des machines virtuelles créées à l’aide de l’API Gestion des services, vérifiez qu’une autre machine virtuelle Azure du même service cloud ou réseau virtuel peut établir des connexions Bureau à distance avec votre machine virtuelle Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)

> [AZURE.NOTE]Pour les machines virtuelles créées dans Resource Manager, passez à [Source 4 : Groupes de sécurité réseau](#nsgs).

Si vous ne disposez pas d’une autre machine virtuelle dans le même service cloud ou réseau virtuel, vous pouvez facilement en créer une. Pour plus d’informations, consultez [Création d’une machine virtuelle exécutant Windows dans Azure](virtual-machines-windows-tutorial.md). Une fois le test terminé, supprimez la machine virtuelle supplémentaire.

Si vous pouvez créer une connexion Bureau à distance avec une machine virtuelle dans le même service cloud ou réseau virtuel, vérifiez les paramètres suivants :

- La configuration du point de terminaison pour le trafic de Bureau à distance sur la machine virtuelle cible. Le port TCP privé du point de terminaison doit correspondre au port TCP sur lequel le service Services Bureau à distance de la machine virtuelle procède à l’écoute. Par défaut, il s’agit du port 3389.
- La liste de contrôle d’accès du point de terminaison du trafic de Bureau à distance sur la machine virtuelle cible. Les listes de contrôle d’accès vous permettent de spécifier le trafic Internet entrant autorisé et interdit en fonction de l’adresse IP source. Une mauvaise configuration des listes de contrôle d’accès peut empêcher le trafic du Bureau à distance d’accéder au point de terminaison. Examinez vos listes de contrôle d’accès pour vous assurer que le trafic entrant provenant des adresses IP publiques de votre proxy ou d’un autre serveur Edge est autorisé. Pour plus d’informations, consultez [Qu’est-ce qu’une liste de contrôle d’accès (ACL) réseau ?](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Pour vérifier que le point de terminaison n’est pas la source du problème, supprimez le point de terminaison actuel et créez un autre point de terminaison en choisissant un port aléatoire dont le numéro externe se situe entre 49152 et 65535. Pour plus d’informations, consultez [Configuration des points de terminaison sur une machine virtuelle](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Source 4 : groupes de sécurité réseau

Les groupes de sécurité réseau vous permettent de mieux contrôler le trafic entrant et sortant autorisé. Vous pouvez créer des règles qui s’étendent aux sous-réseaux et aux services cloud d’un réseau virtuel Azure. Examinez les règles de votre groupe de sécurité réseau pour vous assurer que le trafic de Bureau à distance provenant d’Internet est autorisé.

Pour plus d’informations, consultez [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

### Source 5 : Machine virtuelle Azure Windows

La machine virtuelle Azure elle-même représente la dernière source possible de votre problème ou de votre erreur de configuration.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)

Tout d’abord, si vous ne parvenez pas à exécuter le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour résoudre le problème de **connectivité RDP à une machine virtuelle Azure (redémarrage requis)**, suivez les instructions décrites dans [Réinitialisation d’un mot de passe ou du Service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-password.md) afin de réinitialiser le service Services de Bureau à distance sur la machine virtuelle. Avec cette opération, vous pouvez :

- activer la règle par défaut du pare-feu Windows Bureau à distance (port TCP 3389) ;
- activer les connexions Bureau à distance en définissant la valeur de registre HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections sur 0.

Essayez une nouvelle fois de vous connecter à partir de votre ordinateur. Si vous ne réussissez toujours pas à vous connecter, cela peut être dû à l’une des raisons suivantes :

- Le service Services Bureau à distance ne fonctionne pas sur la machine virtuelle cible.
- Le service Services Bureau à distance n’est pas compatible avec l’écoute sur le port TCP 3389.
- Le pare-feu Windows ou un autre pare-feu local comporte une règle sortante qui empêche le trafic du Bureau à distance.
- Le logiciel de détection d’intrusion ou de surveillance réseau s’exécutant sur la machine virtuelle Azure empêche les connexions Bureau à distance.

Pour corriger ces problèmes éventuels des machines virtuelles créées à l’aide de l’API de gestion des services, vous pouvez utiliser une session Azure PowerShell distante pour la machine virtuelle Azure. Tout d’abord, vous devez installer un certificat correspondant au service cloud d’hébergement de la machine virtuelle. Accédez à [Configure l’accès à distance sécurisé de PowerShell vers les machines virtuelles Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) et téléchargez le fichier de script **InstallWinRMCertAzureVM.ps1** dans un dossier de votre ordinateur local.

Installez ensuite Azure PowerShell si ce n’est pas déjà fait. Consultez [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md).

Ouvrez une invite de commande Azure PowerShell et modifiez le dossier actif à l’emplacement du fichier de script **InstallWinRMCertAzureVM.ps1**. Pour exécuter un script Azure PowerShell, vous devez définir la bonne stratégie d’exécution. Exécutez la commande **Get-ExecutionPolicy** afin de déterminer votre niveau de stratégie actuel. Pour plus d’informations sur la définition du niveau approprié, consultez [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Indiquez le nom de votre abonnement Azure, le nom du service cloud et le nom de votre machine virtuelle (en supprimant les caractères < and >), puis exécutez ces commandes.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Le nom de l’abonnement apparaît dans la propriété SubscriptionName de la commande **Get-AzureSubscription**. Le nom du service cloud de la machine virtuelle apparaît dans la colonne ServiceName de la commande **Get-AzureVM**.

Pour prouver que vous disposez de ce nouveau certificat, ouvrez un composant logiciel enfichable Certificats axé sur l’utilisateur actuel et accédez au dossier **Autorités de certification racines de confiance\\Certificats**. Vous devriez voir un certificat portant le nom DNS de votre service cloud doit apparaître dans la colonne Issued To (exemple : cloudservice4testing.cloudapp.net).

Lancez ensuite une session Azure PowerShell distante à l’aide de ces commandes.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Une fois que vous avez entré les informations d’identification administrateur valides, l’invite de commande Azure PowerShell doit indiquer un message de ce type :

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La première partie de l’invite de commande indique que vous émettez désormais les commandes Azure PowerShell pour le service cloud contenant la machine virtuelle cible. Le nom de votre service cloud ne sera pas « cloudservice4testing.cloudapp.net ».

Vous pouvez maintenant émettre des commandes Azure PowerShell pour examiner les problèmes supplémentaires cités ci-dessus et corriger la configuration.

### Correction manuelle des Services Bureau à distance permettant l’écoute du port TCP

Si vous ne parvenez pas à exécuter le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour résoudre le problème de **connectivité RDP à une machine virtuelle Azure (redémarrage requis)**, exécutez cette commande à l’invite de session Azure PowerShell distante.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propriété PortNumber indique le numéro de port actuel. Si nécessaire, utilisez cette commande pour modifier le numéro de port du Bureau à distance afin de revenir à sa valeur par défaut (3389).

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Vérifiez que le port affiche désormais la valeur 3389 à l’aide de cette commande.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Quittez la session Azure PowerShell distante à l’aide de cette commande.

	Exit-PSSession

Vérifiez que le point de terminaison du Bureau à distance de la machine virtuelle Azure utilise également le port TCP 3398 comme port interne. Redémarrez ensuite la machine virtuelle Azure et testez une nouvelle fois votre connexion Bureau à distance.

## Étape 5 : Soumission de votre problème sur les forums de support Azure

Pour obtenir de l’aide de la part d’experts Azure du monde entier, vous pouvez soumettre votre problème sur les forums MSDN Azure ou Stack Overflow. Pour plus d’informations, consultez les [forums Microsoft Azure](http://azure.microsoft.com/support/forums/).

## Étape 6 : Signalement d’un incident de support Azure

Si vous avez exécuté le [package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) pour résoudre le problème de **connectivité RDP à une machine virtuelle Azure (redémarrage requis)** ou effectué les étapes 2 à 5 présentées dans cet article et soumis votre problème sur les forums de support Azure, et ce, sans succès, vous pouvez envisager de recréer la machine virtuelle.

Si vous ne pouvez pas recréer la machine virtuelle, il est peut-être temps de signaler un incident de support Azure.

Pour signaler un incident, allez sur le [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

Pour plus d’informations sur l’utilisation du support Azure, consultez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Ressources supplémentaires

[Package de diagnostic Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Réinitialisation d’un mot de passe ou du service Bureau à distance pour les machines virtuelles Windows](virtual-machines-windows-reset-password.md)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

[Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=August15_HO8-->