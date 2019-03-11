<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Application Registry Portal (ARP).

1. Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.

    > [!NOTE]
    > Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.

1. Legen Sie auf der Seite **Ihre Anwendung registrieren** den **Anwendungsnamen** auf **reagieren Graph-Lernprogramm** fest, und wählen Sie **Erstellen**aus.

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](./images/arp-create-app-01.png)

1. Kopieren Sie auf der Seite mit der **Lernprogramm Registrierung des Reaktions Graphen** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.

    ![Screenshot der neu erstellten Anwendungs-ID](./images/arp-create-app-02.png)

1. Scrollen Sie nach unten zum Abschnitt **Plattformen** .

    1. Wählen Sie **Plattform hinzufügen**aus.
    1. Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.

        ![Screenshot Erstellen einer Plattform für die APP](./images/arp-create-app-03.png)

    1. Geben Sie **** im Feld Webplattform für `http://localhost:3000` die Umleitungs- **URLs**ein.

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](./images/arp-create-app-04.png)

1. Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.