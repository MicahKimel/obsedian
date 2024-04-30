```
public static async Task CallPush(String message, String userDeviceToken, String team, String keyid, String bundle)
{
    try
    {
        var teamId = team;
        var keyId = keyid

        var key = new ECDsaCng(CngKey.Import(Convert.FromBase64String("publicKey"), CngKeyBlobFormat.Pkcs8PrivateBlob));    
        _logger.LogDebug("CallPush: key :" + key.ToString());
        //Set Key and Team
        var token = CreateToken(key, keyId, teamId);
        
        //Set Device Token
        var deviceToken = userDeviceToken;
        
        var url = "";
        if (deviceToken.Length > 64)
            url = string.Format("https://api.sandbox.push.apple.com/3/device/{0}", deviceToken);
        else
            url = string.Format("https://api.push.apple.com/3/device/{0}", deviceToken);
        var request = new HttpRequestMessage(HttpMethod.Post, url);
        //Authenticate
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", token);
        //Set Type
        request.Headers.TryAddWithoutValidation("apns-push-type", "alert"); // or background
        request.Headers.TryAddWithoutValidation("apns-id", Guid.NewGuid().ToString("D"));
        //Set Expire
        request.Headers.TryAddWithoutValidation("apns-expiration", Convert.ToString(0));
        //Send imediately
        request.Headers.TryAddWithoutValidation("apns-priority", Convert.ToString(10));
        //Set App Bundle
        request.Headers.TryAddWithoutValidation("apns-topic", bundle);
                                                                      //Category
        request.Headers.TryAddWithoutValidation("apns-collapse-id", Guid.NewGuid().ToString("D"));//this will group like types to collapse them

        var body = JsonConvert.SerializeObject(new
        {
            aps = new
            {
                alert = new
                {
                    title = "Appointment Scheduled",
                    body = message,
                    time = DateTime.Now.ToString()
                },
                badge = 0,
                sound = "default"
            },
            acme2 = new string[] { "bang", "whiz" }
        });
        request.Version = HttpVersion.Version20;
        using (var stringContent = new StringContent(body, Encoding.UTF8, "application/json"))
        {
            //Set Body
            request.Content = stringContent;
            var handler = new HttpClientHandler();
            handler.SslProtocols = SslProtocols.Tls12 | SslProtocols.Tls11 | SslProtocols.Tls;
            handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;

            //Continue
            using (HttpClient client = new HttpClient(handler))
            {
                HttpResponseMessage resp = await client.SendAsync(request).ContinueWith(responseTask =>
                {
                    _logger.LogDebug("CallPush: resp:" + responseTask.Result);
                    return responseTask.Result;

                });
                if (resp != null)
                {
                    string apnsResponseString = await resp.Content.ReadAsStringAsync();
                    _logger.LogDebug("CallPush: apnsResponseString: "+ apnsResponseString);
                    handler.Dispose();
                    //ALL GOOD ....
                    _logger.LogDebug("CallPush: ALL GOOD");
                    return;
                }
                handler.Dispose();
            }
        }
    }
    catch(Exception err)
    {

        _logger.LogError(err,  message + " " +  userDeviceToken + " " + team + " " + keyid + " " + bundle, err);
    }
}
```

```
public static string CreateToken(ECDsa key, string keyID, string teamID)
{
    try
    {
        var securityKey = new ECDsaSecurityKey(key) { KeyId = keyID };
        var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.EcdsaSha256);

        var descriptor = new SecurityTokenDescriptor
        {
            IssuedAt = DateTime.Now,
            Issuer = teamID,
            SigningCredentials = credentials
        };

        var handler = new JwtSecurityTokenHandler();
        var encodedToken = handler.CreateEncodedJwt(descriptor);
        _logger.LogDebug("CreateToken: " + key + " " + keyID + " " + teamID + "" + credentials);
        return encodedToken;
    }
    catch(Exception err)
    {
        _logger.LogError(err, "CreateToken: " + key +" "+ keyID + " " + teamID, err);
        return "";
    }
}
```




Insert new record
```
string sql2 = $@"insert into GWORLDF2.PSHNOTU (User, DEVICE_Token, MAC_ADDRESS, PSHNOTA_ID, appver)
        values ({User}, '{DEVICE_TOKEN.TrimEnd()}', '{MAC_ADDRESS}', {ids.First().id}, '{appver.TrimEnd()}')";
```




```
// Will also need bundle_ID, team_id, and key_id in order to post notification

let mc = UIDevice().identifierForVendor?.uuidString ?? ""
PostPushData(eid: Employee.eid, deviceToken: self.deviceToken, appName: "Plug", macAddress: mc)
```

