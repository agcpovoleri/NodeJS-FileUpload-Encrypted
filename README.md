# NodeJS-FileUpload-Encrypted
File upload with encryptation and signature validation with NodeJS and URSA

Validate a form based file upload that receives a encrypted file and a signature. The code decrypt the content and validate with user signature passed as form attribute.

1 - Generate private and public keys
* GENERATE PRIVATE KEY:
   <code> openssl genrsa -aes128 -passout pass:<keyPassword> -out privateKey.pem 4096 </code>
   
* GENERATE PUBLIC KEY:
   <code> openssl rsa -in privateKey.pem -passin pass:<keyPassword> -pubout -out publicKey.pem </code>
 - Generate server private key (bobPrivateKey.pem)
 - Generate server public key (bobPublicKey.pem)
 - Generate client private key (alicePrivateKey.pem)
 - Generate client public key (alicePublicKey.pem)


2 - Prepare Server files:
- Create a folder ./keys
- Copy SERVER private key to ./keys folder
- Copy CLIENT public key to ./keys folder

2 - Preparing Client files:
  
- Select a file that will be sent to the server (sendFile.txt)
- Encrypt file with user public server key
	<code> openssl rsautl -in sendFile.txt  -out encryptedFile.enc -pubin -inkey bobPublicKey.pem  -encrypt </code>
- Sign with private user key
	<code> openssl dgst -sha256 -sign alicePrivateKey.pem -out sign.sha256 encryptedFile.enc </code>
- Extract signature from 'sign.sha256'
  <code> openssl base64 -in sign.sha256 -out signature.txt </code>
  
3 - Reproducing:

  - Install a SERVER private key in a ./keys
  - Run the FileUploadCryptValidation.js on a machine with nodejs.
  <code> nodejs FileUploadCryptValidation.js </code>
  - On the browser of your choice, access http://<machine_ip_address>:3000/form.html 
  - On the form, select the <b>Encrypted file</b> from step 2.1 and use the generated sha256 signature from step 2.4. 
  Submit the form!

4 - You will be able to see the results described on the page. 
Note that if you send an invalid signature for the file, the file will be removed from server.
