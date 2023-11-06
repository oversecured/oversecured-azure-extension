Oversecured Extension for Azure
============================

This Azure Extension enables you to automatically upload your app versions to Oversecured for security scanning. An extension user must have an [active Integration](https://oversecured.com/integrations).

### Inputs

- `access_token`: Required. Your Oversecured API key
- `integration_id`: Required. The integration ID from Oversecured
- `branch_name`: Optional. The branch name, `main` is default
- `app_path`: Required. The path to the app file you wish to upload

### Usage

1. Store your Oversecured API key as a secret in your Azure repository. Navigate to your Azure repository, go to the `Pipelines` tab, pick the pipeline, select `Edit` and then `Variables`, and click the `+` (`New variable`) button. Name the secret `OVERSECURED_API_KEY`, paste your key and pick `Keep this value secret`.
2. Add the Oversecured step to your Azure workflow.

Android example:
```yml
pool:
  vmImage: ubuntu-latest

steps:
- task: JavaToolInstaller@0
  inputs:
    versionSpec: '17'
    jdkArchitectureOption: 'x64'
    jdkSourceOption: 'PreInstalled'

- task: Gradle@2
  inputs:
    workingDirectory: ''
    gradleWrapperFile: 'gradlew'
    gradleOptions: '-Xmx3072m'
    publishJUnitResults: false
    testResultsFiles: '**/TEST-*.xml'
    tasks: 'assembleDebug'

- task: oversecured-azure-extension@1
  displayName: Oversecured scanner
  inputs:
    access_token: $(OVERSECURED_API_KEY)
    integration_id: $(OVERSECURED_INTEGRATION_ID)
    branch_name: $(OVERSECURED_BRANCH_NAME)
    app_path: ./app/build/outputs/apk/debug/app-debug.apk
```

iOS example:
```yml
pool:
  vmImage: macos-latest

steps:
- script: |
    gem install cocoapods --pre
  displayName: Install CocoaPods

- script: |
    pod install
  displayName: Pods Install

- script: |
    zip -q -r OversecuredZipped.zip .
  displayName: Zip Sources

- task: oversecured-azure-extension@1
  displayName: Oversecured
  inputs:
    access_token: $(OVERSECURED_API_KEY)
    integration_id: $(OVERSECURED_INTEGRATION_ID)
    branch_name: $(OVERSECURED_BRANCH_NAME)
    app_path: OversecuredZipped.zip
```


Have Question or Feedback?
--------------------------

Submit a request using the [contact form](https://support.oversecured.com/hc/en-us/requests/new).


### License

The scripts and documentation in this project are released under the [MIT License](https://github.com/oversecured/oversecured-github/blob/main/LICENSE).
