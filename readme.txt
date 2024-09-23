SCM basic authentication is disabled for your app. Click here to go to your configuration settings to enable, or choose the "User-assigned identity" option below.
Deploy and build code from your preferred source and build provider. Learn more

Source*
Building with GitHub Actions.Change provider.
GitHub
App Service will place a GitHub Actions workflow in your chosen repository to build and deploy your app whenever there is a commit on the chosen branch. If you can't find an organization or repository, you may need to enable additional permissions on GitHub. You must have write access to your chosen GitHub repository to deploy with GitHub Actions. Learn more

Signed in as
ajadaniChange Account
Organization*
ajadani
Repository*
web-thymeleaf
Branch*
main
Build
Runtime stack
Java
Version
8.0
Java web server stack
Java SE
Authentication settings
Select how you want your GitHub Action workflow to authenticate to Azure. If you choose user-assigned identity, the identity selected will be federated with GitHub as an authorized client and given write permissions on the app. Learn more

Authentication type*


Workflow Configuration
File with the workflow configuration defined by the settings above.

Workflow Configuration
File path: .github/workflows/main_spring-az-demo.yml

If an existing workflow configuration exists, it will be overwritten.
# Docs for the Azure Web Apps Deploy action: https://github.com/Azure/webapps-deploy
# More GitHub Actions for Azure: https://github.com/Azure/actions

name: Build and deploy JAR app to Azure Web App - spring-az-demo

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build:
    runs-on: windows-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up Java version
        uses: actions/setup-java@v4
        with:
          java-version: '8'
          distribution: 'microsoft'

      - name: Build with Maven
        run: mvn clean install

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v4
        with:
          name: java-app
          path: '${{ github.workspace }}/target/*.jar'

  deploy:
    runs-on: windows-latest
    needs: build
    environment:
      name: 'production'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}
    
    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v4
        with:
          name: java-app
      
      - name: Deploy to Azure Web App
        id: deploy-to-webapp
        uses: azure/webapps-deploy@v3
        with:
          app-name: 'spring-az-demo'
          slot-name: 'production'
          package: '*.jar'
          publish-profile: ${{ secrets.AzureAppService_PublishProfile_725f1cb1358d43d1be3e6bcef3e51008 }}