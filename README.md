## Integrating MOLPay with SDK
Version 1.0.0

### Pre-Requisite
1. Visual Studio 2012 or above.
2. MOLPay Development or Production ID.
3. MOLPay General API.

### Installation
#### Via File Explorer
1. Download `MOLPay.dll` library.
2. Open Visual Studio project, right click on your project name in `Solution Explorer` and choose `Add Reference`.
3. Click `Browse` to search for downloaded library. Click `OK` to add.

### Usage
Create MOLPayVB object in order to access the properties of Base: 

```VB.Net
Dim objectMolpay As New MOLPayVB.MOLPay.Base
```
Set the values received from MOLPay's payment page 
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
Set which type of enviroment with either **Sandbox** or **Production**
```VB.Net
objectMol.Type = "sandbox" ' "sandbox" or "production"
```
### IPN(Instant Payment Notification)
Additional object must be set and passed when using IPN
```VB.Net
objectMolpay.Treq = "1" 'Value is always 1. Do not change
```
Call the IPN function
```VB.Net
objectMolpay.IPN()
```
### Notification URL wtih IPN(Instant Payment Notification)
Set additional object for Notification URL 
```VB.Net
objectMolpay.Nbcb = request.Form("nbcb")  
```
### Verifying the integrity of data send by MOLPay
```VB.Net
objectMolpay.getKey1() 
```
Return value of the function is a string which contains the key1 value.

### Sample of all 3 endpoints
E.G return URL,notification script

```VB.Net
'invalid transaction if the key is different. Merchant might issue a requery to MOLPay to double check payment status with MOLPay. 
 If objectMolpay.Skey()  <> objectMolpay.getKey1()  then   
  status= -1
 End if 
 
If status = "00" then  
  ’checking the validity of cart amount & orderid.  
  ’if the verification test passed then can update the order status to paid. 
  ’you can also do further checking on the paydate as well 
Else
  ’failure action  
  ’Merchant might send query to MOLPay using merchant requery  
  'to double check payment status for that particular order. 
End if 
  ’Merchant is to implement IPN to ack on receiving of payment status ’ regardless the payment status
```
E.G callback URL
```VB.Net
If objectMolpay.Skey()  <> objectMolpay.getKey1()  then   
  status= -1
End if 
 
If objectMolpay.Status() = "00" then  
  'write your scripe here.... 
Else
   'failure action   
   'write your script here ..... 
End if

If objectMolpay.Nbcb = "1" then
 'callback IPN feedback to notified MOLPay 
  response.write( “CBTOKEN:MPSTATOK”)
  Exit [construct]
End if 
```

### Support or Contact
This is NOT official library from MOLPay. Therefore, no official support will be given to you. Please use this library as a guideline to integrate MOLPay service with VB.NET project. If there is any dispute between this document and official MOLPay release, please use the MOLPay version. 

You also understand that you accept the risk and no other party will be held liable for any loss or damage cause by the usage of any information obtained in this page or software library.

### Changelog
1. 2018-04-16 - v1.0.0 - Initial Release
