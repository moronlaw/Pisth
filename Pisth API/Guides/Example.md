# AppDelegate

```swift
import UIKit
import Pisth_API

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

        // Set app URL scheme
        Pisth.shared.urlScheme = URL(string: "pisth-api://")


        return true
    }

    func application(_ app: UIApplication, open url: URL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {

        let viewController = (UIApplication.shared.keyWindow?.rootViewController as? ViewController)

        if let data = Pisth.shared.dataReceived {
            viewController?.data = data
            if let image = UIImage(data: data) {
                viewController?.imageView.image = image
            }
        }

        if let filename = Pisth.shared.filename(fromURL: url) {
            viewController?.filename.text = filename
        }

        return true
    }

}
```

# ViewController

```swift
import UIKit
import Pisth_API

class ViewController: UIViewController {

    @IBOutlet weak var importButton: UIButton!
    @IBOutlet weak var imageView: UIImageView!
    @IBOutlet weak var filename: UILabel!

    var data: Data?

    @IBAction func share(_ sender: Any) {

        // Share file

        let fileURL = FileManager.default.urls(for: .documentDirectory, in: .allDomainsMask)[0].appendingPathComponent(filename.text!)
        _ = FileManager.default.createFile(atPath: fileURL.path, contents: data, attributes: nil)

        let activityVC = UIActivityViewController(activityItems: [fileURL], applicationActivities: nil)
        activityVC.popoverPresentationController?.sourceView = sender as? UIView
        self.present(activityVC, animated: true, completion: nil)
    }

    @IBAction func importFromPisth(_ sender: Any) {

        // Import file
        Pisth.shared.importFile()
    }

    override func viewDidLoad() {
        super.viewDidLoad()

        // Enable button only if app can import file from Pisth
        importButton.isEnabled = Pisth.shared.canOpen
    }

}
