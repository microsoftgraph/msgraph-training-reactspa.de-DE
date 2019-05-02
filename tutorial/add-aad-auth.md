<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung zur Unterstützung der Authentifizierung mit Azure AD. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken für den Aufruf von Microsoft Graph abzurufen. In diesem Schritt integrieren Sie die [Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-js) -Authentifizierungs Bibliotheks Bibliothek in die Anwendung.

Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Config.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```js
module.exports = {
  appId: 'YOUR_APP_ID_HERE',
  scopes: [
    "user.read",
    "calendars.read"
  ]
};
```

Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal.

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `Config.js` aus der Quellcodeverwaltung auszuschließen, um versehentlich Ihre APP-ID zu vernichten.

Öffnen `./src/App.js` Sie und fügen Sie `import` die folgenden Anweisungen am Anfang der Datei.

```js
import config from './Config';
import { UserAgentApplication } from 'msal';
```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

Beginnen Sie damit, `constructor` die `App` Klasse zu aktualisieren, um eine Instanz der `UserAgentApplication` Klasse zu erstellen `getUser` , und rufen Sie ab, ob bereits ein angemeldeter Benutzer vorhanden ist. Ersetzen Sie das `constructor` vorhandene durch Folgendes.

```js
constructor(props) {
  super(props);

  this.userAgentApplication = new UserAgentApplication(config.appId, null, null);

  var user = this.userAgentApplication.getUser();

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

Dieser Code initialisiert die `UserAgentApplication` Klasse mit ihrer Anwendungs-ID und überprüft, ob ein Benutzer vorhanden ist. Wenn ein Benutzer vorhanden ist, wird er `isAuthenticated` auf true festgelegt. Die `getUserProfile` Methode ist noch nicht implementiert. Sie implementieren dies etwas später.

Als Nächstes fügen Sie der `App` Klasse eine Funktion hinzu, um die Anmeldung durchzuführen. Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:

```js
async login() {
  try {
    await this.userAgentApplication.loginPopup(config.scopes);
    await this.getUserProfile();
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

Diese Methode ruft die `loginPopup` Funktion zum Ausführen der Anmeldung auf und ruft dann `getUserProfile` die Funktion auf.

Fügen Sie nun eine Funktion zum Logout hinzu. Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:

```js
logout() {
  this.userAgentApplication.logout();
}
```

Nachdem `login` die `logout` and-Methoden implementiert wurden, aktualisieren Sie `NavBar` die `Welcome` -und- `render` Elemente in der `App` -Methode der Klasse. Ersetzen Sie das `NavBar` vorhandene Element durch Folgendes.

```JSX
<NavBar
  isAuthenticated={this.state.isAuthenticated}
  authButtonMethod={this.state.isAuthenticated ? this.logout.bind(this) : this.login.bind(this)}
  user={this.state.user}/>
```

Ersetzen Sie das `Welcome` vorhandene Element durch Folgendes.

```JSX
<Welcome {...props}
  isAuthenticated={this.state.isAuthenticated}
  user={this.state.user}
  authButtonMethod={this.login.bind(this)} />
```

Implementieren Sie schließlich die `getUserProfile` -Funktion. Fügen Sie die folgende Funktion zur `App`-Klasse hinzu:

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent(config.scopes);

    if (accessToken) {
      // TEMPORARY: Display the token in the error flash
      this.setState({
        isAuthenticated: true,
        error: { message: "Access token:", debug: accessToken }
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

Dieser Code ruft `acquireTokenSilent` zum Abrufen eines Zugriffstokens auf und gibt dann nur das Token als Fehler aus.

Speichern Sie die Änderungen, und aktualisieren Sie den Browser. Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den erforderlichen Berechtigungen zu. Die APP-Seite sollte mit dem Token aktualisiert werden.

### <a name="get-user-details"></a>Benutzer Details abrufen

Erstellen Sie zunächst eine neue Datei für alle Microsoft Graph-Aufrufe. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `GraphService.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```js
var graph = require('@microsoft/microsoft-graph-client');

function getAuthenticatedClient(accessToken) {
  // Initialize Graph client
  const client = graph.Client.init({
    // Use the provided access token to authenticate
    // requests
    authProvider: (done) => {
      done(null, accessToken);
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

Dadurch wird die `getUserDetails` Funktion implementiert, die das Microsoft Graph-SDK zum Aufrufen `/me` des Endpunkts und zum Zurückgeben des Ergebnisses verwendet.

Aktualisieren Sie `getUserProfile` die- `./src/App.js` Methode in, um diese Funktion aufzurufen. Fügen Sie zunächst die folgende `import` Anweisung am Anfang der Datei hinzu.

```js
import { getUserDetails } from './GraphService';
```

Ersetzen Sie die vorhandene `getUserProfile`-Funktion durch den folgenden Code.

```js
async getUserProfile() {
  try {
    // Get the access token silently
    // If the cache contains a non-expired token, this function
    // will just return the cached token. Otherwise, it will
    // make a request to the Azure OAuth endpoint to get a token

    var accessToken = await this.userAgentApplication.acquireTokenSilent(config.scopes);

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

Wenn Sie nun Ihre Änderungen speichern und die app starten, sollten Sie nach der Anmeldung wieder auf der Homepage enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.

![Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

Klicken Sie auf den Benutzer Avatar in der oberen rechten Ecke, **** um auf den Link abmelden zuzugreifen. Wenn **** Sie auf Abmelden klicken, wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite.

![Screenshot des Dropdownmenüs mit dem Link "Abmelden"](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>Speichern und Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, mit dem die APP auf Microsoft Graph im Namen des Benutzers zugreifen kann.

Dieses Token ist jedoch kurzlebig. Das Token läuft eine Stunde nach der Ausgabe ab. An dieser Stelle wird das Aktualisierungstoken nützlich. Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.

Da die APP die MSAL-Bibliothek verwendet, müssen Sie keine Tokenspeicher-oder Aktualisierungslogik implementieren. Die `UserAgentApplication` -Methode speichert das Token in der Browsersitzung zwischen. Die `acquireTokenSilent` -Methode überprüft zuerst das zwischengespeicherte Token, und wenn es nicht abgelaufen ist, wird es zurückgegeben. Wenn es abgelaufen ist, wird das zwischengespeicherte Aktualisierungstoken verwendet, um eine neue zu erhalten. Sie verwenden diese Methode im folgenden Modul.