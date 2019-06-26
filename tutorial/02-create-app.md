<!-- markdownlint-disable MD002 MD041 -->

Öffnen Sie die Befehlszeilenschnittstelle (CLI), navigieren Sie zu einem Verzeichnis, in dem Sie Berechtigungen zum Erstellen von Dateien haben, und führen Sie die folgenden Befehle aus, um das [Create-reagiere-App-](https://www.npmjs.com/package/create-react-app) Tool zu installieren und eine neue Reaktions-APP zu erstellen.

```Shell
npm install create-react-app@3.0.1 -g
create-react-app graph-tutorial
```

Nachdem der Befehl abgeschlossen ist, wechseln Sie `graph-tutorial` in das Verzeichnis in der CLI, und führen Sie den folgenden Befehl aus, um einen lokalen Webserver zu starten.

```Shell
npm start
```

Ihr Standardbrowser wird [https://localhost:3000/](https://localhost:3000) mit einer Standard reaktionsseite geöffnet. Wenn Ihr Browser nicht geöffnet wird, öffnen Sie ihn, [https://localhost:3000/](https://localhost:3000) und navigieren Sie zu, um zu überprüfen, ob die neue APP funktioniert.

Bevor Sie fortfahren, installieren Sie einige zusätzliche Pakete, die Sie später verwenden werden:

- [reagiere-Router-Dom](https://github.com/ReactTraining/react-router) für das deklarative Routing innerhalb der Reaktions-app.
- [Bootstrap](https://github.com/twbs/bootstrap) für Styling und allgemeine Komponenten.
- [reactstrap](https://github.com/reactstrap/reactstrap) für reaktionskomponenten basierend auf Bootstrap.
- [fontawesome-kostenlos](https://github.com/FortAwesome/Font-Awesome) für Symbole.
- [Zeitpunkt](https://github.com/moment/moment) für die Formatierung von Datums-und Uhrzeitangaben.
- [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js) zur Authentifizierung bei Azure Active Directory und zum Abrufen von Zugriffstoken.
- [Microsoft-Graph-Client](https://github.com/microsoftgraph/msgraph-sdk-javascript) zum tätigen von Anrufen an Microsoft Graph.

Führen Sie den folgenden Befehl in der CLI aus.

```Shell
npm install react-router-dom@5.0.0 bootstrap@4.3.1 reactstrap@8.0.0 @fortawesome/fontawesome-free@5.8.2
npm install moment@2.24.0 msal@1.0.0 @microsoft/microsoft-graph-client@1.6.0
```

## <a name="design-the-app"></a>Entwerfen der APP

Erstellen Sie zunächst ein navbar für die app. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Navbar.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```JSX
import React from 'react';
import { NavLink as RouterNavLink } from 'react-router-dom';
import {
  Collapse,
  Container,
  Navbar,
  NavbarToggler,
  NavbarBrand,
  Nav,
  NavItem,
  NavLink,
  UncontrolledDropdown,
  DropdownToggle,
  DropdownMenu,
  DropdownItem } from 'reactstrap';
import '@fortawesome/fontawesome-free/css/all.css';

function UserAvatar(props) {
  // If a user avatar is available, return an img tag with the pic
  if (props.user.avatar) {
    return <img
            src={props.user.avatar} alt="user"
            className="rounded-circle align-self-center mr-2"
            style={{width: '32px'}}></img>;
  }

  // No avatar available, return a default icon
  return <i
          className="far fa-user-circle fa-lg rounded-circle align-self-center mr-2"
          style={{width: '32px'}}></i>;
}

function AuthNavItem(props) {
  // If authenticated, return a dropdown with the user's info and a
  // sign out button
  if (props.isAuthenticated) {
    return (
      <UncontrolledDropdown>
        <DropdownToggle nav caret>
          <UserAvatar user={props.user}/>
        </DropdownToggle>
        <DropdownMenu right>
          <h5 className="dropdown-item-text mb-0">{props.user.displayName}</h5>
          <p className="dropdown-item-text text-muted mb-0">{props.user.email}</p>
          <DropdownItem divider />
          <DropdownItem onClick={props.authButtonMethod}>Sign Out</DropdownItem>
        </DropdownMenu>
      </UncontrolledDropdown>
    );
  }

  // Not authenticated, return a sign in link
  return (
    <NavItem>
      <NavLink onClick={props.authButtonMethod}>Sign In</NavLink>
    </NavItem>
  );
}

export default class NavBar extends React.Component {
  constructor(props) {
    super(props);

    this.toggle = this.toggle.bind(this);
    this.state = {
      isOpen: false
    };
  }

  toggle() {
    this.setState({
      isOpen: !this.state.isOpen
    });
  }

  render() {
    // Only show calendar nav item if logged in
    let calendarLink = null;
    if (this.props.isAuthenticated) {
      calendarLink = (
        <NavItem>
          <RouterNavLink to="/calendar" className="nav-link" exact>Calendar</RouterNavLink>
        </NavItem>
      );
    }

    return (
      <div>
        <Navbar color="dark" dark expand="md" fixed="top">
          <Container>
            <NavbarBrand href="/">React Graph Tutorial</NavbarBrand>
            <NavbarToggler onClick={this.toggle} />
            <Collapse isOpen={this.state.isOpen} navbar>
              <Nav className="mr-auto" navbar>
                <NavItem>
                  <RouterNavLink to="/" className="nav-link" exact>Home</RouterNavLink>
                </NavItem>
                {calendarLink}
              </Nav>
              <Nav className="justify-content-end" navbar>
                <NavItem>
                  <NavLink href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                    <i className="fas fa-external-link-alt mr-1"></i>
                    Docs
                  </NavLink>
                </NavItem>
                <AuthNavItem
                  isAuthenticated={this.props.isAuthenticated}
                  authButtonMethod={this.props.authButtonMethod}
                  user={this.props.user} />
              </Nav>
            </Collapse>
          </Container>
        </Navbar>
      </div>
    );
  }
}
```

Erstellen Sie als nächstes eine Startseite für die app. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Welcome.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```JSX
import React from 'react';
import {
  Button,
  Jumbotron } from 'reactstrap';

function WelcomeContent(props) {
  // If authenticated, greet the user
  if (props.isAuthenticated) {
    return (
      <div>
        <h4>Welcome {props.user.displayName}!</h4>
        <p>Use the navigation bar at the top of the page to get started.</p>
      </div>
    );
  }

  // Not authenticated, present a sign in button
  return <Button color="primary" onClick={props.authButtonMethod}>Click here to sign in</Button>;
}

export default class Welcome extends React.Component {
  render() {
    return (
      <Jumbotron>
        <h1>React Graph Tutorial</h1>
        <p className="lead">
            This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from React
        </p>
        <WelcomeContent
          isAuthenticated={this.props.isAuthenticated}
          user={this.props.user}
          authButtonMethod={this.props.authButtonMethod} />
      </Jumbotron>
    );
  }
}
```

Erstellen Sie nun eine Fehlermeldung, um Nachrichten an den Benutzer anzuzeigen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `ErrorMessage.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```JSX
import React from 'react';
import { Alert } from 'reactstrap';

export default class ErrorMessage extends React.Component {
  render() {
    let debug = null;
    if (this.props.debug) {
      debug = <pre className="alert-pre border bg-light p-2"><code>{this.props.debug}</code></pre>;
    }
    return (
      <Alert color="danger">
        <p className="mb-3">{this.props.message}</p>
        {debug}
      </Alert>
    );
  }
}
```

Nachdem diese grundlegenden Komponenten definiert wurden, aktualisieren Sie die APP, um Sie zu verwenden. Öffnen Sie zunächst die `./src/index.css` Datei, und ersetzen Sie den gesamten Inhalt durch Folgendes.

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

Öffnen Sie anschließend `./src/App.js` den gesamten Inhalt, und ersetzen Sie ihn durch Folgendes.

```JSX
import React, { Component } from 'react';
import { BrowserRouter as Router, Route } from 'react-router-dom';
import { Container } from 'reactstrap';
import NavBar from './NavBar';
import ErrorMessage from './ErrorMessage';
import Welcome from './Welcome';
import 'bootstrap/dist/css/bootstrap.css';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      isAuthenticated: false,
      user: {},
      error: null
    };
  }

  render() {
    let error = null;
    if (this.state.error) {
      error = <ErrorMessage message={this.state.error.message} debug={this.state.error.debug} />;
    }

    return (
      <Router>
        <div>
          <NavBar
            isAuthenticated={this.state.isAuthenticated}
            authButtonMethod={null}
            user={this.state.user}/>
          <Container>
            {error}
            <Route exact path="/"
              render={(props) =>
                <Welcome {...props}
                  isAuthenticated={this.state.isAuthenticated}
                  user={this.state.user}
                  authButtonMethod={null} />
              } />
          </Container>
        </div>
      </Router>
    );
  }

  setErrorMessage(message, debug) {
    this.setState({
      error: {message: message, debug: debug}
    });
  }
}

export default App;
```

Speichern Sie alle Änderungen, und aktualisieren Sie die Seite. Nun sollte die APP sehr unterschiedlich aussehen.

![Screenshot der neu gestalteten Startseite](images/create-app-01.png)
