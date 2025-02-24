---
published: true
title: FOSSLight Dependency Scanner
---
# FOSSLight Dependency Scanner

<img src="https://img.shields.io/pypi/l/fosslight_dependency" alt="License" /> <img src="https://img.shields.io/pypi/v/fosslight_dependency" alt="Current python package version." /> <img src="https://img.shields.io/pypi/pyversions/fosslight_dependency" /> [![REUSE status](https://api.reuse.software/badge/github.com/fosslight/fosslight_dependency_scanner)](https://api.reuse.software/info/github.com/fosslight/fosslight_dependency_scanner)
    
[**FOSSLight Dependency Scanner**](https://github.com/fosslight/fosslight_dependency_scanner)는 여러 패키지 매니저에 대한 종속성 분석을 지원하는 도구입니다. 패키지 매니저의 Manifest 파일을 자동으로 감지하고 오픈 소스 도구를 사용하여 종속성을 분석합니다. 그 후 종속성의 OSS 정보가 포함된 보고서 파일을 생성합니다. 

지원하는 Package Manager 목록은 하기와 같습니다.
- [Gradle](https://gradle.org/) (Java/Android)
- [Maven](http://maven.apache.org/) (Java)
- [NPM](https://www.npmjs.com/) (Node.js)
- [PIP](https://pip.pypa.io/) (Python)
- [Pub](https://pub.dev/) (Dart with flutter)
- [Cocoapods](https://cocoapods.org/) (Swift/Obj-C)
- [Swift](https://swift.org/package-manager/) (Swift)
- [Carthage](https://github.com/Carthage/Carthage) (Carthage)

## 사용 방법

  - [Prerequisite](#-prerequisite)
    - [NPM](#npm)
    - [Gradle](#gradle)
    - [Android-gradle](#android-gradle)
    - [Pypi](#pypi)
    - [Maven (optional)](#maven-optional)
    - [Pub](#pub)
    - [Cocoapods](#cocoapods)
    - [Swift](#swift)
    - [Carthage](#carthage)
  - [How to install](#-how-to-install)
  - [How to run](#-how-to-run)
  - [Result](#-result)


## 📋 Prerequisite
각 패키지 매니저마다 다른 오픈소스 소프트웨어를 이용하기 때문에, 분석하고자 하는 패키지 매니저에 따라 아래 [Prerequisite](#-prerequisite) 단계를 수행하시기 바랍니다.

### NPM

1. Npm dependency 분석을 수행하기 위해 NPM License Checker를 설치합니다.

```
$ npm install -g license-checker
```
> license-checker를 전역 패키지로 설치하기 위해서는, 반드시 '-g' option을 추가해 주어야 합니다. 만약 'sudo' 권한이 없는 경우, 전역 모듈이 설치되는 기본 path를 변경하여 이용하실 수 있습니다.
> ```
$ npm set prefix ~/.npm
$ PATH=~/.npm/bin:$PATH
```

2. dependency를 설치하기 위해 다음 명령어를 실행합니다. (optional)

```
$ npm install
```

> - package.json 파일이 input directory에 존재하는 경우, 해당 명령어 실행은 FOSSLight Dependency Scanner에서 자동으로 수행하므로 skip 가능합니다.
> - 이미 dependency들이 설치된 node_modules 디렉토리가 존재하는 경우, node_modules폴더가 존재하는 path를 input directory로 설정하여 실행 가능합니다.


### Gradle

1. 'build.gradle' 파일에 License Gradle Plugin을 추가합니다.

```
plugins {
    id 'com.github.hierynomus.license' version '0.15.0'
}

downloadLicenses {
    includeProjectDependencies = true
    dependencyConfiguration = 'runtimeClasspath'
}
```

> - 사용하는 gradle 버전이 4.6 또는 더 낮은 버전인 경우에는, dependencyConfiguration에 'runtimeClasspath' 대신 'runtime'을 추가합니다.

2. 'downloadLicenses' task를 실행합니다.

```
$ gradlew downloadLicenses
```

### Android (gradle)

1. 'build.gradle' 파일에 android-dependency-scanning Plugin을 추가합니다.
```
buildscript {
    repositories {
        mavenCentral()
    }

    dependencies {
        classpath 'org.fosslight:android-dependency-scanning:1.0.0'
    }
}
```

2. 플러그인이 적용되는 app 디렉토리 내에 위치한 build.gradle 파일 내에 다음과 같이 추가합니다. 
```
apply plugin: 'org.fosslight'
```

3. 'generateLicenseTxt' task를 실행합니다.
```
$ gradlew generateLicenseTxt
```

### Pypi

시스템 내 전역으로 설치된 파이썬 dependency로부터 분석하고자 하는 프로젝트 dependency를 분리하기 위해 가상환경을 설정하여 이용하기를 권장합니다.

1. 가상환경을 생성하고 활성화합니다.

```
// virtualenv example
$ virtualenv -p /usr/bin/python3.6 venv
$ source venv/bin/activate

// conda example
$ conda create --name {venv name}
$ conda activate {venv name}
```

2. 가상환경 내 분석하고자 하는 프로젝트의 dependency를 설치합니다.

```
// If you install the dependencies with requirements.txt...
$ pip install -r requirements.txt
```

### Maven (optional)

> - Maven의 경우, input directory에 pom.xml 파일이 존재하는 경우, plugin 추가 및 실행을 FOSSLight Dependency Scanner 내부에서 자동으로 수행하므로 다음은 skip하셔도 됩니다.

1. pom.xml 파일에 license-maven-plugin을 추가합니다.

```
<project>
  ...
  <build>
  ...
    <plugins>
    ...
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>license-maven-plugin</artifactId>
        <version>2.0.0</version>
        <executions>
          <execution>
            <id>aggregate-download-licenses</id>
            <goals>
              <goal>aggregate-download-licenses</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
    ...
  </build>
  ...
</project>
```

2. license-maven-plugin task를 실행합니다.

```
$ mvnw license:aggregate-download-licenses
```

### Pub

1. 다음 명령어를 통해 flutter_oss_licenses를 실행합니다.

```
$ flutter pub get
$ flutter pub global activate flutter_oss_licenses
$ flutter pub global run flutter_oss_licenses:generate.dart
```

### Cocoapods

1. Podfile을 통해 pod package를 설치합니다.

```
$ pod install
```

### Swift
1. Github personal access token을 생성하여 FOSSLight Dependency Scanner 실행 시 '-t' 파라미터로 사용합니다. 이 토큰은 Github repository의 license정보를 가져오기 위해 Github API를 사용하기 위해 필요합니다.
Token생성 방법은 [Github docs 가이드](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)를 참조하시기 바랍니다.


### Carthage

1. 다음과 같이 패키지 설치 명령어를 수행하여 'Cartfile.resolved' 파일을 생성합니다.
```
$ carthage update
```
2. Github personal access token을 생성하여 FOSSLight Dependency Scanner 실행 시 '-t' 파라미터로 사용합니다. 이 토큰은 Github repository의 license정보를 가져오기 위해 Github API를 사용하기 위해 필요합니다.
Token생성 방법은 [Github docs 가이드](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)를 참조하시기 바랍니다.


## 🎉 How to install

FOSSLight Dependency Scanner는 pip3를 이용하여 설치할 수 있습니다.     
[python 3.6 + virtualenv](etc/guide_virtualenv.md) 환경에서 설치할 것을 권장합니다.

```
$ pip3 install fosslight-dependency
```


## 🚀 How to run

FOSSLight Dependency Scanner는 패키지 매니저에 따라 다음 option들을 이용하여 실행할 수 있습니다.

```
$ fosslight_dependency
```

| Option | Argument                                         | Description                                                                                  |
| ------ | ------------------------------------------------ | -------------------------------------------------------------------------------------------- |
| -m     | npm, maven, gradle, pip, pub, cocoapods, android, swift, carthage | (optional) <br> 프로젝트의 package manager                                                   |
| -p     | (path)                                           | (optional) <br> 분석하고자 하는 input directory                                              |
| -o     | (path)                                           | (optional) <br> 결과 파일이 생성되는 output directory                                        |
| -a     | conda example: 'conda activate (venv name)'      | (pypi only required) <br> 가상환경 activate command                                          |
| -d     | conda example: 'conda deactivate'                | (pypi only required) <br> 가상환경 deactivate command                                        |
| -c     | (customized output directory name)               | (gradle, maven only optional) <br> 커스터마이즈한 build output directory명 (default: target) |
| -n     | (app name)                                       | (android only optional) <br> app directory name (default: app)                               |
| -v     | N/A                                              | release 버전                                                                                 |
| -t     | github token                                     | (swift, carthage only required) <br> github personal access token                                     |

이때, FOSSLight Dependency Scanner는 패키지 매니저의 manifest 파일이 존재하는 프로젝트의 top directory에서 실행되어야 합니다.
예를 들면, NPM 패키지 매니저를 이용하는 프로젝트의 경우, input directory는 'package.json' 파일이 존재하는 directory여야 합니다.
각 패키지 매니저별 manifest 파일은 다음과 같습니다.

| Package manager | Npm          | Pip              | Maven   | Gradle (Android) | Pub          | Cocoapods | Swift            | Carthage          |
| --------------- | ------------ | ---------------- | ------- | ---------------- | ------------ | --------- | ---------------- | ----------------- |
| Manifest file   | package.json | requirements.txt | pom.xml | build.gradle     | pubspec.yaml | Podfile   | Package.resolved | Cartfile.resolved |

즉, FOSSLight Dependency Scanner 실행 시, input directory('-p' 옵션)는 위와 같이 패키지 매니저의 manifest 파일이 존재하는 프로젝트의 top directory로 지정해 주어야 합니다.

\<swift package manager 실행 Tip\>
  * {프로젝트명}.xcodeproj 파일이 위치한 path에서 "fosslight_dependency -m swift -t {token}" 명령어를 실행하실 수 있습니다.
  * 이 경우에는 {프로젝트명}.xcodeproj/project.xcworkspace/xcshareddata/swiftpm path에서 'Package.resolved' 파일을 자동으로 찾고 프로그램이 실행됩니다.

## 📁 Result

FOSSLight Dependency Scanner는 xlsx(Microsoft Excel file)양식의 결과 파일을 생성합니다.

결과 파일에는 transitive dependency들을 포함한 모든 분석된 dependency들의 manifest 파일을 기반으로 OSS 정보가 기록됩니다.
이때, 고유한 OSS명을 작성하기 위해, OSS명은 (패키지 매니저):(OSS명) 또는 (group id):(artifact id) 양식으로 기록됩니다.

| Package manager                | OSS Name                 | Download Location                                                                                  | Homepage                                            |
| ------------------------------ | ------------------------ | -------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Npm                            | npm:(oss name)           | 우선순위1. repository in package.json <br> 우선순위2. npmjs.com/package/(oss name)/v/(oss version) | npmjs.com/package/(oss name)                        |
| Pip                            | pypi:(oss name)          | pypi.org/project/(oss name)/(version)                                                              | homepage in (pip show) information                  |
| Maven<br>& Gradle<br>& Android | (group_id):(artifact_id) | mvnrepository.com/artifact/(group id)/(artifact id)/(version)                                      | mvnrepository.com/artifact/(group id)/(artifact id) |
| Pub                            | pub:(oss name)           | pub.dev/packages/(oss name)/versions/(version)                                                     | homepage in (pub information)                       |
| Cocoapods                      | cocoapods:(oss name)     | source in (pod spec information)                                                                   | cocoapods.org/pods/(oss name)                            |
| Swift                      | swift:(oss name)     | repositoryURL in Package.resolved                                                                   | repositoryURL in Package.resolved                            |
| Carthage                      | carthage:(oss name)     | github repository in Cartfile.resolved                                                                   | github repository in Cartfile.resolved                            |