# appsec-scan
This Action will combine the application security scanning tools

##Usage examples
```yaml
name: CI with Security Scanning

on:
  push:
    branches:
      - "main"
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3 # This will checkout the source codes from repository

    - name: Set up JDK 1.11 # This will add Java into the runners PATH
      uses: actions/setup-java@v3.6.0
      with:
        java-version: '11'
        distribution: 'temurin'
        cache: 'maven'
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: .
    #-----------AppSec Analysis START-----------------------------------#
    - uses: lejouni/appsec-scan@main
      with:
        #----------common param----------------#
        build_command: mvn -B package --file pom.xml
        log_level: DEBUG
        cache: false
        github_token: ${{secrets.GITHUB_TOKEN}}
        #----------polaris param---------------#
        polaris: true
        polaris_url: ${{secrets.POLARIS_SERVER_URL}}
        polaris_token: ${{secrets.POLARIS_ACCESS_TOKEN}}
        polaris_project: ${{github.repository}}
        polaris_branch: ${{github.ref_name}}
        polaris_sarif: true
        #----------black duck param------------#
        blackduck: true
        blackduck_url: ${{secrets.BLACKDUCK_SERVER_URL}}
        blackduck_token: ${{secrets.BLACKDUCK_ACCESS_TOKEN}}
        blackduck_project: ${{github.repository}}
        blackduck_version: ${{github.ref_name}}
        blackduck_sarif: true
    #-----------AppSec Analysis END-----------------------------------#
```
