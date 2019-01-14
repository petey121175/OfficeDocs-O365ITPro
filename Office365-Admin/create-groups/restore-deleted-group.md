---
title: "Restore a deleted Office 365 Group"
ms.author: dianef
author: dianef77
manager: mnirkhe
ms.audience: Admin
ms.topic: article
ms.service: o365-administration
localization_priority: Normal
ms.collection: Adm_O365
ms.custom:
- Adm_O365
- Core_O365Admin_Migration
- MiniMaven
search.appverid:
- BCS160
- MET150
- MOE150
ms.assetid: b7c66b59-657a-4e1a-8aa0-8163b1f4eb54

description: "Learn how to restore a deleted Office 365 Group using the Exchange admin center. "
---

# Restore a deleted Office 365 Group
  
If you're a user who wants to restore an Office 365 group, ask the person in your business who has admin permissions to do these steps for you. In a large business, this is the internal help desk / technical support.  
   
If you've deleted an Office 365 group, by default it's retained for 30 days. This 30-day period is called "soft-delete" because you can still restore the group. After 30 days, the group and associated content is permanently deleted and cannot be restored.
  
During the "soft-delete" period if a user tries to access the site they will get a 'The webpage cannot be found' error. After this period if the user tries to access the site they will get a 404 not found message.
  
When a group is restored, the following content is restored:
  
- Azure Active Directory (AD) Office 365 Groups object, properties and members
    
- Group SMTP address
    
- Exchange Online shared inbox and calendar
    
- SharePoint Online team site and files
    
- OneNote notebook
    
- Planner
    
- Teams
    
