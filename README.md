Fintech Application
This is a Fintech project built using the Java and  MERN (MongoDB, Express.js, React, Node.js) stack. It includes features like authentication, posts, retweeting, and user registration with validation. Below, you'll find instructions on how to install and run the project.

Features
User Registration: New users can sign up with unique usernames and emails.
Authentication: Secure login and logout functionality.
Posts: Users can create, view, and interact with posts. Posts are visible to unregistered users too, but to make post they you the authentication.


User Stories
As a new user
So that I can use application
I want to be able to register for an account

As a registered user
So that I can interact with others on the platform
I want to be able to log in to my account

As a logged-in user
So that I can share my thoughts and updates
I want to be able to create and post messages (posts) on the platform

As a logged-in user
So that I can engage with content
I want to be able to view posts in reverse chronological order and who created them

As a logged-in user
So that I can engage with content
I want to have the option to retweet (share) posts

As a logged-in user
So that I can manage my account
I want to be able to log out of my account

As a logged-in user
So that I can engage with content
I want to be able to like (or upvote) posts
Installation
Follow these steps to get the project up and running on your local machine:

Clone the Repository: Use the following command to clone the project repository to your local machine:

git clone <repository-url> // Replace <repository-url> with the actual URL of project's repository.
Install Server Dependencies: In the project directory, navigate to the server folder and run:

cd server
npm install
Install Client Dependencies: Navigate to the client folder and run:

cd client
npm install
Start Your Application: After all dependencies are installed, you can start your application. In the server and client folders, run server:

npm start
Access Your Application: Once both the server and client are running:

for Backend application

Prerequisites
Java 21, Docker for building + running the containerized application:

brew install openjdk@21
brew install --cask docker # or just `brew install docker` if you don't want the Desktop app
For the provided Git hooks you will need:

brew install lefthook node talisman
Getting started
To get started with development run:

./run.sh init
This will replace placeholders in the application template and install a couple of Git hooks.

Tests
The project has distinct unit and integration test sets.

To run just the unit tests:

./gradlew test
To run the integration tests:

./gradlew integrationTest
Note: Running integration tests requires passing unit tests (in Gradle terms: integration tests depend on unit tests), so unit tests are going to be run first. In case there are failing unit tests we won't attempt to continue running any integration tests.

To run integration tests exclusively, without the unit test dependency:

./gradlew integrationTest --exclude-task test
Denoting an integration test is accomplished by using a JUnit 5 tag annotation: @Tag("integration").

Furthermore, there is another type of test worth mentioning. We're using ArchUnit for ensuring certain architectural characteristics, for instance making sure that there are no cyclic dependencies.

Formatting
Java source code formatting must conform to the Google Java Style. Consistent formatting, for Java as well as various other types of source code, is being enforced via Spotless.

Check formatting:

./gradlew spotlessCheck
Autoformat sources:

./gradlew spotlessApply
Git hooks
The repo contains a Lefthook configuration, providing a Git hooks setup out of the box.

To install these hooks, run:

./run.sh init
The hooks are supposed to help you to:

commit properly formatted source code only (and not break the build otherwise)
write conventional commit messages
not accidentally push secrets and sensitive information
Code quality analysis
Continuous code quality analysis is performed in the pipeline upon pushing to trunk; it requires a token provided as SONAR_TOKEN repository secret that needs to be obtained from https://sonarcloud.io.

To run the analysis locally:

SONAR_TOKEN=[sonar-token] ./gradlew sonarqube
Go to https://sonarcloud.io for the analysis results.

Container image
Container images running the application are automatically published by the pipeline to the GitHub Packages Container registry.

To run the latest published image:

docker run -p8080:8080 "ghcr.io/digitalservicebund/java-application-template:$(git log -1 origin/main --format='%H')"
The service will be accessible at http://localhost:8080.

We are using Spring's built-in support for producing an optimized container image:

./gradlew bootBuildImage
docker run -p8080:8080 ghcr.io/digitalservicebund/java-application-template
Container images in the registry are signed with keyless signatures.

To verify an image:

cosign verify "ghcr.io/digitalservicebund/java-application-template:$(git log -1 origin/main --format='%H')" --certificate-identity="https://github.com/digitalservicebund/java-application-template/.github/workflows/pipeline.yml@refs/heads/main" --certificate-oidc-issuer="https://token.actions.githubusercontent.com"
If you need to push a new container image to the registry manually there are two ways to do this:

Via built-in Gradle task:

export CONTAINER_REGISTRY=ghcr.io
export CONTAINER_IMAGE_NAME=digitalservicebund/java-application-template
export CONTAINER_IMAGE_VERSION="$(git log -1 --format='%H')"
CONTAINER_REGISTRY_USER=[github-user] CONTAINER_REGISTRY_PASSWORD=[github-token] ./gradlew bootBuildImage --publishImage
Note: Make sure you're using a GitHub token with the necessary write:packages scope for this to work.

Using Docker:

echo [github-token] | docker login ghcr.io -u [github-user] --password-stdin
docker push "ghcr.io/digitalservicebund/java-application-template:$(git log -1 --format='%H')"
Note: Make sure you're using a GitHub token with the necessary write:packages scope for this to work.

Vulnerability Scanning
Scanning container images for vulnerabilities is performed with Trivy as part of the pipeline's build job, as well as each night for the latest published image in the container repository.

To run a scan locally:

Install Trivy:

brew install aquasecurity/trivy/trivy
./gradlew bootBuildImage
trivy image --severity HIGH,CRITICAL ghcr.io/digitalservicebund/java-application-template:latest
As part of the automated vulnerability scanning we are generating a Cosign vulnerability scan record using Trivy, and then use Cosign to attach an attestation of it to the container image, again signed with keyless signatures similar to signing the container image itself. Using a policy engine in a cluster the vulnerability scan can be verified and for instance running a container rejected if a scan is not current.

License Scanning
License scanning is performed as part of the pipeline's build job. Whenever a production dependency is being added with a yet unknown license the build is going to fail.

To run a scan locally:

./gradlew checkLicense

Open a web browser and navigate to http://localhost:5000
Author:
Suraj I hope you find inspiration and valuable insights from exploring the Twitter Clone project.


Happy coding! 🚀
