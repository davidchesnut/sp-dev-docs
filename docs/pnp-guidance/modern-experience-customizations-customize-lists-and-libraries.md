# Customizing "modern" lists and libraries
During the summer of 2016 the SharePoint Online team released "modern" document libraries and lists which bring a better user experience which is faster, more intuitive, and responsive. **The "modern" experiences have many benefits and we highly recommend using them**. If your current customizations do not yet work with the "modern" experience then it's time to revisit those so your users can benefit from these great improvements:
 - "Modern" document libraries have an updated user interface that offers an experience similar to OneDrive, so it’s more intuitive to create a new folder and upload files in the browser
 - Click Pin to top to add documents “above the fold” in any onscreen view
 - Copying isn’t new, but the copy and move gestures are intelligent about displaying your information architecture and letting you create new folders on the fly
 - You may not have to make as many copies anymore. Document libraries are also intelligent about remembering other files you’ve been using in SharePoint. That’s why you can import other files from other libraries as links, without having to duplicate files between multiple sites
 - The new document libraries let you group files directly in the main page without clicking to a separate admin screen. You can also click and drag to change the size of your columns, as well as sort, filter and group from any column header
 - Mobile browsers have the same features as the desktop, making SharePoint productive for every user — whether they interact via mouse, keyboard, touch, or screen reader
 - You can now edit metadata directly from the main view in the information panel. No more clicking into multiple screens to apply an update!
 - Thanks to Office Online integration, you can navigate a complete document preview at the top of the information panel. The panel offers metadata, including the history of recent activity, updates to the file, and who received a share to the file

This article focuses on the extensibility options within the "modern" library and list experiences. If, however, you want to learn more about the functionalities offered by the "modern" experiences then the following links will help:
- Overview of the "modern" document library experience: https://blogs.office.com/2016/06/07/modern-document-libraries-in-sharepoint
- Overview of the "modern" list experience: https://blogs.office.com/2016/07/25/modern-sharepoint-lists-are-here-including-integration-with-microsoft-flow-and-powerapps 
- Differences between "modern" and "classic" experiences: https://support.office.com/en-us/article/Differences-between-classic-and-new-experiences-for-lists-and-document-libraries-30e1aab0-a5cc-4363-b7f2-09e2ae07d4dc?ui=en-US&rs=en-US&ad=US

In the remainder of this article we'll use "modern" for the new user experience and "classic" for the legacy user experience. 

>**Important:** 
We're not deprecating the "classic" experience, both "classic" and "modern" will coexist.


_**Applies to:** SharePoint Online_

## Overview of the customization options
<a name="customizationoptions"> </a>

"Modern" list and libraries do not support as many customization options as "classic" lists and libraries. In this article we'll provide details and examples of the supported options. The SharePoint team is working to support more options in the future. The list below gives a quick overview of the supported capabilities for "modern" lists and libraries:
 - Subset of User Custom Actions
 - Custom branding
 - PowerApps and Flow integration

There are numerous customizations which currently are not supported for "modern" lists and libraries:
 - JSLink based field customizations - More options will become available in the future
 - JSLink based view customizations - More options will become available in the future
 - Custom CSS via AlternateCSSUrl web property
 - Custom JavaScript embedded via User Custom Actions - There will a be more controlled way to embed JavaScript on the pages through the SharePoint Framework (not only client-side web parts)
 - Custom master pages - More extensive branding will be supported later using alternative options
 - Customization via InfoPath
 - Minimal Download Strategy (MDS)
 - SharePoint Server Publishing

## User Custom Actions
<a name="supportedcustomactions"> </a>

The "modern" experiences allow certain user custom actions to be surfaced in the new user interface, but not all user action configurations which are supported by "classic" mode are supported in the "modern" experience. The table below gives a high level overview of the supported custom action locations and how they're surfaced in the "modern" UI:

