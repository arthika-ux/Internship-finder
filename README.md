# Internship Finder for Students

A beginner-friendly college mini project that displays **sample demonstration listings**. The examples in this application are not real or verified vacancies. The website uses HTML, CSS, and JavaScript; Spring Boot serves the website and provides its JSON REST API. Students can create local accounts and sign in; browsing internship listings remains public.

## Requirements

- JDK 17 or later
- Apache Maven 3.6 or later
- A web browser
- Internet access on the first Maven build to download dependencies

The Maven configuration compiles the project for Java 17. If several JDK versions are installed, select JDK 17 for the terminal before building.

## Build and run on Windows CMD

Open Command Prompt and move into the project folder. Quote the path because it contains a space:

```cmd
cd /d "C:\Users\DELL\OneDrive\Desktop\internship finder"
java -version
mvn -version
mvn package
java -jar target\internship-finder-1.0.0.jar
```

Wait for Spring Boot to report that it has started, then open:

```text
http://localhost:8080
```

Keep that CMD window open while using the application. Press `Ctrl+C` in that window to stop it. After the first successful build, `mvn spring-boot:run` can also start the application from the project folder.

## Check the API

Open these URLs in a browser, or run the commands below in a second CMD window. The listing endpoint returns JSON and accepts any combination of the filters.

```cmd
curl.exe "http://localhost:8080/api/internships"
curl.exe "http://localhost:8080/api/internships?keyword=Java&domain=Software%20Engineering&paymentStatus=unpaid"
curl.exe "http://localhost:8080/api/internships?location=Remote"
curl.exe "http://localhost:8080/api/internships/1"
```

For a validation check, an unsupported payment value returns HTTP 400 with a JSON error message:

```cmd
curl.exe -i "http://localhost:8080/api/internships?paymentStatus=maybe"
```

An unknown positive listing ID returns HTTP 404. A non-positive ID or filter longer than 100 characters returns HTTP 400.

## Demonstrate the project

1. Start the project using the commands above and show the homepage at `http://localhost:8080`.
2. Point out the demonstration-data notice; all initial listings are sample data and are not verified vacancies.
3. Search for `Java`, then try a different skill such as `Python`.
4. Choose a domain, location, and paid/unpaid option. Filters combine, and the results update from the backend.
5. Choose filter values that return no matches and show the empty state. Use **Clear filters** to reset.
6. Open a card with **View details** and show its skills, duration, location, description, and sample-data note.
7. Select **Student sign in**, create an account with an email and a password of at least 8 characters, sign out, then sign in again.
8. In CMD, request `/api/internships?keyword=Java` to show the JSON REST API, then try the invalid-payment command to show error handling.

## Project file guide

- `pom.xml`: Maven project identity, Spring Boot dependency, and Java 17 compiler target.
- `src/main/java/com/example/internshipfinder/InternshipFinderApplication.java`: application entry point; starts Spring Boot.
- `src/main/java/com/example/internshipfinder/model/Internship.java`: internship data model serialized as JSON. The `demoData` field identifies sample records.
- `src/main/java/com/example/internshipfinder/service/InternshipService.java`: in-memory sample listings and filtering logic. Replacing this service with a database-backed implementation is a natural next step.
- `src/main/java/com/example/internshipfinder/controller/InternshipController.java`: REST routes, filter parameters, input validation, and not-found behavior.
- `src/main/java/com/example/internshipfinder/exception/ApiExceptionHandler.java`: converts expected API errors into readable JSON responses.
- `src/main/java/com/example/internshipfinder/auth/StudentAccount.java`: student account entity. Only a BCrypt password hash is stored, never the submitted password.
- `src/main/java/com/example/internshipfinder/auth/StudentAccountRepository.java`: database access for student accounts.
- `src/main/java/com/example/internshipfinder/auth/StudentAccountDetailsService.java`: finds accounts for Spring Security authentication.
- `src/main/java/com/example/internshipfinder/security/SecurityConfig.java`: configures BCrypt, session security, and CSRF protection. Internship browsing stays public.
- `src/main/java/com/example/internshipfinder/controller/AuthController.java`: registration, sign-in, sign-out, session status, and CSRF-token API routes.
- `src/main/resources/application.properties`: application name and local server port (`8080`).
- `src/main/resources/application-production.properties`: PostgreSQL connection and HTTPS-only session cookie configuration for hosting.
- `Dockerfile`: builds the app with Java 17 and packages a small runtime image.
- `render.yaml`: Render blueprint for a web service and PostgreSQL database.
- `src/main/resources/static/index.html`: page structure, navigation, filter controls, listing area, and details dialog.
- `src/main/resources/static/style.css`: responsive layout, colors, typography, cards, and mobile styles.
- `src/main/resources/static/script.js`: fetches API data, submits search/filter values, renders cards safely, and opens listing details.
- `src/main/resources/static/login.html`: sign-in and account-creation page.
- `src/main/resources/static/login.css`: responsive styles for the sign-in page.
- `src/main/resources/static/login.js`: calls the authentication API and handles CSRF tokens, validation feedback, session status, and sign-out.

