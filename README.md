# ConsentKit
Note: This library only helps you collect consents for the services in your app that needs it, actually disabling those services is developer's job.

![Screenshot](https://image.ibb.co/b9C4rT/Screen_Shot_2018_05_23_at_00_41_15.png)

## Usage
### Define the services needing the user consent

	enum Services: String, ConsentKitItem {
          
          case icloud = "iCloud"
          case analytics = "analytics"
          
          func title() -> String {
              switch self {
                  case .icloud: return "iCloud"
                  case .analytics: return "Google analytics"
              }
          }
          func description() -> String {
              switch self {
                  case .icloud: return "Wether to accept iCloud or not"
                  case .analytics: return "Google analytics"
              }
          }
          func alertMessage() -> String {
              switch self {
                  case .icloud: return "I hereby accept this app to store my data in the Apple's iCloud!"
                  case .analytics: return "I hereby accept this app to store analytics in Google analytics!"
              }
          }
	}
	
### In the flow of your app where you need consent, instantiate the lib

	let gdpr = ConsentKit()
	
### Check if you have missing consents
If yes, add the default ConsentKitViewController to handle all the switches for you. One item is a touple of (ConsentKitItem, Bool)

    if gdpr.needsReviewing([Services.icloud, Services.analytics]) {
        
        let gdprView = ConsentKitViewController()
        gdprView.items = [
            (Services.icloud, gdpr.isAccepted(Services.icloud)),
            (Services.analytics, gdpr.isAccepted(Services.analytics))
        ]
        self.present(gdprView, animated: true)
    }

### Custom storage
By default ConsentKit keeps values in UserDefaults, but you can change that with the gdpr.dataSource property. Just assign a class implementing the ConsentKitDataSource

    protocol ConsentKitDataSource {
        func isAccepted (_ item: ConsentKitItem) -> Bool
        func isReviewed (_ item: ConsentKitItem) -> Bool
        func setAccepted (_ value: Bool, for item: ConsentKitItem)
        func reset (_ item: ConsentKitItem)
    }