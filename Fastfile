fastlane_version '2.53.1'
PROJECT_NAME = "Runner"
XCODE_PROJECT = "./ios/#{PROJECT_NAME}.xcodeproj"
XCODE_WORKSPACE = "./ios/#{PROJECT_NAME}.xcworkspace"
OUTPUT_DIRECTORY = "./fastlane/builds"

# before_all do
#     ensure_git_branch(branch: 'develop')
#     ensure_git_status_clean
#     git_pull
#   end

platform :ios do
    
    private_lane :build do
        configuration_name = "Release"
        export_options_method = "development"
        code_sign_identity = "iPhone Developer"
        MY_APP_ID = ENV["APP_IDENTIFIER"]
        MY_PROFILE = "match Development #{ENV["APP_IDENTIFIER"]}"
        MY_TEAM = ENV["DEVELOPER_TEAM_ID"]

        settings_to_override = {
            # :BUNDLE_IDENTIFIER => MY_APP_ID,
            #:PROVISIONING_PROFILE_SPECIFIER => MY_PROFILE,
            #:DEVELOPMENT_TEAM => MY_TEAM,
          }

        create_keychain(
            name: ENV["CI_KEYCHAIN_NAME"],
            password: ENV["CI_KEYCHAIN_PASSWORD"],
            default_keychain: true,
            unlock: true,
            timeout: 3600,
            lock_when_sleeps: false
        )

        match(
            app_identifier: 'com.parralelscore.lafia',
            git_url: ENV["GIT_REPO_URL"],
            git_basic_authorization: ENV["MATCH_GIT_BASIC_AUTHORIZATION"],
            type: "development",
            username: ENV["APPLE_ID"],
            team_id: ENV["DEVELOPER_TEAM_ID"],
            keychain_name: ENV["CI_KEYCHAIN_NAME"],
            keychain_password: ENV["CI_KEYCHAIN_PASSWORD"],
            readonly: true
            )

        increment_build_number(xcodeproj: './ios/Runner.xcodeproj')
        # increment_version_number(
        #     bump_type: "patch",
        #     xcodeproj: XCODE_PROJECT,
        # )
    
        #update_code_signing_settings(
         #   use_automatic_signing: false,
          #  path: XCODE_PROJECT,
           # code_sign_identity: code_sign_identity,
            #bundle_identifier: MY_APP_ID,
            #profile_name: MY_PROFILE
          #)

        gym( scheme: PROJECT_NAME,
        workspace: XCODE_WORKSPACE,
        xcargs: `#{settings_to_override} -UseNewBuildSystem=YES`,
        output_directory: OUTPUT_DIRECTORY,
        export_team_id: ENV["DEVELOPER_TEAM_ID"],
        output_name: "#{PROJECT_NAME}.ipa",
        export_method: "development",
        export_options: {
        #method: "development",
        #signingStyle: "manual",
        provisioningProfiles: { 
            MY_APP_ID => MY_PROFILE
        }
        
      }
        )
    
    end

    desc "Create Signed IPA Build"
    lane :beta do
      build

      firebase_app_distribution(
            app: "1:847334044156:ios:bc61fae22a878c58c3c279",
            groups: "public",
            firebase_cli_token: ENV["FIREBASE_TOKEN"] ,
            release_notes: "Lots of amazing new features to test out!",
            ipa_path: "#{OUTPUT_DIRECTORY}/#{PROJECT_NAME}.ipa"
        )

    end

end
 
platform :android do
  lane :build do
    gradle(task: 'clean', project_dir: 'android/')
    gradle(task: 'assemble', build_type: 'Release', project_dir: 'android/')
    end


  desc "Create BlessingJar APK"
  lane :distribute do
    #build
    firebase_app_distribution(
          app: "1:847334044156:android:dffd90bba924ea6ac3c279",
          groups: "public",
          firebase_cli_token: ENV["FIREBASE_TOKEN"] ,
          release_notes: "Lots of amazing new features to test out!",
          apk_path: "./build/app/outputs/apk/release/app-release.apk"
      )
    end

end
