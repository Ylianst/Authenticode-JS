# Authenticode-JS

Authenticode-JS is a fully NodeJS tool that can sign Windows executables. You can also get information about an executable signature, un-sign an executable and generate your own code signing certificate.

This module can be useful when wanting to sign windows executable on non-windows platforms, like signing a Windows executable on Linux or macOS. This code is used by [MeshCentral](https://meshcentral.com) to code-sign the agent on each installed server.

## Using as a command line tool

You can run authenticode.js as a command line tool like this:

```
node authenticode
```

You will see this:

```
MeshCentral Authenticode Tool.
Usage:
  node authenticode.js [command] [options]
Commands:
  info: Show information about an executable.
          --json                   Show information in JSON format.
  sign: Sign an executable.
          --exe [file]             Required executable to sign.
          --out [file]             Resulting signed executable.
          --pem [pemfile]          Certificate & private key to sign the executable with.
          --desc [description]     Description string to embbed into signature.
          --url [url]              URL to embbed into signature.
  unsign: Remove the signature from the executable.
          --exe [file]             Required executable to un-sign.
          --out [file]             Resulting executable with signature removed.
  createcert: Create a code signging self-signed certificate and key.
          --out [pemfile]          Required certificate file to create.
          --cn [value]             Required certificate common name.
          --country [value]        Certificate country name.
          --state [value]          Certificate state name.
          --locality [value]       Certificate locality name.
          --org [value]            Certificate organization name.
          --ou [value]             Certificate organization unit name.
          --serial [value]         Certificate serial number.

Note that certificate PEM files must first have the signing certificate,
followed by all certificates that form the trust chain.
```

## Using as a module

You can use authenticode-js in your code as an external module. To load an exectuable call `createAuthenticodeHandler`. This call will return null if the executable can't be loaded or is invalid.

```
var exehandler = require("authenticode").createAuthenticodeHandler("/tmp/windowsexecutable.exe");
```

You can then get information about the existing signature of this executable:

```
console.log(exehandler.header);            // Display executable header information.
console.log(exehandler.fileHashAlgo);      // Display the type of hashing used, typically 'sha256' or 'sha384'.
console.log(exehandler.fileHashSigned);    // Display the hash included in the signature.
console.log(exehandler.fileHashActual);    // Display the actual hash of the file.
console.log(exehandler.signingAttribs);    // DIsplay any signing attributes, typically description and/or url.
```

You can remove the signature of an executable like this:

```
exehandler.unsign({ out: '/tmp/windowsexecutable-unsigned.exe' });
```

You can sign or re-sign an exectuable. You can load the certificate PEM file using the built in `loadcertificate()` method, or you can pass null as the certificate and a test certificate will be generated and used to sign the executable. The `desc` and `url` options are optional.

```
const cert = require("authenticode").loadCertificates("/tmp/signingcert.pem");
exehandler.sign(cert, { out: '/tmp/windowsexecutable-signed.exe', desc: "description", url: "https://sample.org" });
```

You can generate your own code signing certificate. All of the certificate creation values are optional, but `cn` should really be present. Oddly, the serial number should be an integer that starts with a single leading `0`, for example `0123` or `01111`. Once created, you can use node-forge to save the certificate.

```
const cert = require("authenticode").createSelfSignedCert({ cn: "commonName", serial: "012345", state: "state", locality: "locality", country: "x", org: "org", orgunit: "orgunitf" });
require('fs').writeFileSync("/tmp/signingcert.pem", require('node-forge').pki.certificateToPem(cert.cert) + '\r\n' + require('node-forge').pki.privateKeyToPem(cert.key));
```

## Certificate file format

In the above examples, a `signingcert.pem` file contains the certificate and private key used to sign the windows executable. The format of this .pem file is as follows:

```
This is the code signing public certificate, must be first.
-----BEGIN CERTIFICATE-----
MIIEQDCC...
-----END CERTIFICATE-----

This is the code signing certificate private key, can be anywhere in the file.
-----BEGIN RSA PRIVATE KEY-----
MIIG5AIB...
-----END RSA PRIVATE KEY-----

This is one of the public certificate in the validation chain.
-----BEGIN CERTIFICATE-----
MIIEQzCCA...
-----END CERTIFICATE-----

This is another public certificate in the validation chain, you can put as many as needed.
-----BEGIN CERTIFICATE-----
MIIEQzCCA...
-----END CERTIFICATE-----
```

## License
This software is licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).