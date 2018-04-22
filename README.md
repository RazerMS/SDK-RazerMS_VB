## Integrating MOLPay with VB SDK
Version 1.0.1 (Updated)

### Pre-Requisite
1. Visual Studio 2012 or above.
2. MOLPay Development or Production ID.
3. MOLPay General API.

### Installation
#### Via File Explorer
1. Download `MOLPayVB.zip` file.
2. Extract the zip file. 
3. Open Visual Studio project, right click on your project name in `Solution Explorer` and choose `Add Reference`.
4. Click `Browse` to search for downloaded MOLPayVB.dll. Click `OK` to add.

### Usage
1. Create MOLPayVB object in order to access the properties of Base: 

```VB.Net
Dim objectMolpay As New MOLPayVB.MOLPay.Base
```
2. Set which type of enviroment with either **Sandbox** or **Production**
```VB.Net
objectMol.Type = "sandbox" ' "sandbox" or "production"
```
### Payment Page integration
Set these needed objects that will send the buyer infromation to MOLPay hosted payment page.
```VB
'Value set are examples
objectMolpay.MerchantID = "MOLPay" 'Replace with your Merchant ID provided  
objectMolpay.Vkey = "xxxx" 'Replace ​xxxxxxxxxx with your MOLPay Secret_Key
objectMolpay.Amounut = "1.1"
objectMolpay.OrderID = "Testing123"
```
Optional objects. Details can be key in at the payment page as well.
```VB
objectMolpay.Bill_Name = "Molpay Tester"
objectMolpay.Bill_email = "molpay@molpay.com
objectMolpay.Bill_desc = "Send me money"
objectMolpay.Currency = "MYR"
objectMolpay.Country = "Malaysia"
```
It is not needed to set all the Endpoint URLs. If not set,by default the Endpoint URLs would be taken from Merchant Portal's End Point setting.
```VB
objectMolpay.ReturnUrl = "molpay.com" 'Desired returned page after payment page
objectMolpay.NotificationUrl = "molpay.com" 
objectMolpay.CallbackUrl = "molpay.com"
```
Objects that are set will be send to hosted payment page by calling a function.
```VB
HttpContext.Current.Response.Write(ob.hostedPay())
```
### Payment endpoint integration
Set the values received from MOLPay's payment page.
```VB.Net
objectMolpay.Vkey = "xxxxxxx"   'Replace ​xxxxxxxxxx with your MOLPay Secret_Key
objectMolpay.TranID = request.Form("tranID")
objectMolpay.OrderID = request.Form("orderid")
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
objectMolpay.getKey1() 
```
Return value of the function is a string which contains the key1 value.

#### Sample of all 3 endpoints
`E.G` return & notification URL

```VB
'invalid transaction if the key is different. Merchant might issue a requery to MOLPay to double check payment status with MOLPay. 
 If objectMolpay.Skey()  <> objectMolpay.getKey1()  then   
  objectMolpay.Status= -1
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
If objectMolpay.Skey <> objectMolpay.getKey1 then   
  objectMolpay.Status= -1
End if 
 
If objectMolpay.Status = "00" then  
  'write your scripe here.... 
Else
   'failure action   
   'write your script here ..... 
End if

'callback IPN feedback to notify MOLPay 
response.write( objectMolpay.IPN)
 
```
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
