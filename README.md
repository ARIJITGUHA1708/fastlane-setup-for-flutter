# fastlane-setup-for-flutter

# Continues Integration/Continues Distribution

This is a flutter project where I am trying to setup CI/CD pipeline for my own flutter project using Fastlane.
So Fastlane is a tool for Android/iOS CI/CD pipeline.

these are the steps I use for setup the CI/CD pipeline

Step 1: Create a flutter project using
flutter create --org <package_name> <project_name>

Step 2: Remove all the file which is not required for me
    
    rm -rf web android linux macos

Step 3: You have to install the fastlane in your machine
  
    brew install fastlane

Step 4: check the proper installation

    fastlane --version

step 5: go to ios folder of your flutter project
    
    cd ios

step 6: run the command and fill the required things which is asked
    
    fastlane init

This will first of all ask for apple developer account username and password.

Then Fastlane check that identifier and application is present in apple developer account or not
if not, fastlane is do all the stuffs for you, just give the app name only(identifier is created by fastlane).

Now these things created a fastlane folder under your ios folder, inside that there is two important file FastFile and AppFile

In AppFile all the information are stored, for example, app_identifier, apple_id, itc_team_id, team_id

In FastFile, put these things

    default_platform(:ios)
    platform :ios do
      desc "Push a new beta build to TestFlight"
      lane :beta do
      increment_build_number(xcodeproj: "Runner.xcodeproj")
      build_app(
        workspace: "Runner.xcworkspace",
        scheme: "Runner",
        clean: true,
        export_method: "app-store",
        export_options: {
        provisioningProfiles: {
          "com.fourbrains.ciCdDemo2" => "com.fourbrains.ciCdDemo2 AppStore"
          # "your identifier or bundle ID" => "your provisional profile name"
        }
    } 
    )
  
    end
  
    lane :upload_app do
    
      deliver(
      
        ipa: "Runner.ipa",
      
        force: true,
      
        skip_screenshots: true,
      
        skip_metadata: true,
      
        submit_for_review: false
      
        )

      end
    end

Step 7: Save FastFile and then run two commands to get certificate and profile

    fastlane get_certificates
    fastlane get_provisional_profile

Step 8: go to ios folder and hit the command
    
    fastlane beta
    fastlane upload_app

this will create a ipa file 'Runner.ipa' and upload it to testflight automatically
in future when you change some code in your flutter code, just only hit two command
    
    fastlane beta
    fastlane upload_app
(or make a different lane for both task)

    lane :upload_to_testflight do
    beta
    upload_app
    end

---

troubleshooting if get some error

1. Certificate and Profile should be downloaded and installed in your local system
2. create a github repository to store credential
3. In AppFile put this
   ENV["FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD"] = "your app specific password"
   App specific password should be created in you apple account

Thats It, No need to create identifier and app and all the time go to Xcode for archive the flutter app and select many options, just one command from your command line, and fastlane do all the stuff for you.

Improvements are welcomes
Create a pull request for your add-ons. 