|**User Custom Action location**|**Visible in "modern" UI**|
|:-----|:-----|
| `EditControlBlock` | Yes, these entries show up as custom menu items|
| `CommandUI.Ribbon` | Yes, these entries show up as toolbar items |
| All other locations (e.g. `scriptlink`) | Sorry, these user custom actions won't work |


### EditControlBlock User Custom Actions 
<a name="editcontrolblockcustomactions"> </a>

Adding custom links to the context menu can be done by using the `EditControlBlock` as the location for your custom action. The below PnP provisioning XML creates 2 custom entries. 

```XML
<pnp:ProvisioningTemplate ID="EditControlBlockSamples" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:CustomActions>
    <pnp:SiteCustomActions>
      <pnp:CustomAction Name="CA_1" Description="ca 1" Location="EditControlBlock" RegistrationType="List" RegistrationId="101" Title="CA 1 Title" Sequence="3000" Url="https://contoso.azurewebsites.net/pages/index.aspx" Enabled="true"/>
      <pnp:CustomAction Name="CA_2" Description="ca 2" Location="EditControlBlock" RegistrationType="ContentType" RegistrationId="0x0101" Title="CA 2 Title" Sequence="4000" Url="https://contoso.azurewebsites.net/pages/index.aspx" Enabled="true"/>
    </pnp:SiteCustomActions>
  </pnp:CustomActions>
</pnp:ProvisioningTemplate>
```

