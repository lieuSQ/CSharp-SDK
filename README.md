## Integrating MOLPay with C# SDK
Version 1.2.0 (updated)

### Pre-Requisite
1. Visual Studio 2012 or above.
2. MOLPay Development or Production ID.
3. MOLPay General API.

### Installation
#### Via File Explorer
1. Download `MOLPay.dll` library inside MOLPayCS.zip file
2. Open Visual Studio project, right click on your project name in `Solution Explorer` and choose `Add Reference`.
3. Click `Browse` to search for downloaded library. Click `OK` to add.

-------
### Payment Page integration

Add `using MolPayCS;` 

Create MOLPayCS object in order to access the properties of Base: 

```CSharp
MolPayCS.MOLPay object = new MolPayCS.MOLPay();
```
Set the values to Post data for MOLPay's payment request
```CSharp
object.Verifykey = "xxxxxxx";           //Replace ​xxxxxxxxxx with your MOLPay Verify Key
object._Domain = "XXXXXXX";             //Replace ​XXXXXX with your MOLPay Merchant ID
object._Amount = "100.00";              //2 decimal points numeric value
object._Orderid = "Testing321";         //alphanumeric, 32 characters
object.Bill_name = "MOLPay Test";
object.Bill_email = "example@gmail.com";
object.Bill_desc = "This is used for initiation of payment request";
object._Currency = "MYR";               //2 or 3 chars (ISO-4217) currency code
object.Country = "MY";                  //2 chars of ISO-3166 country code
object.Returnurl = "http://exampleurl"; //Desired returned page after payment page
// All 3 type of url is already combine in this returnurl
```
Set which type of environment with either **Sandbox** or **Production**
```CSharp
object._Type = "sandbox" // "sandbox" or "production"
```

Use `object.Pay();` to trigger POST data function

-------

### Payment endpoint integration
Add `using MolPayCS;` 

Create MOLPayCS object in order to access the properties of Base: 

```CSharp
MolPayCS.MOLPay object = new MolPayCS.MOLPay();
```
Set the values received from MOLPay's payment response
```CSharp
object.Vkey = "xxxxxxx"   //Replace ​xxxxxxxxxx with your MOLPay Secret_Key
object.TranID = String.Format("{0}", Request.Form["tranID"]);
object.Orderid = String.Format("{0}", Request.Form["orderid"]);
object.Status = String.Format("{0}", Request.Form["status"]);
object.DomainID = String.Format("{0}", Request.Form["domain"]);
object.Amount = String.Format("{0}", Request.Form["amount"]);
object.Currency= String.Format("{0}", Request.Form["currency"]);
object.Paydate = String.Format("{0}", Request.Form["paydate"]);
object.Appcode = String.Format("{0}", Request.Form["appcode"]);
object.Skey = String.Format("{0}", Request.Form["skey"]);
```
Set which type of enviroment with either **Sandbox** or **Production**
```CSharp
object.Type = "sandbox" // "sandbox" or "production"
```
### IPN(Instant Payment Notification)
Additional object must be set when using IPN
```CSharp
object.Treq = "1"  //Value is always 1. Do not change
```
Call the IPN function
```CSharp
object.IPN()
```
### Notification & Callback URL with IPN(Instant Payment Notification)
Set additional object for Notification URL 
```CSharp
object.Nbcb = String.Format("{0}", Request.Form["nbcb"]); 
```

### Sample of all 3 endpoints
`E.G` return & notification & callback URL (all 3 url are using this structure)

```CSharp
//invalid transaction if the key is different. Merchant might issue a requery to MOLPay to double check payment status with MOLPay. 
if (object.Skey != object.Key1checked())
    object.Status = "-1";
 
If status == "00" then  
 /*** NOTE : this is a user-defined function which should be prepared by merchant ***/
// action to change cart status or to accept order
// you can also do further checking on the paydate as well
// write your script here .....
Else
  // failure action. Write your script here .....
// Merchant might send query to MOLPay using Merchant requery
// to double check payment status for that particular order.
}
```
-------

### Requery
We have 5 type of requery method.
On first, same we create MOLPayCS object in order to access the properties of Base: 

```CSharp
MolPayCS.MOLPay obj = new MolPayCS.MOLPay();
```
#### 1. Query by unique transaction ID
Set the values to requery

