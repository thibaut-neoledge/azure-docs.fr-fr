Le service Sauvegarde Azure comporte deux types de coffres : le coffre de sauvegarde et le coffre Recovery Services. Le coffre de sauvegarde a été créé en premier. Ensuite, le coffre Recovery Services est venu prendre en charge les déploiements Resource Manager étendus. Microsoft recommande d’utiliser des déploiements Resource Manager, sauf si vous avez spécifiquement besoin d’un déploiement Classic.

| **Déploiement** | **Portail** | **Coffre** |
| --- | --- | --- |
| Classique |[Classique](https://manage.windowsazure.com) |Sauvegarde |
| Gestionnaire de ressources |[Microsoft Azure](https://portal.azure.com) |Recovery Services |

> [!NOTE]
> Les coffres de sauvegarde ne peuvent pas protéger les solutions déployées par le biais de Resource Manager. Toutefois, vous pouvez utiliser un coffre Recovery Services pour protéger les serveurs et les machines virtuelles déployés de façon classique.  
> 
> 