You can apply this [PnP provisioning template](https://msdn.microsoft.com/en-us/pnp_articles/pnp-provisioning-engine-and-the-core-library) to a site using the PnP Core library or PnP PowerShell. We've opted to show the PowerShell approach in this article. A first step is installing the PnP PowerShell module as described in https://github.com/SharePoint/PnP-PowerShell. Once that's done, save the PnP provisioning xml to a file and then 2 simple lines of PnP PowerShell are enough to apply the template:

```PowerShell

# Connect to a previously created Modern Site
$cred = Get-Credential
Connect-PnPOnline -Url https://[tenant].sharepoint.com/sites/siteurl -Credentials $cred

# Apply the PnP provisioning template
Apply-PnPProvisioningTemplate -Path c:\customaction_modern_editcontrolblock.xml -Handlers CustomActions

```

If you refresh the "modern" view of a document library in your site you'll see the new entries appear

![Custom EditControlBlock actions visible in the menu](media/modern-experiences/custom-editcontrolblock-actions.png)


>**Note**:
>If you want to use this sample for a list then please set the `RegistrationId` attribute to 100

### CommandUI.Ribbon User Custom Actions 
<a name="ribboncustomactions"> </a>

If you want to extend the toolbar in the "modern" list and library experiences you can do so via adding a user custom action targeting the CommandUI.Ribbon location as shown in this PnP provisioning XML sample.

```XML
<pnp:ProvisioningTemplate ID="CommandUIRibbonSamples" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:CustomActions>
    <pnp:SiteCustomActions>
      <pnp:CustomAction Name="CA_4" Description="ca 4" Location="CommandUI.Ribbon" RegistrationType="List" RegistrationId="101" Title="CA 4 Title" Sequence="6000" Enabled="true">
        <pnp:CommandUIExtension>
          <CommandUIDefinitions>
            <CommandUIDefinition Location="Ribbon.Documents.Copies.Controls._children">
              <Button
                Id="Ribbon.Documents.Copies.OfficeDevPnPDownloadAll"
                Command="OfficeDevPnP.Cmd.DownloadAll"
                Image16by16="/_layouts/15/images/sharepointfoundation16.png"
                LabelText="Download All"
                Description="Download all files separately"
                ToolTipTitle="Download All"
                ToolTipDescription="Download all files separately"
                TemplateAlias="o1"
                Sequence="15"/>
            </CommandUIDefinition>
          </CommandUIDefinitions>
          <CommandUIHandlers>
            <CommandUIHandler
              Command="OfficeDevPnP.Cmd.DownloadAll"
              CommandAction="https://contoso.azurewebsites.net/pages/index.aspx" />
          </CommandUIHandlers>
        </pnp:CommandUIExtension>
      </pnp:CustomAction>
      <pnp:CustomAction Name="CA_6" Description="ca 6" Location="CommandUI.Ribbon" RegistrationType="ContentType" RegistrationId="0x0101" Title="CA 6 Title" Sequence="5000" Enabled="true">
        <pnp:CommandUIExtension>
            <CommandUIDefinitions>
              <CommandUIDefinition Location="Ribbon.CustomTabs._children">
                <Tab Id="Custom Tab" Title="Custom Tab" Description="Custom Tab">
                  <Scaling Id="Custom Tab.Scaling">
                    <MaxSize Id="Custom Group.Scaling.MaxSize" GroupId="Custom Group" Size="LargeLarge" />
                    <MaxSize Id="Custom Group 2.Scaling.MaxSize" GroupId="Custom Group 2" Size="LargeLarge" />
                    <Scale Id="Custom Group.Scaling.Scale" GroupId="Custom Group" Size="LargeLarge" />
                    <Scale Id="Custom Group 2.Scaling.Scale" GroupId="Custom Group 2" Size="LargeLarge" />
                  </Scaling>
                  <Groups Id="Custom Tab.Groups">
                    <Group Id="Custom Group 2" Title="Custom Group 2" Description="Custom Group 2" Sequence="7888" Template="Ribbon.Templates.Flexible2">
                      <Controls Id="Custom Group 2.Controls">
                        <Button Id="CustomButton3" LabelText="Custom Button 3" Image16by16="/_layouts/15/images/attach16.png" ToolTipTitle="Custom Button 3" ToolTipDescription="Custom Button 3" Command="CustomButton3.Command" TemplateAlias="o1" />
                      </Controls>
                    </Group>
                    <Group Id="Custom Group 1" Title="Custom Group 1" Description="Custom Group 1" Sequence="10000" Template="Ribbon.Templates.Flexible2">
                      <Controls Id="Custom Group 1.Controls">
                        <Button Id="CustomButton1" LabelText="Custom Button 1" Image16by16="/_layouts/15/images/itslidelibrary.png" ToolTipTitle="Custom Button 1" ToolTipDescription="Custom Button 1" Command="CustomButton1.Command" TemplateAlias="o1" />
                        <Button Id="CustomButton2" LabelText="Custom Button 2" Image16by16="/_layouts/15/images/dldsln16.png" ToolTipTitle="Custom Button 2" ToolTipDescription="Custom Button 2" Command="CustomButton2.Command" TemplateAlias="o1" />
                      </Controls>
                    </Group>
                  </Groups>
                </Tab>
              </CommandUIDefinition>
              <CommandUIDefinition Location="Ribbon.Templates._children">
                <GroupTemplate Id="Ribbon.Templates.Flexible2">
                  <Layout Title="LargeLarge" LayoutTitle="LargeLarge">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="LargeMedium" LayoutTitle="LargeMedium">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="LargeSmall" LayoutTitle="LargeSmall">
                    <OverflowSection DisplayMode="Large" TemplateAlias="o1" Type="OneRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="MediumLarge" LayoutTitle="MediumLarge">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="MediumMedium" LayoutTitle="MediumMedium">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="MediumSmall" LayoutTitle="MediumSmall">
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="SmallLarge" LayoutTitle="SmallLarge">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Large" TemplateAlias="o2" Type="OneRow" />
                  </Layout>
                  <Layout Title="SmallMedium" LayoutTitle="SmallMedium">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Medium" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                  <Layout Title="SmallSmall" LayoutTitle="SmallSmall">
                    <OverflowSection DisplayMode="Small" TemplateAlias="o1" Type="ThreeRow" />
                    <OverflowSection DisplayMode="Small" TemplateAlias="o2" Type="ThreeRow" />
                  </Layout>
                </GroupTemplate>
              </CommandUIDefinition>
            </CommandUIDefinitions>
            <CommandUIHandlers>
              <CommandUIHandler Command="CustomButton1.Command" CommandAction="https://contoso.azurewebsites.net/pages/index.aspx" />
              <CommandUIHandler Command="CustomButton2.Command" CommandAction="http://www.bing.com" />
              <CommandUIHandler Command="CustomButton3.Command" CommandAction="http://dev.office.com/sharepoint" />
            </CommandUIHandlers>
        </pnp:CommandUIExtension>
      </pnp:CustomAction>
    </pnp:SiteCustomActions>
  </pnp:CustomActions>
</pnp:ProvisioningTemplate>
```

After adding these user custom actions you'll see them appear in the toolbar. Notice that custom tabs are transformed into a dropdown menu:

![Custom action visible in the toolbar](media/modern-experiences/custom-actions-toolbar.png)

>**Note**:
>If you want to use this sample for a list then please set the `RegistrationId` attributes to 100 and use the below XML for the CA_4 user custom action
>
```XML
<CommandUIDefinition Location="Ribbon.Templates._children">
  <Button
    Id="Ribbon.Templates.OfficeDevPnPDownloadAll"
    Command="OfficeDevPnP.Cmd.DownloadAll"
    Image16by16="/_layouts/15/images/sharepointfoundation16.png"
    LabelText="Download All"
    Description="Download all files separately"
    ToolTipTitle="Download All"
    ToolTipDescription="Download all files separately"
    TemplateAlias="o1"
    Sequence="15"/>
</CommandUIDefinition>
```

### User Custom Action limitations 
<a name="customactionlimitations"> </a>

When developing user custom actions that need to work in modern experiences please take into account the following limitations:
 - You can't completely control the order in which the user custom actions show up: the user custom actions are added in the order the `_api/web/Lists(guid'listid')/CustomActionElements` returns the user custom actions... and this API currently does not take in account the sequence attributes. Buttons defined inside a custom tab can be ordered by adding them in the correct order in the CommandUIDefinition xml. Our sample shows Button 3 as first and that's because of the order in the XML
 - Command actions cannot contain JavaScript... using for example `CommandAction="javascript:alert('My custom Action');"` will mean the user custom action will not show up
 - Using the `ScriptLink` or `ScriptBlock` properties is not possible since they can only be used with user custom action location `ScriptLink`, which is not supported
 - The `RegistrationId` cannot refer to a specific library ID (e.g. {7A46F86F-D6CC-4263-8A1B-1BC1658B506C}), only out of the box template types (e.g. 100 for a List or 101 for a Document Library) or content type id's (e.g. 0x0101 for the Document content type) are allowed
 - Using image maps (e.g. `Image16by16="/_layouts/15/1033/images/formatmap16x16.png?rev=33" Image16by16Left="-144" Image16by16Top="-107"`) does not work, you'll need to specify individual images. Also note that only 16x16 images are relevant


## Custom branding
<a name="themingimpact"> </a>
If your site happens to use a custom theme then this custom theme will be respected in the "modern" list and library experiences as shown in below sample:

![Modern list with custom branding coming from custom theme](media/modern-experiences/modern-list-with-custom-theme.png)

## How to configure the end user experience
<a name="configuremodernlibrariesandlists"> </a>
You have multiple options to control whether the "modern" or "classic" library and list experience will be used. 

### Tenant level configuration
If you want to completely disable the "modern" experience, then it's best to use the tenant setting for this. Navigate to your tenant admin center (e.g. contoso-admin.sharepoint.com), go to settings, and select the "classic" experience:

![SharePoint Lists and Libraries experience settings in the SharePoint Admin UI](media/modern-experiences/lists-libraries-tenant-settings.png)


>**Note**:
> - When you switch from between "New experience (auto detect)" and "Classic experience" the change will not be immediately visible.
> - When you've selected "New experience (auto detect)" then you'll always see the "Return to classic SharePoint" option. This is by design given today not all functionalities of "classic" libraries and lists are implemented in the "modern" libraries and lists. There's no option to change this behavior.

### Site/Web level configuration
You can prevent a site collection or web from using the "modern" experience by enabling a feature:
- Site collection scoped feature with ID **E3540C7D-6BEA-403C-A224-1A12EAFEE4C4** for site collection control
- Web scoped feature with ID **52E14B6F-B1BB-4969-B89B-C4FAA56745EF** for web scoped control

You can use below [PnP provisioning XML](https://msdn.microsoft.com/en-us/pnp_articles/pnp-provisioning-engine-and-the-core-library) to enable this feature on site or web level

```XML
<pnp:ProvisioningTemplate ID="experiencecontrol" Version="1" xmlns:pnp="http://schemas.dev.office.com/PnP/2015/12/ProvisioningSchema">
  <pnp:Features>
    <!-- for site collection scoped control -->
    <pnp:SiteFeatures>
      <pnp:Feature ID="E3540C7D-6BEA-403C-A224-1A12EAFEE4C4" Description="Disable modern list experience"/>
    </pnp:SiteFeatures>
    <!-- for web scoped control -->
    <pnp:WebFeatures>
      <pnp:Feature ID="52E14B6F-B1BB-4969-B89B-C4FAA56745EF" Description="Disable modern list experience"/>
    </pnp:WebFeatures>
  </pnp:Features>
</pnp:ProvisioningTemplate>
```

Use the following PnP PowerShell to apply this template:

```PowerShell

# Connect to a previously created Modern Site
$cred = Get-Credential
Connect-PnPOnline -Url https://[tenant].sharepoint.com/sites/siteurl -Credentials $cred

# Apply the PnP provisioning template
Apply-PnPProvisioningTemplate -Path c:\experiencecontrol.xml -Handlers Features

```

### List/Library configuration
If you want to control the experience at the library level, you can use go to list settings, advanced settings, and change the behavior:

![List experience configuration in the SharePoint tenant level settings in admin ui](media/modern-experiences/list-experience-setting.png)

The same can also be done using CSOM as shown in the below snippet:

```C#
// Load the list you want to update
var list = context.Web.Lists.GetByTitle(title);
context.Load(list);
context.ExecuteQuery();

// Possible options are Auto (= what it's defined at tenant level), NewExperience (= "modern") and ClassicExperience
list.ListExperienceOptions = ListExperience.ClassicExperience;

// Persist the changes
list.Update();
context.ExecuteQuery();
```

>**Note**:
> - The settings at the library level **override** the settings at the web, site, or tenant level. This also implies that you could pilot the "modern" list and library experience to a sub site of sites by having the "modern" experience turned off at tenant level but enabled at list level in the pilot sites.
> - If you're not able to get the "modern" experience to show up then inspect the cookies being passed to SharePoint as it could be possible that the opt out of modern experiences cookie (splnu with value set to 0) is still present. Clearing the browser cookies should get this fixed.


## When does the built-in auto-detect automatically switch rendering back to "classic"?
<a name="autodetect"> </a>
SharePoint will use an auto-detect system to automatically switch the rendering of a list to "classic" assuming you've not disabled the "modern" experience for your list using either the site, web, or list scoped overrides explained in the previous chapter. This auto-detect system will automatically switch you back to "classic" whenever SharePoint detects your list is using features which are not (yet) supported in "modern".

Below are the settings that are evaluated as part of the auto-detect system and which make the list render in "classic" mode:
- If the requested list form page has zero or more than 1 web part on it
- If the Web scoped feature "Metadata Navigation and Filtering" is enabled
- If the available webpart is an **XSLTListViewWebPart** (default way to render the list) and:
	- There's a non standard JSLink or XslLink value set for the web part properties
	- The page is shown in a dialog (IsDlg=1)
	- The list is not based on one of the following types: Document library (101), Picture library (109), Web page library (119) or Generic list (100)
	- The JSLink property is set on one of the fields to render
	- One of the fields to render is of type "BCS external data", "Geolocation", "OutcomeChoice" or one of these publishing field types "Image", "Html", or "SummaryLinks"
	- There are list scoped user custom actions which have their ScriptSrc property set

### Programmatically detecting if your library/list will be shown using "modern" or "classic" 
The previous chapter explained the reasoning behind our auto-detect mechanism, but luckily there's an easy way for you as a developer to understand how a library/list will be rendered. Getting this information is as simple as getting the value of the **PageRenderType** file property which you can obtain using CSOM or REST. Below samples show how to first load the page rendering the list and then get the **PageRenderType**:

*CSOM sample:*
```C#
using (var cc = new ClientContext(siteUrl))
{
    cc.Credentials = new SharePointOnlineCredentials(userName, password);
    
    // Load the AllItems.aspx file from the list
    File file = cc.Web.GetFileByServerRelativeUrl("/sites/dev/ECMTest/Forms/AllItems.aspx");
    cc.Load(file, f => f.PageRenderType);
    cc.ExecuteQuery();

    // Check page render type
    Console.WriteLine($"Status = {file.PageRenderType}");
}
```


>**Note**:
> The PageRenderType property was introduced in [January 2017 CSOM release (16.1.6112.1200)](https://dev.office.com/blogs/new-sharepoint-csom-version-released-for-Office-365-january-2017).

*REST request:*
```Html
GET _api/web/getfilebyserverrelativeurl('/sites/dev/ECMTest/Forms/AllItems.aspx')/pageRenderType
```

The REST call will get you integer value which is explained in below table:

Value | Reason
:------:|-------
0 | Undefined = 0, (there's not enough information to know the render mode)
1 | MultipeWePart
2 | JSLinkCustomization
3 | XslLinkCustomization
4 | NoSPList
5 | HasBusinessDataField
6 | HasTaskOutcomeField
7 | HasPublishingfield
8 | HasGeolocationField
9 | HasCustomActionWithCode
10 | HasMetadataNavFeature 
11 | SpecialViewType
12 | ListTypeNoSupportForModernMode
13 | AnonymousUser
14 | ListSettingOff
15 | SiteSettingOff
16 | WebSettingOff
17 | TenantSettingOff
18 | CustomizedForm
19 | DocLibNewForm
20 | UnsupportedFieldTypeInForm
21 | InvalidFieldTypeInForm 
22 | InvalidControModeInForm
23 | CustomizedPage
24 | ListTemplateNotSupported
100 | Modern


## Additional Considerations
<a name="sectionSection22"> </a>

We'll gradually introduce more customization options for "modern" list and library experiences. These will be aligned with the release of additional SharePoint framework capabilities. Currently, there's no exact schedule available, but we'll be updating the "modern" experience articles whenever new capabilities are released.

## Additional resources
<a name="bk_addresources"> </a>

-  [Switch the default experience for lists or document libraries from new or classic](https://support.office.com/en-us/article/Switch-the-default-experience-for-lists-or-document-libraries-from-new-or-classic-66dac24b-4177-4775-bf50-3d267318caa9)
- [Overview of the "modern" document library experience](https://blogs.office.com/2016/06/07/modern-document-libraries-in-sharepoint)
- [Overview of the "modern" list experience](https://blogs.office.com/2016/07/25/modern-sharepoint-lists-are-here-including-integration-with-microsoft-flow-and-powerapps) 
- [Differences between "modern" and "classic" experiences](https://support.office.com/en-us/article/Differences-between-classic-and-new-experiences-for-lists-and-document-libraries-30e1aab0-a5cc-4363-b7f2-09e2ae07d4dc?ui=en-US&rs=en-US&ad=US)
