# Exercise 3: Implement link unfurling messaging extensions

In this exercise, you’ll learn how to add link unfurling to your Microsoft Teams app and how to implement this type of messaging extension.

> [!IMPORTANT]
> This exercise assumes you have created the Microsoft Teams app project with the Yeoman generator.

## Task 1: Add a new search messaging extension to the Teams app

In a previous exercise, you created an action messaging extension that enabled a user to add the details of a planet to a message and search for a planet.

In this section, you'll add the link unfurling ability to the app

### Update the app's configuration

1. You must increment the version of the app to upgrade an existing installed version. Use the following command to increment the version:

```console
npm version patch
```

2. Locate and open the **./src/manifest/manifest.json** file. Locate the `composeExtensions` property. Add the following property after the `commands` property to add the link unfurling messaging extension:

```json
"messageHandlers": [
  {
    "type": "link",
    "value": {
      "domains": [
        "*.wikipedia.org"
      ]
    }
  }
]
```

3. Next, locate the `validDomains` property. Add the following domain to the array of valid domains: `"*.wikipedia.org"`

### Add the link query command handler to the bot

The next step is to implement the link query command handler using a well-known method in the bot.

4. Locate and open the bot in the file **./src/server/planetBot/PlanetBot.ts**.

5. Update the `import` statement for the **botbuilder** package to include the object `AppBasedLinkQuery`:

```typescript
import {
  // ... existing imports
  AppBasedLinkQuery
} from "botbuilder";
```

6. Next, add the following method to the `PlanetBot` class:

```typescript
protected handleTeamsAppBasedLinkQuery(context: TurnContext, query: AppBasedLinkQuery): Promise<MessagingExtensionResponse> {
  // load planets
  const planets: any = require("../planets.json");
  // get the selected planet
  const selectedPlanet: any = planets.filter((planet) => planet.wikiLink === query.url)[0];
  const heroCard = CardFactory.heroCard(selectedPlanet.name, selectedPlanet.summary, [selectedPlanet.imageLink]);

  // generate the response
  return Promise.resolve({
    type: "result",
    attachmentLayout: "list",
    attachments: [heroCard]
  } as MessagingExtensionResponse);
}
```

This method is called by the Bot Framework when a URL matching the domain listed in the app's manifest. It will find a planet with the matching URL and return a `MessagingExtensionResult` object that contains the updated card matching the URL to the existing message.

### Test the updated messaging extension

7. From the command line, navigate to the root folder for the project and execute the following command:

```console
gulp ngrok-serve -debug
```

> [!IMPORTANT]
> Recall from a previous exercise, Ngrok will create a new subdomain. You need to update your bot registration's **Messaging endpoint** in the Azure portal (*shown in a previous exercise*) with this new domain before testing it.

8. First, update the existing installed version of the bot.

9. After updating the app, go back to the 1:1 chat where you tested the messaging extension in the previous exercise. Copy and paste the URL of one of the planets from the **planets.json** file into the compose box. Notice the message has been updated to include the card, which is also included when you send the message:

![Screenshot of a working link unfurling messaging extension.](../../Linked_Image_Files/4-Teams/messaging-extensions/07-test-01.png)

## Summary

In this exercise, you learned how to add link unfurling to your Microsoft Teams app and how to implement this type of messaging extension.