## API reference

- `GET /api/internships`: list all sample records, optionally filtered by `keyword`, `domain`, `location`, and `paymentStatus`.
- `GET /api/internships/{id}`: retrieve one sample record by its positive numeric ID.
- `GET /api/auth/csrf`: get the CSRF token required for account-changing requests.
- `GET /api/auth/me`: check whether the current session is signed in.
- `POST /api/auth/register`: create an account with JSON fields `email`, `password`, and `confirmPassword`.
- `POST /api/auth/login`: sign in with JSON fields `email` and `password`.
- `POST /api/auth/logout`: end the current session.
- Supported domains: `Software Engineering`, `Data Science`, `Cybersecurity`, `Product Design`, and `Marketing`.
- Supported `paymentStatus` values: `paid` and `unpaid` (case-insensitive).

Internship data is in memory, so listing changes are not persisted. Student accounts are stored in the local H2 database under the `data` folder and persist across restarts. Passwords are BCrypt-hashed; sessions use HTTP-only cookies and mutating authentication routes require CSRF tokens. Search checks titles, company names, locations, domains, skills, and descriptions. An empty result is a successful response with an empty JSON array.

This is a local learning project, not a production identity service. It does not include email verification, password reset, abuse throttling, or deployment-grade database backup and secret management. Do not expose it publicly or use an important password.

## Publish a public URL with Render

The application is prepared for deployment, but a public URL can only be created from an account that owns the deployment. You will need a GitHub account and a Render account. Do not commit passwords, database secrets, or credentials to the repository.

1. Create a GitHub repository for this project and push the project files to its default branch. Include `Dockerfile` and `render.yaml`.
2. Sign in to Render and choose **New +** then **Blueprint**.
3. Connect your GitHub account, select this repository, and apply the blueprint. It creates the web service and its PostgreSQL database, then builds and deploys the app.
4. In Render, wait for the web service deployment to finish successfully. Open the service's `onrender.com` URL. That is the public URL to share.
5. Check the homepage, create an account, sign out, sign in again, and confirm listings load.

Render may require a payment method and plan changes; availability and free-tier limits can change. If the database or service is suspended, deleted, or its storage is not persistent, account data can become unavailable. For a real public application, choose an appropriate persistent database plan and configure backups. The listings themselves remain demonstration data.

### Push the project from Windows CMD

After creating an empty GitHub repository, use its URL in the final command below:

```cmd
cd /d "C:\Users\DELL\OneDrive\Desktop\internship finder"
git init
git add .
git commit -m "Prepare Internship Finder for deployment"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git push -u origin main
```

If the folder is already a Git repository, do not run `git init` or replace an existing remote; check `git status` and `git remote -v` first, then commit and push using your normal workflow.

## Future AI ideas (not implemented)

- **Skill matching:** compare skills a student enters with a listing's required skills, then rank matching opportunities and explain the overlap.
- **Resume-based recommendations:** with clear student consent, extract skills from an uploaded resume and use them to personalize results. A production implementation would need privacy, retention, and file-security safeguards.
- **Skills to learn:** compare a student's skills with the skills in roles they want and suggest a prioritized learning list. Recommendations should be transparent and should not promise selection or employment.

The current project does not upload resumes, call an AI service, or generate recommendations. A future recommendation service could be added beside `InternshipService` and exposed through a separate API endpoint without mixing AI logic into the page or controller.