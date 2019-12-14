<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="70e6c-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="70e6c-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="70e6c-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="70e6c-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="70e6c-103">In diesem Schritt werden Sie die Bibliothek der [Microsoft-Authentifizierungsbibliothek](https://github.com/AzureAD/microsoft-authentication-library-for-js) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="70e6c-103">In this step you will integrate the [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-js) library into the application.</span></span>

<span data-ttu-id="70e6c-104">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Config.js` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-104">Create a new file in the `./src` directory named `Config.js` and add the following code.</span></span>

```js
module.exports = {
  appId: 'YOUR_APP_ID_HERE',
  redirectUri: 'http://localhost:3000',
  scopes: [
    'user.read',
    'calendars.read'
  ]
};
```

<span data-ttu-id="70e6c-105">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal.</span><span class="sxs-lookup"><span data-stu-id="70e6c-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="70e6c-106">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `Config.js` aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="70e6c-106">If you're using source control such as git, now would be a good time to exclude the `Config.js` file from source control to avoid inadvertently leaking your app ID.</span></span>

<span data-ttu-id="70e6c-107">Öffnen `./src/App.js` Sie und fügen Sie `import` die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-107">Open `./src/App.js` and add the following `import` statements to the top of the file.</span></span>

```js
import config from './Config';
import { UserAgentApplication } from 'msal';
```

## <a name="implement-sign-in"></a><span data-ttu-id="70e6c-108">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="70e6c-108">Implement sign-in</span></span>

<span data-ttu-id="70e6c-109">Aktualisieren Sie zunächst die `constructor` für die `App` -Klasse, um eine Instanz der `UserAgentApplication` Klasse zu erstellen `getUser` , und rufen Sie ab, um zu sehen, ob bereits ein angemeldeter Benutzer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="70e6c-109">Start by updating the `constructor` for the `App` class to create an instance of the `UserAgentApplication` class and call `getUser` to see if there's already a logged-in user.</span></span> <span data-ttu-id="70e6c-110">Ersetzen Sie das `constructor` vorhandene durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="70e6c-110">Replace the existing `constructor` with the following.</span></span>

```js
constructor(props) {
  super(props);

  this.userAgentApplication = new UserAgentApplication({
        auth: {
            clientId: config.appId,
            redirectUri: config.redirectUri
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    });

  var user = this.userAgentApplication.getAccount();

  this.state = {
    isAuthenticated: (user !== null),
    user: {},
    error: null
  };

  if (user) {
    // Enhance user object with data from Graph
    this.getUserProfile();
  }
}
```

<span data-ttu-id="70e6c-111">Dieser Code initialisiert die `UserAgentApplication` Klasse mit ihrer Anwendungs-ID und überprüft, ob ein Benutzer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="70e6c-111">This code initializes the `UserAgentApplication` class with your application ID and checks for the presence of a user.</span></span> <span data-ttu-id="70e6c-112">Wenn ein Benutzer vorhanden ist, wird er `isAuthenticated` auf true festgelegt.</span><span class="sxs-lookup"><span data-stu-id="70e6c-112">If there is a user, it sets `isAuthenticated` to true.</span></span> <span data-ttu-id="70e6c-113">Die `getUserProfile` Methode ist noch nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="70e6c-113">The `getUserProfile` method isn't implemented yet.</span></span> <span data-ttu-id="70e6c-114">Sie werden diese ein wenig später implementieren.</span><span class="sxs-lookup"><span data-stu-id="70e6c-114">You will implement this a bit later.</span></span>

<span data-ttu-id="70e6c-115">Fügen Sie als nächstes der `App` Klasse eine Funktion hinzu, um die Anmeldung durchführen zu können.</span><span class="sxs-lookup"><span data-stu-id="70e6c-115">Next, add a function to the `App` class to do the login.</span></span> <span data-ttu-id="70e6c-116">Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="70e6c-116">Add the following function to the `App` class.</span></span>

```js
async login() {
  try {
    await this.userAgentApplication.loginPopup(
        {
          scopes: config.scopes,
          prompt: "select_account"
      });
    await this.getUserProfile();
  }
  catch(err) {
    var error = {};

    if (typeof(err) === 'string') {
      var errParts = err.split('|');
      error = errParts.length > 1 ?
        { message: errParts[1], debug: errParts[0] } :
        { message: err };
    } else {
      error = {
        message: err.message,
        debug: JSON.stringify(err)
      };
    }

    this.setState({
      isAuthenticated: false,
      user: {},
      error: error
    });
  }
}
```

<span data-ttu-id="70e6c-117">Diese Methode ruft die `loginPopup` Funktion auf, um die Anmeldung durchführen und `getUserProfile` dann die Funktion aufruft.</span><span class="sxs-lookup"><span data-stu-id="70e6c-117">This method calls the `loginPopup` function to do the login, then calls the `getUserProfile` function.</span></span>

<span data-ttu-id="70e6c-118">Fügen Sie nun eine Funktion zum Abmelden hinzu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-118">Now add a function to logout.</span></span> <span data-ttu-id="70e6c-119">Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="70e6c-119">Add the following function to the `App` class.</span></span>

```js
logout() {
  this.userAgentApplication.logout();
}
```

<span data-ttu-id="70e6c-120">Nachdem die `login` Methoden und `logout` implementiert wurden, aktualisieren Sie die `NavBar` Elemente `Welcome` und in der `render` -Methode der `App` Klasse.</span><span class="sxs-lookup"><span data-stu-id="70e6c-120">Now that the `login` and `logout` methods are implemented, update the `NavBar` and `Welcome` elements in the `render` method of the `App` class.</span></span> <span data-ttu-id="70e6c-121">Ersetzen Sie das `NavBar` vorhandene Element durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="70e6c-121">Replace the existing `NavBar` element with the following.</span></span>

```JSX
<NavBar
  isAuthenticated={this.state.isAuthenticated}
  authButtonMethod={this.state.isAuthenticated ? this.logout.bind(this) : this.login.bind(this)}
  user={this.state.user}/>
```

<span data-ttu-id="70e6c-122">Ersetzen Sie das `Welcome` vorhandene Element durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="70e6c-122">Replace the existing `Welcome` element with the following.</span></span>

```JSX
<Welcome {...props}
  isAuthenticated={this.state.isAuthenticated}
  user={this.state.user}
  authButtonMethod={this.login.bind(this)} />
```

<span data-ttu-id="70e6c-123">Implementieren Sie schließlich die `getUserProfile` -Funktion.</span><span class="sxs-lookup"><span data-stu-id="70e6c-123">Finally, implement the `getUserProfile` function.</span></span> <span data-ttu-id="70e6c-124">Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="70e6c-124">Add the following function to the `App` class.</span></span>

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent({
        scopes: config.scopes
      });

    if (accessToken) {
      // TEMPORARY: Display the token in the error flash
      this.setState({
        isAuthenticated: true,
        error: { message: "Access token:", debug: accessToken.accessToken }
      });
    }
  }
  catch(err) {
    var errParts = err.split('|');
    this.setState({
      isAuthenticated: false,
      user: {},
      error: { message: errParts[1], debug: errParts[0] }
    });
  }
}
```

<span data-ttu-id="70e6c-125">Dieser Code ruft `acquireTokenSilent` an, um ein Zugriffstoken abzurufen, und gibt dann lediglich das Token als Fehler aus.</span><span class="sxs-lookup"><span data-stu-id="70e6c-125">This code calls `acquireTokenSilent` to get an access token, then just outputs the token as an error.</span></span>

<span data-ttu-id="70e6c-126">Speichern Sie Ihre Änderungen, und aktualisieren Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="70e6c-126">Save your changes and refresh the browser.</span></span> <span data-ttu-id="70e6c-127">Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="70e6c-127">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="70e6c-128">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-128">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="70e6c-129">Die APP-Seite sollte aktualisiert werden, wobei das Token angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="70e6c-129">The app page should refresh, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="70e6c-130">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="70e6c-130">Get user details</span></span>

<span data-ttu-id="70e6c-131">Erstellen Sie zunächst eine neue Datei, in der alle Microsoft Graph-Anrufe gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="70e6c-131">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="70e6c-132">Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `GraphService.js` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-132">Create a new file in the `./src` directory called `GraphService.js` and add the following code.</span></span>

```js
var graph = require('@microsoft/microsoft-graph-client');

function getAuthenticatedClient(accessToken) {
  // Initialize Graph client
  const client = graph.Client.init({
    // Use the provided access token to authenticate
    // requests
    authProvider: (done) => {
      done(null, accessToken.accessToken);
    }
  });

  return client;
}

export async function getUserDetails(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const user = await client.api('/me').get();
  return user;
}
```

<span data-ttu-id="70e6c-133">Dadurch wird die `getUserDetails` Funktion implementiert, die das Microsoft Graph-SDK verwendet, `/me` um den Endpunkt aufzurufen und das Ergebnis zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="70e6c-133">This implements the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="70e6c-134">Aktualisieren Sie `getUserProfile` die- `./src/App.js` Methode in, um diese Funktion aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="70e6c-134">Update the `getUserProfile` method in `./src/App.js` to call this function.</span></span> <span data-ttu-id="70e6c-135">Fügen Sie zunächst die folgende `import` Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="70e6c-135">First, add the following `import` statement to the top of the file.</span></span>

```js
import { getUserDetails } from './GraphService';
```

<span data-ttu-id="70e6c-136">Ersetzen Sie die vorhandene `getUserProfile`-Funktion durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="70e6c-136">Replace the existing `getUserProfile` function with the following code.</span></span>

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent({
      scopes: config.scopes
    });

    if (accessToken) {
      // Get the user's profile from Graph
      var user = await getUserDetails(accessToken);
      this.setState({
        isAuthenticated: true,
        user: {
          displayName: user.displayName,
          email: user.mail || user.userPrincipalName
        },
        error: null
      });
    }
  }
  catch(err) {
    var error = {};
    if (typeof(err) === 'string') {
      var errParts = err.split('|');
      error = errParts.length > 1 ?
        { message: errParts[1], debug: errParts[0] } :
        { message: err };
    } else {
      error = {
        message: err.message,
        debug: JSON.stringify(err)
      };
    }

    this.setState({
      isAuthenticated: false,
      user: {},
      error: error
    });
  }
}
```

<span data-ttu-id="70e6c-137">Wenn Sie nun Ihre Änderungen speichern und die app starten, sollten Sie nach der Anmeldung wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="70e6c-137">Now if you save your changes and start the app, after sign-in you should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Ein Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

<span data-ttu-id="70e6c-139">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="70e6c-139">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="70e6c-140">Durch Klicken auf **Abmelden** wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="70e6c-140">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Screenshot des Dropdownmenüs mit dem Link zum Abmelden](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="70e6c-142">Speichern und Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="70e6c-142">Storing and refreshing tokens</span></span>

<span data-ttu-id="70e6c-143">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="70e6c-143">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="70e6c-144">Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="70e6c-144">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="70e6c-145">Dieses Token ist jedoch nur kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="70e6c-145">However, this token is short-lived.</span></span> <span data-ttu-id="70e6c-146">Das Token läuft eine Stunde nach seiner Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="70e6c-146">The token expires an hour after it is issued.</span></span> <span data-ttu-id="70e6c-147">Hier wird das Aktualisierungstoken nützlich.</span><span class="sxs-lookup"><span data-stu-id="70e6c-147">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="70e6c-148">Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass sich der Benutzer erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="70e6c-148">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="70e6c-149">Da die APP die MSAL-Bibliothek verwendet, müssen Sie keine Token-Speicher-oder Aktualisierungslogik implementieren.</span><span class="sxs-lookup"><span data-stu-id="70e6c-149">Because the app is using the MSAL library, you do not have to implement any token storage or refresh logic.</span></span> <span data-ttu-id="70e6c-150">Die `UserAgentApplication` -Methode speichert das Token in der Browsersitzung zwischen.</span><span class="sxs-lookup"><span data-stu-id="70e6c-150">The `UserAgentApplication` method caches the token in the browser session.</span></span> <span data-ttu-id="70e6c-151">Die `acquireTokenSilent` Methode überprüft zuerst das zwischengespeicherte Token, und wenn es nicht abgelaufen ist, wird es zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="70e6c-151">The `acquireTokenSilent` method first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="70e6c-152">Wenn er abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um ein neues zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="70e6c-152">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="70e6c-153">Sie verwenden diese Methode mehr im folgenden Modul.</span><span class="sxs-lookup"><span data-stu-id="70e6c-153">You'll use this method more in the following module.</span></span>
