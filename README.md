## Integrating MOLPay with VB SDK
Version 1.2.0 (Updated)

### Pre-Requisite
1. Visual Studio 2012 or above.
2. MOLPay Development or Production ID.
3. MOLPay General API.

### Installation
#### Via File Explorer
1. Download `MOLPayCS.zip` file.
2. Extract the zip file. 
3. Open Visual Studio project, right click on your project name in `Solution Explorer` and choose `Add Reference`.
4. Click `Browse` to search for downloaded MOLPayCS.dll. Click `OK` to add.

### Usage
1. Create MOLPayCS object in order to access the properties of Payment: 

```VB.Net
Dim objectMolpay As New MOLPayVB.MOLPay.Base
```
2. Set which type of enviroment with either **Sandbox** or **Production**
```VB.Net
objectMol.TypeID = "sandbox" ' "sandbox" or "production"
```
### Payment Page integration
Set these needed objects that will send the buyer infromation to MOLPay hosted payment page.
```VB
'Value set are examples
objectMolpay.Domain = "MOLPay" 'Replace with your Merchant ID provided  
objectMolpay.Vkey = "xxxx" 'Replace ​xxxxxxxxxx with your MOLPay Secret_Key
objectMolpay.Amount = "1.1"
objectMolpay.Orderid = "Testing123"
```
Optional objects. Details can be key in at the payment page as well.
```VB
'Value set are examples
objectMolpay.Bill_name = "Molpay Tester"
objectMolpay.Bill_email = "molpay@molpay.com
objectMolpay.Bill_desc = "Send me money"
objectMolpay.Currency = "MYR"
objectMolpay.Country = "Malaysia"
```
It is not needed to set all the Endpoint URLs. If not set,by default the Endpoint URLs would be taken from Merchant Portal's End Point settings.
```VB
'Value set are examples
objectMolpay.ReturnUrl = "molpay.com" 'Desired returned page after payment page
```
Objects that are set will be send to hosted payment page by calling a function.
```VB
objectMolpay.Pay()
```
### Payment endpoint integration
Set the values received from MOLPay's payment page.
```VB.Net
objectMolpay.Vkey = "xxxxxxx"   'Replace ​xxxxxxxxxx with your MOLPay Secret_Key
objectMolpay.TranID = request.Form("tranID")
objectMolpay.Orderid = request.Form("orderid")
objectMolpay.Status = request.Form("status")
objectMolpay.Domain = request.Form("domain") 
objectMolpay.Amount = request.Form("amount")
objectMolpay.Currency = request.Form("currency")
objectMolpay.Appcode = request.Form("appcode")
objectMolpay.Paydate = request.Form("paydate")
objectMolpay.Skey = request.Form("skey")
```
#### IPN(Instant Payment Notification)
Additional object must be set when using IPN
```VB.Net
objectMolpay.Treq = "1" 'Value is always 1. Do not change
```
Call the IPN function
```VB.Net
objectMolpay.IPN()
```
#### Notification & Callback URL with IPN 
Set additional object for Notification & Callback URL 
```VB.Net
objectMolpay.Nbcb = request.Form("nbcb")  
```
#### Verifying the integrity of data send by MOLPay
```VB.Net
objectMolpay.Key1checked() 
```
Return value of the function is a string which contains the key1 value.

#### Sample of all 3 endpoints
`E.G` return & notification URL

```VB.Net
'invalid transaction if the key is different. Merchant might issue a requery to MOLPay to double check payment status with MOLPay. 
 If objectMolpay.Skey()  <> objectMolpay.Key1checked()  then   
  objectMolpay.Status= "-1"
 End if 
 
If objectMolpay.Status = "00" then  
  'checking the validity of cart amount & orderid.  
  'if the verification test passed then can update the order status to paid. 
  'you can also do further checking on the paydate as well 
Else
  'failure action  
  'Merchant might send query to MOLPay using merchant requery  
  'to double check payment status for that particular order. 
End if 
  'Merchant is to implement IPN to ack on receiving of payment status 
  'regardless the payment status
```
`E.G` callback URL
```VB.Net
If objectMolpay.Skey <> objectMolpay.Key1checked then   
  objectMolpay.Status= -1
End if 
 
If objectMolpay.Status = "00" then  
  'write your scripe here.... 
Else
   'failure action   
   'write your script here ..... 
End if

'callback IPN feedback to notify MOLPay 
response.write(objectMolpay.IPN)
```
### Indirect Status Requery
There are several types of status requery on MOLPay system:
1. Query by unique transaction ID (recommended)
2. Query by order ID & get latest matched result (single output)
3. Query by order ID & get all matched results (batch output) 
4. Query by multiple order ID (batch output) 
5. Query by multiple transaction ID (batch output) 
*For bulk requery, best practice is to utilize the 4th or 5th APIs once every half an hour for unknown status transactions only 