```
func PostPushData(eid: String, deviceToken: String, appName: String, macAddress: String){
    let appVersion = Bundle.main.infoDictionary?["CFBundleShortVersionString"] as? String ?? ""
    let app = Bundle.main.infoDictionary?["CFBundleDisplayName"] as? String ?? ""
    LogEntry(_level: .info, _category: .service, _message: "InsertEmployeePushNotificationData: \(eid)-\(deviceToken)-\(appName)-\(macAddress)-\(app + ": " + appVersion) ").writeToCache()
    var components = URLComponents(string: urls.HeadUrl + "/api/InsertEmployeePushNotificationData")!
    
    //var components = URLComponents(string: "https://salesservice.terminix-triad.com" + "/api/InsertEmployeePushNotificationData")!
    //localhost
    //var components = URLComponents(string: "https://localhost:5001/api/InsertEmployeePushNotificationData?")!
    components.queryItems = []
    components.queryItems!.append(URLQueryItem(name: "eid", value: eid))
    components.queryItems!.append(URLQueryItem(name: "DEVICE_TOKEN", value: deviceToken))
    components.queryItems!.append(URLQueryItem(name: "APP_NAME", value: appName))
    components.queryItems!.append(URLQueryItem(name: "MAC_ADDRESS", value: macAddress))

    components.queryItems!.append(URLQueryItem(name: "appver", value: app + ": " + appVersion))

    let myurl = components.url!
    print(myurl)
    LogEntry(_level: .info, _category: .service, _message: "InsertEmployeePushNotificationData: \(myurl)").writeToCache()
    var request = URLRequest(url: myurl)
    request.httpMethod = "POST"
    request.addValue("application/json", forHTTPHeaderField: "Content-Type")
    let session = URLSession.shared
    session.configuration.urlCache = nil
    var Response: String = ""
    let group = DispatchGroup()
    group.enter()
    DispatchQueue.global(qos: .background).async{
        let task = session.dataTask(with: request, completionHandler: {(data, response, error) -> Void in
            do{
                if (data != nil){
                    Response = try JSONDecoder().decode(String.self, from: data!)
                    group.leave()
                } else {
                    LogEntry(_level: .notice, _category: .service, _message: "InsertEmployeePushNotificationData: no data returned").writeToCache()
                    group.leave()
                }
            } catch{
                LogEntry(_level: .error, _category: .service, _message: "InsertEmployeePushNotificationData: \(error)").writeToCache()
                group.leave()
            }
        })
        task.resume()
    }
    group.wait()
    session.finishTasksAndInvalidate()
    print(Response)
    
}
```



In main app file update device token
```
.onChange(of: appDelegate.device_Token){newValue in
	globalObj.deviceToken = newValue
}
.onChange(of: appDelegate.receivedNotification){newValue in
	globalObj.receivedNotification = newValue
}
.onChange(of: globalObj.receivedNotification){newValue in
	if !newValue{
		appDelegate.receivedNotification = false
	}
}
```




```
class AppDelegate: NSObject, UIApplicationDelegate {
    
    let gcmMessageIDKey = "gcm.message_id"
    var device_Token = ""
    var receivedNotification = false
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {
        
        //Messaging.messaging().delegate = self
        
        if #available(iOS 10.0, *) {
            // For iOS 10 display notification (sent via APNS)
            UNUserNotificationCenter.current().delegate = self
            
            let authOptions: UNAuthorizationOptions = [.alert, .badge, .sound]
            UNUserNotificationCenter.current().requestAuthorization(
                options: authOptions,
                completionHandler: {_, _ in })
        } else {
            let settings: UIUserNotificationSettings =
            UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
            application.registerUserNotificationSettings(settings)
        }
        
        application.registerForRemoteNotifications()
        return true
    }
    
    //    func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable: Any],
    //                     fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    //
    //      if let messageID = userInfo[gcmMessageIDKey] {
    //        print("Message ID: \(messageID)")
    //      }
    //
    //      print(userInfo)
    //
    //      completionHandler(UIBackgroundFetchResult.newData)
    //    }
    
}
```


```
extension AppDelegate : UNUserNotificationCenterDelegate {
    
    
    // Receive displayed notifications for iOS 10+ devices.
    func userNotificationCenter(_ center: UNUserNotificationCenter,
                                willPresent notification: UNNotification,
                                withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        let userInfo = notification.request.content.userInfo
        
        if let messageID = userInfo[gcmMessageIDKey] {
            print("Message ID: \(messageID)")
        }
        
        print(userInfo)
        
        // Change this to your preferred presentation option
        completionHandler([[.banner, .badge, .sound]])
        self.receivedNotification = true
    }
    
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let token = deviceToken.map { String(format: "%02.2hhx", $0) }.joined()
        print("Device Token: \(token)")
        device_Token = "\(token)"
    }
    
    func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print("i am not available in simulator :( \(error)")
    }
    
    //this does not work yet
    func application(_ application: UIApplication,
                     didReceiveRemoteNotification userInfo: [AnyHashable: Any],
                     fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
        // Silent Push Notification processing
        if let silentNotification = userInfo["aps"] as? [String: AnyObject],
           silentNotification["content-available"] as? Int == 1 {
            // Launch of a some service
            //SomeManager.shared.startUpdatingAnything()
        }
        // Ending a background operation
        completionHandler(.noData)
    }
    
    func userNotificationCenter(_ center: UNUserNotificationCenter,
                                didReceive response: UNNotificationResponse,
                                withCompletionHandler completionHandler: @escaping () -> Void) {
        let userInfo = response.notification.request.content.userInfo
        
        if let messageID = userInfo[gcmMessageIDKey] {
            print("Message ID from userNotificationCenter didReceive: \(messageID)")
        }
        
        print(userInfo)
        
        completionHandler()
    }
}

