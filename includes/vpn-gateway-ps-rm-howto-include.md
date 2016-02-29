Plusieurs options s’offrent à vous pour installer les modules : PowerShell Gallery et Web Platform Installer. Le résultat final est pratiquement identique, même si le mode d’installation choisi détermine l’emplacement d’installation par défaut des modules sur votre ordinateur.

Lorsque vous installez à partir de PowerShell Gallery, vos fichiers se trouvent par défaut dans *%ProgramFiles%\\WindowsPowerShell\\Modules*. Lorsque vous installez à partir de Web Platform Installer, vos fichiers se trouvent par défaut dans *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*. Pour cette raison, vous vous en tiendrez à l’un ou l’autre de ces modes d’installation afin d’éviter des erreurs lors de la mise à jour ultérieure de vos applets de commande. Web Platform Installer reçoit des applets de commande à jour chaque mois. PowerShell Gallery reçoit des versions à jour des applets de commande dès leur publication. Pour cette raison, certaines personnes préfèrent utiliser PowerShell Gallery.

Pour des informations supplémentaires sur l’installation d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md).

**Pour installer les modules à partir de PowerShell Gallery**

1. Pour installer le module Resource Manager directement à partir de PowerShell Gallery, ouvrez Windows PowerShell en tant qu’administrateur et tapez les éléments suivants :

		Install-Module AzureRM
		Install-AzureRM

2. Une fois que vous avez installé les modules, vous devez les importer afin de pouvoir les utiliser :

		Import-AzureRM

**Pour installer les modules à l’aide de Web Platform Installer**

- Vous pouvez installer les modules à l’aide de [Web Platform Installer](http://aka.ms/webpi-azps). Lorsque vous cliquez sur le lien, le programme d’installation s’exécute.

- Si vous obtenez des erreurs lors de l’utilisation de Web Platform Installer, cela peut être dû au fait que vous avez déjà installé une version précédente des applets de commande à l’aide de PowerShell Gallery. Consultez ce [billet de blog](https://azure.microsoft.com/blog/azps-1-0/), qui peut vous aider à supprimer les anciennes versions des modules et vous permettre de récupérer un système fonctionnel. En général, des erreurs se produisent lorsque vous utilisiez Web Platform Installer et que vous êtes passé à PowerShell Gallery et inversement. La suppression des modules installés précédemment élimine ce problème, et vous pouvez alors effectuer l’installation à partir du nouvel emplacement.

<!---HONumber=AcomDC_0218_2016-->