#### 1. Query by unique transaction ID
Set the values to requery
```VB.Net
 objectMolpay.Amount = "100.00"             ' 2 decimal points numeric value
 objectMolpay.TxID = "xxxxxxxx"             ' Replace ​xxxxxxxxx with Merchant transaction ID , which might be duplicated.
 objectMolpay.Domain = "XXXXXXXX"           ' Replace ​XXXXXXX with your MOLPay Merchant ID
 objectMolpay.Vkey = "zzzzzzzz"             ' Replace ​zzzzzzz with your verify key
 objectMolpay.Url = "http://exampleurl"     ' The URL to receive POST response from MOLPay
 objectMolpay.Type = "0"                    ' 0 = plain text result (default),  1 = result via POST method
 objectMolpay.TypeID = "sandbox"            ' Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryTransactionID function to trigger 
```VB.Net
 objectMolpay.RequeryTransactionID()
 ```
Example response with type=0 (default output, plain text with linebreaks)
```VB.Net
/*
StatCode=00
StatName=captured
TranID=65234
Amount=3899.00
Domain=shopA
Channel=fpx
VrfKey=xxxxxxxxxxxxxxxxx
*/
```
Example response with type=1 (POST result sent to URL)
```VB.Net
/*
$_POST [StatCode] => “00”;
$_POST [StatName] => “captured”;
$_POST [TranID] => “65234”;
$_POST [Amount] => “3899.00”;
$_POST [Domain] => “shopA”;
$_POST[Channel] => “fpx”;
$_POST[VrfKey:]=> “456cf69e5bddfe8ed47371096”;
*/
```

#### 2. Query by order ID (single output)

Set the values to requery

```VB.Net
 objectMolpay.Amount = "100.00"             ' 2 decimal points numeric value
 objectMolpay.OID = "xxxxxxxx"              ' Replace ​xxxxxxxxx with the Order id that you want to check
 objectMolpay.Domain = "XXXXXXXX"           ' Replace ​XXXXXXX with your MOLPay Merchant ID
 objectMolpay.Vkey = "zzzzzzzz"             ' Replace ​zzzzzzz with your verify key
 objectMolpay.Url = "http://exampleurl"     ' The URL to receive POST response from MOLPay
 objectMolpay.Type = "0"                    ' 0 = plain text result (default),  1 = result via POST method
 objectMolpay.Req4token = "1"               ' 0 = No (default), 1 = Yes for more card related information
 objectMolpay.TypeID = "sandbox"            ' Set which type of environment with either **sandbox** or **production**
 ```
 Use equeryOrderIDSingle function to trigger 
 ```VB.Net
 objectMolpay.RequeryOrderIDSingle()
 ```
 
#### 3. Query by order ID (batch output)

Set the values to requery

```VB.Net
 objectMolpay.OID = "xxxxxxxx"              ' Replace ​xxxxxxxxx with the Order id that you want to check
 objectMolpay.Domain = "XXXXXXXX"           ' Replace ​XXXXXXX with your MOLPay Merchant ID
 objectMolpay.Vkey = "zzzzzzzz"             ' Replace ​zzzzzzz with your verify key
 objectMolpay.Url = "http://exampleurl"     ' The URL to receive POST response from MOLPay
 objectMolpay.Type = "0"                    ' 0 = plain text result (default),  1 = result via POST method
 objectMolpay.Format = "0"                  ' 0 = result string with delimiter ( | ), 1 = result in array
 objectMolpay.Req4token = "1"               ' 0 = No (default), 1 = Yes for more card related information
 obj.TypeID = "sandbox"                     ' Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryOrderIDBatch function to trigger 
```VB.Net
 objectMolpay.RequeryOrderIDBatch()
 ```
 
#### 4. Query by multiple order ID (batch output)

Set the values to requery

