# Exercise 3: Work with file relationships and trends in OneDrive


Microsoft Graph enables more features than just reading and writing files. In this exercise, you'll learn how to get a list of files trending around a user and how to get recently modified and accessed files by the current user.

> [!IMPORTANT]
> This exercise assumes you have created the Azure AD application and .NET console application from the previous unit in this module. You'll edit the existing Azure AD application and .NET console application created in that exercise in this exercise.

## Task 1: Update the Azure AD application

The first step is to add a permission grant to the Azure AD application that will enable the .NET Core console app to upload files to the signed-in user's OneDrive account.

1. Open a browser and navigate to the [Azure Active Directory admin center (https://aad.portal.azure.com)](https://aad.portal.azure.com). Sign in using a **Work or School Account** that has global administrator rights to the tenancy.

2. Select **Azure Active Directory** in the left-hand navigation.

  ![Screenshot of the App registrations](../../Linked_Image_Files/02-05-azure-ad-portal-home.png)

3. Select **Manage > App registrations** in the left-hand navigation.

4. On the **App registrations** page, select the **Graph Console App**.

5. Select **Manage > API Permissions** in the left-hand navigation panel.

6. Select the **+ Add a permission** button.

![Screenshot of the Add permission button](../../Linked_Image_Files/02-05-07-azure-ad-portal-new-app-permissions-02.png)

7. In the **Request API permissions** panel that appears, select **Microsoft Graph** from the **Microsoft APIs** tab.

![Screenshot of Microsoft Graph in the Request API permissions panel](../../Linked_Image_Files/02-05-azure-ad-portal-new-app-permissions-03.png)

8. When prompted for the type of permission, select **Delegated permissions**.

9. Enter **Sites.R** in the **Select permissions** search box and select the **Sites.Read.All** permission, followed by the **Add permissions** button at the bottom of the panel.

![Screenshot of the Files.ReadWrite permission in the Request API permissions panel](../../Linked_Image_Files/02-05-07-azure-ad-portal-new-app-permissions-04.png)

10. In the **Configured Permissions** panel, select the button **Grant admin consent for [tenant]**, and then select the **Yes** button in the consent dialog to grant all users in your organization this permission.

## Task 2: Update .NET Core console application

In this section, you'll update the .NET console app display all files trending around the currently signed in user.

1. Locate the **Program.cs** file from the application you created in a previous unit in this module.

2. First, update the permission requests to include the new permission added to the app. Locate the `CreateAuthorizationProvider` method. In this method, add the new permission as another scope to the list of existing scopes:

```csharp
List<string> scopes = new List<string>();
scopes.Add("User.Read");
scopes.Add("Files.Read");
scopes.Add("Files.ReadWrite");
scopes.Add("Sites.Read.All");
```

3. Within the `Main` method, locate the following line:

```csharp
var client = GetAuthenticatedGraphClient(config, userName, userPassword);
```

4. Delete all code within the `Main` method after the above line.

5. Add the following code to the end of the `Main` method. This will get a list of all trending files around the currently signed-in user:

```csharp
// request 1 - get trending files around a specific user (me)
var request = client.Me.Insights.Trending.Request();

var results = request.GetAsync().Result;
foreach (var resource in results)
{
  Console.WriteLine("(" + resource.ResourceVisualization.Type + ") - " +resource.ResourceVisualization.Title);
  Console.WriteLine("  Weight: " + resource.Weight);
  Console.WriteLine("  Id: " + resource.Id);
  Console.WriteLine("  ResourceId: " + resource.ResourceReference.Id);
}
```

### Build and test the application

6. Run the following command in a command prompt to compile and run the console application:

```console
dotnet build
dotnet run
```

7. After entering the username and password of a user, the console app will display a list of files trending around the current user. Notice they're stored in descending order by their relative weight property:

![Screenshot of the console application displaying the files trending around the currently signed in user](../../Linked_Image_Files/02-05-07-app-run-01.png)

## Task 3: List files accessed and used by the current user

In this section, you'll update the .NET console app to display the files recently used and accessed by the currently signed-in user.

1. Locate the code you added above for `// request 1 - get trending files around a specific user (me)` and comment it out so it doesn't continue to execute.

2. Add the following code to the `Main` method of the console application.
This code will get a list of all files in the group's root OneDrive folder and write the files to the console:

```csharp
// request 2 - used files
var request = client.Me.Insights.Used.Request();

var results = request.GetAsync().Result;
foreach (var resource in results)
{
  Console.WriteLine("(" + resource.ResourceVisualization.Type + ") - " +resource.ResourceVisualization.Title);
  Console.WriteLine("  Last Accessed: " + resource.LastUsed.LastAccessedDateTime.ToString());
  Console.WriteLine("  Last Modified: " + resource.LastUsed.LastModifiedDateTime.ToString());
  Console.WriteLine("  Id: " + resource.Id);
  Console.WriteLine("  ResourceId: " + resource.ResourceReference.Id);
}
```

### Build and test the application

3. Run the following command in a command prompt to compile and run the console application:

```console
dotnet build
dotnet run
```

4. After entering the username and password of a user, the console app will display a list of files recently accessed and modified by the current user:

![Screenshot of the console application displaying recently used files](../../Linked_Image_Files/02-05-07-app-run-02.png)

## Summary

In this exercise, you learned how to get a list of files trending around a user and how to get recently modified and accessed files by the current user.