```CSharp
 obj.Amount = "100.00";             // 2 decimal points numeric value
 obj.TxID = "xxxxxxxx";             // Replace ​xxxxxxxxx with Merchant transaction ID , which might be duplicated.
 obj.Domain = "XXXXXXXX";           // Replace ​XXXXXXX with your MOLPay Merchant ID
 obj.Verifykey = "zzzzzzzz";         // Replace ​zzzzzzz with your verify key
 obj.Url = "http://exampleurl";     // Desired page to get requery data
 obj.Type = "0";                    // 0 = plain text result (default),  1 = result via POST method
 obj.TypeID = "sandbox";            // Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryTransactionID() fucntion to trigger 
 ```CSharp
 obj.RequeryTransactionID();
 ```
Example response with type=0 (default output, plain text with linebreaks)
```CSharp
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
```CSharp
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

```CSharp
 obj.Amount = "100.00";             // 2 decimal points numeric value
 obj.OID = "xxxxxxxx";              // Replace ​xxxxxxxxx with the Order id that you want to check
 obj.Domain = "XXXXXXXX";           // Replace ​XXXXXXX with your MOLPay Merchant ID
 obj.Verifykey = "zzzzzzzz";         // Replace ​zzzzzzz with your verify key
 obj.Url = "http://exampleurl";     // Desired page to get requery data
 obj.Type = "0";                    // 0 = plain text result (default),  1 = result via POST method
 obj.Req4token = "1";               // 0 = No (default), 1 = Yes for more card related information
 obj.TypeID = "sandbox";            // Set which type of environment with either **sandbox** or **production**
 ```
 Use equeryOrderIDSingle() fucntion to trigger 
 ```CSharp
 obj.RequeryOrderIDSingle();
 ```
 
#### 3. Query by order ID (batch output)

Set the values to requery

```CSharp
 obj.OID = "xxxxxxxx";              // Replace ​xxxxxxxxx with the Order id that you want to check
 obj.Domain = "XXXXXXXX";           // Replace ​XXXXXXX with your MOLPay Merchant ID
 obj.Verifykey = "zzzzzzzz";         // Replace ​zzzzzzz with your verify key
 obj.Url = "http://exampleurl";     // Desired page to get requery data
 obj.Type = "0";                    // 0 = plain text result (default),  1 = result via POST method
 obj.Format = "0";                  // 0 = result string with delimiter ( | ), 1 = result in array
 obj.Req4token = "1";               // 0 = No (default), 1 = Yes for more card related information
 obj.TypeID = "sandbox";            // Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryOrderIDBatch() fucntion to trigger 
 ```CSharp
 obj.RequeryOrderIDBatch();
 ```
 
#### 4. Query by multiple order ID (batch output)

Set the values to requery

```CSharp
 obj.OIDs = "xx|yy|zz";             // ex) xx & yy & zz are the order id you want to check (separate with "|")
 obj.Delimiter = "|";               /* Single character, default is "|". Avoid using any symbol that might exist in order ID,
                                      and also any of these: “,%, *, <, >, ? , \, $, &, = */
 obj.Domain = "XXXXXXXX";           // Replace ​XXXXXXX with your MOLPay Merchant ID
 obj.Verifykey = "zzzzzzzz";         // Replace ​zzzzzzz with your verify key
 obj.Url = "http://exampleurl";     // Desired page to get requery data
 obj.Type = "0";                    // 0 = plain text result (default),  1 = result via POST method
 obj.Format = "0";                  // 0 = result string with delimiter ( | ), 1 = result in array
 obj.Req4token = "1";               // 0 = No (default), 1 = Yes for more card related information
 obj.TypeID = "sandbox";            // Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryMultiOrderID() fucntion to trigger 
 ```CSharp
 obj.RequeryMultiOrderID();
 ```
#### 5. Query by multiple transaction ID (batch output)

Set the values to requery

```CSharp
 obj.TIDs = "xx|yy|zz";             // ex) xx & yy & zz are the transaction id you want to check (separate with "|")
 obj.Domain = "XXXXXXXX";           // Replace ​XXXXXXX with your MOLPay Merchant ID
 obj.Verifykey = "zzzzzzzz";         // Replace ​zzzzzzz with your verify key
 obj.Url = "http://exampleurl";     // Desired page to get requery data
 obj.Type = "0";                    // 0 = plain text result (default),  1 = result via POST method
 obj.Format = "0";                  // 0 = result string with delimiter ( | ), 1 = result in array
 obj.Req4token = "1";               // 0 = No (default), 1 = Yes for more card related information
 obj.TypeID = "sandbox";            // Set which type of environment with either **sandbox** or **production**
 ```
 Use RequeryMultiTransactionID(); fucntion to trigger 
 ```CSharp
 obj.RequeryMultiTransactionID();
 ```
-------

## Seamless Integration
Add `using MolPayCS;` 

Create MOLPayCS object in order to access the properties of Base: 

```CSharp
 MolPayCS.Seamlesspayment obj = new MolPayCS.Seamlesspayment();
```

**Set the values for seamless integration**
```CSharp
 MolPayCS.Seamlesspayment obj = new MolPayCS.Seamlesspayment();
 obj.Merchantid = "xxx";        //Replace xxx with your merchant id
 obj.Vkey = "yyy";              //Replace yyy with your verify key
 obj.ReturnUrl = "zzz";         //Replace zzz with your return url
 obj.ProcessRequest();          // used to trigger seamless integration
```
### For quick demo with UI that we provide

1. Open the visual studio solution inside SeamlessIntergration.zip file

2. Set which type of environment with either **Sandbox** or **Production** in the User_Interface.aspx
![demotype](https://user-images.githubusercontent.com/26453374/39283372-7b674fc8-4940-11e8-88ff-0b177e8cc42e.PNG)

3. Set the `<form action =""` to the link you used to **Set the value for seamless integration** on above
![capture](https://user-images.githubusercontent.com/26453374/39284827-d67ed15e-4947-11e8-9c88-18ba511f3972.PNG)

4. Debug User_Interface.aspx

#### For more information about seamless please refer this link
[Latest MOLPay Seamless Integration](https://github.com/MOLPay/Seamless_Integration/wiki/Latest-MOLPay-Seamless-Integration-(non-PCI))

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
1. 2018-04-18 - v1.0.0 - Initial Release
2. 2018-04-24 - v1.1.0 - Add 5 requery method
3. 2018-04-26 - v1.2.0 - Add seamless integration, add a UI file inside zip to test seamless integration