```VB.Net
 objectMolpay.OIDs = "xx|yy|zz"             ' e.g) xx & yy & zz are the order id you want to check (separate with "|")
 objectMolpay.Delimiter = "|"               ' Single character, default is "|". Avoid using any symbol that might exist in order ID,
                                            ' and also any of these: “,%, *, <, >, ? , \, $, &, = 
 objectMolpay.Domain = "XXXXXXXX"           ' Replace ​XXXXXXX with your MOLPay Merchant ID
 objectMolpay.Vkey = "zzzzzzzz"             ' Replace ​zzzzzzz with your verify key
 objectMolpay.Url = "http://exampleurl"     ' The URL to receive POST response from MOLPay
 objectMolpay.Type = "0"                    ' 0 = plain text result (default),  1 = result via POST method
 objectMolpay.Format = "0"                  ' 0 = result string with delimiter ( | ), 1 = result in array
 objectMolpay.Req4token = "1"               ' 0 = No (default), 1 = Yes for more card related information
 objectMolpay.TypeID = "sandbox"            ' Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryMultiOrderID function to trigger 
 ```VB.Net
 objectMolpay.RequeryMultiOrderID()
 ```
#### 5. Query by multiple transaction ID (batch output)

Set the values to requery
```VB.Net
 objectMolpay.TIDs = "xx|yy|zz"             ' e.g) xx & yy & zz are the transaction id you want to check (separate with "|")
 objectMolpay.Domain = "XXXXXXXX"           ' Replace ​XXXXXXX with your MOLPay Merchant ID
 objectMolpay.Vkey = "zzzzzzzz"             ' Replace ​zzzzzzz with your verify key
 objectMolpay.Url = "http://exampleurl"     ' The URL to receive POST response from MOLPay
 objectMolpay.Type = "0"                    ' 0 = plain text result (default),  1 = result via POST method
 objectMolpay.Format = "0"                  ' 0 = result string with delimiter ( | ), 1 = result in array
 objectMolpay.Req4token = "1"               ' 0 = No (default), 1 = Yes for more card related information
 objectMolpay.TypeID = "sandbox"            ' Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryMultiTransactionID function to trigger 
```VB.Net
 objectMolpay.RequeryMultiTransactionID()
```
-------

## Seamless Integration
Create MOLPayCS object in order to access the properties of Seamlesspayment: 

```VB.Net
 Dim objSeamless As New MolPayCS.Seamlesspayment
```

Set the values for seamless integration
```VB.Net
 objSeamless.Merchantid = "xxx"        ' Replace xxx Merchant login username provided by MOLPay
 objSeamless.Vkey = "xxx"              ' Replace xxx with your verify key
 objSeamless.Orderid = "xxx"           ' Replace xxx with Bill / Invoice no. provided by merchant
 objSeamless.Country = "MY"            ' Buyer country
 objSeamless.ReturnUrl = "xxx"         ' Replace xxx with your return URL
 objSeamless.CancelUrl = "xxx"         ' Replace xxx with URL to redirect when the payment is time out. Mandatory when timer is enable.
 objSeamless.Failureurl = "xxx"        ' Replce xxx with URL to redirect when transcation fail
 objSeamless.ProcessRequest()          ' Used to trigger seamless integration
```

### Seamless Integration Demo

---------------------------------------------
1. Download Seamless.zip and extract the file.
2. Open the solution inside Seamless file.
3. Right click on "WebForm1.aspx" file and select "View Code".
4. Set which type of environment by assigning variable "demo_type" to either **sandbox** or **production**.
5. Set your MerchantID and Vkey on the file seamlessHandler.ashx.
5. Build Solution and Debug the project. 

#### For more information about Seamless Integration and its functionality please refer to this link
[Latest MOLPay Seamless Integration](https://github.com/MOLPay/Seamless_Integration/wiki/MOLPay-Seamless-Integration-v3.17-(non-PCI))

FAQ
----------
**1. Exception of type 'System.IO.FileLoadException' occurred**
1. Simply right-click the "References" folder and select "Manage NuGet Packages...".
2. Select the Browse tab.
3. In the search bar in the upper left type "Newtonsoft.Json".
4. Click "Install".

**OR**
1. Remove Newtonsoft.Jason on reference.

Support
-------

Merchant Technical Support / Customer Care : support@molpay.com <br>
Marketing Campaign : marketing@molpay.com <br>
Channel/Partner Enquiry : channel@molpay.com <br>
Media Contact : media@molpay.com <br>
R&D and Tech-related Suggestion : technical@molpay.com <br>
Abuse Reporting : abuse@molpay.com

Disclaimer
----------
Any amendment by your end is at your own risk.

Changelog
----------
1. 2018-04-17 - v1.0.0 - Initial Release
2. 2018-04-19 - v1.0.1 - Update the SDK Structure
3. 2018-04-20 - v1.1.0 - Add payment page integration
4. 2018-05-07 - v1.2.0 - Add Seamless Integration and Indirect Status Requery
