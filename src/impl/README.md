# Webová aplikace pro kompasová dotazníková šetření

Tato aplikace slouží k vytváření, správě a vyhodnocování tzv. kompasových dotazníků. 
Jedná se o webový systém postavený na architektuře klient–server, kde backendová část 
je implementována v jazyce Java s využitím Spring Boot a frontendová část ve frameworku 
Vue.js.

## Požadavky na prostředí
- Java 17+
- Gradle 8.11
- Docker + Docker Compose (minimálně pro Keycloak a databázi)
- Node.js
- npm

## První nastavení aplikace

Následuje postup krok za krokem, jak celou aplikaci uvézt do provozu na lokální síti.

### Databáze a Keycloak

PostgreSQL databáze ukládá jak data služby Keycloak, tak samotné backendové aplikace.
Keycloak je open-source řešením správy uživatelských účtů. Obě služby lze spustit pomocí docker-compose:

1. `cd server/bak_survey_server/survey`
2. V souboru `docker-compose.yml` nastavte libovolné přihlašovací údaje k databázi.
3. `docker-compose up --build postgres keycloak`

Konfigurační soubor `docker-compose.yml` obsahuje i službu `survey-app`. Jde o kontejner obsahující
backendovou aplikaci, která v produkčním prostředí bude také kontejnerizovaná, nicméně pro zprovoznění
na localhost není potřeba.

Dále je potřeba provést prvotní konfiguraci služby Keycloak:

1. Zadejte v libovolném prohlížeči URL `http://localhost:8080`
2. Přihlašte se do administrátorského rozhraní (login: admin, password: admin)
3. Zvolte `Manage realms`
4. `Create realm`
5. Do položky `Realm name` vyplňte "CompassApp" a `Enabled` nechte na `On`
6. V levém panelu zvolte `Users`
7. `Create new user`
8. Zvolte si uživatelské jméno a pokračujte `Create`
9. Ze záložek v horní liště vyberte `Credentials`
10. `Set password`
11. Zvolte si libovolné přístupové heslo, odšktněte možnost `Temporary` a zvolte `Save`
12. Právě jste vytvořil/a první administrátorský účet
13. V levém panelu zvolte `Clients`
14. `Create client`
15. Nastavte `Client ID` na "compassclient" a zvolte `Next`
16. Nechte `Standard flow` a zvolte `Next`
17. Do položek `Valid redirect URIs` a `Web origins` vyplňte "*"
18. Zvolte `Save`
19. V levém panelu zvolte `Realm settings`
20. Vyberte záložku `Tokens`
21. Nastavte trvanlivost přístupových tokenů dle vašho uvážení, výchozí hodnoty jsou nicméně příliš krátké.

### Backendový server

Backendový server je implementován jako Spring Boot aplikace. 
Aby server funguval, musí být spuštěny databáze a služba Keycloak. Pro jeho první spuštění proveďte následující kroky:

1. `cd server/bak_survey_server/survey`
2. Upravte soubor `server/bak_survey_server/survey/src/main/resources/application.properties` tak, aby odpovídaly přihlašovací údaje k databázi.
3. `./gradlew bootRun`

Seznam endpointů backendové API najdete na `http://localhost:8082/swagger-ui/index.html#/`

### Frontendový server

Prezentační vrstva byla implementována ve frameworku Vue. Aby aplikace mohla fungovat, je nutné nejprve spustit
databázi, Keycloak službu a backendový Spring Boot server, viz kroky výše. Následně pro spuštění klienta na
localhostu ve vývojovém režimu proveďte následující kroky:

1. `cd client/bak_survey_client`
2. Přejmenujte soubor `.env-example` na `.env`
3. `npm install`
4. `npm run dev`

Frontendová aplikace je nyní dostupná na `http://localhost:3000`

### Repozitáře
Backendová aplikace: https://gitlab.fit.cvut.cz/vlcekm15/bak_survey_server.git
Frontendová aplikace: https://gitlab.fit.cvut.cz/vlcekm15/bak_survey_client.git