You can also [Permanently delete an Office 365 group](#permanently-delete-an-office-365-group) if you can't wait the 30 days for the retention period to expire for the content to be permanently deleted. 
  
## Restore an Office 365 Group using the Exchange admin center

::: moniker range="o365-worldwide"

1. Go to the [![Go to the Office 365 admin center.](../media/e00ba917-c3fb-4173-b344-43eb5c7eeb15.png)](https://portal.office.com/adminportal/home).

::: moniker-end

::: moniker range="o365-germany"

1. Go to the [Office 365 admin center](https://portal.office.de/adminportal/home).

::: moniker-end

::: moniker range="o365-21vianet"

1. Go to the [Office 365 admin center](https://login.partner.microsoftonline.cn).

::: moniker-end
    
2. In the left navigation of the Office 365 admin center, expand **Admin center**, and then choose **Exchange**.
    
3. In the Exchange admin center, select **recipients**, and then choose **groups**. You can view whether the group is Active or soft Deleted. If the group has been permanently deleted, it won't be listed at all.
    
    ![Choose recipients and then choose groups.](../media/8254e426-06f0-4311-8760-478751f7d988.png)
  
4. To view the exact time when the group was soft deleted, select the group and view the info in the right pane.
    
    ![To view when a group was deleted, choose the group and view the info in the right pane.](../media/12160297-5ce6-4163-81fd-478a76d185bc.png)
  
5. Select the Office 365 group you want to restore, and then choose the restore icon.
    
    ![Choose the group you want to restore, and then click the restore icon.](../media/85c1c45b-1847-4a55-9138-468dc5934125.png)
  
6. Choose refresh ![Refresh icon](../media/6464df90-2a91-4c1f-92a6-9a38c7696ac3.gif) to update the information on the page. Your group will show as Active. 
    
## Restore an Office 365 Group using PowerShell

IMPORTANT: If you use Remove-MsolGroup in PowerShell to delete a group, this will delete the group permanently. When using PowerShell to delete groups, it's best practice to use **Remove-AzureADMSGroup** to soft-delete the Office 365 group. That way you can restore it if needed. 
  
### Install the preview version of the Azure Active Directory PowerShell for Graph

 **IMPORTANT**: You cannot install both the preview and GA versions on the same computer at the same time **.**
  
As a best practice, we recommend  *always*  staying current: uninstall the old AzureADPreview or old AzureAD version and get the latest one. 
  
1. Open Windows PowerShell as an administrator:
    
    The Windows PowerShell window will pop open. The prompt C:\Windows\system32 means you opened it as an administrator.
    
    ![What PowerShell looks like when you first open it.](../media/246a4acc-149d-4b96-b8a3-2d702fee1ddc.png)
  
1. In your search bar, type Windows PowerShell.
    
2. Right-click on **Windows PowerShell** and select **Run as Administrator**.
    
    ![Open PowerShell as "Run as administrator."](../media/52517af8-c7b0-4c8f-b2f3-0f82f9d5ace1.png)
  
2. Check installed module:
    
  ```
  Get-InstalledModule -Name "AzureAD*"
  ```

3. To uninstall a previous version of AzureADPreview or AzureAD, run this command:
  
```
   Uninstall-Module AzureADPreview
```

or
  
```
   Uninstall-Module AzureAD
```

4. To install the latest version of AzureADPreview, run this command:
  
```
   Install-Module AzureADPreview
```



At the message about an untrusted repository, type **Y**. It will take a minute or so for the new module to install.
  
### Restore the deleted group

You must have Office 365 global admin or organization management permissions.
  
1. Did you install the **AzureADPreview** module, as instructed in the above section "Install the preview version of the Azure Active Directory Module for Windows PowerShell"? Not having the most current **preview** version is the #1 reason these steps don't work for people. 
    
2. If you haven't already, open a Windows PowerShell window on your computer (it doesn't matter if it's a normal Windows PowerShell window, or one you opened by selecting **Run as administrator**).
    
3. Run the following commands. Press **Enter** after each command. 
    
  ```
  Import-Module AzureADPreview
  ```

  ```
  Connect-AzureAD
  ```



  In the **Sign in to your Account** screen that opens, enter your Office 365 admin account and password to connect you to your service, and click **Sign in**.
    
  ![Enter your Office 365 credentials](../media/a2b4e2f3-436f-4a6c-b571-1a192698acea.png)
  
4. Run this command to display all soft-deleted Office 365 groups in your organization that are still within the 30 day retention period:
    
  ```
  Get-AzureADMSDeletedGroup
  ```

5. Take note of the object ID of the group, or groups, you want to restore. If you don't see the group you're looking for on this list then it has likely been purged permanently.
    
    > [!CAUTION]
    > If a new group has been created with the same alias or SMTP address as your deleted group, you will have to delete that new group before you'll be able to restore your deleted group. 
  
6. To restore that group run this command:
    
  ```
  Restore-AzureADMSDeletedDirectoryObject -Id <objectId>
  ```

7. This process usually takes just a few minutes but in a few rare cases it can take as long as 24 hours to completely restore. To verify that the group has been successfully restored, run this command in PowerShell:
    
  ```
  Get-AzureADGroup -ObjectId <objectId>
  ```

Once the restore has successfully completed, the group should reappear on the navigation pane in Outlook and Outlook on the web. All restored content, including SharePoint and Planner, should be available to the group members again.
  
## Permanently delete an Office 365 group

Sometimes you may want to permanently purge a group without waiting the 30 days for the soft-delete to expire. To do that, start PowerShell and run this command to get the object ID of the group:
  
```
Get-AzureADMSDeletedGroup
```

Take note of the object ID of the group, or groups, you want to permanently delete.
  
> [!CAUTION]
> Purging the group removes the group and its data forever. 
  
To purge the group run this command in PowerShell:
  
```
Remove-AzureADMSDeletedDirectoryObject -Id <objectId>
```

To confirm that the group has been successfully purged, run the  *Get-AzureADMSDeletedGroup*  cmdlet again to confirm that the group no longer appears on the list of soft-deleted groups. In some cases it may take as long as 24 hours for the group and all of its data to be permanently deleted. 
  
## Got questions about Office 365 Groups?

Visit the [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Office-365-Groups/ct-p/Office365Groups) to post questions and participate in conversations about Microsoft Office 365 Groups. 
  
## Related articles

[Manage Office 365 Groups with PowerShell](https://support.office.com/article/aeb669aa-1770-4537-9de2-a82ac11b0540)
  
[Delete groups using the Remove-UnifiedGroup cmdlet](https://technet.microsoft.com/en-us/library/mt238270%28v=exchg.160%29.aspx)
  
[Manage your group-connected team site settings](https://support.office.com/article/8376034d-d0c7-446e-9178-6ab51c58df42.aspx)
  
[Delete a group in Outlook](https://support.office.com/article/ca7f5a9e-ae4f-4cbe-a4bc-89c469d1726f.aspx)
  

