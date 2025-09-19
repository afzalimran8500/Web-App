trigger:
- main

pool:
  vmImage: 'windows-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'

stages:
- stage: Build
  displayName: "Build Stage"
  jobs:
  - job: BuildJob
    displayName: 'Build job'
    steps:
      - task: NuGetToolInstaller@1

      - task: NuGetCommand@2
        inputs:
          restoreSolution: '$(solution)'

      - task: VSBuild@1
        inputs:
          solution: '$(solution)'
          msbuildArgs: >
            /p:DeployOnBuild=true 
            /p:WebPublishMethod=Package 
            /p:PackageAsSingleFile=true 
            /p:SkipInvalidConfigurations=true 
            /p:DesktopBuildPackageLocation="$(build.artifactStagingDirectory)\WebApp.zip" 
            /p:DeployIisAppPath="Default Web Site"
          platform: '$(buildPlatform)'
          configuration: '$(buildConfiguration)'

      - task: VSTest@2
        inputs:
          platform: '$(buildPlatform)'
          configuration: '$(buildConfiguration)'

      - task: PublishBuildArtifacts@1
        inputs:
          PathtoPublish: '$(Build.ArtifactStagingDirectory)'
          ArtifactName: 'drop'
          publishLocation: 'Container'

- stage: Deploy
  displayName: "Deploy Stage"
  dependsOn: Build
  condition: succeeded()
  jobs:
  - job: DeployJob
    displayName: "Deploy to Azure WebApp"
    steps:
      - task: DownloadBuildArtifacts@1
        inputs:
          buildType: 'current'
          downloadType: 'single'
          downloadPath: '$(System.ArtifactsDirectory)'

      - task: AzureRmWebAppDeployment@4
        inputs:
         ConnectionType: 'AzureRM'
         azureSubscription: 'Azure subscription 1(97360b6e-781f-49ee-8604-635e07a981b2)'
         appType: 'webApp'
         WebAppName: 'myonlinestudy'
         ResourceGroupName: 'MyRG'
         DeployToSlotOrASE: true
         SlotName: 'production'
         Package: '$(System.ArtifactsDirectory)/**/*.zip'
         DeploymentMethod: 'MSDeploy'

