# CI test project

Projekt za testiranje funkcionalnosti kontinuirane integracije za iOS operacijsku sustav.

Kontinuirana integracija implementirana korištenjem Xcode Server aplikacije a kontinuirana dostava korištenjem fastlane alata.

Fastfile:

```
#!/bin/bash

fastlane_version "2.28.7"

default_platform :ios

platform :ios do
  before_all do |lane|
    cocoapods
    carthage(platform: "iOS", cache_builds: true)
  end

  lane :test do
    scan
  end

  lane :develop do
    increment_build_number
    match(app_identifier: "com.rep.Diplomski-rad.development", type: "development")
    gym(scheme: "Diplomski_rad", configuration: "Debug", export_method: "development")
    crashlytics(
        api_token: "002d1e80e7bf3a300de583f69a92347342fc38a9",
        build_secret: "e3df1c13235f9dab0dc7accc866329608184f2134ce261febd9bf33b9940b592",
        groups: "Rep")
  end

  lane :test_release do
    increment_build_number
    match(app_identifier: "com.rep.Diplomski-rad.test-release", type: "adhoc")
    gym(scheme: "Diplomski_rad", configuration: "Test", export_method: "ad-hoc")
    crashlytics(
        api_token: "002d1e80e7bf3a300de583f69a92347342fc38a9",
        build_secret: "e3df1c13235f9dab0dc7accc866329608184f2134ce261febd9bf33b9940b592",
        groups: "Rep")
  end

  lane :release_candidate do
    increment_build_number
    match(app_identifier: "com.rep.Diplomski-rad.release-candidate", type: "adhoc")
    gym(scheme: "Diplomski_rad", configuration: "RC", export_method: "ad-hoc")
    crashlytics(
    api_token: "002d1e80e7bf3a300de583f69a92347342fc38a9",
    build_secret: "e3df1c13235f9dab0dc7accc866329608184f2134ce261febd9bf33b9940b592",
    groups: "Rep")
  end

  lane :release do
    increment_build_number
    match(app_identifier: "com.rep.Diplomski-rad", type: "appstore")
    gym(scheme: "Diplomski_rad", configuration: "Release", export_method: "app-store")
    deliver(force: true)
  end


  after_all do |lane|
  end

  error do |lane, exception|
  end
end
```

CocoaPods:

```
#!/bin/bash

if [ -f Podfile ]; then #1
    echo "Podfile found. Starting CocoaPods"

    if ! which pod >/dev/null; then #2
        echo "Installing CocoaPods"

        gem install cocoapods --user-install #3
        pod repo update

        echo "CocoaPods installed"
    fi

    pod install #4

    echo "Finished dependancy fetch using CocoaPods"
fi
```
